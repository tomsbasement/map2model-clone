# Map2Model Local Reconstruction

This repository is a local, static reconstruction of a captured Map2Model browser session. It lets you run the recovered front end from your own machine with a small local web server.

It is not an official Map2Model release, not the original source project, and not a full offline clone. It is a practical archive of the front-end files that were recoverable from a working browser tab.

## Quick Start

From this folder, run:

```bash
python3 serve.py
```

Then open:

```text
http://127.0.0.1:8080/
```

You can choose another port:

```bash
python3 serve.py 8090
```

On macOS you can double-click `start_local_server.command`. On Windows you can double-click `start_local_server.bat` if Python is installed.

Do not open `index.html` directly with `file://`. Browser module imports and workers need an HTTP origin.

## What Works

- The captured Map2Model single-page app loads locally.
- The main editor, map, model settings, mesh-generation path, and export UI are present.
- The restored Turf worker asset is included.
- Informational pages such as About, Attribution, FAQ, Commercial Use, Terms, Privacy, and Changelog render locally.
- The local `serve.py` server falls back to `index.html` for app routes, so refreshing `/faq` or `/terms-of-use` still works.

## How It Works

This is a static web app. There is no build step and no package install.

- `index.html` loads the captured app bundle and CSS.
- `assets/index-CmNODdig.js` contains the app shell, router, UI framework code, map UI, and the captured Terms component.
- `assets/MeshCreator-Di8fxqi1.js` contains the 3D model generation and export code.
- `assets/turf.worker-CHPnEU_N.js` runs heavier Turf geometry operations in a web worker.
- The small route chunks in `assets/*View-*.js` provide the informational pages that were not captured as original chunks.
- `serve.py` is only a static file server with SPA fallback. It does not process map data or generate models server-side.

## External Services

The local app still depends on live third-party services for map and terrain data. Depending on the settings used, it may request data from services such as:

- OpenStreetMap and Overpass endpoints for map features
- Mapterhorn for elevation and terrain tiles
- Esri World Imagery for the optional satellite basemap
- Overture/PMTiles-related data sources
- Public map tile providers

If those services change, rate-limit, block CORS, or go offline, the local app can still load but generation may fail or lose features.

## Common Issues

### Blank page

Use `python3 serve.py` and open the HTTP URL. Do not use `file://`.

### Route refresh gives 404

Use `serve.py`, not a basic static server. The custom server returns `index.html` for app routes.

### Missing fonts or icons

Some font/icon files were not captured. The app falls back cosmetically; missing fonts are not usually mesh blockers.

### Turf worker errors

Messages like `Unable to complete output ring` usually mean Turf failed to union a complex water, road, greenery, or cutout polygon. The app catches those failures and continues where it can. If generation finishes and export enables, the model is usable, but that affected overlay may be missing or simplified. Try a smaller area or disable the related overlay/cutout setting if the result matters.

### Generation fails

Open browser DevTools and check Console plus Network. Most failures are from missing local files, blocked external data services, or a geometry edge case in the selected area.

## Verified Local Test

Tested with the default London map view:

- Draw shape: Rectangle mode around central London.
- Approximate bounds: south `51.5068`, west `-0.1219`, north `51.5109`, east `-0.1150`.
- Default settings: OSM map data, Mapterhorn elevation, terrain enabled.
- Result: mesh generation completed and `Export 3MF` became enabled.

## Repository Notes

This folder intentionally does not include a build pipeline. The captured bundles are already built browser assets.

`captured_page_reference.html` is a local reference capture and is ignored by git because it is large and not needed to run the app.

## Legal And Licensing

This repository contains recovered/captured production assets, not original source code. Do not assume this grants a license to redistribute Map2Model code, branding, assets, or bundled third-party code.

