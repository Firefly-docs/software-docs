# Introduction to GStreamer

## Linux GStreamer Usage and Platform Support

### 1. Introduction to GStreamer

GStreamer is an open-source, plugin-based multimedia framework for audio and video playback, capture, encoding and decoding, format conversion, network transmission, recording, and media analysis.

Applications connect multiple elements through a pipeline. Data flows from a source, passes through demuxing, parsing, encoding or decoding, and processing, and finally reaches a sink.

```text
File, camera, or network stream
        │
        ▼
     Source
        │
        ▼
 Demuxer / Depayloader
        │
        ▼
      Parser
        │
        ▼
 Encoder / Decoder
        │
        ▼
 Convert / Scale / Filter
        │
        ▼
   Display / File / Network
```

GStreamer's main features include:

- Functionality is assembled through plugins, so applications do not need to handle every container and codec format directly.
- Caps are used for media format negotiation.
- Dynamic pads, multiple audio/video streams, and branched pipelines are supported.
- The same high-level pipeline can use software codecs or hardware-accelerated plugins for different platforms.
- Command-line tools, a C API, and bindings for multiple languages are available.
