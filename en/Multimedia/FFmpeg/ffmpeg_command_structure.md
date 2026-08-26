# Basic Command Structure

## General FFmpeg Usage

### 3. Basic Command Structure

The basic structure of an FFmpeg command is:

```bash
ffmpeg [global options] [input options] -i <input> [output options] <output>
```

For example:

```bash
ffmpeg -hide_banner -i input.mp4 -c:v libx264 -c:a aac output.mp4
```

Option placement is important:

- Options before `-i` normally apply to the following input.
- Options before an output file normally apply to that output.
- Different options can be assigned to each input and output.

Common global options:

| Option | Description |
| --- | --- |
| `-hide_banner` | Hide the version banner and reduce log output |
| `-y` | Overwrite existing output files without asking |
| `-n` | Stop if the output file already exists |
| `-loglevel <level>` | Set the log level, such as `error`, `info`, `verbose`, or `debug` |
| `-stats` | Display real-time processing progress |
| `-benchmark` | Print processing-time and resource statistics |
