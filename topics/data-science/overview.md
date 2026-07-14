# Overview

> Data science = Data engineering + Statistics + Machine learning + Communication/domain thinking + Programming tools

Data science is an applied discipline built from four core components:

1. Data engineering

- Getting data into usable form.
- SQL databases, data extraction
- Data cleaning, missing values, formatting
- Pipelines, APIs, basic ETL (extract, transform, load)

2. Statistics and probability

- Understanding uncertainty and relationships in data.
- Descriptive statistics (mean, variance, distributions)
- Inference (hypothesis testing, confidence intervals)
- Regression and correlation
- Experiment design (A/B testing)

3. Machine learning

- Building predictive or pattern-based models.
- Supervised learning (classification, regression)
- Unsupervised learning (clustering, dimensionality reduction)
- Model evaluation and validation
- Feature engineering

4. Communication and domain understanding

- Turning analysis into decisions.
- Data visualization (charts, dashboards)
- Storytelling with data
- Business or domain context (finance, health, marketing, etc.)
- Translating results into actions

Supporting layer: programming tools

- Python or R
- Libraries: Pandas, NumPy, scikit-learn
- Notebooks, version control, basic software practices

# Differences

Statistics is the mathematical foundation for reasoning under uncertainty. It focuses on designing experiments, collecting data, and drawing conclusions from samples about populations. Core work: estimation, hypothesis testing, confidence intervals, probability models, inference. Output is usually interpretable statements about relationships and uncertainty.

Data science is an applied field that combines statistics, programming, and domain knowledge to extract value from data. It covers the full pipeline: data collection, cleaning, transformation, analysis, visualization, and communication. It often uses statistical methods plus software engineering and sometimes machine learning. Output is insights, dashboards, reports, or data-driven decisions.

Machine learning is a subset of artificial intelligence focused on algorithms that learn patterns from data to make predictions or decisions without being explicitly programmed with rules. Core work: training models, optimizing loss functions, generalization, feature learning. Output is predictive models (classification, regression, clustering, recommendation systems).

Relationship:

Statistics → foundation for inference and uncertainty modeling
Machine learning → prediction-focused algorithms built on statistics + optimization
Data science → umbrella practice that uses both plus engineering to solve real problems

# Learning

Start with programming and data handling. Python is standard. Learn basic syntax, data structures, functions, and file handling. Then move directly into NumPy and Pandas for numerical work and tabular data manipulation.

Learn statistics in parallel. Focus on probability, distributions, descriptive statistics, hypothesis testing, correlation vs causation, and regression basics. Skip excessive theory early; prioritize intuition and application.

Add data visualization. Learn Matplotlib and Seaborn. Practice turning datasets into clear plots: histograms, scatter plots, box plots, time series.

Work with real datasets immediately. Kaggle datasets or public government data. Do cleaning, exploration, and simple analysis. This is where most learning happens.

Then machine learning basics. Start with scikit-learn. Learn train/test split, linear regression, logistic regression, decision trees, random forests. Understand overfitting, bias-variance, evaluation metrics (accuracy, precision, recall, RMSE).

Build small projects repeatedly instead of one large project. Examples: spam classifier, house price prediction, sales analysis, recommendation prototype.

Learn SQL early. Most real data lives in databases. Focus on SELECT, JOIN, GROUP BY, filtering, aggregation.

After fundamentals, specialize. Options: NLP, computer vision, time series, deep learning, or business analytics.

Core progression:
Python → Pandas → SQL → Statistics → Visualization → Machine learning → Projects → specialization

Constant practice with real datasets matters more than passive learning.
