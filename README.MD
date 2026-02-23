# 📸 Static Media Gallery — Architecture & Dev Guide

A minimal, fully static media gallery powered by GitHub + Cloudflare Pages with automatic media processing via GitHub Actions.

This project is designed to:

* Showcase images with optimized thumbnails
* Support GIFs and videos
* Auto-generate gallery metadata
* Stay fully free with zero backend
* Use CDN delivery for performance

---

# ✨ Features

- **Automatic media processing** on push to `ass/` directory
- **Support for images** (JPEG, PNG, WebP, BMP, TIFF, etc.)
- **GIF support** with automatic conversion to MP4 for 80-90% size reduction
- **Video support** (MP4, WebM, MKV, AVI, MOV, OGG)
- **20MB file size limit** for Cloudflare Pages compatibility
- **Automatic compression** for oversized files
- **MKV/AVI transcoding** to browser-compatible MP4
- **Original file deletion** after processing to keep repo clean

---

# 🧠 High-Level Architecture

```
ass/            → original media (deleted after processing)
thumbs/         → generated thumbnails (600x600 max)
blur/           → tiny blurred previews (40x40)
webp/           → optimized images (WebP format)
video/          → all videos (MP4 format)
gallery.json    → metadata index
index.html      → gallery UI
.github/workflows/gallery.yml → automation pipeline
```

Flow:

```
Push media → GitHub Action runs → thumbnails + metadata generated → Cloudflare deploys → gallery updates
```

---

# 📂 Directory Structure

| Directory | Purpose | Max Size |
|-----------|---------|----------|
| `ass/` | Source media files (deleted after processing) | 20MB |
| `thumbs/` | Thumbnails (600x600 max) | Small |
| `blur/` | Blur placeholders (40x40) | Tiny |
| `webp/` | Compressed images (WebP format) | Optimized |
| `video/` | All videos (MP4 format) | <20MB |

---

# 📂 Folder Details

## `/ass`

Contains original media files.

Allowed:

* Images (jpg, png, webp, jpeg, bmp, tiff)
* GIFs (converted to MP4 automatically)
* Videos (mp4, webm, mkv, mov, avi, ogg)

⚠️ **20MB file size limit** — Files exceeding this will be compressed or skipped.

**Note:** Files in `ass/` are automatically deleted after successful processing.

---

## `/thumbs`

Auto-generated preview images (max 600x600 pixels).

Safe to delete — will regenerate automatically.

---

## `/blur`

Tiny blurred placeholders (40x40) for progressive loading.

Safe to delete — will regenerate automatically.

---

## `/webp`

Optimized full-resolution images in WebP format.

Safe to delete — will regenerate automatically.

---

## `/video`

All processed videos in MP4 format.

- GIFs are converted here for 80-90% size reduction
- MKV/AVI files are transcoded to browser-compatible MP4
- Oversized videos are automatically compressed

Safe to delete — will regenerate automatically.

---

## `gallery.json`

Auto-generated metadata index used by UI.

Contains:

* file name
* date
* size
* type
* thumbnail path
* blur path
* full path

DO NOT edit manually.

---

## `index.html`

Static frontend gallery.

Features:

* Responsive masonry layout
* Lazy loading
* Hover metadata overlay
* Video autoplay (muted)
* Fullscreen viewer
* Infinite scroll
* Slow immersive auto-scroll

---

# ⚙️ Automation Pipeline

Defined in:

```
.github/workflows/gallery.yml
```

**Triggers:**
- Push to `ass/` directory on `main` branch
- Manual trigger from Actions tab (workflow_dispatch)

Pipeline does:

1. Detect media type
2. Generate thumbnails (600x600 max)
3. Create blur previews (40x40)
4. Convert images to WebP
5. Convert GIFs to MP4 (80-90% smaller)
6. Transcode MKV/AVI to MP4
7. Compress oversized videos
8. Extract EXIF dates
9. Generate gallery.json
10. Delete processed originals from `ass/`
11. Commit generated assets

---

# 🎥 Video Support

Supported formats:

* mp4
* webm
* mkv
* mov
* avi
* ogg

Videos:

* autoplay muted in grid
* open with controls in viewer
* thumbnail extracted automatically

**Processing:**
- Files under 20MB: transcoded to MP4 for consistency
- Files over 20MB: compressed with CRF 28, then CRF 32 + 720p downscale if needed
- MKV/AVI: always transcoded to browser-compatible MP4

⚠️ Videos that cannot be compressed under 20MB will be skipped.

---

# 🚨 Hard Platform Limits

## Cloudflare Pages

* Max file size: **25 MB**
* Static hosting only
* No backend runtime
* All files in repo are scanned

## GitHub

* Recommended repo size: < 5–8 GB
* Large files increase clone time
* Git history keeps deleted files

## This Project

* **20MB limit** per processed file (safety margin under Cloudflare's 25MB)
* All processed files guaranteed under 20MB

---

# 📊 Performance Strategy

To reduce bandwidth:

* blur preview loads first
* thumbnail loads second
* full image/video loads only on click

---

# 🧩 Supported Media Types

### Images

* jpg
* jpeg
* png
* webp
* bmp
* tiff

### GIF

* converted to MP4 for 80-90% size reduction
* thumbnail extracted from first frame

### Video

* mp4, webm, mov, ogg — transcoded to MP4
* mkv, avi — transcoded to browser-compatible MP4
* thumbnail extracted automatically

---

# 🛠️ Development Workflow

## Add new media

```
Add file to ass/ → commit → push
```

GitHub Action will process automatically and delete the original.

---

## Manual Trigger

The workflow can be manually triggered from the **Actions** tab in GitHub:

1. Go to Actions tab
2. Select "Build Gallery" workflow
3. Click "Run workflow"
4. Select branch and run

---

## Force regenerate assets

Delete folders:

```
thumbs/
blur/
webp/
video/
```

Commit + push → pipeline rebuilds.

---

## Remove problematic file

```
git rm ass/file.ext
git commit -m "remove file"
git push
```

---

# 🧯 Troubleshooting

## Deploy fails with file size error

Cause: file > 25 MB

Solution:

* remove file
* host video externally

---

## Thumbnails missing

Cause: workflow failed

Check GitHub Actions logs.

---

## Gallery not updating

Cloudflare may cache.
Wait ~1 minute or hard refresh.

---

## Video skipped during processing

Cause: Video could not be compressed under 20MB

Solution:
* Use a smaller source file
* Pre-compress the video locally
* Host video externally

---

# 🔒 Privacy Warning

All media is:

* public
* cached by CDN
* accessible by URL

Do not upload sensitive content.

---

# 🧬 Future Improvements Roadmap

Possible upgrades:

* External video hosting integration
* Object storage migration (Cloudflare R2)
* Tagging system
* Search
* Albums
* Password protection
* Analytics
* Upload UI
* Duplicate detection
* Git history cleanup

---

# 🏗️ Scaling Path

Current setup is ideal for:

✔ Personal gallery
✔ Portfolio
✔ Archive
✔ Small public collections

Migration needed if:

* > 10k images
* large video library
* high traffic
* need private access

---

# 📜 Design Philosophy

* Zero backend
* Fully reproducible
* Minimal dependencies
* CDN first
* Auto processing
* Low maintenance

---

# 👨‍💻 Maintainer Notes

If modifying pipeline:

Always ensure:

* generated files stay under 20MB
* no large files copied to output
* JSON schema remains consistent

---

# 📦 Tech Stack

* GitHub (storage + automation)
* Cloudflare Pages (hosting + CDN)
* ImageMagick (image processing)
* FFmpeg (video processing + transcoding)
* EXIFTool (metadata)
* jq (JSON generation)
* Vanilla JS frontend

---

# 🏁 Summary

This project is a lightweight static media system optimized for:

* performance
* simplicity
* cost (free)
* reliability

It intentionally avoids backend complexity.

---

# 📜 License

Personal project — adapt freely.

---
