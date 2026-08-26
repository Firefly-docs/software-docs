# General Troubleshooting

## General FFmpeg Usage

### 7. General Troubleshooting

| Symptom | What to check |
| --- | --- |
| `Unknown encoder` | Use `ffmpeg -encoders` to confirm that the encoder was built |
| `Invalid data found when processing input` | Check file integrity and whether the detected container format is correct |
| `Could not find codec parameters` | Increase `-analyzeduration` and `-probesize`, or check whether the live stream contains complete headers |
| `Non-monotonous DTS` | Inspect input timestamps, concatenation, remuxing, and the capture clock |
| Output has no audio or subtitles | Use `ffprobe` and `-map` to select streams explicitly |
| Output file is too large | Adjust CRF, bitrate, resolution, or frame rate |
| Processing is slow | Check the encoding `preset`, software filters, I/O, and hardware acceleration status |

Enable detailed logging:

```bash
ffmpeg -loglevel verbose -i input.mp4 <output-options> output.mp4
```

Save a complete log for issue reporting:

```bash
ffmpeg -report -i input.mp4 <output-options> output.mp4
```
