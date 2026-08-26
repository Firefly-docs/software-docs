# FFmpeg User Guide

## General FFmpeg Usage

### 1. Introduction to FFmpeg

FFmpeg is a collection of open-source multimedia processing tools and libraries. It can parse, convert, encode, decode, filter, mux, and transmit video, audio, subtitles, and metadata.

Common FFmpeg command-line tools include:

| Tool | Purpose |
| --- | --- |
| `ffmpeg` | Transcoding, remuxing, capture, filtering, and streaming |
| `ffprobe` | Inspecting media containers, streams, frames, and metadata |
| `ffplay` | A simple player for testing and debugging |

The main libraries are:

| Library | Purpose |
| --- | --- |
| `libavcodec` | Video and audio encoding and decoding |
| `libavformat` | Media containers, demuxing, muxing, and network protocols |
| `libavfilter` | Audio and video filter graphs |
| `libavutil` | Pixel formats, time bases, data structures, and common utilities |
| `libswscale` | Software image scaling and pixel-format conversion |
| `libswresample` | Audio resampling, channel conversion, and mixing |
| `libavdevice` | Input and output for cameras, sound devices, and displays |

An FFmpeg processing pipeline can be simplified as follows:

```text
Input file / camera / network stream
                │
                ▼
             Demuxing
                │
                ▼
              Decoding
                │
                ▼
         Video or audio filters
                │
                ▼
              Encoding
                │
                ▼
              Muxing
                │
                ▼
       Output file / network stream
```

Remuxing skips decoding, filtering, and encoding, and directly copies the compressed streams. It is fast and does not reduce media quality.
