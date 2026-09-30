# Ripple Tank

A tiny web app that turns clicks into water ripples with sound. It is a single HTML file under 3 KB, with no libraries and no external files.

## How to use

1. Open the file in a browser (or paste the `data:text/html,...` one-liner into the address bar).
2. Click or tap anywhere to drop a ripple.
3. Hold the click longer for wider, faster, lower-pitched ripples.
4. Drop several ripples and watch them interfere with each other.

## How it works

- **Waves:** every pixel adds up one sine wave per source. Where crests meet, it gets bright. Where a crest meets a trough, they cancel.
- **Sound:** each source plays a tone using the Web Audio API. Wider rings sound lower and tighter rings sound higher.
- **Fading:** waves weaken with distance. Each source holds full strength until half of its life has passed, then fades out exponentially and is removed.
- **Canvas:** the picture is drawn on a small canvas that matches the window's shape, then scaled up by CSS to fill the screen.

## Limits

- Up to 8 ripples at a time. A new one can be added once an old one has faded.
- Sound starts after the first click, because browsers block audio until you interact with the page.

## Tweaking

- `h` is the canvas height in pixels. Raise it for sharper rings, lower it if it lags.
- `0.05` in the gain line is the volume per ripple.
- The hold time is capped at 3 seconds.