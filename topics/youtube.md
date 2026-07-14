# Download video

720p

```
yt-dlp \
    -f "bestvideo[height<=720][ext=mp4]+bestaudio[ext=m4a]/best[height<=720][ext=mp4]" \
    --merge-output-format mp4 \
    -o "$HOME/Downloads/%(title)s.%(ext)s" \
    --no-playlist \
    "https://www.youtube.com/watch?v=VIDEO_ID"
```

1080p

```
yt-dlp \
    -f "bestvideo[height<=1080][ext=mp4]+bestaudio[ext=m4a]/best[height<=1080][ext=mp4]" \
    --merge-output-format mp4 \
    -o "$HOME/Downloads/%(title)s.%(ext)s" \
    --no-playlist \
    "https://www.youtube.com/watch?v=VIDEO_ID"
```

Best available quality

```
yt-dlp \
    -f "bestvideo[ext=mp4]+bestaudio[ext=m4a]/best[ext=mp4]" \
    --merge-output-format mp4 \
    -o "$HOME/Downloads/%(title)s.%(ext)s" \
    "https://www.youtube.com/watch?v=VIDEO_ID"
```

## Parameters

| Parameter                         | Description                                                                |
| --------------------------------- | -------------------------------------------------------------------------- |
| `-f`                              | Selects the download format.                                               |
| `bestvideo[height<=720][ext=mp4]` | Downloads the best MP4 video stream up to 720p.                            |
| `bestaudio[ext=m4a]`              | Downloads the best M4A audio stream.                                       |
| `+`                               | Merges the video and audio streams.                                        |
| `/best[height<=720][ext=mp4]`     | Fallback: downloads a single MP4 file if separate streams are unavailable. |
| `--merge-output-format mp4`       | Ensures the final merged file is MP4. Requires FFmpeg.                     |
| `-o`                              | Sets the output filename and location.                                     |
| `$HOME/Downloads/`                | Saves files to the user's Downloads folder.                                |
| `%(title)s`                       | Replaced with the YouTube video title.                                     |
| `%(ext)s`                         | Replaced with the file extension.                                          |
| `--no-playlist`                   | Downloads only the video, not the entire playlist.                         |
| `URL`                             | The YouTube video address.                                                 |

# Check available formats

```
yt-dlp -F "https://www.youtube.com/watch?v=VIDEO_ID"
```

# Download playlist text

```
yt-dlp --flat-playlist --print "https://www.youtube.com/watch?v=%(id)s" "https://www.youtube.com/playlist?list=LIST_ID"
```

# Download audio

```
yt-dlp -f -x "worstaudio" -o "./audio.%(ext)s" --no-playlist "https://www.youtube.com/watch?v=VIDEO_ID"
```

```
yt-dlp -f bestaudio -x --audio-format mp3 --audio-quality 0 URL
```

### Explanation of the Command

```bash
yt-dlp \
    -f "worstaudio" \
    -x \
    --audio-quality 9 \
    -o "$OUTPUT_DIR/%(title)s.%(ext)s" \
    --no-playlist \
    "$url"
```

Downloads the audio from a YouTube video, extracts it, and saves it to a specified directory.

| Parameter           | Description                                                                                                                                         |
| ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| `-f "worstaudio"`   | Selects the lowest-quality audio-only stream. Reduces file size and bandwidth usage. (`bestaudio` = highest quality, `worstaudio` = lowest quality) |
| `-x`                | Extracts audio from the downloaded media. Requires FFmpeg.                                                                                          |
| `--audio-quality 9` | Sets conversion quality. For MP3: `0` = best quality, `9` = lowest quality. Only affects re-encoded audio.                                          |
| `-o`                | Sets the output filename and location.                                                                                                              |
| `$OUTPUT_DIR`       | Destination folder.                                                                                                                                 |
| `%(title)s`         | Replaced with the YouTube video title.                                                                                                              |
| `%(ext)s`           | Replaced with the file extension.                                                                                                                   |
| `--no-playlist`     | Downloads only the specified video, ignoring playlists.                                                                                             |
| `$url`              | The YouTube video URL.                                                                                                                              |

## Note

These options optimize for minimum size:

```bash
-f "worstaudio"
--audio-quality 9
```

For better quality:

```bash
yt-dlp -f bestaudio -x --audio-format mp3 --audio-quality 0 URL
```

To keep the original audio without re-encoding:

```bash
yt-dlp -f bestaudio -x URL
```
