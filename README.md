# How to make GIFs that avoid encoding issues

Frequently when making gifs in video software, we get stuck with their approach and the resulting gif becomes bugged when used in certain environments such as Discord

In the below examples, the wonderfools file is the input and the gifski.gif is the output

## Option A: Fast and easy -- all in one step

Pipes the frames directly from ffmpeg into gifski so they never get written to disk.

```
ffmpeg -i "wonderfools_car (preGIF).mp4" \
  -vf "fps=12,scale=480:-1:flags=lanczos" \
  -f yuv4mpegpipe - | gifski -o gifski_test.gif -
```

## Option B: Slow and careful -- 2 steps

Saves the frames first, and then makes the GIF out of the frames. This approach lets us see each individual frame -- easier to debug if ffmpeg creates bad frames. Also we get a stack of screenshots as a bonus.

### B1: Make PNG frames out of MP4 source

```
 mkdir frames

ffmpeg -i "wonderfools_car (preGIF).mp4" \
  -vf "fps=12,scale=480:-1:flags=lanczos" \
  frames/frame_%05d.png
```

### B2: Make GIF out of PNG frames

```
gifski -o gifski_test.gif --fps 12 --quality 90 frames/frame_*.png
gifski created /home/mainuser/Videos/gifski_test.gif
```
## Option A+B Supreme

Script was created and installed.

```
mp4togif inputfile.mp4
```

OR

```
mp4togif inputfile.mp4 --high
```

OR

```
mp4togif inputfile.mp4 --low
```
