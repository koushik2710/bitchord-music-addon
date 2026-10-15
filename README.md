# BitChord Open Archive Audio Add-on — upgrade 1.1.0

This is an upgrade for a small Flask add-on that follows BitChord's documented
`/manifest.json`, `/search?q=...`, and `/stream/{id}` contract.

## What it does
- Runs as a public HTTPS service on Render.
- Uses BitChord's `tracks` search response shape and stream metadata response.
- Supports `quality=HIGH`, `quality=LOW`, and `quality=LOSSLESS`.
- Searches Internet Archive and only returns records whose metadata explicitly
  declares a Creative Commons or public-domain license.
- Prefers MP3/M4A/AAC for HIGH, FLAC/WAV for LOSSLESS, and never claims 320 kbps
  unless the source has provided trustworthy bitrate metadata.
- Does not offer downloads through BitChord (`allowDownloads: 0`).

## Important limitations
This is **not** a complete commercial music catalogue. Archive.org's licensed
catalogue is variable and may not contain the popular Bollywood, Bengali, or
international track you're searching for. HIGH means "best available lossy
rendition", not a guarantee of 320 kbps. It cannot lawfully turn YouTube,
Spotify, or paid-streaming catalogues into unrestricted direct-download links.

The provider's public search API and metadata can change; the service may return
no results or become temporarily unavailable. Render's free services may sleep
when idle, so the first request after inactivity can be slow.

## Upgrade your existing Render service
1. Download and unzip this project.
2. Put `server.py`, `requirements.txt`, `render.yaml`, and `README.md` in the
   root of the GitHub repository connected to your existing Render service.
3. Commit and push the changes to that repository. If your service is configured
   for automatic deploys, Render should redeploy. Otherwise open Render and use
   **Manual Deploy → Deploy latest commit**.
4. Wait until Render says **Live**.
5. Open these URLs, replacing `YOUR-SERVICE` with your Render hostname:
   - `https://YOUR-SERVICE.onrender.com/`
   - `https://YOUR-SERVICE.onrender.com/manifest.json`
   - `https://YOUR-SERVICE.onrender.com/search?q=bach&quality=HIGH`
6. In BitChord, open **Settings → Sources → Add an addon** and enter the root
   URL or `/manifest.json` URL.

## If your Render service uses a different start command
Use:
`gunicorn server:app --workers 2 --threads 4 --timeout 60`

## Quick checks
- `/manifest.json` should return JSON with an `id`, `name`, and `resources`.
- `/search?q=bach` should return `{ "tracks": [...] }` (an empty array is possible).
- A valid stream URL should return JSON with an absolute `url`, `codec`,
  `container`, `manifest`, and `encrypted` fields.

## Rights and usage
Only use this add-on for content you are permitted to stream. A Creative Commons
or public-domain label should still be checked against the record's actual terms
and jurisdiction when using a particular recording.
