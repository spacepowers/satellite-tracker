# Satellite Tracker

A single-file, browser-based satellite tracker that plots the real-time position of Earth-orbiting satellites on a world map.

There is nothing to install and no server to run. Open `satellite-tracker.html` in a web browser, or host it on any static web host such as GitHub Pages.

## Background

The first piece of space-related software that I ever used, back in 1990, was a satellite tracker called STSORBIT, written by David H. Ransom, Jr. I read about this program in a magazine and Dave kindly sent a copy on disk, since I had no modem to download it. Using STSORBIT to follow Space Shuttle missions, while listening to shortwave broadcasts of the astronauts' communications with mission control, helped inspire my interest in the technical aspects of spaceflight and put me on the path to an engineering career.

This project is inspired by the subsequent STSORBIT Plus. You can still download the last version of that program from a mirror of its original website hosted by Dr. TS Kelso's Celestrak at https://celestrak.org/software/dransom/stsplus.html.

The original version runs on MS-DOS, including in emulators like DOSBox. My goal with this project is to bring some of the unique functions and simple but elegant map display of STSORBIT to a web browser. 

This version is an original creation from scratch using Claude AI.

## Features

**Map**
- Black background with continent and large-island outlines
- Yellow day/night terminator and a sun icon at the subsolar point
- White satellite icons with name labels
- White line showing the part of the Earth visible from the selected satellite
- Ground track from one orbit in the past to two orbits in the future, with one dot per minute: green for points the satellite has not reached yet, red for points it has passed
- The selected satellite's icon changes color with its illumination: white in full sun, dark red in partial sun (penumbra), gray in Earth's shadow
- Satellites that are plotted but not selected show three white dots for their last three whole-minute positions

**Primary satellites** (for example, the International Space Station)
- Load one or many satellites and tick the ones to plot
- If the list was opened from a file or pasted, an "Update ticked satellites from CelesTrak" button downloads the latest orbit data for each ticked satellite individually and uses it in the application. The file itself is not changed, and the updates are remembered with your saved settings until you load a new list
- Select one as the primary satellite: from the dropdown, or by clicking its icon on the map
- Detail box: name, catalog number, inclination, altitude, orbital period, illumination, and the predicted **Sun AOS** and **Sun LOS** (entering and leaving sunlight)
- Status bar under the map: UTC time, local time, and the satellite's latitude, longitude and altitude

**Secondary satellites** (for example, TDRS relay satellites)
- Load a second list and tick the satellites to plot
- A dashed circle for each shows where the primary satellite has line of sight to that secondary (not blocked by the Earth)
- A table shows, for each ticked secondary: in view or out of view, the off-nadir angle from the secondary to the primary (shown only while in view), and the next AOS and LOS
- If the list was opened from a file or pasted, an "Update ticked satellites from CelesTrak" button downloads the latest orbit data for each ticked satellite individually and uses it in the application. The file itself is not changed, and the updates are remembered with your saved settings until you load a new list

**Ground station**
- Enter a name, latitude, longitude and minimum elevation, click on the map, or use your browser's location
- Shown as a magenta marker with a magenta circle of visibility for the selected satellite
- A dedicated box shows the current elevation, azimuth and range, and the next AOS and LOS of the primary satellite

**Saved settings**
- Selections are remembered automatically in the browser
- Settings can also be saved to and loaded from a JSON file

## Quick start

**Run locally**
1. Download `satellite-tracker.html`.
2. Double-click it to open it in your browser.

**Host on GitHub Pages**
1. Put `satellite-tracker.html` in your repository (renaming it to `index.html` makes it the default page).
2. In the repository, go to Settings, then Pages, and choose the branch to publish.

An internet connection is required (see [Network use](#network-use)).

On first run the page downloads current ISS orbit data and the TDRSS list from CelesTrak. TDRS 7, TDRS 11 and TDRS 12 are ticked by default. If the downloads fail, a built-in sample ISS orbit is used, but it is old and will place the satellite in the wrong position. Paste or download a current element set for accurate results.

## Loading orbit data

Both the primary and secondary lists accept any of these, pasted into the text box, opened from a file, or downloaded from a web address:

| Format | Notes |
|---|---|
| Two-line elements (TLE) | With or without a name line; several satellites per file |
| TLE with Alpha-5 catalog numbers | For catalog numbers above 99999 |
| CCSDS OMM, KVN | Default download format |
| CCSDS OMM, JSON | Array or single object |
| CCSDS OMM, XML | |

Orbits are propagated with SGP4 (via satellite.js), including the deep-space model for satellites such as TDRS.

The default web addresses use CelesTrak's GP data service, for example:

```
https://celestrak.org/NORAD/elements/gp.php?CATNR=25544&FORMAT=KVN
https://celestrak.org/NORAD/elements/gp.php?GROUP=tdrss&FORMAT=KVN
```

Any other address that returns a supported format works, as long as the server allows cross-origin requests.

### Be considerate to data providers

To avoid unnecessary requests, the page keeps the last downloaded data and only downloads again when it is more than 24 hours old. The Fetch buttons always download immediately.

The page also follows [CelesTrak's usage policy](https://celestrak.org/usage-policy.php) for error responses. If CelesTrak answers with HTTP 301, 403, 404 or any 50x error, the page shows a warning, stops all CelesTrak queries for one hour (including the Fetch buttons, and across page reloads), and keeps using the last data it has. If you see this warning, check that your web addresses match the current [CelesTrak documentation](https://celestrak.org/NORAD/documentation/gp-data-formats.php).

## Using the application

1. **Choose the primary satellite** in the Primary satellites section. Tick other satellites to plot them too.
2. **Choose secondary satellites** if you want line-of-sight circles and AOS/LOS times between the primary and relay satellites.
3. **Set a ground station** to see its visibility circle and pass times.
4. **Settings** are remembered automatically. Use "Save settings file" to keep a named setup or move it to another computer, and "Forget saved settings" to return to the defaults.

### Terms

| Term | Meaning here |
|---|---|
| AOS | Acquisition of signal: when the link begins (rises above the minimum elevation, or comes into line of sight) |
| LOS | Loss of signal: when the link ends |
| Sun AOS / Sun LOS | When the satellite enters or leaves sunlight; partial sun counts as sunlight |
| Off-nadir angle | At the secondary satellite, the angle between straight down (toward the Earth's center) and the direction to the primary |

Predictions look 24 hours ahead in 30-second steps and refine each event to about one second.

## Network use

The page makes these requests:

| Purpose | Source |
|---|---|
| SGP4 library (satellite.js 5.0.0) | cdnjs.cloudflare.com |
| Coastline data (world-atlas 2.0.2, 1:110m land) | cdn.jsdelivr.net |
| Orbit data (default lists) | celestrak.org |

Nothing is sent to any server owned by this project. If you open the page without an internet connection, the map and calculations will not load.

## Privacy

- Settings and a copy of your orbit data are stored in your browser's local storage, on your computer only.
- "Use my location" asks your browser for permission and uses your position only to fill in the ground station fields. It is not sent anywhere by this page.

## Browser support

Written for current versions of Google Chrome, Microsoft Edge, Mozilla Firefox and Apple Safari, with no extensions needed. Some browsers restrict local storage and location access for files opened directly from disk. If automatic saving or "Use my location" does not work, host the file on the web or use the settings file instead.

## Accuracy and limitations

- Positions come from TLE or OMM data propagated with SGP4. Accuracy depends on how recent the elements are; refresh them regularly, especially for low orbits.
- Line-of-sight tests consider only the Earth's limb. They ignore the atmosphere, terrain, antenna pointing limits and satellite hardware constraints.
- Visibility circles use a spherical Earth, and the sun position uses a low-precision formula. Both are fine for display and for planning to within a minute or so, but are not suited to precision work.
- Shadow prediction uses a simple geometric model of the Sun and Earth discs, without atmospheric effects.
- Passes shorter than 30 seconds could be missed by the AOS/LOS search.
- Visible-area circles that cross a pole or the map edge can look split or distorted on the flat map.
- Currently only real-time plotting is supported (past and future times to be added in a later update).

## Project layout

```
satellite-tracker.html   The whole application (HTML, CSS and JavaScript in one file)
README.md                This file
LICENSE.md               GNU General Public License v3
```

## Third-party software and data

| Component | License | Source |
|---|---|---|
| [satellite.js](https://github.com/shashwatak/satellite-js) | MIT | SGP4/SDP4 propagation |
| [world-atlas](https://github.com/topojson/world-atlas) | ISC | Packaging of the map data as TopoJSON |
| [Natural Earth](https://www.naturalearthdata.com/) | Public domain | Underlying coastline data ("Made with Natural Earth") |
| [CelesTrak](https://celestrak.org/) | See https://celestrak.org/usage-policy.php | Default orbit data; please follow their terms of use |

These libraries and data are loaded from public CDNs at run time and are not included in this repository. If you copy any of them into your own distribution, include their license notices.

## License

GPL v3, see LICENSE.md

## Acknowledgments

- David H. Ransom and STSORBIT Plus, for 3+ decades of inspiration 
- Dr. T.S. Kelso and [CelesTrak](https://celestrak.org/) for orbit data and SGP4 documentation
- Shashwat Kandadai and the satellite.js contributors
