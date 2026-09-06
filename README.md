# metrobox-setup

The WiFi-connected setup page for [metrobox](https://github.com/TokyoExpress/metrobox-1)
displays, served at **https://setup.metrobox.org** by GitHub Pages.

A user reads a pairing code off their display, picks a transit system and a
station here, and the page POSTs the selection to the metrobox API. The device
polls for it and switches within a few seconds.

This repo is public only because GitHub Pages requires it. It holds the page
and nothing else — the firmware and infrastructure live in the private
`metrobox-1` repo.

## Editing

`index.html` is the whole site: no build step, no dependencies. Open it
directly, or serve it locally to exercise the real API:

```bash
python3 -m http.server 8765   # then http://localhost:8765/
```

The only address it hardcodes is the discovery endpoint, the same one the
firmware carries. Everything else — the station list, the arrivals reader — is
fetched from whatever that returns, so moving those endpoints needs no change
here.

## Adding a transit system

`SYSTEMS` in `index.html` holds what the page knows about each agency: the id,
how to label it, and how to draw its line bullets. Which of them a user can
actually pick comes from discovery's `systems` map — an entry the server isn't
serving renders disabled rather than vanishing, so "New York isn't deployed
yet" looks different from "New York doesn't exist".

The `id` is the string POSTed as `system`, the key discovery files the agency
under, and what `systemFromName()` in the firmware matches on. All three have
to agree.

Pushing to `main` republishes within a minute or so.
