# 📸 Static Media Gallery — Architecture & Dev Guide

A minimal, fully static media gallery powered by GitHub + Cloudflare Pages with automatic media processing via GitHub Actions.

This project is designed to:

* Showcase images with optimized thumbnails
* Support GIFs and videos
* Auto-generate gallery metadata
* Stay fully free with zero backend
* Use CDN delivery for performance

---

# 🧠 High-Level Architecture

```
ass/            → original media (images + small videos only)
thumbs/         → generated thumbnails
blur/           → tiny blurred previews
webp/           → optimized images
gallery.json    → metadata index
index.html      → gallery UI
.github/workflows/gallery.yml → automation pipeline
```

Flow:

```
Push media → GitHub Action runs → thumbnails + metadata generated → Cloudflare deploys → gallery updates
```

---

# 📂 Folder Structure

## `/ass`

Contains original media files.

Allowed:

* Images (jpg, png, webp, jpeg)
* GIFs
* SMALL videos (< 25 MB)

⚠️ Cloudflare Pages has a **hard limit of 25 MB per file**
Any file larger will break deployment.

---

## `/thumbs`

Auto-generated preview images.

Safe to delete — will regenerate automatically.

---

## `/blur`

Tiny blurred placeholders for progressive loading.

Safe to delete — will regenerate automatically.

---

## `/webp`

Optimized full-resolution images.

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

Triggered when files change in `/ass`.

Pipeline does:

1. Detect media type
2. Generate thumbnails
3. Create blur previews
4. Convert images to WebP
5. Extract EXIF dates
6. Generate gallery.json
7. Commit generated assets

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

⚠️ Videos MUST be under 25 MB if stored in repo.

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

* kept as original animated file

### Video

* thumbnail extracted
* original file used

---

# 🛠️ Development Workflow

## Add new media

```
Add file → commit → push
```

GitHub Action will process automatically.

---

## Force regenerate assets

Delete folders:

```
thumbs/
blur/
webp/
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

* generated files stay small
* no large files copied to output
* JSON schema remains consistent

---

# 📦 Tech Stack

* GitHub (storage + automation)
* Cloudflare Pages (hosting + CDN)
* ImageMagick (processing)
* FFmpeg (video thumbnails)
* EXIFTool (metadata)
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
