# Testimonials Widget — Setup Guide

## What you have

| File | Purpose |
|------|---------|
| `testimonials-widget.html` | The widget — upload this to your server |
| `testimonials-template.csv` | Sample data — use this to build your Google Sheet |
| `testimonials-setup-guide.md` | This guide |

---

## Step 1 — Create your Google Sheet

1. Go to [sheets.google.com](https://sheets.google.com) and create a new spreadsheet.
2. Name it something like **My Testimonials**.
3. Import the template: **File → Import → Upload** → select `testimonials-template.csv` → choose *Replace current sheet*.

Your sheet now has these columns (do not rename them — the widget reads by header name):

| Column | What goes there |
|--------|----------------|
| `name` | Full name |
| `role` | Job title |
| `institution` | Organisation or institution |
| `quote` | The testimonial text |
| `photo` | Direct URL to a headshot image (optional — initials are shown if blank) |
| `rating` | Number 1–5 (optional) |
| `date` | Date received, e.g. `2024-03-15` or `March 2024` |
| `email` | Their email address (stored privately, not displayed) |
| `website` | Their website URL — clicking the name links here (optional) |
| `tags` | Comma-separated tags — see below |

---

## Step 2 — Tags

Tags are free text, comma-separated in the `tags` column. The starting set is:

- `Programme attended`
- `Boot camp`
- `Virtual incubator`
- `Individual programme`
- `IMDP`
- Actual years: `2019`, `2020`, `2021`, `2022`, `2023`, `2024`, `2025` …

**To add a new tag** — just type it into the tags cell of any row. No settings to change anywhere.

Example tags cell: `IMDP,2023,Programme attended`

---

## Step 3 — Publish the sheet as a CSV

This is the step that connects your sheet to the widget.

1. In Google Sheets: **File → Share → Publish to web**
2. In the first dropdown choose your sheet tab (usually *Sheet1*)
3. In the second dropdown choose **Comma-separated values (.csv)**
4. Click **Publish** → confirm
5. Copy the URL that appears — it will look like:
   `https://docs.google.com/spreadsheets/d/XXXX/pub?gid=0&single=true&output=csv`

---

## Step 4 — Configure the widget

Open `testimonials-widget.html` in a text editor and find this section near the top:

```javascript
const CONFIG = {
  sheetUrl: 'YOUR_PUBLISHED_SHEET_CSV_URL_HERE',
  accentColor: '#2563eb',
  defaultLayout: 'grid',
};
```

- Paste your CSV URL as the value of `sheetUrl`
- Change `accentColor` to match your brand colour (any hex code)
- Change `defaultLayout` to `'list'` if you prefer a single-column layout by default

Save the file.

---

## Step 5 — Upload to your server

Upload `testimonials-widget.html` to your web server. It can go anywhere — for example:

```
https://yourdomain.com/testimonials-widget.html
```

---

## Step 6 — Embed on your pages

Paste an `<iframe>` tag wherever you want testimonials to appear:

```html
<!-- All testimonials -->
<iframe src="https://yourdomain.com/testimonials-widget.html"
        width="100%" height="600" frameborder="0" scrolling="no">
</iframe>

<!-- Filter by a single tag -->
<iframe src="https://yourdomain.com/testimonials-widget.html?tags=IMDP"
        width="100%" height="600" frameborder="0" scrolling="no">
</iframe>

<!-- Filter by multiple tags (shows any match) -->
<iframe src="https://yourdomain.com/testimonials-widget.html?tags=Boot+camp,2023"
        width="100%" height="600" frameborder="0" scrolling="no">
</iframe>
```

---

## URL parameters — full reference

Add these to the iframe `src` URL to control each embed independently:

| Parameter | What it does | Example |
|-----------|-------------|---------|
| `tags` | Show only testimonials matching these tags (comma-separated, any match) | `?tags=IMDP,2024` |
| `layout` | `grid` (default) or `list` | `?layout=list` |
| `limit` | Maximum number of testimonials to show | `?limit=3` |
| `min_rating` | Only show testimonials with this rating or higher | `?min_rating=5` |
| `shuffle` | Randomise order each time the page loads | `?shuffle=true` |
| `theme` | `light` (default) or `dark` | `?theme=dark` |

Parameters can be combined: `?tags=IMDP&limit=3&shuffle=true`

---

## Auto-height iframe (optional)

To make the iframe resize automatically to fit its content, add this snippet to the page that contains the iframe — place it just before `</body>`:

```html
<script>
window.addEventListener('message', function(e) {
  if (e.data && e.data.type === 'testimonials-height') {
    var frames = document.querySelectorAll('iframe');
    frames.forEach(function(f) {
      try {
        if (f.contentWindow === e.source) {
          f.style.height = (e.data.height + 20) + 'px';
        }
      } catch(_) {}
    });
  }
});
</script>
```

---

## Adding & editing testimonials

All changes are made directly in your Google Sheet — no other steps needed.

- **Add** a new row at the bottom (or anywhere)
- **Edit** any cell to update a testimonial
- **Hide** a testimonial without deleting it: just clear the `quote` cell (the widget skips rows with no quote)

Changes appear on your site within a minute or two (the widget fetches fresh data on every page load).

---

## Photo hosting tips

The `photo` column expects a direct image URL. Good options:

- **Google Drive**: Upload the photo → right-click → *Get link* → change sharing to *Anyone with the link* → use the direct image URL format: `https://drive.google.com/uc?export=view&id=FILE_ID`
- **Your own server**: Upload headshots to a folder on your site and use the full URL
- **Leave blank**: The widget automatically shows the person's initials in a coloured circle

---

## Troubleshooting

**"Could not load testimonials"** — Check that the sheet is published (Step 3) and that the URL in CONFIG.sheetUrl is correct.

**No testimonials showing for a tag** — Tag matching is case-sensitive in the URL but matched case-insensitively. Check the tag is spelled exactly as it appears in your sheet.

**Widget shows old data** — Browsers sometimes cache the CSV. A hard refresh (Ctrl+Shift+R / Cmd+Shift+R) will force a reload.

**iframe too short / scrollbar appears** — Either increase the `height` attribute on the iframe, or add the auto-height snippet above.
