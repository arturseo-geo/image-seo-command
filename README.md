# /image-seo — Claude Code Command

Optimize images for page speed and SEO, upload to WordPress, set all metadata fields, and set as featured image.

## What it does

One command replaces a 5-minute manual process per image:

1. Analyses original image (dimensions, format, size)
2. Resizes to 1200px width if larger
3. Converts to WebP (quality 80, ~95% size reduction)
4. Uploads to WordPress via REST API
5. Sets all 4 SEO metadata fields (title, alt, caption, description)
6. Optionally sets as featured image on a post
7. Clears Nginx cache
8. Verifies OG image tag on live page

## Install

```bash
cp image-seo.md ~/.claude/commands/image-seo.md
```

## Usage

```
/image-seo path/to/image.png 1234
```

Where `1234` is the WordPress post ID to set as featured image (optional).

For multiple images:
```
/image-seo image1.png image2.png image3.png 1234
```

## SEO Metadata Generated

| Field | Rules |
|---|---|
| Title | Descriptive, includes keyword, 50-70 chars |
| Alt text | Keyword-rich, describes image, 50-90 chars |
| Caption | Visible on page, provides context, ≥20 chars |
| Description | For media library and image sitemap, ≥20 chars |

## Requirements

- Python 3 with Pillow (`pip install Pillow`)
- WordPress with Application Passwords
- SSH access to VPS (for cache clearing)

## Licence

[MIT](LICENSE)

---

Built by [Artur Ferreira](https://thegeolab.net/about/) | [X](https://x.com/TheGEO_Lab) | [LinkedIn](https://linkedin.com/in/arturgeo)
