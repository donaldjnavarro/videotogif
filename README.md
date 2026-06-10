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
videotogif input.ext
videotogif input.ext --low
videotogif input.ext --high
```

Outputs:

```text
video.gif
video_low.gif
video_high.gif
```

in the same directory as the source video.

Works with any video format FFmpeg can decode.

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

The Dolphin integration adds right-click actions and displays KDE success/failure notifications after conversion.

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

## Archaeology -- notes from before

Below are notes from the original commands used before packaging everything into the script.

### Option A: Fast and easy -- all in one step

Pipes the frames directly from ffmpeg into gifski so they never get written to disk.

```bash
ffmpeg -i "input.mp4" \
  -vf "fps=12,scale=480:-1:flags=lanczos" \
  -f yuv4mpegpipe - | gifski -o output.gif -
```

### Option B: Slow and careful -- 2 steps

Saves the frames first, then builds the GIF from those frames. Useful for debugging.

#### B1: Make PNG frames

```bash
mkdir frames

ffmpeg -i "input.mp4" \
  -vf "fps=12,scale=480:-1:flags=lanczos" \
  frames/frame_%05d.png
```

#### B2: Make GIF from PNG frames

```bash
gifski -o output.gif --fps 12 --quality 90 frames/frame_*.png
```
