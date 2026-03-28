---
description: Optimize an image for page speed and SEO, upload to WordPress, set all metadata fields, and optionally set as featured image on a post.
---

# Image SEO Pipeline

Optimize, upload, and set full SEO metadata for an image on thegeolab.net.

## Input

The user provides:
- **Image path** (required) — local file path to the image
- **Post ID** (optional) — WordPress post ID to set as featured image
- **Context** (optional) — what the image is about, for generating metadata

## Pipeline Steps

### Step 1 — Analyze original image

```python
from PIL import Image
import os
img = Image.open(path)
print(f"Original: {img.size[0]}x{img.size[1]}, {os.path.getsize(path)/1024:.0f}KB, mode: {img.mode}")
```

### Step 2 — Optimize

1. **Resize** if wider than 1200px → resize to 1200px width, maintain aspect ratio
2. **Convert** RGBA/P to RGB (white background)
3. **Save as WebP** quality 80, method 6
4. Report size reduction percentage

Target: <200KB for featured images, <100KB for inline images.

### Step 3 — Upload to WordPress

```python
# WordPress REST API
# Auth: Basic jorge:u2Ee z5sw OaLz GrcM PpC3 FtKc
# Endpoint: POST /wp-json/wp/v2/media
# Content-Type: image/webp
# Content-Disposition: attachment; filename="slug-featured.webp"
```

### Step 4 — Set all 4 SEO metadata fields

Generate and set ALL of these — none optional:

| Field | Rules |
|---|---|
| **Title** | Descriptive, includes primary keyword. NOT the filename. 50-70 chars. |
| **Alt text** | Keyword-rich, describes image content for screen readers. 50-90 chars. |
| **Caption** | Visible below image on page. Provides context. ≥20 chars. |
| **Description** | For media library and image sitemap. Full context. ≥20 chars. |

```python
# POST /wp-json/wp/v2/media/{id}
data = {
    "title": "...",
    "alt_text": "...",
    "caption": "...",
    "description": "..."
}
```

If the user didn't provide context, infer from:
- The filename
- The post title (if post ID provided)
- The post's focus keyword (from RankMath meta)

### Step 5 — Set as featured image (if post ID provided)

```python
# POST /wp-json/wp/v2/posts/{post_id}
data = {"featured_media": media_id}
```

### Step 6 — Clear cache

```bash
ssh root@100.87.191.9 "rm -rf /var/cache/nginx/fastcgi/* && systemctl reload nginx"
```

### Step 7 — Verify

Check the post's OG image tag:
```bash
curl -s https://thegeolab.net/[slug]/ | grep 'og:image'
```

## Output

Print a summary table:
```
| Field      | Value |
|------------|-------|
| Format     | WebP, 1200xNNN |
| Size       | XXkb (YY% smaller) |
| Media ID   | NNNN |
| Title      | ... |
| Alt        | ... |
| Caption    | ... |
| Description| ... |
| Featured   | Set on post NNNN |
| OG image   | Verified |
```

## Multiple images

If the user provides multiple image paths, process each one sequentially using the same pipeline. Ask once for shared context (e.g. "these are all for post 1275") rather than per image.
