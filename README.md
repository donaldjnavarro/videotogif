# videotogif

Convert videos to GIFs using ffmpeg + gifski with sane presets.

## Dependencies

Install:

* ffmpeg
* gifski

Verify:

```bash
ffmpeg -version
gifski --version
```

## Installation

Clone the repo:

```bash
git clone <repo-url> ~/code/videotogif
```

Create a symlink so the command is on PATH:

```bash
ln -sf ~/code/videotogif/videotogif ~/.local/bin/videotogif
chmod +x ~/code/videotogif/videotogif
```

Verify:

```bash
which videotogif
```

## Usage

```bash
videotogif input.mp4
videotogif input.mp4 --low
videotogif input.mp4 --high
```

Outputs:

```text
video.gif
video_low.gif
video_high.gif
```

in the same directory as the source video.

## Dolphin Integration (KDE)

Service menu file:

```text
~/.local/share/kio/servicemenus/videotogif.desktop
```

This should be a symlink to:

```text
~/code/videotogif/dolphin/videotogif.desktop
```

Create/update:

```bash
ln -sf ~/code/videotogif/dolphin/videotogif.desktop \
      ~/.local/share/kio/servicemenus/videotogif.desktop
```

After changes:

```bash
kquitapp6 dolphin
```

then reopen Dolphin.

## Notes For Future Me

If Dolphin appears to do nothing:

1. Verify the service menu symlink exists.
2. Verify `videotogif` works from a terminal.
3. Verify `gifski` is on PATH.
4. Remember: this already broke once because Dolphin had a different PATH than the terminal.


# Archaeology -- notes from before

Below are notes for the bash commands we initially were using before packaging the script above.

## Option A: Fast and easy -- all in one step

Pipes the frames directly from ffmpeg into gifski so they never get written to disk.

```
ffmpeg -i "input.mp4" \
  -vf "fps=12,scale=480:-1:flags=lanczos" \
  -f yuv4mpegpipe - | gifski -o output.gif -
```

## Option B: Slow and careful -- 2 steps

Saves the frames first, and then makes the GIF out of the frames. This approach lets us see each individual frame -- easier to debug if ffmpeg creates bad frames. Also we get a stack of screenshots as a bonus.

### B1: Make PNG frames out of MP4 source

```
 mkdir frames

ffmpeg -i "input.mp4" \
  -vf "fps=12,scale=480:-1:flags=lanczos" \
  frames/frame_%05d.png
```

### B2: Make GIF out of PNG frames

```
gifski -o output.gif --fps 12 --quality 90 frames/frame_*.png
gifski created /home/mainuser/Videos/output.gif
```
