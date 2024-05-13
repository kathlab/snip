# Fedora Snips

Fix Video acceleration issues (Fedora >= 38)
---

I had experienced playback/editing issues of videos (eg. only audio, or no playback at all, stuttering). While I was able to fix playback somewhat with installing VLC from flathub, I was not able to edit videos in Blender or Kdenlive. I fixed this by installing necessary codecs and replacing the ffmpeg package.

**Important:** Install rpmfusion free and non-free packages first!

See: https://rpmfusion.org/Configuration

```
sudo dnf install compat-ffmpeg4 gstreamer1-plugin-{libav,openh264} gstreamer1-plugins-{bad-free,good,ugly,ugly-free}
sudo dnf group upgrade --with-optional --allowerasing Multimedia
```