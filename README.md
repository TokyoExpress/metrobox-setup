# metrobox-setup

The WiFi-connected setup page for [metrobox](https://github.com/TokyoExpress/metrobox-1)
displays, served at **https://setup.metrobox.org** by GitHub Pages.

A user reads a pairing code off their display, picks a station here, and the
page POSTs the selection to the metrobox API. The device polls for it and
switches within a few seconds.

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

Pushing to `main` republishes within a minute or so.
