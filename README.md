# Create Optimized Videos Script

A bash script that optimizes videos for web delivery by generating multiple formats:
- WebM (VP9)
- MP4 (H.265/HEVC)
- MP4 (H.264) in various resolutions
- HLS (HTTP Live Streaming) adaptive streaming
- DASH (Dynamic Adaptive Streaming over HTTP)
- Poster images in multiple sizes

## Requirements

- FFmpeg
- imagemin-cli and imagemin-mozjpeg (optional, for poster optimization)

## Installation

1. Clone this repository
2. Make the script executable:
```sh
chmod +x optimize_video.sh
```
3. (Optional) Install imagemin for poster optimization:
```sh
npm install -g imagemin-cli imagemin-mozjpeg
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
| **MP4 (H.264) 720p** | H.264 | `<filename>_720p.mp4` |
| **MP4 (H.264) 480p** | H.264 | `<filename>_480p.mp4` |
| **MP4 (H.264) 360p** | H.264 | `<filename>_360p.mp4` |

### **🖼️ Poster Images (First Frame)**
| Resolution | Dimensions (16:9) | Filename |
|------------|-------------------|----------|
| **Original Size** | Original | `<filename>-poster.jpg` |
| **1080p** | 1920×1080 | `<filename>-poster-1080p.jpg` |
| **720p** | 1280×720 | `<filename>-poster-720p.jpg` |
| **480p** | 854×480 | `<filename>-poster-480p.jpg` |
| **360p** | 640×360 | `<filename>-poster-360p.jpg` |

---

## 📂 **HLS Directory Structure**
```
hls/
├── master.m3u8   # Master playlist
├── 0/            # 360p variant
│   ├── playlist.m3u8
│   ├── segment_00000.ts
│   ├── segment_00001.ts
├── 1/            # 480p variant
│   ├── playlist.m3u8
│   ├── segment_00000.ts
├── 2/            # 720p variant
│   ├── playlist.m3u8
│   ├── segment_00000.ts
├── 3/            # 1080p variant
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
   - `360p` (640×360, low bandwidth)
   - `480p` (854×480, medium bandwidth)
   - `720p` (1280×720, standard quality)
   - `1080p` (1920×1080, high quality)
4. **Encodes a DASH version** with the same adaptive bitrates.
5. **Extracts a poster image from the first frame** and creates resized versions.
6. **Optimizes poster images** with imagemin/mozjpeg if available.
7. **Outputs all files** into a structured directory.

---

### OPTIONS

#### Variant Selection

You can specify exactly which variants to generate:

Examples:
- `bin/optimize_video input.mp4 --variants=[webm,360]` - Only generate WebM and 360p MP4 versions
- `bin/optimize_video input.mp4 --variants=[mp4,posters]` - Generate all MP4 variants and poster images
- `bin/optimize_video input.mp4 --variants=[all]` - Generate all formats (same as default)
- `bin/optimize_video input.mp4 --variants=[webm,h265]` - Generate only WebM and H.265 formats
- `bin/optimize_video input.mp4 --variants=[posters]` - Generate only poster images

#### Skip Specific Variants

Alternatively, you can generate everything except specific formats:

Examples:
- `bin/optimize_video input.mp4 --no-dash --no-hls` - Generate everything except streaming formats
- `bin/optimize_video input.mp4 --no-webm --no-posters` - Skip WebM and poster generation

Skip streaming formats:

#### Other Options

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
- Use **DASH for Chrome/Firefox/Edge** where it's better optimized.

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
├── my_video_720p.mp4
├── my_video_480p.mp4
├── my_video_360p.mp4
├── my_video-poster.jpg
├── my_video-poster-1080p.jpg
├── my_video-poster-720p.jpg
├── my_video-poster-480p.jpg
├── my_video-poster-360p.jpg
├── hls/
│   ├── master.m3u8
│   ├── 0/ (360p)
│   ├── 1/ (480p)
│   ├── 2/ (720p)
│   ├── 3/ (1080p)
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
- **H.265 (HEVC)** offers better compression but has more limited browser support.
- **Adaptive streaming** dynamically adjusts quality based on network conditions.

## Output

The script creates a directory with the same name as the input file and generates all variants inside it. The final output includes:

- WebM (VP9) video
- H.265 MP4 video (main file)
- H.264 MP4 video (fallback)
- H.264 MP4 in 720p, 480p, and 360p resolutions
- HLS adaptive streaming folder with master playlist
- DASH adaptive streaming folder with manifest
- Poster images in full resolution, 1080p, 720p, 480p, and 360p

## Examples

Basic usage (generate all formats):
```sh
bin/optimize_video my_video.mp4
```

Generate only specific variants:
```sh
bin/optimize_video my_video.mp4 --variants=[webm,h265,posters]
```

Skip streaming formats (good for quick processing):
```sh
bin/optimize_video my_video.mp4 --no-hls --no-dash
```

Generate only poster images:
```sh
bin/optimize_video my_video.mp4 --variants=[posters]
```

Process multiple files in a batch:
```sh
for video in *.mp4; do bin/optimize_video "$video"; done
```
