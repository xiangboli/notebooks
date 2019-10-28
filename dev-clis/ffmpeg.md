# Useful FFmpeg Commands

## Decode to yuv raw video
```bash
ffmpeg -i example.mp4 -c:v rawvideo -pix_fmt yuv420p example.yuv
```

## Remove stream

Remove a specific audio stream
```
ffmpeg -i input -map 0 -map -0:a:2 -c copy output
```
`-map 0` selects all streams from the input.
`-map -0:a:2` then deselects audio stream 3. The stream index starts counting from 0, so audio stream 10 would be `0:a:9`.

Remove all audio streams
```
ffmpeg -i input -map 0 -map -0:a -c copy output
```
-map 0 selects all streams from the input.
-map -0:a then deselects all audio streams from the input.

or 
```bash
ffmpeg -i example.mp4 -vcodec copy -an example.mp4
```

Removing other stream types

If you want to remove other stream types you can use the appropriate stream specifier.

v - video, such as `-map -0:v`

a - audio, such as `-map -0:a` (as shown above)

s - subtitles, such as `-map -0:s`

d - data, such as `-map -0:d`



## How to run video quality tool with FFmpeg
Before running FFmpeg quality tools, both videos needs to be the same resolution, frame rate, sar, pix_fmt, otherwise the results can be very off, especially for vmaf.
### PSNR
Without saving the results in a log file:
```bash
ffmpeg -i distorted.mp4 -i reference.mp4 -lavfi  psnr -f null -
```
With saving the results in a log file:
```
ffmpeg -i distorted.mp4 -i reference.mp4 -lavfi  psnr=psnr.log -f null -
```

### SSIM and PSNR
Without saving the results in log file:
```
ffmpeg -i distorted.mp4 -i reference.mp4 -lavfi "ssim;[0:v][1:v]psnr" -f null –
```
With saving the results in ssim.log and psnr.log files containing the results for every frame:
```
ffmpeg -i distorted.mp4 -i reference.mp4 -lavfi "ssim=ssim.log;[0:v][1:v]psnr=psnr.log" -f null –
```

### VMAF
Insall libvmaf:
```
git clone https://github.com/Netflix/vmaf.git
cd vmaf
```
and then follow: https://github.com/Netflix/vmaf/blob/master/resource/doc/libvmaf.md

*Note: You may need to copy `vmaf` `libvmaf.pc` from `/usr/local/lib/pkgconfig` to `/usr/lib64/pkgconfig`
or `export PKG_CONFIG_PATH=/usr/local/lib/pkgconfig`*

After install FFmpeg with libvmaf, run: 
```
ffmpeg -i input.h264 -i output.mp4 -lavfi libvmaf="n_threads=4:log_fmt=json:log_path=vmaf.log" -f null -
```
Get PSNR, SSIM and VMAF at the same time:
```
ffmpeg -i input_1.mp4 -an -i input_2.mp4 -an -sws_flags lanczos -filter_complex \ 
'[0:v]setfield=prog,scale=1920:1080,setsar=sar=1/1,format=yuv420p,fps=24000/1001,split=3[encoded1][encoded2][encoded3];\
[1:v]setfield=prog,scale=1920:1080,setsar=sar=1/1,format=yuv420p,fps=24000/1001,split=3[reference1][reference2][reference3];\
[encoded1][reference1]ssim=stats_file=ssim.log;[encoded2][reference2]psnr=stats_file=psnr.log;[encoded3][reference3]libvmaf=n_threads=50:log_path=vmaf.log' \
-f null -y /dev/null 2>process.log 1>&2
```


*Note: some of the following contents are shamfully copied from (https://github.com/leandromoreira/digital_video_introduction) for easy reference*


## Transmuxing

From `mp4` to `ts`:

```
ffmpeg -i input.mp4  output.ts
```

From `mp4` to `ts` explicitly telling to copy audio and video codec:

```
ffmpeg -i input.mp4 -c:a copy -c:v copy  output.ts
```

## Transcoding

From `h264` to `vp9`:

```
ffmpeg -i input.mp4 -c:v libvpx-vp9 -c:a libvorbis output.webm
```

From `h264` to `h265`:

```
ffmpeg -i input.mp4 -c:v libx265 output.mp4
```

From `h264` to `h264` with I-frame at each second (for a 30FPS video):

```
ffmpeg -i input.mp4 -c:v libx264 -x264-params keyint=30:min-keyint=30:no-scenecut=1 -c:a copy output.mp4
```


## Split and merge smoothly

To work with smaller videos you can split the whole video into segments and you can also merge then after.

```
# spliting into several likely 2s segments
ffmpeg -fflags +genpts -i input.mp4 -map 0 -c copy -f segment -segment_format mp4 -segment_time 2 -segment_list video.ffcat -reset_timestamps 1 -v error chunk-%03d.mp4

# joining them 
ffmpeg -y -v error -i video.ffcat -map 0 -c copy output.mp4
```

## 1 I-Frames per second vs 0.5 I-Frames per second

From `h264` to `h264` with I-frame at each second (for a 30FPS video):

```
ffmpeg -i input.mp4 -c:v libx264 -x264-params keyint=30:min-keyint=30:no-scenecut=1 -c:a copy output.mp4
```
From `h264` to `h264` with I-frame at each two seconds (for a 30FPS video):

```
ffmpeg -i input.mp4 -c:v libx264 -x264-params keyint=60:min-keyint=60:no-scenecut=1 -c:a copy output.mp4
```

## 1 I-frame and the rest P-Frames

Generates a video with a `single I frame` and the `rest are P frames`.

```
ffmpeg -i input.mp4 -c:v libx264 -x264-params keyint=300:min-keyint=300:no-scenecut=1:bframes=0 -c:a copy output.mp4
```


## No B-frames at all

Generates a video with 0 B-frames.

```
ffmpeg -i input.mp4 -c:v libx264 -x264-params keyint=30:min-keyint=30:no-scenecut=1:bframes=0 -c:a copy outputs.mp4
```


## CABAC vs CAVLC

Generates `h264` using CAVLC (faster, less cpu intensive, less compression):

```
ffmpeg -i input.mp4 -c:v libx264 -x264-params keyint=30:min-keyint=30:no-scenecut=1:no-cabac=1 -c:a copy output.mp4
```

Generates `h264` using CABAC ("slower", more cpu intensive, more compression):

```
ffmpeg -i input.mp4 -c:v libx264 -x264-params keyint=30:min-keyint=30:no-scenecut=1:coder=1 -c:a copy output.mp4
```

## Transrating

CBR from `1928 kbps` to `964 kbps`:

```
ffmpeg -i input.mp4 -b:v 964K -minrate 964K -maxrate 964K -bufsize 2000K  output.mp4
```

Constrained VBR or ABR from `1928 kbps` to `max=3856 kbps ,min=964 kbps`:

```
ffmpeg -i input.mp4 -minrate 964K -maxrate 3856K -bufsize 2000K  output.mp4
```

## Transsizing

From `1080p` to `480p`:

```
ffmpeg -i input.mp4 -vf scale=480:-1 output.mp4
```

## Demuxing

Extracting `audio` from `container`:

```
ffmpeg -i input.mp4 -vn -c:a copy output.aac
```

## Muxing

Joining `audio` with `video`:

```
ffmpeg -i audio_input.aac -i video_input.mp4 muxed_output.mp4
```

## Generates YUV histogram

It generates a video with color histogram as an overlay.
```
ffmpeg -i input.mp4 -vf "split=2[a][b],[b]histogram,format=yuv420p[hh],[a][hh]overlay" output_with_histogram.mp4
```

## Generate debug video

It generates a video with macro blocks debug over the video. Please refer to https://trac.ffmpeg.org/wiki/Debug/MacroblocksAndMotionVectors to understand the meaning of each block color.
```
ffmpeg -debug vis_mb_type -i input.mp4 output.mp4
```

It generates a video with motion vector over the video.
```
ffmpeg -flags2 +export_mvs -i input.mp4 -vf codecview=mv=pf+bf+bb output_with_mv.mp4
```

## Generate images from video

Get `images` from `1s video`:

```
ffmpeg -y -i input.mp4 -ss 00:01:24 -t 00:00:01  output_%3d.jpg
```

## Generate video from images

```
# from one image
ffmpeg -loop 1 -i images_001.jpg -c:v libx264 -pix_fmt yuv420p -t 10 output.mp4

# from multiple images (repeating 10s)
ffmpeg -loop 1 -i images_%03d.jpg -c:v libx264 -pix_fmt yuv420p -t 10 output.mp4
```

## Generate a single frame video

It generates a single frame video which is great for learning and analysis.

```
ffmpeg -i image.png -pix_fmt yuv420p output_yuv420.mp4

ffmpeg -i image.png  output_yuv444.mp4
```

## Generate a simple video

It generates a video from a sequence of images.

```
# 2 simple images with white background
ffmpeg -i solid_background_ball_%d.png -pix_fmt yuv420p solid_background_ball_yuv420.mp4

# 4 simple images with background
ffmpeg -i smw_background_ball_%d.png -pix_fmt yuv420p smw_background_ball_yuv420.mp4

# 4 white images as a video (great to test predicitons)

for i in {1..4}; do cp i/solid_background.png i/solid_background_$i.png; done
ffmpeg -i solid_background_%d.png -pix_fmt yuv420p solid_background_yuv420.mp4
```

## Generate a single frame h264 bitstream

```
ffmpeg -i image.png -pix_fmt yuv420p output_yuv420.h264

# you can check the raw h264 bit stream
hexdump  output_yuv420.h264
```

## Audio sampling

From `original` to `8kHz`:

```
ffmpeg -i input.mp4 -ar 8000 output.mp4
```

## Audio bit depth

From `original` to `8 bits`:

```
ffmpeg -i input.mp4 -sample_fmt:0:1 u8p output.mp4 -y
```

> Technically speaking, bit depth is only meaningful when applied to pure PCM devices. Non-PCM formats, such as lossy compression systems like MP3, have bit depths that are not defined in the same sense as PCM. In lossy audio compression, where bits are allocated to other types of information, the bits actually allocated to individual samples are allowed to fluctuate within the constraints imposed by the allocation algorithm.

## Adaptive bitrate streaming

[HLS](https://tools.ietf.org/html/draft-pantos-http-live-streaming-20) streaming:

### A VOD stream with 1s chunk size
```
ffmpeg -i small_bunny_1080p_30fps_h264_keyframe_each_second.mp4 -c:a copy -c:v libx264 -x264-params keyint=30:min-keyint=30:no-scenecut=1 -hls_playlist_type vod -hls_time 1 playlist_keyframe_each_second.m3u8
```

### Playlists for 720p(2628kbs), 480p(480p1128kbs) and 240p(264kbs) streams

```
ffmpeg -i small_bunny_1080p_30fps_h264_keyframe_each_second.mp4 \
        -c:a copy -c:v libx264 -x264-params keyint=30:min-keyint=30:no-scenecut=1 \
        -b:v 2500k -s 1280x720 -profile:v high -hls_time 1 -hls_playlist_type vod /files/v/720p2628kbs.m3u8 \
        -c:a copy -c:v libx264 -x264-params keyint=30:min-keyint=30:no-scenecut=1 \
        -b:v 1000k -s 854x480 -profile:v high -hls_time 1 -hls_playlist_type vod /files/v/480p1128kbs.m3u8 \
        -c:a copy -c:v libx264 -x264-params keyint=30:min-keyint=30:no-scenecut=1 \
        -b:v 200k -s 426x240 -profile:v high -hls_time 1 -hls_playlist_type vod /files/v/240p264kbs.m3u8
```

### The variant playlist
```
cat <<EOF > v/variant.m3u8
#EXTM3U
#EXT-X-VERSION:6
#EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=2500000,CODECS="avc1.640028,mp4a.40.2",RESOLUTION=1280x720
720p2628kbs.m3u8
#EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=1000000,CODECS="avc1.4d001f,mp4a.40.2",RESOLUTION=854x480
480p1128kbs.m3u8
#EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=200000,CODECS="avc1.42001f,mp4a.40.2",RESOLUTION=426x240
240p264kbs.m3u8
EOF
```

## Video quality perception

You can learn more about [vmaf](http://techblog.netflix.com/2016/06/toward-practical-perceptual-video.html) and [general video quality perception](https://leandromoreira.com.br/2016/10/09/how-to-measure-video-quality-perception/).

```
# generating a 2 seconds example video
ffmpeg -y -i input.mp4 -ss 00:01:24 -t 00:00:02  output.mp4

# generate a transcoded video (600kbps vp9)
ffmpeg -i input.mp4 -c:v libvpx-vp9 -b:v 600K -c:a libvorbis output_vp9.webm

# extract the yuv (yuv420p) color space from them
ffmpeg -i input.mp4 -c:v rawvideo -pix_fmt yuv420p output.yuv

# run vmaf original h264 vs transcoded vp9
vmaf run_vmaf yuv420p 1080 720 input.yuv output.yuv --out-fmt json
```



## FFMpeg as a library

There are some documentations, examples and tutorials:

* http://dranger.com/ffmpeg/tutorial01.html
* https://github.com/leandromoreira/player-ffmpeg
* https://github.com/FFmpeg/FFmpeg/tree/master/doc/examples
* https://www.ffmpeg.org/doxygen/3.2/index.html

## Other useful articles

[FFmpeg – the swiss army knife of Internet Streaming](https://sonnati.wordpress.com/2011/07/11/ffmpeg-the-swiss-army-knife-of-internet-streaming-part-i/)