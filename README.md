# Computational Cosmology Group website

A simple static website. All content (research, people, publications,
collaborations, news, projects) lives in plain text files under `data/` — you do
**not** need to touch the HTML or any code to update the site.

## Previewing your changes locally

The page loads the data files with `fetch()`, which does **not** work when you
just double-click `index.html` (the browser blocks it). Start a tiny local
server instead:

```bash
cd ccg_webpage
python3 -m http.server 8000
```

Then open <http://localhost:8000> in your browser. Refresh after each edit.

## Before going live

The site currently assumes it will be published at `https://ccg.cft.edu.pl/`.
If the final domain is different, update it in all three places:

- `og:url` and `og:image` in `<head>` of `index.html` (for social media / chat
  link previews).
- `robots.txt` (the `Sitemap:` line).
- `sitemap.xml` (the `<loc>` line).

Also double-check the **address** in the footer is correct.

## How to edit content

### Add / edit a member

Edit [`data/members.json`](data/members.json). Copy an existing block and change
the fields. Leave a field as `""` if you don't have it (the icon just won't
appear).

```json
{
  "name": "Jane Doe",
  "role": "PhD Student",
  "image": "assets/members/doe.jpg",
  "orcid": "https://orcid.org/0000-0000-0000-0000",
  "scholar": "",
  "linkedin": "",
  "website": "",
  "github": ""
}
```

Put the photo in `assets/members/` and point `image` at it.

### Add a news item

Add an entry to [`data/news.json`](data/news.json):

```json
{
  "title": "New publication",
  "date": "2026-07-01",
  "text": "A short sentence or two about what happened."
}
```

Each news item is shown as a calendar-page card (date on top, title and text
below), so **no image is needed**. The `date` must be in `YYYY-MM-DD` format.
News are sorted by date (newest first) and only the **3 most recent** are shown.

### Edit the Research section

Everything lives in [`data/research.json`](data/research.json):

```json
{
  "intro": "The short paragraph shown under the Research heading.",
  "areas": [
    {
      "title": "Galaxy surveys & large-scale structure",
      "image": "assets/research/surveys.jpg",
      "text": "Description that appears when you hover over the card."
    }
  ]
}
```

Each card shows a background image with the title on top; the `text` appears when
you hover over the card. Put images in `assets/research/` and point `image` at
them. If you leave `image` as `""`, the card falls back to a coloured gradient —
so the section still looks fine before you have images.

### Edit collaborations & funding

The logo strip is driven by [`data/collaborations.json`](data/collaborations.json).
Each entry can use a logo image or just fall back to the name as text:

```json
{ "name": "DESI", "url": "https://www.desi.lbl.gov/", "logo": "assets/logos/desi.png" }
```

Put logo files in `assets/logos/` and point `logo` at them. Leave `logo` as `""`
to show the name as text instead. The entries currently in the file are examples
— adjust them to the surveys and funders you are actually part of.

### Edit publications

Edit [`data/publications.json`](data/publications.json). It has two parts:

```json
{
  "ads_library": "https://ui.adsabs.harvard.edu/public-libraries/XXXX",
  "highlights": [
    {
      "year": "2026",
      "title": "Paper title",
      "authors": "A. Author, B. Author",
      "url": "https://arxiv.org/abs/..."
    }
  ]
}
```

- `highlights` — a few hand-picked papers shown on the page.
- `ads_library` — paste your group's ADS library URL and an "All publications on
  ADS" button appears automatically. Leave it as `""` to hide the button.

### Add / edit a former member

Edit [`data/former_members.json`](data/former_members.json). They appear in the
expandable "Former members" list under the People section (no photo needed):

```json
{
  "name": "Jane Doe",
  "role": "PhD",
  "years": "2018–2022",
  "now": "now postdoc at MPA Garching"
}
```

`role`, `years` and `now` are optional — leave out or set to `""` if unknown.

### Add a project

Add an entry to [`data/projects.json`](data/projects.json). Projects show up as
an expandable list under "Join us": only the `title` is visible, and the details
expand on click.

```json
{
  "title": "My new project (mention the survey it uses)",
  "description": "A short paragraph describing the project.",
  "requirements": "Python, basic ML knowledge",
  "duration": "6 months",
  "contact": "Your Name"
}
```

`duration` (and any other field) can be left as `""` — empty fields are simply
hidden.

## Tips

- **JSON commas**: every entry must be separated by a comma, and the last entry
  must have **no** trailing comma. If the page shows nothing, this is the most
  common cause — check the browser console (F12) for a JSON error.
- Keep images reasonably small (a few hundred KB) so the page loads fast.
