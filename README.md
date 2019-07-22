# LPvis 🕺
> Pronounce: "Elpvis" | A land parcel visualizer powered by a lightweight Leaflet map

Use LPvis to visualize the results of crop type classifications in comparison to IACS/CAP (Common Agricultural Policy) declarations. Simple traffic light symbology shows computed conformity and tooltips and a data table let you dig deeper into the data.

>//TODO: DEMO 

![LPvis 🕺 Screenshot](media/Screenshot_LPvis_🕺.png)

## Prerequisites

LPvis expects LPIS (Land-Parcel Identification System) data and administrative boundaries as vector tiles in `.pbf` (Protobuf) format. They must be served as tile directories using a `ZXY`-scheme.

### Example using Austrian LPIS/IACS data

1. Download [agricultural parcels](https://www.data.gv.at/katalog/dataset/e21a731f-9e08-4dd3-b9e5-cd460438a5d9), [phyical blocks](https://www.data.gv.at/katalog/dataset/b9cab416-896b-408a-8665-9f4ac30f79db) and [municipalities](https://www.data.gv.at/katalog/dataset/566c99be-b436-365e-af4f-27be6c536358) as `shp`.
- Transform them in QGIS to `EPSG:4326` (required by [`tippecanoe`](https://github.com/mapbox/tippecanoe)).
- Join classification results to agricultural parcels.
- Export as `gpkg`.
- Pipe `gpkg` with `fiona` to [`tippecanoe`](https://github.com/mapbox/tippecanoe). E.g.:
```shell
fio cat agricultural_parcels.gpkg | tippecanoe --drop-densest-as-needed --read-parallel --minimum-zoom=14 --maximum-zoom=18 --output=agricultural_parcels.mbtiles -f
```
6. Serve `mbtiles` with [`tileserver-gl-light`](https://github.com/klokantech/tileserver-gl).

## Installation / Getting started

1. Download the source files.
2. Start a simple development server (e.g. using Python 3: `python -m http.server 8000`).
3. Navigate to `http://localhost:8000/`.

The above steps will let you view LPvis in your browser. Most probably you will have to make some configurations before LPIS data and administrative boundaries show up.

### Initial Configuration

Replace the URLs in `main.js` with the ones pointing to your hosted tile directories. You should also adapt the respective `attribution` options further down in the code.

```javascript
const AGRICULTURAL_PARCELS_URL_TEMPLATE = 'http://localhost:8080/data/agricultural_parcels/{z}/{x}/{y}.pbf'
const PHYSICAL_BLOCKS_URL_TEMPLATE = 'http://localhost:8080/data/physical_blocks/{z}/{x}/{y}.pbf'
const MUNICIPALITIES_URL_TEMPLATE = 'http://localhost:8080/data/municipalities/{z}/{x}/{y}.pbf'
```

NUTS administrative boundaries are used to automatically center LPvis on the area of interest. A good source is [Eurostat's Nuts2json](https://github.com/eurostat/Nuts2json), but you can also replace the URL with the path to a local GeoJSON. `NUTS_LEVEL` and `NUTS_CODE_STARTS_WITH` are used to specify the administrative level and filter by NUTS code, respectively. They only apply to GeoJSONs from Nuts2json.

```javascript
const NUTS_LEVEL = 3
const NUTS_CODE_STARTS_WITH = 'AT'
const NUTS2_GEOJSON_URL = `https://raw.githubusercontent.com/eurostat/Nuts2json/gh-pages/2016/4258/10M/nutsrg_${NUTS_LEVEL}.json`
```

The unique identifier as given in the data attributes must be specified too or symbology and tooltips will get messed up. The tooltips might need additional configuration (search for `setTooltipContent` in the code).

```javascript
const AGRICULTURAL_PARCELS_UNIQUE_IDENTIFIER = 'ID'
const PHYSICAL_BLOCKS_UNIQUE_IDENTIFIER = 'RFL_ID'
```

For the peak-through feature to work in other countries than Austria, you will have to change this URL too:

```javascript
const ORTHOPHOTO_URL_TEMPLATE = 'https://maps{s}.wien.gv.at/basemap/bmaporthofoto30cm/normal/google3857/{z}/{y}/{x}.jpeg'
```
### Configure traffic light symbology

When the confidence in the classification of a parcel lies below a certain threshold, it is coloured in yellow. You can configure this value in `main.js`:

```javascript
const CONFIDENCE_THRESHOLD = 95
```


## Features

* Visualize crop type classification vs. declaration using traffic light symbology
* Tooltips show detailed information on parcels when you mouseover them
* Populate data table by clicking on parcels and export attributes as CSV
* Overlay parcels with reference areas by swiping
* Peak-through to ground truth and view the orthophoto
* EOX Terrain Light or Sentinel-2 Cloudless 2018 basemaps
* Minimap providing an overview area using OpenStreetMap data


## Contributing

We are interested in your contributions. For questions, feedback and feature proposals open an issue. For minor bug fixes you can create a PR right away.

## Dependencies
- [Leaflet.js](https://leafletjs.com/) / [License information](https://github.com/Leaflet/Leaflet/blob/master/LICENSE)
- [Leaflet.VectorGrid](https://github.com/Leaflet/Leaflet.VectorGrid) / [License information](https://github.com/Leaflet/Leaflet.VectorGrid#legalese)
- [Leaflet table](https://github.com/diogok/leaflet.table) / MIT License
- [Leaflet-control-swipe](https://github.com/frogcat/leaflet-control-swipe) / MIT License
- [Leaflet Magnifying Glass](https://github.com/bbecquet/Leaflet.MagnifyingGlass) / MIT License
- [Leaflet.Control.Custom](https://github.com/yigityuce/Leaflet.Control.Custom) / MIT License
- [Leaflet.MiniMap](https://github.com/Norkart/Leaflet-MiniMap) / BSD 2-Clause "Simplified" License

## Licensing

_The code in this project is licensed under the MIT license._

___

Copyright 2019 EOX IT Services GmbH

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
