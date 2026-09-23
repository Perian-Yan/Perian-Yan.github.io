This note wrties some FFmpeg command I used when preparing ICRA 2027 supplementary video.

## Official document
[https://ffmpeg.org/documentation.html]

## Download
After downloading it in Windows, we get three executables:
bin/
- ffmpeg.exe (video conversion and processing)
- ffprobe.exe (inspect video informaion)
- ffplay.exe (play the video)


## add ffmpeg to the path
Let the Bash know where ffmpeg.exe is, and no need to write the full path any more.

```
nano ~/.bashrc
```

```
export PATH="$PATH:/c/Users/JXZ/Downloads/ffmpeg-master-latest-win64-gpl/ffmpeg-aster-latest-win64-gpl/bin"
```

Verify after `source ~/.bashrc` :
```
# shows the above directory
which ffmpeg
```
or
```
ffmpeg -version
```


## video conversion
1. The experimemt videos collected via ROS node is encoded by MJPEG, which cannot be opened by the default palyer on Windows (can be opened by VLC), nor can it be decoded in Davinci Resolve 21. MJPEG, however, is suitable for image acquisition and frame-by-frame processing, since each frame is a JPEG image and independently compressed. In order to open the video in Davinci, the codec needs to be changed from mjpeg to h264.
```
ffmpeg -i "input.mp4" \
-c:v libx264 \
-crf 18 \
-preset medium \
-pix_fmt yuv420p \
-an \
"output_h264.mp4"
```
- Transcoding: `-c:v libx264` 
  - decoding a **video** stream and encoding it by **H.264 encoder**
 - `-crf 18` constant rate factor. 
   - the lower the number, the higher the quality. 18 is a high quality.
 - `-preset medium` 
   - slow preset means more computation time for the encorder to find a efficient compression way. This means to achieve the same quality but use less bitrate.
 - `pix_fmt yuv420p` pixel format
 - `-an` skip the audio stream in the output. Audio None.

#### batch conversion

```
mkdir -p h264
```

```
for f in experiment*.mp4; do
    ffmpeg -i "$f" \
        -c:v libx264 \
        -crf 18 \
        -preset medium \
        -pix_fmt yuv420p \
        -an \
        "h264/${f%.mp4}_h264.mp4"
done
```
For instance, `$f` is `experiment01.mp4`, then `${f%.mp4}` gives `experiment01` by removing the suffix `.mp4`.

2. Davinci Resolve exports clips as `.mov`, using the following command to change it to `.mp4`. Only change the container while keeping the codec.
```
ffmpeg -i "input.mov" -c copy "output.mp4"
```


## video compression
```
ffmpeg -i "ICRA_master.mov" \
-c:v libx264 \
-b:v 800k \
-preset slow \
-pix_fmt yuv420p \
-an \
-movflags +faststart \
"ICRA_final.mp4"
```

- `-b:v 800k` denotes the bitrate as 800 kbit/s.
- `-movflags +faststart` moves the metadata/index to the beginning of the file. This is useful for web playback because playback can start before the entire file has been downloaded.
- If there is no audio, the goal bitrate can be approximately computed by
$$
bitrate = \frac{8 * size}{duration} \ Mbps
$$
For instance, ICRA requires the final video less than 180 s and 20 MB. 
$$
bitrate = \frac{8 * 18}{180} \ Mbps = 0.8 \ Mbps = 800 \ kbps
$$
## video check
```
ffprobe -v error \
-select_streams v:0 \
-show_entries stream=codec_name,width,height,r_frame_rate,field_order,pix_fmt \
-of default=noprint_wrappers=1 \
"final.mp4"
```
- `-v error` only prints errors in addition to the requested information.
- `-select_streams v:0` selects the first video stream.
My final icra video gives
```
codec_name=h264
width=1920
height=1080
pix_fmt=yuvj420p
field_order=progressive
r_frame_rate=30/1
```
