# marchvidkjaer.com

I made this website from scratch, using ChatGPT and Hugo. To edit the site, I use VS Code.  
I made this site from scratch as I wanted a minimal site with a two-column setup and a top menu bar.  
The closest thing that emulated minimalism was the Academimal theme by Lei Yang.  
Yang had the helpful advice to not fork his site, but instead understand how Hugo works and build a site yourself.  
This was good advice. This made me develop a website which I have full control over.

---

## Overview

- **Generator:** [Hugo](https://gohugo.io/) (no theme; custom layout)
- **Layout files:**  
  `layouts/_default/baseof.html` (shared),  
  `layouts/_default/single.html` (page body),  
  `layouts/index.html` (home body)
- **Styles:** `static/css/style.css`
- **Content:** Markdown in `content/`
- **Deploy target:** `docs/` (GitHub Pages)

Directory structure:
.
├── content/                    # All page content (Markdown)
│   ├── _index.md               # Home page
│   └── pages/
│       ├── media.md
│       ├── research.md
│       └── teaching.md
├── layouts/
│   ├── index.html              # Home body (extends base)
│   └── _default/
│       ├── baseof.html         # Shared chrome: head, header, sidebar, footer
│       └── single.html         # Wrapper that renders {{ .Content }}
├── static/
│   ├── css/style.css           # Single stylesheet
│   ├── images/                 # Photos
│   └── files/                  # PDFs
├── docs/                       # Build output (what GitHub Pages serves)
└── config.toml                 # Hugo config

Do not edit files under `docs/` by hand; Hugo overwrites this when you build.

---

## Quick start (local)

1. Install Hugo Extended:
   hugo version
2. Run the dev server with live reload:
   hugo server --disableFastRender -D
3. Open http://localhost:1313

If you don’t see edits:
- Make sure you’re editing the correct file.
- Hard refresh the browser (Shift + Reload).
- Restart the server.

---

## Editing content

- **Home page:** content/_index.md
- **Media:** content/pages/media.md
- **Research:** content/pages/research.md
- **Teaching:** content/pages/teaching.md

Create a new page:
hugo new pages/my-page.md

### Images
- Put files in static/images/
- Use in Markdown:  
  ![Alt text](/images/filename.jpg)

### PDFs / downloads
- Put files in static/files/
- Link in Markdown:  
  [CV](/files/cv_2024.pdf)

---

## Footer (rights + “Last updated”)

1) Enable Git timestamps  
In config.toml:
enableGitInfo = true

2) Add footer to layouts/_default/baseof.html (just above </body>):
<footer class="site-footer">
  <span>© {{ now.Format "2006" }} Marc Sabatier Hvidkjær. All rights reserved.</span>
  <span> · Last updated:
    {{ with .Lastmod }}{{ .Format "January 2, 2006" }}
    {{ else }}{{ .Date.Format "January 2, 2006" }}{{ end }}
  </span>
</footer>

3) Style in static/css/style.css:
.site-footer {
  max-width: var(--maxw);
  margin: 40px auto;
  padding: 10px 24px 0;
  color: #666;
  font-size: 14px;
  border-top: 1px solid #eee;
  text-align: center;
}

---

## Media list in reverse chronological order

Two options:

### Option A — Keep media.md as one file
- Add new items at the top of the list manually.
- Use numbered list (1. for each item) or bullets.

Example:
1. “Klogt krudt”, Weekendavisen, 04.07.25
1. “Truslen kommer fra højre”, Weekendavisen, 06.06.25

If you prefer bullets but want numbers on screen:
- Add this to <body> in baseof.html:
  <body class="{{ .File.ContentBaseName | lower }}">
- Add to style.css:
  body.media .content ul { list-style: decimal; padding-left: 1.5rem; }

---

### Option B — Automatic ordering
1. Change content/pages/media.md → content/pages/media/_index.md.
2. Create one file per entry with a date in front matter:
   content/pages/media/2025-07-04-klogt-krudt.md
   ---
   title: "“Klogt krudt”, Weekendavisen"
   date: 2025-07-04
   ---
   Link: https://www.weekendavisen.dk/ideer/klogt-krudt
3. Create layouts/pages/media/list.html:
   {{ define "main" }}
     <h1 class="page-title">{{ .Title }}</h1>
     {{ .Content }}
     <ol>
       {{ range .Pages.ByDate.Reverse }}
         <li>
           <a href="{{ .Permalink }}">{{ .Title }}</a>
           — {{ .Date.Format "Jan 2, 2006" }}
         </li>
       {{ end }}
     </ol>
   {{ end }}

---

## Navigation & sidebar

- Menu: in layouts/_default/baseof.html
- Sidebar content (photo, bio, links): in baseof.html
- Images: static/images/
- PDFs: static/files/

---

## Build & deploy (GitHub Pages)

This repo publishes from the docs/ folder.

1. Build:
   hugo
2. Ensure these files exist:
   touch docs/.nojekyll
   echo "marchvidkjaer.com" > docs/CNAME
3. Commit & push:
   git add -A
   git commit -m "Rebuild site"
   git push
4. In GitHub → Settings → Pages:  
   - Source: Deploy from a branch  
   - Branch: main / folder: /docs

---

## Tips

- **Backup:**
  cp -R ~/Documents/marchvidkjaer.com_2.0 ~/Documents/marchvidkjaer_backup_$(date +%Y%m%d)
- **Safer edits:**
  git checkout -b edits
  # work
  git commit -am "my change"
  git checkout main && git merge edits
- **Title size:** in style.css:
  .page-title, .content h1:first-child { font-size: 32px; }
- **Sidebar spacing:** in style.css:
  .sidebar p { margin: 4px 0; }
  .sidebar .links a { margin: 2px 0; line-height: 1.2; }
  .sidebar-group + .sidebar-group { margin-top: 24px; }

---

## Troubleshooting

- **Home edits don’t show:**  
  Edit content/_index.md and ensure layouts/index.html contains:
  {{ define "main" }}
    {{ if .Title }}<h1 class="page-title">{{ .Title }}</h1>{{ end }}
    {{ .Content }}
  {{ end }}
  Restart with:
  hugo server --disableFastRender -D

- **Footer date wrong:**  
  Commit changes (Git timestamps) or set lastmod in front matter. Ensure enableGitInfo = true.

- **Page layout off:**  
  Remove or adjust custom templates; default should be single.html unless overridden.

---

## License

- **Code:** MIT (reuse welcome)  
- **Content:** © Marc Sabatier Hvidkjær. All rights reserved.
