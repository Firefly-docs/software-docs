# Queue, Tee, and Multithreading

## 2. Basic Concepts

### 2.5 Queue, Tee, and Multithreading

`tee` duplicates one data stream into multiple branches, while `queue` creates a separate thread and buffer for each branch.

```bash
gst-launch-1.0 videotestsrc \
  ! tee name=t \
  t. ! queue ! autovideosink \
  t. ! queue ! fakesink
```

A `queue` should normally be added to every branch. Otherwise, if any branch blocks, the entire pipeline may stop.
