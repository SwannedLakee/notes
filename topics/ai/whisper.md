# Install

```
pipx install openai-whisper
```

# Transcription

```
whisper filename.mp3 --model tiny --language en --output_format txt --output_dir ./output
```

| Model    | Size  | Speed         | 1 min audio | 1 hour audio |
| -------- | ----- | ------------- | ----------- | ------------ |
| `tiny`   | 75MB  | fastest       | 10 sec      | 10 min       |
| `base`   | 142MB | fast          | 20 sec      | 20 min       |
| `small`  | 466MB | balanced      | 1 min       | 1 hour       |
| `medium` | 1.5GB | accurate      | 3 min       | 3 hours      |
| `large`  | 3GB   | most accurate | 7 min       | 7 hours      |
