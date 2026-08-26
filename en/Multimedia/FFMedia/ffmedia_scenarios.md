# Typical Application Scenarios

## Low-Latency Video Preview

After obtaining video from a camera or network stream, MPP is used for decoding and DRM is used for direct display. This is suitable for local preview, device debugging, remote image echo, and similar scenarios.

```text
Camera / RTSP -> MppDec -> DRM Display
```

## Real-Time Transcoding and Streaming

Read a local file, camera, or network stream, then push it to an RTSP/RTMP service after decoding, scaling, rotation, or re-encoding.

```text
File / Camera / RTSP -> MppDec -> RGA -> MppEnc -> RTSP / RTMP
```

## Multi-Channel Video Processing

Pull multiple RTSP video streams at the same time, then decode, stitch, display, or re-encode and stream them. Video Stack can be used for multi-channel video stitching output, and hardware acceleration can reduce CPU pressure in multi-channel scenarios.

```text
RTSP x N -> MppDec x N -> Video Stack -> DRM Display / MppEnc -> Network
```

## AI Video Analysis

After video decoding, the RKNN inference module can be connected to implement object detection, object tracking, multi-threaded inference, and related services.

```text
File / RTSP / Camera -> MppDec -> Inference -> OSD / Display / Encode
```
