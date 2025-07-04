# First Responder Kit - Brent Ozar

> If you run sp_Blitz and you do what the high priorities say... And then you run sp_BlitzFirst to find out why performance is bad, read the URL advice and follow the instructions... You won't need me - Brent Ozar

https://training.brentozar.com/p/how-i-use-the-first-responder-kit

| Procedure                   | Description                         |
| --------------------------- | ----------------------------------- |
| sp_Blitz                    | Server-wide health check.           |
| sp_BlitzFirst               | Performance check.                  |
| sp_BlitzCache               | Find the queries causing the waits. |
| sp_BlitzWho, sp_WhoIsActive | What's running now.                 |
| sp_BlitzLock                | Analyzing deadlocks.                |
| sp_BlitzIndex               | Checking indexes that help queries. |
| sp_DatabaseRestore          | Restore databases.                  |

Everything here has a link column for further research.

### Quick reference

```sql
sp_Blitz @CheckServerInfo = 1 -- Health check
sp_BlitzFirst @SinceStartup = 1 -- Top wait stats
sp_BlitzCache @SortOrder = 'avg duration', @MinutesBack = 90 -- Top 10 queries causing those wait stats
```

# sp_Blitz

Overall health check. Is the server configured optimally.

Does all kinds of checks, and orders them by priority. Level 1-50 are fireable offenses.

> DBAs don't get fired because a server is slow. They get fired if a server is down, and they can bring it back.

Use when:

-   Taking over a new SQL Server.
-   Before signing off that a server is read for production.
-   When you come back from vacation.
-   DON'T use on a scheduled basis.

Show extra data under `Server Info`.

```
sp_Blitz @CheckServerInfo = 1
```

### CHECKDB

Check for data corruption.

It's essential to run CHECKDB on a regular basis to find errors if they exist because if there's corruption, we need to fix it before our last good backups disappear.

```sql
-- Look for dbi_dbccLastKnownGood for the time it last ran.
DBCC DBINFO('StackOverflow') WITH TABLERESULTS;

-- The corruption check
DBCC CHECKDB('StackOverflow') WITH NO_INFOMSGS, ALL_ERRORMSGS;
-- 40 min execution time
```

# sp_BlitzFirst

Performance health check. Find the top server bottlenecks.

Check for performance issues as of **right now**, it helps with **everything is on fire** problems.

It takes a snapshot, and then another one after 5 seconds, and it compares the two. You can increase the gap as shown below.

```sql
sp_BlitzFirst @ExpertMode = 1, @Seconds = 60
```

A great way to use this is to set an Agent Job that runs the check every 15 minutes. Use this setting:

```
EXEC dbo.sp_BlitzFirst
@OutputDatabaseName = 'DBAtools',
@OutputSchemaName = 'dbo',
@OutputTableName = 'BlitzFirst',
@OutputTableNameFileStats = 'BlitzFirst_FileStats',
@OutputTableNamePerfmonStats = 'BlitzFirst_PerfmonStats',
@OutputTableNameWaitStats = 'BlitzFirst_WaitStats',
@OutputTableNameBlitzCache = 'BlitzFirst_BlitzCache',
@OutputTableNameBlitzWho = 'BlitzFirst_BlitzWho'
```

You can then query the tables to check what happened in the past.

```sql
sp_BlitzFirst @SinceStartup = 1
```

Base the stats on the complete server uptime. Good when you don't have an Agent Job history.

### Wait types

Use the result of this to solve with sp_BlitzCache.

```
sp_BlitzFirst @sinceStartup = 1, @OutputType = 'Top10'
```

A quick wait types report (try to have at least 24 hours of hours sample/wait time). Solve the top wait times first for the most improvement. There are links explaining each one.

# sp_BlitzCache

Find the queries causing the top bottlenecks (wait types).

It shows the top 10 most resource-intensive queries.

IMPORTANT: Run it after finding your top wait times with `sp_BlitzFirst @sinceStartup = 1, @OutputType = 'Top10'`.

By default it shows the most expensive by CPU usage, but the problem might be of another wait type, so modify the @SortOrder parameter based on your top wait type.

Based on your top wait in sp_BlitzFirst, here's a decoder ring for the 6 most common wait types, and how you should use sp_BlitzCache's @SortOrder parameter:

| wait type                      | sort         | description                                                                                                                            |
| ------------------------------ | ------------ | -------------------------------------------------------------------------------------------------------------------------------------- |
| CXPACKET, CXCONSUMER, LATCH_EX | CPU, READS   | Queries going parallel to read a lot of data or do a lot of CPU work.                                                                  |
| LCK%                           | DURATION     | Locking, so look for long-running queries, and look for the warning of "Long Running, Low CPU." That's probably a query being blocked. |
| PAGEIOLATCH                    | READS        | Reading data pages that aren't cached in RAM.                                                                                          |
| RESOURCE_SEMAPHORE             | MEMORY GRANT | Queries can't get enough workspace memory to start running.Not available in older versions of SQL.                                     |
| SOS_SCHEDULER_YIELD            | CPU          | CPU pressure.                                                                                                                          |
| WRITELOG, HADR_SYNC_COMMIT     | WRITES       | Writing to the transaction log for delete/update/insert (DUI) work.                                                                    |

```sql
sp_BlitzCache @SortOrder = 'READS'
```

It returns two results:

1. The top 10 queries by your sort order.
2. Explanations of the "Warnings" column.

IMPORTANT: Always check the bottom result first, and focus on priority 1 warnings. Read the URL explanations. Fix that first, or you're wasting time.

Look at the columns to the right for Execution, Total Reads, Total CPU etc.

A lot of times, over-night maintenance jobs will turn up as the most expensive queries. You can filter those out like this:

```sql
sp_BlitzCache @SortOrder = 'avg duration', @MinutesBack = 90 -- How many minutes since 8AM (start of work) have passed?
```

# sp_BlitzIndex

Analyzes desing issues with indexes. This is not a quick-fix script.

Index tuning is the fastest way to make queries go faster without buying hardware or spending time in development. Indexing is as much an art as a science, though: there are a lot of vague guidelines, and sometimes you have to break those guidelines in order to get better performance. sp_BlitzIndex is about analyzing your database's overall issues, understanding which indexes are just holding you back, and which indexes Clippy wants to add.

```sql
-- returns prioritized findings based on the D.E.A.T.H. optimization method.
sp_BlitzIndex

-- Inventory of existing indexes, good for copy/paste in Excel for offline analysis. Has a @SortOrder parameter for things like rows, size, reads, writes, lock time...
sp_BlitzIndex @Mode = 2, @SortOrder = 'rows'
```

You can tune indexes with the D.E.A.T.H. method:

-   Dedupe.
-   Eliminate Overlaps and unused indexes.
-   Add desperately needed indexes.
-   Tune indexes for specific queries.
-   Heaps i.e. Clustered indexes.

Look for `Est. benefit per day` above 1,000,000 as these are worth solving.

Ex. A finding of `Index Suggestion: High Value Missing Index` and a usage of `27,425 uses, Impact: 100%; Avg query cost: 810` says that this index should be created because it will speed up the query by 100%. SQL Server, under definition, gives just the columns in the table, and NOT the order they should be in. Look for the Size column to try and be around the 5 indexes with 5 columns per index, per table best practice.

If you have this query:

```sql
SELECT * FROM Phonebook WHERE LastName = 'Ozar';
```

And these indexes:

```
LastName, FirstName, MiddleName INCLUDE Address, PhoneNumber

LastName, PhoneNumber
```

You are better off with just using the first one.

### One table analysis

```sql
EXEC dbo.sp_BlitzIndex @DatabaseName='DATABASE_NAME', @SchemaName='dbo', @TableName='TABLE_NAME';
```

Results:

-   1st: Indexes we already have.
-   2nd: Desperately needed indexes.
-   3rd: Table structure.
-   4th: Foreign keys
-   5th: Table statistics. Histogram (how pages are batched) for query tuning.

### Usage stats

The amount of times the index made the query go:

-   Reads = FASTER
-   Writes = SLOWER (because of the INSERT/UPDATE/DELETE to keep the index in sync)

Remove indexes that have 0 reads, or a low read high write ratio.

Before you do any tuning, you want to server to have been up for at least 1 business cycle i.e. a full month. If the business is the same all the time (like a stock exchange), a day is enough. Avoid longer than a month.

This is an Excel friendly way to see the indexes.

### Index tuning example workflow

Create a change script:

```sql
-- The DROP and CREATE statements are generated for copying in the sp_BlitzIndex report.

/* Merge these 3 indexes into 1:

    The CURRENT definitions of the 3 indexes.

    into this new one:
*/

CREATE INDEX DownVotes_DisplayName_LastAccessDate ON dbo.Users(DownVotes, DisplayName, LastAccessDate)
GO
DROP INDEX [Index_DownVotes] ON [StackOverflow].[dbo].[Users];
DROP INDEX [DownVotes] ON [StackOverflow].[dbo].[Users];
DROP INDEX [IX_DV_LAD_DN] ON [StackOverflow].[dbo].[Users];
GO

/* Undo script (if you make a mistake and want to revert):

    The CREATE queries for the ORIGINAL 3 indexes.
*/
```

# sp_BlitzLock

Analyzes recent deadlocks, and groups them by table, query, app, login...

SQL Servers deadlock monitor wakes up every 5 seconds, looks for this scenario, and when it finds it, it kills the query that's easiest to roll back, called the victim.

Run when:

-   When sp_Blitz warns you about a high number of deadlocks per day.
-   When users complain about deadlock errors.

```sql
sp_BlitzLock
```

Usage:

1. Run with no parameters.
2. Jump to the bottom result set.
3. Identify the 1-3 tables involved in most deadlocks and tune their indexes.
4. Identify the top 1-3 queries, tune them, make them more SARGable, make their transactions short and sweet, change their isolation level.

To avoid deadlocks, you want to use tables in the same order everywhere. Otherwise, deadlocks happen like this (executed in sequence):

**LEFT SIDE**

```sql
BEGIN TRAN

-- Run 1st
UPDATE dbo.Lefty
    SET Numbers = Numbers + 1;
GO

-- Run 3rd -- BLOCKING HAPPENS HERE, NOT DEADLOCK!
UPDATE dbo.Righty
    SET Numbers = Numbers + 1;
GO
```

**RIGHT SIDE**

```sql
BEGIN TRAN

-- Run 2nd
UPDATE dbo.Righty
    SET Numbers = Numbers + 1;
GO

-- Run 4th - DEADLOCK HAPPENS HERE!!!!!!
UPDATE dbo.Lefty
    SET Numbers = Numbers + 1;
GO
```

# sp_BlitzWho

It lists currently runnig queries, sorted from longest running, to shortest.

Useful in EMERGENCIES.

Use when:

-   When people are complaining that SQL Server is slow.

sp_WhoIsActive is a similar script, used when the server is so slow, sp_BlitzWho doesn't respond.

```sql
sp_BlitzWho @ExpertMode = 1 -- Show extra columns like logical reads, CPU usage
```

# Identifying slow queries in MySQL

1. MySQL Slow query log. Enable it.
2. Use `processlist`.

```sql
show processlist;
show full processlist;
```
