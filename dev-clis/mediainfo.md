# Useful Mediainfo Commands

## Inspect stream

To see some details:

```
mediainfo example.mp4
```

To see full details:

```
mediainfo --Details example.mp4
```

To see only the frame, slice types:

```
mediainfo --Details example.mp4 | grep slice_type
```

Count how many `I-slice` (keyframes) were inserted:

```
mediainfo --Details example.mp4 | grep "slice_type I" | wc -l
```

Count how many frames:
```
mediainfo --Output="Video;%FrameCount%" example.mp4
```

