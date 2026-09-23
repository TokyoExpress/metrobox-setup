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
firmware carries. Each system's station list is fetched from whatever that
returns, so moving those endpoints needs no change here.

## Adding a transit system

`SYSTEMS` in `index.html` holds what the page knows about each agency: the id,
how to label it, and how to draw its line bullets. Which of them a user can
actually pick comes from discovery's `systems` map — an entry the server isn't
serving renders disabled rather than vanishing, so "New York isn't deployed
yet" looks different from "New York doesn't exist".

The `id` is the string POSTed as `system`, the key discovery files the agency
under, and what `systemFromName()` in the firmware matches on. All three have
to agree.

## Display Settings

A system with a `designs` array on its `SYSTEMS` entry grows a fourth step,
after the station picker, letting the user choose which board the display
draws: a modern board modelled on the newer platform screens, or the classic
flip-board rows. Every served system has one; systems without the array don't
render the step at all.

The first entry is the system's default, and it is per-system rather than a
fixed order: New York leads with its modern board, Washington with the retro
rows. Its `id` is POSTed as `design` and
ends up in `device_design` on the firmware, where `resolveDesign()` in
`metrobox_utils.cpp` owns the mapping; a design a device's build doesn't
recognise falls back to the board it has rather than a blank panel. Systems
with no `designs` omit the field from the POST entirely.

## Advanced settings

A collapsed disclosure under the display settings, sent on every save:

- `switchSeconds` (3–60, default 5): how long the display holds one direction.
- `lineFilter`: which lines to show and in which directions, as
  `{"6": ["1"], "4": ["1", "2"]}`. `{}` is every line both ways.

The lines and the names of their directions come from the station list's
`directions` (`{"RD": {"1": "Glenmont", "2": "Shady Grove"}}`), which the
server computes daily from each agency's schedule, so they do not depend on
what is running when the page is opened.

Pushing to `main` republishes within a minute or so.
