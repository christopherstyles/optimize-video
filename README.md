# :movie_camera: :sparkles: `optimize-video`

This lil’ utility is a Bash script that optimizes a video file for multiple formats and devices. It:

- **Generates adaptive streaming (HLS & DASH)** for fast, scalable playback.
- **Encodes WebM (VP9) and MP4 (H.265 & H.264)** for broad compatibility.
- **Extracts a poster image** and resizes it for different screen sizes.
- **Automatically detects audio** and includes it if available.

## Install

Ensure you have [ffmpeg](https://ffmpeg.org/download.html) installed. If you’re on a Mac, install it eaily with [Homebrew](https://brew.sh/):

```sh
brew install ffmpeg
```

## 🚀 **Usage**
```sh
bin/optimize_video <input_file>
```

> **Tip**: drop this script into a `bin` directory (i.e. `bin/optimize_video`), and make it executable with `chmod +x bin/optimize_video`.

## 📂 **Output Files**
All optimized versions are stored inside a **directory named after the input file**.

### **🎬 Video Files**
| Format | Codec | Filename |
|--------|--------|------------------------------|
| **HLS (Adaptive Streaming)** | H.264 | `hls/master.m3u8` (playlist) |
| **DASH (Adaptive Streaming)** | H.264 | `dash/manifest.mpd` (playlist) |
| **WebM (VP9)** | VP9 | `<filename>.webm` |
| **MP4 (H.265)** | H.265 (HEVC) | `<filename>.mp4` |
| **MP4 (H.264)** | H.264 | `<filename>_h264.mp4` |

### **🖼️ Poster Images (First Frame)**
| Resolution | Filename |
|------------|---------------------------------|
| **Original Size** | `<filename>-poster.jpg` |
| **720px Width** | `<filename>-poster-720w.jpg` |
| **1080px Width** | `<filename>-poster-1080w.jpg` |
| **1920px Width** | `<filename>-poster-1920w.jpg` |

---

## 📂 **HLS Directory Structure**
```
hls/
├── master.m3u8   # Master playlist
├── 0/            # 360p variant
│   ├── playlist.m3u8
│   ├── segment_00000.ts
│   ├── segment_00001.ts
├── 1/            # 720p variant
│   ├── playlist.m3u8
│   ├── segment_00000.ts
├── 2/            # 1080p variant
│   ├── playlist.m3u8
│   ├── segment_00000.ts
```

## 📂 **DASH Directory Structure**
```
dash/
├── manifest.mpd  # DASH playlist
├── init-stream0.m4s
├── chunk-stream0-00001.m4s
├── chunk-stream0-00002.m4s
├── ...
```

---

## 🛠️ **How It Works**
1. **Detects if audio is present** and includes it if available.
2. **Generates WebM and MP4 (H.265 & H.264) versions** for compatibility.
3. **Encodes an HLS version** with multiple quality levels:
   - `360p` (low bandwidth)
   - `720p` (standard quality)
   - `1080p` (high quality)
4. **Encodes a DASH version** with the same adaptive bitrates.
5. **Extracts a poster image from the first frame** and creates resized versions.
6. **Outputs all files** into a structured directory.

---

## 🎯 **Why HLS + DASH?**
| Feature | HLS | DASH |
|---------|-----|------|
| **Apple/Safari Support** | ✅ Yes | ❌ No |
| **Chrome/Firefox/Edge Support** | ✅ Yes | ✅ Yes |
| **Better for MP4 Playback** | ✅ Yes | ✅ Yes |
| **Supports WebM (VP9)** | ❌ No | ✅ Yes |

### **Best practice:**
- Use **HLS for Apple devices (Safari, iOS, macOS)**.
- Use **DASH for Chrome/Firefox/Edge** where it’s better optimized.

---

## 🎬 **Example**
```sh
bin/optimize_video videos/my_video.mp4
```
📂 **Output Structure:**
```
videos/my_video/
├── my_video.webm
├── my_video.mp4
├── my_video_h264.mp4
├── my_video-poster.jpg
├── my_video-poster-720w.jpg
├── my_video-poster-1080w.jpg
├── my_video-poster-1920w.jpg
├── hls/
│   ├── master.m3u8
│   ├── 0/ (360p)
│   ├── 1/ (720p)
│   ├── 2/ (1080p)
└── dash/
    ├── manifest.mpd
    ├── init-stream0.m4s
    ├── chunk-stream0-00001.m4s
    ├── ...
```

---

## 📝 **Notes**
- **HLS requires a server** that supports byte-range requests (e.g., Nginx, S3, Cloudflare R2).
- **DASH requires a compatible video player**, like **Shaka Player** or **dash.js**.
- **WebM is not supported in Safari**, but works in Chrome and Firefox.
- **MP4 is the best fallback for universal compatibility.**
