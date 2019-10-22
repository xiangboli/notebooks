# Useful FFmpeg Commands

## Decode to yuv raw video
```bash
ffmpeg -i example.mp4 -c:v rawvideo -pix_fmt yuv420p example.yuv
```

## Remove audio 
```bash
ffmpeg -i example.mp4 -vcodec copy -an example.mp4
```