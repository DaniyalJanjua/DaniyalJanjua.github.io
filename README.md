# Muhammad Daniyal — Portfolio Website

A single-page, console-style game developer portfolio. No build tools, no frameworks — just open the files in any text editor and change them.

---

## 1. What's in the folder

```
portfolio/
├── index.html            ← the main website (all text, styles and code are in this one file)
├── theme-neon.html       ← the alternative "neon arcade" theme (linked in the footer)
├── Daniyal_Resume.pdf    ← the CV that the "CV" buttons download
├── README.md             ← this guide
└── media/                ← all project images and videos
    ├── <id>.jpg          ← 16:9 cover image (library cards, trailer poster)
    ├── <id>-tile.jpg     ← 600×600 square thumbnail (the row of tiles on the home screen)
    ├── <id>-loop.mp4     ← 8-second silent clip that plays on the 3D screen
    ├── <id>.mp4          ← full trailer with sound (opens when "Play trailer" is clicked)
    └── <id>-screen.jpg   ← portrait games only: tall cover image for the phone frame
```

`<id>` is the short name of the project, e.g. `biofire`, `cop-chase`, `long-drive`.

---

## 2. Preview the site on your computer

Double-clicking `index.html` works for most things, but **YouTube videos will not play from a double-clicked file**. To preview everything properly, run a small local server:

**Option A — VS Code (easiest):** install the *Live Server* extension, right-click `index.html` → *Open with Live Server*.

**Option B — Python:** open a terminal in the folder and run

```bash
python -m http.server 5173
```

then visit <http://localhost:5173>.

---

## 3. Editing text

Open `index.html` in VS Code (or Notepad++). Use **Ctrl + F** to jump to the right place. Everything you'll normally want to change lives in the `DATA` block near the bottom of the file — search for `/* ---------------- DATA`.

| What you want to change | Search for | Notes |
|---|---|---|
| Games / projects | `const games = [` | One line per project — see section 4 |
| "Shipped titles" chip cloud | `const shipped = [` | Just a list of names |
| Career / jobs | `const jobs = [` | `[company, role, dates, description, initials, colour1, colour2, bar %]` |
| Skills | `const skills = [` | `['emoji', 'Skill name']` |
| Trophies | `const trophies = [` | `[type, title, description, rarity]` — type is `plat`, `gold`, `silver` or `bronze` |
| Accent colour per game | `const ACC = {` | `id: '#hexcolour'` |
| Which games show as a phone | `g.p = g.id === 'cleaning'` | Add `|| g.id === 'your-id'` for other portrait games |
| About-me paragraphs, stats | `id="profile"` | Plain HTML text |
| "Also in the library" (Polker, Exofarm) | `class="xp-item"` | Copy a line to add another |
| Contact buttons | `id="contact"` | Email, WhatsApp, LinkedIn, Upwork, CV |
| Footer | `<footer>` | |

### Contact details

- **Email:** search for `mdaniyaljanjua@gmail.com` (appears 3×).
- **WhatsApp:** search for `wa.me/923115070737` (appears 3×). The format is `wa.me/` + country code + number, **no `+`, spaces or leading 0**. The text after `?text=` is the pre-filled message.
- **LinkedIn / Upwork:** search for `linkedin.com` / `upwork.com`.

### Replace the CV

Overwrite `Daniyal_Resume.pdf` with the new file (keep the same name). The name people see when they download it is set by `download="Muhammad-Daniyal-CV.pdf"` in `index.html`.

---

## 4. Adding or changing a project

Each project is one line in `const games = [ ... ]`:

```js
{id:'biofire', t:'BioFire – Uprising Warzone', st:'X-One Games', eng:'Unity', plat:'Android',
 tags:['Unity','FPS','Mobile'], d:'Short description shown under the title.'},
```

| Field | Meaning |
|---|---|
| `id` | Short name; must match the file names in `media/` |
| `t` | Title |
| `st` | Studio (or a genre if there's no studio) |
| `eng` | Engine |
| `plat` | Platform |
| `tags` | Small pills; also used by the Library filter buttons (`All / Unity / Unreal / XR / Mobile / …`) |
| `d` | Description |
| `yt` | *(optional)* YouTube video ID — use this instead of local video files |
| `link` | *(optional)* website — shows a "Visit website ↗" button in the trailer player |

**Order matters:** the first project in the list is the one shown first on the home screen.

### Option A — project with a YouTube video (easiest)

From `https://www.youtube.com/watch?v=eNZ-DWnp_W8` the ID is `eNZ-DWnp_W8`:

```js
{id:'alboum', t:'Alboum – XR Lexus Configurator', st:'Algoryte', eng:'Unreal Engine', plat:'Meta Quest 3',
 tags:['Unreal','XR','Quest 3'], yt:'eNZ-DWnp_W8', d:'…'},
```

No files needed — the thumbnail and video come from YouTube automatically.

### Option B — project with your own gameplay video

You need [FFmpeg](https://www.gyan.dev/ffmpeg/builds/) (already installed on this PC via WinGet). Run these from the folder that contains your raw video. Replace `mygame` with the project id and `Raw.mp4` with your file.

**1. Find the black bars** (most screen recordings have them baked in):

```bash
ffmpeg -ss 5 -t 10 -i Raw.mp4 -vf cropdetect=limit=24:round=2:reset=0 -f null - 2>&1 | findstr crop=
```

Take the **last** value it prints, e.g. `crop=1920:860:0:110`. Use it as `CROP` below. (If there are no bars, leave `crop=…,` out of the commands.)

**2. Landscape game — make the four files:**

```bash
ffmpeg -ss 20 -i Raw.mp4 -frames:v 1 -vf "crop=1920:860:0:110,scale=1280:720:force_original_aspect_ratio=increase,crop=1280:720" -q:v 4 media/mygame.jpg
ffmpeg -ss 20 -i Raw.mp4 -frames:v 1 -vf "crop=1920:860:0:110,scale=600:600:force_original_aspect_ratio=increase,crop=600:600" -q:v 4 media/mygame-tile.jpg
ffmpeg -ss 10 -t 8 -i Raw.mp4 -an -vf "crop=1920:860:0:110,scale=1280:720:force_original_aspect_ratio=increase,crop=1280:720,fps=30" -c:v libx264 -crf 29 -pix_fmt yuv420p -movflags +faststart media/mygame-loop.mp4
ffmpeg -i Raw.mp4 -vf "crop=1920:860:0:110,scale=1280:-2" -c:v libx264 -crf 27 -pix_fmt yuv420p -c:a aac -b:a 96k -movflags +faststart media/mygame.mp4
```

- `-ss 20` = the second the cover image is taken from; `-ss 10 -t 8` = the 8-second loop starts at 0:10. Pick moments that look good.

**Portrait (phone) game:** use the same commands, but for the loop use `scale=450:900:force_original_aspect_ratio=increase,crop=450:900`, also export a cover with that filter to `media/mygame-screen.jpg`, and add the id to the phone list (see the table in section 3).

**3.** Add the line to `const games`, optionally add a colour in `const ACC`, refresh the page.

Keep each video under ~10 MB so the site stays fast (the commands above already do this).

---

## 5. Changing the look

At the top of `index.html`, inside `<style>`, the `:root { … }` block holds the main colours:

```css
--bg:#03081a;        /* page background */
--accent:#3d8bff;    /* main blue */
--accent2:#7cc4ff;   /* light blue (kickers, eyebrows) */
--gold / --silver / --bronze / --plat   /* trophy colours */
```

- **Fonts:** the `<link href="https://fonts.googleapis.com/…">` line at the top loads *Sora* (headings) and *Manrope* (body). Swap names there and in `font-family` rules.
- **Auto-rotation speed** on the home screen: search `const DUR = 9000` (milliseconds).
- **Boot/intro animation length:** search `setTimeout(finishBoot` (2600 = 2.6 s).
- **3D background:** search `WebGL background`. It's a "game-dev workspace": a blueprint grid, floating code windows, a transform gizmo and a selected object.
  - **Code shown in the windows:** edit the `const snippets = [` list (each window is a list of lines; lines starting with `//` are green comments).
  - **Window positions:** `const spots = [` — `[x, y, z]` per window; more negative `z` = further away.
  - **Window brightness:** `opacity:.62` on the line after `spots`.
  - It uses the free **three.js** library loaded from cdnjs; if that ever fails, the site still works with the colour glow only.
- **Logo:** the hexagon "D" is an inline SVG — search `class="brand-mark"` (top bar) and `class="mark"` (intro screen).


