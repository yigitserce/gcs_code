# Nova-X Ground Control Station (GCS) // Lunar Operations

## Project Overview

Nova-X Ground Control Station is an operational-grade, mission-critical flight telemetry, trajectory tracking, and vehicle control console for aerospace lunar landing and ascent vehicles (LDAM - Lunar Descent & Ascent Module). Designed for deep-space mission operations and situational awareness, the console adheres to aerospace ergonomics and mission control visual standards (SpaceX, NASA, and OpenMCT), featuring high-density telemetry readouts, a Primary Flight Display (PFD) with integrated downward optical camera video feed (`output.mp4`), transparent artificial lunar horizon and velocity vector HUD, a Lunar Tactical Situation Display based on NASA LROC / OpenPlanetary cartography, cryogenic propulsion diagnostics, a 10-channel Caution & Warning (C&W) annunciator matrix, an integrated Edge SLM log stream, and synthetic Web Audio mission control acoustic feedback.

### Key Technologies
- **HTML5 & Vanilla JavaScript (ES6+)**: Self-contained client-side single-page application with decoupled state management, discrete numerical physics simulation (Lunar gravity $g = 1.622 \text{ m/s}^2$, hard vacuum dynamics), video time-synchronization, and Web Audio API integration.
- **Optical Video Feed (`output.mp4`) & High-DPI Canvas 2D**: Integrated 183.8-second real/simulated lunar descent video displayed directly in the Upper MFD camera field with a 60Hz Retina-scaled transparent HUD overlay (pitch ladder, roll scale arc, flight path marker, crosshairs, and tracking indicators).
- **Leaflet.js (v1.9.4) & Lunar Cartography**: High-resolution Moon tile services provided by **OpenPlanetary (CartoCDN)** and **NASA LROC** (Lunar Reconnaissance Orbiter Camera), with real-time trajectory tracking, historical Apollo 11 Tranquillity Base landmark markers, nearby crater toponyms (Collins, Aldrin, Armstrong), and landing zone (LZ-LUNAR-1) recovery perimeters.
- **Tailwind CSS & Aerospace Operational Palette**: Deep matte carbon palette (`#07080b` background, `#0d1017` panels) engineered to prevent operator eye fatigue while providing high-contrast MIL-STD annunciator alert colors.
- **Typography**: Google Fonts (`Inter` for structured system UI and `JetBrains Mono` with tabular numerals for high-frequency telemetry data).
- **Web Audio API**: Browser-native operational sound synthesizer providing configurable audio annunciators (telemetry locks, command clicks, caution chimes, warning alarms, and mute control).

---

## File Structure

```
/home/yigit/playground/gcs/
├── aerospace_gcs_software.html   # Mission control console UI, Lunar Leaflet map, Canvas PFD, and physics engine
├── output.mp4                    # 183.8-second 1080p Lunar descent camera footage (high orbit to touchdown)
└── GEMINI.md                     # Project architectural documentation, tile sources, and operational guide
```

---

## Lunar Descent Video Synchronization (`output.mp4`)

The flight dynamics engine, instrument readouts, and mission milestone sequencer are synchronized with the 183.8-second descent video (`output.mp4`):

| Video Timestamp | Flight Stage | Altitude (ASL) | Vertical Vel | Pitch | Physical / Visual Events |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **00:00 – 00:25** | `LUNAR_DEORBIT_COAST` | 32,000m → 22,000m | -15 m/s | 15° → 25° | High lunar orbit coast. Oblique view of the lunar horizon across the top of the frame. DSN Goldstone link locked. |
| **00:25 – 01:05** | `POWERED_BRAKING_BURN` | 22,000m → 12,000m | -20 → -45 m/s | 25° → 50° | Powered Descent Initiation (PDI). Retrograde braking burn at 100% throttle. Horizon tilts off screen as camera points downward. |
| **01:05 – 01:45** | `TERRAIN_NAV_APPROACH` | 12,000m → 3,200m | -45 → -27 m/s | 50° → 72° | Terrain Relative Navigation (TRN) locks onto crater chains and mare features. Craters expand rapidly in view. Throttle at 85%. |
| **01:45 – 02:20** | `TERMINAL_HAZARD_AVOID`| 3,200m → 450m | -27 → -8 m/s | 72° → 85° | Steep nadir descent. High-resolution small crater mapping and hazard avoidance. Throttle at 78%. |
| **02:20 – 02:40** | `FINAL_HOVER_APPROACH` | 450m → 30m | -8 → -1.8 m/s | 85° → 90° | Low altitude approach over touchdown plains. Landing legs deploy at 60m. Final hover braking. |
| **02:40 – 02:48** | `REGOLITH_DUST_BLOWOUT`| 30m → 0m | -1.8 → 0 m/s | 90° (Nadir) | Rocket exhaust impinges on lunar surface. **High-velocity radial regolith dust blowout occurs in video!** `REGOLITH_DUST` annunciator flashes amber/warning. |
| **02:48 – 03:03** | `TOUCHDOWN_LUNAR_SURFACE`| 0.0m | 0.0 m/s | 90° | Touchdown confirmed at LZ-LUNAR-1. Engine shutdown ($P_c = 0$, RPM = 0). Vehicle settled and secure. |

---

## Lunar Map Tile Sources

The application integrates public, high-speed, CORS-enabled XYZ raster tile services designed for web mapping on the Moon:

### 1. OpenPlanetaryMap Moon Basemap (Nomenclature & Labels)
- **URL**: `https://cartocdn-gusc.global.ssl.fastly.net/opmbuilder/api/v1/map/named/opm-moon-basemap-v0-1/all/{z}/{x}/{y}.png`
- **Data Source**: OpenPlanetary / NASA LROC WAC global mosaic / USGS Astrogeology.
- **Features**: Global Moon surface imagery complete with official IAU lunar crater names, maria (seas), and landing sites.
- **Projection**: Web Mercator (EPSG:3857) compatible with Leaflet without custom projection plugins.
- **Zoom Levels**: `minZoom: 2`, `maxZoom: 9`.

### 2. OpenPlanetary Moon Hillshaded Albedo (Relief)
- **URL**: `https://s3.amazonaws.com/opmbuilder/301_moon/tiles/w/hillshaded-albedo/{z}/{x}/{y}.png`
- **Data Source**: NASA Lunar Reconnaissance Orbiter (LRO) Wide Angle Camera (WAC) Hillshaded Albedo mosaic.
- **Features**: Photorealistic relief-shaded typography emphasizing crater rims, mountain massifs, and impact ejecta blankets.
- **Zoom Levels**: `minZoom: 2`, `maxZoom: 8`.

---

## Running and Serving

The software runs as a zero-build client application. No compilation, bundling, or package installations are required.

### Local Development Server

Run a local HTTP server from the project directory:

```bash
# Using Python 3:
python3 -m http.server 8000

# Using Node.js:
npx serve .
```

Open `http://localhost:8000/aerospace_gcs_software.html` in any modern web browser.

Alternatively, the file can be opened directly:
```bash
xdg-open aerospace_gcs_software.html
# or
google-chrome aerospace_gcs_software.html
```

---

## Console Features & Operations

### 1. Header & Deep Space Network (Top Bar - 46px)
- **Vehicle Status**: Active pulse beacon, vehicle identity (`NOVA-X LUNAR LDAM-02`), and dynamic flight state badge (`SYS_STANDBY`, `SYS_ARMED`, `DEORBIT_COAST`, `POWERED_BRAKING_BURN`, `TERRAIN_NAV_APPROACH`, `TERMINAL_HAZARD_AVOID`, `FINAL_HOVER_APPROACH`, `REGOLITH_DUST_BLOWOUT`, `TOUCHDOWN_LUNAR_SURFACE`, `FTS_ACTIVATED`).
- **Deep Space Network (DSN) Link Matrix**: Ground station (`GOLDSTONE 70M`), Uplink frequency (`50 Hz`), Downlink bandwidth (`4.8 Mb/s`), Light travel time delay / RTT (`1.28s`), and Packet Loss rate (`0.0%`).
- **Lunar Milestone Sequencer**: Visual breadcrumb tracker highlighting active and completed mission gates (`PRE-LNCH` → `ARMED` → `DE-ORBIT` → `PDI BRAKE` → `APPROACH` → `TERMINAL` → `DUST BLOW` → `TOUCHDOWN`).
- **Primary Control Deck**: Safety arming (`ARM VEHICLE`), launch/descent trigger (`START DESCENT`), pause/inspect toggle (`HOLD / RESUME`), and emergency Flight Termination System button (`ABORT / FTS`).

### 2. Primary Flight Display: 3-Way Multi-Viewport 3D Lunar Cameras (Upper MFD)
- **3-Way Multi-Viewport Split Architecture**:
  - The 3D camera display is split vertically into two sections (60% left, 40% right), with the right section divided horizontally into two viewports (50% top, 50% bottom):
    - **Left Viewport (60% Width, Full Height - CAM 1: Primary Descent EO-01)**: Primary optical descent sensor replicating `output.mp4` trajectory (high orbit horizon at 00:00, braking pitch-down at 00:25, crater approach at 01:05, vertical nadir at 01:45, dust blowout at 02:40, touchdown at 02:48) with high-DPI transparent HUD instrumentation overlay (pitch ladder, roll scale arc, flight path marker, boresight crosshair, and LZ-1 target diamond).
    - **Top-Right Viewport (40% Width, 50% Height - CAM 2: Oblique Horizon & Limb)**: Wide-angle 68° FOV panoramic view showing the curved lunar limb, crater rims, sunrise shadows, and deep space starfield.
    - **Bottom-Right Viewport (40% Width, 50% Height - CAM 3: Nadir TRN Crater Zoom)**: Direct downward optical mapping camera with narrow 32° telephoto optics (2.5X zoom) tracking crater landmarks and LZ-1 touchdown apron, complete with center reticle crosshair.
- **Hardware-Accelerated Scissor Rendering**:
  - Implemented via WebGL `renderer.setScissorTest(true)` and `renderer.setViewport()` rendering all 3 independent cameras in a single 60 FPS draw cycle on a unified WebGL context without memory duplication.
- **Layout & Display Toggles**:
  - **`[VIEW: 3D MULTI-CAM]` vs `[VIEW: MP4 VIDEO]`**: Toggle between the 3D multi-camera simulation and the recorded `output.mp4` video feed.
  - **`[LAYOUT: 3-WAY SPLIT]` vs `[LAYOUT: MAXIMIZE MAIN]`**: Instantly maximize the primary descent camera to 100% width or restore the 3-way multi-camera split.
  - **`[HUD: ON / OFF]`**: Toggle transparent HUD overlays over the primary camera.
- **3D Lunar Environment (Real NASA SVS 4720 CGI Moon Kit)**:
  - 100% unobstructed optical view of the Moon surface (no vehicle body geometry blocking the camera view).
  - Integrated real **NASA SVS 4720 CGI Moon Kit** data:
    - **NASA LROC Photographic Color Mosaic**: Authentic photographic surface albedo of Mare Tranquillitatis derived from the Lunar Reconnaissance Orbiter Camera 8K global polar mosaic (`lroc_color_poles_8k.tif`), capturing true mare basalt coloration, ejecta blankets, and crater rays.
    - **NASA LOLA 16-Bit Laser Altimeter DEM**: Real topological surface elevation displacement derived from the Lunar Orbiter Laser Altimeter (`ldem_16_uint.tif`), reproducing the true $-1,915\text{m}$ Mare Tranquillitatis basin depression and surrounding massifs.
    - **Zero-CORS Embedded Data Asset (`nasa_moon_data.js`)**: Packaged as a local base64/array script asset so real NASA surface textures and DEM grids render immediately upon opening `file:///` without CORS restriction or running an HTTP server.
  - 16,000m subdivided terrain mesh (200x200 segments, 40,000+ vertices) combining NASA LOLA elevation with high-frequency micro-regolith undulations and global lunar radius curvature ($R = 1,737.4\text{ km}$).
  - Tangent-space procedural regolith normal map (tiled 60x60) providing crisp rock and pebble relief down to ground level.
  - Level engineered landing zone pad (LZ-1) apron at $(0, 0)$ with concentric circular target markings and range crosshairs.
  - Radial regolith dust blowout particle system triggered at touchdown altitudes (< 30m).
- **Interactive Scrubber & Stage Jump**:
  - `DE-ORBIT (00:00)`, `PDI BRAKE (00:25)`, `APPROACH (01:45)`, `DUST (02:40)`, `TOUCHDOWN (02:48)` scrub all 3 viewports and the video in lockstep.

### 3. Lunar Surface Map (Center Panel - Lower MFD)
- **High-resolution Lunar cartography** using OpenPlanetary / NASA LROC tiles.
- **Layer Toggle Button**: Switch between `MAP: LABELED` (craters, maria, landing sites) and `MAP: RELIEF SHADED` (photorealistic relief).
- **View presets**: `FOLLOW VEHICLE`, `TRANQUILLITY BASE`, and `LZ-LUNAR-1`.
- **Synchronized Ground Track**: As the video plays, the vehicle traverses the ground track across Mare Tranquillitatis directly to the landing zone (`LZ-LUNAR-1`).

### 4. Vehicle Systems, Propulsion & 3D Rocket Model (Right Panel - 360px)
- **Nova-1 Lunar Methalox Engine**: Main engine state (`STANDBY`, `FIRING`, `SHUTDOWN`), Chamber Pressure ($P_c$) bar, Throttle command (%), Vacuum thrust (kN), and Turbopump RPM.
- **Cryogenic Tankage**: LOX and LCH4 fill percentage and mass depleting in sync with engine firing duration.
- **TVC Gimbal & RCS**: 2D TVC plot showing active gimbal stabilization nulling lateral drift.
- **3D Rocket Vehicle Model Viewport (`MODEL`)**:
  - Located directly in the `Attitude Control Actuators` card below the TVC and RCS indicators, enlarged to 195px height and scaled prominently.
  - Dedicated hardware-accelerated WebGL viewport rendering the rocket assembly with multi-light studio shading (key, fill, and rim lights).
  - **Isometric Front-Facing Attitude Stance**: Default inspection orientation showing structural depth with 14° down-pitch and 38° yaw rotation, tracking live telemetry deviations (Pitch, Roll, Yaw).
  - **Quick Preset Attitude Modes**: Buttons for `ISO` (Isometric Front-Facing), `FRONT` (Orthogonal elevation), `TOP` (Nadir deck view), and `RESET` (Restores default isometric vantage).
  - Full 360° interactive OrbitControls: Operator can click and drag to rotate the rocket around any axis, scroll to zoom.
  - Zero-CORS automatic loading: Standardized on `model.*` (`model.js`, `model.glb`, `model.3mf`). Automatically loads the 3D model on initial page open even under strict `file:///` browser security without requiring manual file picker dialogs or running a local HTTP server. Also supports direct local file fetch (`model.glb`/`model.3mf`) and drag-and-drop.

### 5. Annunciator Matrix & Command Console (Bottom Panel - 230px)
- **Caution & Warning (C&W) Matrix**: 10 active diagnostic tiles (`DSN_LOCK`, `TRN_LIDAR`, `IMU_NOMINAL`, `REGOLITH_DUST`, `APOGEE_CUT`, `VAC_DESCENT`, `ENGINE_IGN`, `RADAR_ALTIM`, `GEAR_DEPLOY`, `FTS_ARMED`).
- **Interactive Command Input (`CMD>`)**: Supports commands including `help`, `arm`, `launch`, `hold`, `resume`, `abort`, `seek <seconds>`, `throttle <0-100>`, `rcs_test`, `vent`, and `status`.
