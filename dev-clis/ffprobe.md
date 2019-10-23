# Useful FFprobe Commands


```
-show_error
    Show information about the error found when trying to probe the input.

    The error information is printed within a section with name "ERROR".

-show_format
    Show information about the container format of the input multimedia stream.

    All the container format information is printed within a section with name "FORMAT".

-show_entries section_entries
    Set list of entries to show.

    Entries are specified according to the following syntax. section_entries contains a list of section entries separated by ":". Each section
    entry is composed by a section name (or unique name), optionally followed by a list of entries local to that section, separated by ",".

    If section name is specified but is followed by no "=", all entries are printed to output, together with all the contained sections.
    Otherwise only the entries specified in the local section entries list are printed. In particular, if "=" is specified but the list of
    local entries is empty, then no entries will be shown for that section.

    Note that the order of specification of the local section entries is not honored in the output, and the usual display order will be
    retained.

    The formal syntax is given by:

            <LOCAL_SECTION_ENTRIES> ::= <SECTION_ENTRY_NAME>[,<LOCAL_SECTION_ENTRIES>]
            <SECTION_ENTRY>         ::= <SECTION_NAME>[=[<LOCAL_SECTION_ENTRIES>]]
            <SECTION_ENTRIES>       ::= <SECTION_ENTRY>[:<SECTION_ENTRIES>]

    For example, to show only the index and type of each stream, and the PTS time, duration time, and stream index of the packets, you can
    specify the argument:

            packet=pts_time,duration_time,stream_index : stream=index,codec_type

    To show all the entries in the section "format", but only the codec type in the section "stream", specify the argument:

            format : stream=codec_type

    To show all the tags in the stream and format sections:

            stream_tags : format_tags

    To show only the "title" tag (if available) in the stream sections:

            stream_tags=title

-show_packets
    Show information about each packet contained in the input multimedia stream.

    The information for each single packet is printed within a dedicated section with name "PACKET".

-show_frames
    Show information about each frame and subtitle contained in the input multimedia stream.

    The information for each single frame is printed within a dedicated section with name "FRAME" or "SUBTITLE".

-show_log loglevel
    Show logging information from the decoder about each frame according to the value set in loglevel, (see "-loglevel"). This option requires
    "-show_frames".

    The information for each log message is printed within a dedicated section with name "LOG".

-show_streams
    Show information about each media stream contained in the input multimedia stream.

    Each media stream information is printed within a dedicated section with name "STREAM".

-show_programs
    Show information about programs and their streams contained in the input multimedia stream.

    Each media stream information is printed within a dedicated section with name "PROGRAM_STREAM".

-show_chapters
    Show information about chapters stored in the format.

    Each chapter is printed within a dedicated section with name "CHAPTER".

-count_frames
    Count the number of frames per stream and report it in the corresponding stream section.

-count_packets
    Count the number of packets per stream and report it in the corresponding stream section.

-show_pixel_formats
    Show information about all pixel formats supported by FFmpeg.

    Pixel format information for each format is printed within a section with name "PIXEL_FORMAT".
```

Show stream and format information
```
$ ffprobe -v error -show_format -show_streams input.mp4
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


