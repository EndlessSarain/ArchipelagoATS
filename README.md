# ATS Archipelago — Setup Guide

ATS Archipelago integrates **American Truck Simulator** with the [Archipelago](https://archipelago.gg) multiworld randomizer.
Complete deliveries, hit distance milestones, and haul cargo to earn checks — while other players unlock your access to new states, truck brands, skills, and more.

> **Read-only design** — the client never modifies your save files. It only listens to the game telemetry.

---

## Requirements

| Component | Requirement |
|---|---|
| **Windows** | 10 or 11 (64-bit) |
| **American Truck Simulator** | 1.55 or newer |
| **Archipelago** | any recent release — [archipelago.gg](https://archipelago.gg) |
| **scs-sdk-plugin** | v1.12.1 — [github.com/RenCloud/scs-sdk-plugin](https://github.com/RenCloud/scs-sdk-plugin/releases) |
| **ATS DLCs** | only the state/cargo DLCs you want to include in your randomizer |

---

## 1. Install the APWorld

1. Download the latest `ats.apworld` from the [Releases](../../releases) page.
2. Install it using one of these methods:
   - **Double-click** `ats.apworld` — Archipelago will install it automatically.
   - **Drag it** onto the Archipelago Launcher.
   - **Manually copy** it to your Archipelago `worlds/` folder:
     ```
     C:\ProgramData\Archipelago\lib\worlds\    (standard install)
     ```
3. Restart the Archipelago Launcher.

---

## 2. Install the ATS Archipelago client

Download **`ATS Archipelago Setup 0.2.0.exe`** from the [Releases](../../releases) page and run it.

The installer will:
- Create a Start Menu shortcut and a Desktop shortcut.
- Store your settings in `%APPDATA%\ats-archipelago-client\config.json`.

---

## 3. Generate your game

Configure your YAML in the **ATS Archipelago client** → **YAML** tab. Once done, place the `.yaml` file in:
```
C:\ProgramData\Archipelago\lib\Players\
```
Then generate your game from the Archipelago Launcher → **Generate**:

```yaml
name: YourName
game: American Truck Simulator

American Truck Simulator:

  # ── States ──────────────────────────────────────────────────────────────────

  # DLC states included in the randomizer (California always included)
  active_states:
    - nevada
    - arizona
    - oregon
    - washington
    - idaho
    - montana
    - wyoming
    - utah
    - colorado
    - new_mexico
    - kansas
    - nebraska
    - oklahoma
    - texas

  # Starting state (always unlocked at game start)
  # Set randomize_starting_state: true to pick randomly instead
  starting_state: california
  randomize_starting_state: false

  # ── Goals ───────────────────────────────────────────────────────────────────

  # Goal type:
  #   deliver_in_n_states  — make at least one delivery in N distinct states
  #   all_deliveries       — complete a first delivery in every active city
  #   all_states           — make at least one delivery in every active state
  #   total_km             — drive enough total distance (km milestones)
  #   cargo_master         — complete a first delivery for every active cargo type
  goal: deliver_in_n_states
  goal_states: 6   # only used by deliver_in_n_states

  # ── Locations ───────────────────────────────────────────────────────────────

  # Percentage of cities per state that become delivery checks (10–100)
  city_percentage: 100

  # Km milestone checks: how many milestones and distance between each (in km)
  km_milestone_count: 5    # 0 to disable
  km_milestone_step: 10000 # e.g. 5 milestones → 10k / 20k / 30k / 40k / 50k km

  # XP milestone checks: how many milestones and XP between each
  xp_milestone_count: 5    # 0 to disable
  xp_milestone_step: 10000

  # ── Cargo ───────────────────────────────────────────────────────────────────

  # Cargo DLC packs whose cargo types become "First Delivery of X" checks
  # Options: bobcat, jcb, krone, volvo_construction,
  #          special_transport, forest_machinery, heavy_cargo, farm_machinery
  active_cargo_dlcs: []    # empty = base game cargoes only

  # Percentage of base-game cargo types included as cargo checks (10–100)
  cargo_percentage: 100

  # ── Item randomization ──────────────────────────────────────────────────────

  # Add progressive skill upgrades and ADR class unlocks to the item pool
  randomize_skills: true

  # Add trailer type authorizations to the item pool
  randomize_trailers: true

  # Add truck brand unlocks to the item pool
  randomize_truck_brands: true
  starting_truck_brand: peterbilt   # brand you start with
  # Brands: peterbilt, kenworth, freightliner, western_star, mack, volvo, international
  allowed_truck_brands:
    - peterbilt
    - kenworth
    - freightliner
    - western_star
    - mack
    - volvo
    - international

  # Gate individual city access behind City Access items (honour system)
  randomize_city_access: false

  # ── Other ───────────────────────────────────────────────────────────────────

  # Death Link: a heavy crash sends a death signal to all Death Link players
  death_link: false
```

---

## 4. Install the scs-sdk-plugin in ATS

The client reads game telemetry through **[scs-sdk-plugin](https://github.com/RenCloud/scs-sdk-plugin)** — a small DLL that ATS loads on startup and writes telemetry data to shared memory.

1. Download the latest `scs-telemetry.dll` from [github.com/RenCloud/scs-sdk-plugin/releases](https://github.com/RenCloud/scs-sdk-plugin/releases).
2. Place it in your ATS plugins folder:
   ```
   C:\Program Files (x86)\Steam\steamapps\common\American Truck Simulator\bin\win_x64\plugins\
   ```
   (create the `plugins/` folder if it doesn't exist)
3. Launch ATS once to confirm it loads — no error dialog means it worked.

> Without this plugin the ATS Archipelago client will start but **deliveries will never be detected**.

---

## 5. Configure the client

Launch **ATS Archipelago** from your desktop or Start Menu.

In the **Settings** panel (gear icon in the top-right), fill in:

| Field | Value |
|---|---|
| **AP Server URL** | `ws://localhost:38281` (local) · `wss://archipelago.gg:11111` (hosted on archipelago.gg) |
| **Slot name** | your player name in the Archipelago session |
| **Password** | leave empty if the room has no password |

Click **Connect** — the status dot turns green when connected.

---

## 6. Launch ATS and start playing

1. Open **American Truck Simulator**.
2. Load your save (or start a new game).
3. The client detects the game automatically via the telemetry SDK.

When the header shows **ATS: connected**, you're ready.

---

## Gameplay loop

### Deliveries
1. **Accept a delivery job** in ATS — any destination is valid.
2. **Complete the delivery.** The client detects it automatically and sends the location check.
   - Checks are only registered for cities in **states you have access to**.
   - If `randomize_city_access` is enabled, individual cities also require a **City Access** item.
3. For a **Perfect Delivery** check: complete the delivery with **0% cargo damage**.
4. For a **No-Autopark** check: complete the delivery **without using autopark**.

### Milestones
- **Km Milestones**: automatically checked as your total driven distance increases.
- **XP Milestones**: automatically checked as your total XP increases.

### Cargo
- **First Delivery of [cargo]**: complete one delivery carrying that specific cargo type.

### Markets
- **First Job from [market]**: accept your first job from each job market type (Freight Market, Cargo Market, Quick Job, External Contract, External Market).

### Special Transport
- **First Special Transport**: complete one special/oversized transport mission.

---

## Items received

| Item | Effect |
|---|---|
| `[State] Access` | Unlocks a state — deliveries there now count as checks |
| `[City] Access` | Unlocks a specific city (when `randomize_city_access` is on) |
| `[Brand] Access` | Unlocks a truck manufacturer (honour system — only buy from unlocked brands) |
| `Progressive [Skill]` | Raises a driver skill by one level (apply manually in-game) |
| `ADR Class N` | Unlocks a HAZMAT class (apply manually in-game) |
| `[Trailer] Authorization` | Unlocks a trailer type (apply manually in-game) |
| `Trucker's Luck` | Filler — no action needed |

> Skills, ADR classes, trailer authorizations, and truck brand unlocks are tracked in the dashboard but rely on the **honour system** — apply them yourself in-game when you receive them.

---

## Goals

| Goal | Description |
|---|---|
| `deliver_in_n_states` | Make at least one delivery in N distinct states (default) |
| `all_deliveries` | Complete a first delivery in every active city |
| `all_states` | Make at least one delivery in every active state |
| `total_km` | Reach all km milestones |
| `cargo_master` | Complete a first delivery for every active cargo type |

---

## Dashboard

The dashboard is the main app window. It shows:

| Section | Description |
|---|---|
| **AP Status** | connection state, slot name, current goal progress |
| **States** | locked / unlocked map of all active states |
| **Truck Brands** | locked / unlocked truck manufacturers |
| **Checks** | list of completed delivery locations |
| **Items** | all received items with their categories |
| **Truck** | live telemetry (speed, fuel, city, job) |

---

## States & DLCs

| State | DLC required | Base game? |
|---|---|---|
| California | — | ✅ included |
| Nevada | Nevada | |
| Arizona | Arizona | |
| Oregon | Oregon | |
| Washington | Washington | |
| Idaho | Idaho | |
| Montana | Montana | |
| Wyoming | Wyoming | |
| Utah | Utah | |
| Colorado | Colorado | |
| New Mexico | New Mexico | |
| Kansas | Kansas | |
| Nebraska | Nebraska | |
| Oklahoma | Oklahoma | |
| Texas | Texas | |

Only states whose DLCs you **own and list in `active_states`** will be included. You need the DLC installed in ATS to drive there.

### Cargo DLCs

| Key | DLC name |
|---|---|
| `bobcat` | Bobcat Equipment Pack |
| `jcb` | JCB Equipment Pack |
| `krone` | Krone Trailer Pack |
| `volvo_construction` | Volvo Construction Equipment |
| `special_transport` | Special Transport |
| `forest_machinery` | Forest Machinery |
| `heavy_cargo` | Heavy Cargo Pack |
| `farm_machinery` | Farm Machinery Pack |

---

## Troubleshooting

| Problem | Solution |
|---|---|
| Status dot stays red after filling AP settings | Check the server URL format (`ws://` not `wss://` for local servers). Verify your slot name matches exactly. |
| Delivery not registering as a check | The destination city's state (and city if `randomize_city_access` is on) must be unlocked. |
| `ATS: disconnected` shown in header | ATS is not running or `scs-sdk-plugin` is not installed. Check that `scs-telemetry.dll` is in your ATS `plugins/` folder. |
| App opens but no window appears | Check `%APPDATA%\ats-archipelago-client\` — delete `config.json` to reset to defaults. |
| Client crashes on startup | Run as Administrator once to allow the telemetry SDK to initialize. |
| Km/XP milestones not triggering | These require the client to be running while ATS is open — the telemetry SDK tracks cumulative values in real time. |

---

## Uninstall

Use **Add or Remove Programs** → **ATS Archipelago**.
Settings and state files in `%APPDATA%\ats-archipelago-client\` are not removed automatically.
