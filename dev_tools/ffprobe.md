# Useful FFprobe Commands


```
-print_format format  set the output printing format (available formats are: default, compact, csv, flat, ini, json, xml)
-show_data          show packets data
-show_data_hash     show packets data hash
-show_error         show probing error
-show_format        show format/container info
-show_frames        show frames info
-show_format_entry entry  show a particular entry from the format/container info
-show_entries entry_list  show a set of specified entries
-show_log           show log
-show_packets       show packets info
-show_programs      show programs info
-show_streams       show streams info
-show_chapters      show chapters info
-show_program_version  show ffprobe version
-show_library_versions  show library versions
-show_versions      show program and library versions
-show_pixel_formats  show pixel format descriptions
-show_private_data  show private data
```

Show stream and format information
```
$ ffprobe -v error -print_format json -show_format -show_streams input.mp4
[STREAM]
index=0
codec_name=h264
codec_long_name=H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10
profile=High
codec_type=video
codec_time_base=1/50
codec_tag_string=avc1
codec_tag=0x31637661
width=320
height=240
has_b_frames=2
sample_aspect_ratio=1:1
display_aspect_ratio=4:3
pix_fmt=yuv420p
level=13
color_range=N/A
color_space=unknown
color_transfer=unknown
color_primaries=unknown
chroma_location=left
timecode=N/A
refs=4
is_avc=1
nal_length_size=4
id=N/A
r_frame_rate=25/1
avg_frame_rate=25/1
time_base=1/12800
start_pts=0
start_time=0.000000
duration_ts=384000
duration=30.000000
bit_rate=34761
max_bit_rate=N/A
bits_per_raw_sample=8
nb_frames=750
nb_read_frames=N/A
nb_read_packets=N/A
DISPOSITION:default=1
DISPOSITION:dub=0
DISPOSITION:original=0
DISPOSITION:comment=0
DISPOSITION:lyrics=0
DISPOSITION:karaoke=0
DISPOSITION:forced=0
DISPOSITION:hearing_impaired=0
DISPOSITION:visual_impaired=0
DISPOSITION:clean_effects=0
DISPOSITION:attached_pic=0
TAG:language=und
TAG:handler_name=VideoHandler
[/STREAM]
[STREAM]
index=1
codec_name=aac
codec_long_name=AAC (Advanced Audio Coding)
profile=LC
codec_type=audio
codec_time_base=1/44100
codec_tag_string=mp4a
codec_tag=0x6134706d
sample_fmt=fltp
sample_rate=44100
channels=1
channel_layout=mono
bits_per_sample=0
id=N/A
r_frame_rate=0/0
avg_frame_rate=0/0
time_base=1/44100
start_pts=-1024
start_time=-0.023220
duration_ts=1324024
duration=30.023220
bit_rate=56517
max_bit_rate=N/A
bits_per_raw_sample=N/A
nb_frames=1293
nb_read_frames=N/A
nb_read_packets=N/A
DISPOSITION:default=1
DISPOSITION:dub=0
DISPOSITION:original=0
DISPOSITION:comment=0
DISPOSITION:lyrics=0
DISPOSITION:karaoke=0
DISPOSITION:forced=0
DISPOSITION:hearing_impaired=0
DISPOSITION:visual_impaired=0
DISPOSITION:clean_effects=0
DISPOSITION:attached_pic=0
TAG:language=und
TAG:handler_name=SoundHandler
[/STREAM]
[FORMAT]
filename=input.mp4
nb_streams=2
nb_programs=0
format_name=mov,mp4,m4a,3gp,3g2,mj2
format_long_name=QuickTime / MOV
start_time=-0.023220
duration=30.024000
size=368644
bit_rate=98226
probe_score=100
TAG:major_brand=isom
TAG:minor_version=512
TAG:compatible_brands=isomiso2avc1mp41
TAG:title=FFprobe Tips
TAG:encoder=Lavf56.15.101
[/FORMAT]
```
If only want one specific key:
```
$ ffprobe -v error -show_entries format=size -of default=noprint_wrappers=1 input.mp4
size=368644
```
If you would only want the value with no key:
```
$ ffprobe -v error -show_entries format=size -of default=noprint_wrappers=1:nokey=1 input.mp4
368644
```
* `-v` sets the loglevel. `error` will omit the build and "generic" file information, but will allow errors to be shown in addition to the desired `ffprobe` output.
* `-print_format` (or the alias -of) is useful to change the output format. Available formats are: default, compact, csv, flat, ini, json, xml.

* You can use `nw=1:nk=1` instead of `noprint_wrappers=1:nokey=1` if you prefer.

Show the average frame rate:
```
$ ffprobe -v error -select_streams v:0 -show_entries stream=avg_frame_rate -of default=noprint_wrappers=1:nokey=1 input.mp4
```

Show width and height:
```
$ ffprobe -v error -select_streams v:0 -show_entries stream=height,width -of csv=s=x:p=0 input.mp4
  1280x720
```


