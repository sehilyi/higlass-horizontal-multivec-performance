# higlass-horizontal-multivec-performance

## Settings
- The number of rows (`Row`): `200`, `500`, `1,000`, `5,000`, and `10,000`
- The size of a horizontal-multivec track (`Screen`): _full_ (`1742x938`) and _half_ (`875x488`)
- Repeated five times for the each combination of {`Row`, `Screen`}, total `50` times (`5` * `5` * `2`).
- Machine: `MacBook Pro (16-inch, 2019), 2.6 GHz 6-Core Intel Core i7, 32 GB 2667 MHz DDR4` (power adapter connected, no unnecessary apps were running)
- Browser: `Firefox 74.0 (64-bit)` (caching diabled)
- Used a single `horizontal-multivec` track on the center (refer to viewConfig below).
- Test datasets are generated using [sehilyi/higlass-bigwigs-to-multivec](https://github.com/sehilyi/higlass-bigwigs-to-multivec) (base resolution=`1`).
- Time durations to render `horizontal-multivec` tracks are calculated in a [separate branch](https://github.com/sehilyi/higlass/tree/sehilyi/bottleneck) of a [sehilyi/HiGlass fork](https://github.com/sehilyi/higlass/).
- Currently, only the performance of the initial rendering is tested.

## Results
Raw test results ([here](https://docs.google.com/spreadsheets/d/1tKy2PKL6rM5LCi-hvITD9UjhbuMef35b49YBUDroN-c/edit#gid=0))

Summary of results:

<img width="579" alt="Screen Shot 2020-03-19 at 1 52 47 PM" src="https://user-images.githubusercontent.com/9922882/77098863-7043a800-69e9-11ea-9ddd-bf3e547d6e5b.png">

- `Preparing` is the time duration until starting to render the heatmap.
    - Time calculation starts at `hglib.viewer()` ([here](https://github.com/sehilyi/higlass/blob/68573be63d652960539f92bbceeefb65ee393fa5/app/scripts/hglib.js#L108))
    - Time calculation ends at `DataFetcher.fetchTilesDebounced()` ([here](https://github.com/sehilyi/higlass/blob/68573be63d652960539f92bbceeefb65ee393fa5/app/scripts/DataFetcher.js#L224))
    - This duration includes the process of fetching tilesets:
        - `DataFetcher.fetchTilesDebounced()`
        - `tile-proxy.fetchEither()`
- `Rendering` is the time duration for rendering heatmaps using the `loadedtiles`.
    - Time calculation starts at `TiledPixiTrack.receivedTiles()` ([here](https://github.com/sehilyi/higlass/blob/68573be63d652960539f92bbceeefb65ee393fa5/app/scripts/TiledPixiTrack.js#L646))
    - Time calculation ends at `TiledPixiTrack.receivedTiles()` ([here](https://github.com/sehilyi/higlass/blob/68573be63d652960539f92bbceeefb65ee393fa5/app/scripts/TiledPixiTrack.js#L736))
- `Time Total` is the time duration between the start calling `hglib.viewer()` and end calling `TiledPixiTrack.receivedTiles()`.
    - `Time Total` is the union of the time of `Preparing` and `Renderig`, and `Preparing` and `Renderig` are not overlapped each other.

Inside of `Preparing`:

<img width="689" alt="Screen Shot 2020-03-19 at 2 39 07 PM" src="https://user-images.githubusercontent.com/9922882/77102560-6ae95c00-69ef-11ea-8190-185761de93bb.png">

- The process of fetching tilesets takes the most time duration (96%) in the _preparation_, mainly by `DataFetcher.fetchTilesDebounced()`, followed by `tile-proxy.fetchEither()`.

<details>
<summary>View Config Used</summary>

```javascript
{
  "editable": true,
  "zoomFixed": false,
  "trackSourceServers": [
    "http://localhost:8001/api/v1"
  ],
  "exportViewUrl": "/api/v1/viewconfs",
  "views": [
    {
      "initialXDomain": [
        1,
        10000
      ],
      "initialYDomain": [
        1,
        10000
      ],
      "tracks": {
        "top": [],
        "left": [],
        "center": [
          {
            "type": "horizontal-multivec",
            "uid": "cistrome-track-1",
            "tilesetUid": TILESETUID,
            "server": "http://localhost:8001/api/v1",
            "options": {
              "labelPosition": "hidden",
              "labelColor": "black",
              "labelTextOpacity": 0.4,
              "valueScaling": "linear",
              "trackBorderWidth": 0,
              "trackBorderColor": "black",
              "heatmapValueScaling": "linear",
              "name": "6_treat.multires.mv5",
              "labelLeftMargin": 0,
              "labelRightMargin": 0,
              "labelTopMargin": 0,
              "labelBottomMargin": 0,
              "labelShowResolution": true,
              "minHeight": 100,
              "colorbarPosition": "bottomLeft",
              "labelShowAssembly": true,
              "colorbarBackgroundColor": "#ffffff",
              "scaleStartPercent": "0.00000",
              "scaleEndPercent": "1.00000",
              "selectedRow": null
            },
            "width": 2530,
            "height": 458,
            "resolutions": [
              16777216,
              8388608,
              4194304,
              2097152,
              1048576,
              524288,
              262144,
              131072,
              65536,
              32768,
              16384,
              8192,
              4096,
              2048,
              1024,
              512,
              256,
              128,
              64,
              32,
              16,
              8,
              4,
              2,
              1
            ]
          }
        ],
        "bottom": [],
        "right": [],
        "whole": [],
        "gallery": []
      },
      "layout": {
        "w": 12,
        "h": 90,
        "x": 0,
        "y": 0,
        "moved": false,
        "static": false
      },
      "uid": "cistrome-view-1"
    }
  ],
  "zoomLocks": {
    "locksByViewUid": {},
    "locksDict": {}
  },
  "locationLocks": {
    "locksByViewUid": {},
    "locksDict": {}
  },
  "valueScaleLocks": {
    "locksByViewUid": {},
    "locksDict": {}
  }
};
```
</details>

<details>
<summary>Qualitative Peformance Comparison</summary>

### 200 Rows
[![Watch the video](https://img.youtube.com/vi/pebwrWi7G5E/hqdefault.jpg)](https://youtu.be/pebwrWi7G5E)

### 500 Rows
[![Watch the video](https://img.youtube.com/vi/HuCBCqyKLRE/hqdefault.jpg)](https://youtu.be/HuCBCqyKLRE)

### 1,000 Rows
[![Watch the video](https://img.youtube.com/vi/8HpQGxgV3PY/hqdefault.jpg)](https://youtu.be/8HpQGxgV3PY)

### 5,000 Rows
[![Watch the video](https://img.youtube.com/vi/E1xhX0NgrmM/hqdefault.jpg)](https://youtu.be/E1xhX0NgrmM)

### 10,000 Rows
[![Watch the video](https://img.youtube.com/vi/am_R3d7omH4/hqdefault.jpg)](https://youtu.be/am_R3d7omH4)
</details>
