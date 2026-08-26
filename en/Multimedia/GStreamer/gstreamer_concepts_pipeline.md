# Pipeline

## 2. Basic Concepts

### 2.2 Pipeline

On the command line, use `!` to connect elements:

```bash
gst-launch-1.0 videotestsrc ! autovideosink
```

This pipeline uses `videotestsrc` to generate a test pattern, and then lets `autovideosink` select a display plugin automatically.

Production applications usually create and control pipelines through the GStreamer API. `gst-launch-1.0` is mainly used to validate designs, reproduce issues, and run performance tests.
