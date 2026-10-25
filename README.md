# Bookshelf

A personal book-reading tracker that runs entirely in your browser. You can host it for free on GitHub Pages and add it to your phone's home screen, where it works like an app, offline included.

![Bookshelf icon](icons/icon-192.png)

## What it does

- **Shelves:** All, Reading, To read, Finished and Didn't finish, each with a count. The app remembers the last shelf you opened.
- **Search** across titles, authors, notes and "recommended by".
- **Stats** for books you're reading now, books finished this year and pages read this year.
- **Book cards** show the cover photo, or a colored placeholder spine when there's no photo. Reading books get a progress bar, finished books show their star rating and the start of your notes, and to-read books show who recommended them.
- **Add and edit** in a bottom sheet: cover photo (resized on your device), title, author, shelf, a 1–5 star rating, pages, current page, start and finish dates, "recommended by / why" and notes. Moving a book to Finished fills in today's date.
- **Backup and restore** to a single JSON file, with cover images included.
- **Light and dark mode** follow your system setting.

It's one `index.html` file with no build step and no dependencies besides Google Fonts.

## Run it locally

```sh
cd bookshelf
python3 -m http.server 8000
```

Then open <http://localhost:8000>. Serve it over HTTP rather than opening the file directly, because the service worker and offline mode need it.

## Publish on GitHub Pages

1. Push this repository to GitHub.
2. On GitHub, open **Settings → Pages**.
3. Under **Build and deployment**, set **Source** to **Deploy from a branch**, pick your branch (for example `main`) and the `/ (root)` folder, then click **Save**.
4. After a minute or so the app is live at `https://<your-username>.github.io/bookshelf/`.

All paths are relative, so the app works from that sub-path as is.

## Add it to your home screen

**iPhone or iPad (Safari):** open the site, tap **Share** (the square with an arrow), choose **Add to Home Screen**, then **Add**.

**Android (Chrome):** open the site, tap the **⋮** menu, choose **Add to Home screen** or **Install app**, then confirm.

The app then opens full screen with its own icon and works without a connection.

## Use it as a Claude artifact

The same `index.html` also runs as a Claude artifact. It detects the artifact runtime and switches storage:

- **Books** go to the artifact's database, in a private area for your account, so the shelf syncs between every device where you open the artifact while signed in to Claude.
- **Cover photos** are stored as artifact assets. Only people who can edit the artifact can upload covers.
- **Backups** go through Claude's save-file prompt, because artifacts can't start downloads themselves.

There's no offline mode in the artifact, because service workers don't run there.

The artifact and the GitHub Pages copy keep **separate** data. To move books between them, use **Back up to a file** in one and **Restore from a file** in the other.

### Keeping the artifact in sync with this repo

`index.html` in this repo is the source for both versions. After changing it, republish the artifact from the same file (ask Claude Code to "republish the Bookshelf artifact from index.html"). Your books stay in place, because the artifact's database survives republishes.

## Your data lives in the browser

On GitHub Pages or a local server, everything is stored **only on this device, inside this browser** (IndexedDB). There is no server and no account. That means:

- Data doesn't sync between your phone and your laptop, or even between two browsers on the same device. On iPhone, the home-screen app and Safari keep **separate** storage.
- Clearing site data or uninstalling the browser deletes your books.
- **Use "Back up to a file" regularly**, and always before switching devices. On the new device, open the app and use **Restore from a file**. Restoring merges with what's already there: a book that has the same id gets replaced.

The app asks the browser to keep its storage persistent after your first save, which makes automatic eviction less likely, but a backup is still the safe option.

If the browser can't store data at all (some private-browsing modes), a banner tells you, and books are kept in memory until you close the page.

## Files

| File | Purpose |
| --- | --- |
| `index.html` | The whole app: markup, CSS and JavaScript |
| `manifest.webmanifest` | Web app manifest (name, colors, icons) |
| `sw.js` | Service worker that caches the app shell for offline use |
| `icons/` | `icon.svg` plus PNG icons (192, 512, maskable 512, apple-touch 180, favicon 32) |

When you change `index.html`, bump `CACHE` in `sw.js` (for example to `bookshelf-v2`) so installed copies pick up the new version cleanly.
