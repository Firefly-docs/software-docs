# Application Integration

## 4. General GStreamer Usage

### 4.6 Application Integration

`appsrc` and `appsink` exchange data between an application and a pipeline:

- `appsrc`: the application feeds data into the pipeline, for example, a decrypted proprietary stream.
- `appsink`: the application retrieves data from the pipeline, for example, decoded frames for image processing.

A typical application structure:

```text
Custom input → appsrc → GStreamer Pipeline → appsink → Image algorithm
                                            │
                                            ▼
                         appsrc → Post-processing / Encoding / Display
```

The command line is mainly used for validation. Production applications usually use the GStreamer API to handle buffers, timestamps, EOS, and state changes.
