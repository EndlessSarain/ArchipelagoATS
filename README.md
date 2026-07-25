# ATS Archipelago — Setup Guide

ATS Archipelago integrates **American Truck Simulator** with the [Archipelago](https://archipelago.gg) multiworld randomizer.
Complete deliveries to unlock new states, receive items from other players, and explore the full US map.

> **Read-only design** — the client never modifies your save files. It only listens to the game telemetry.

---

## Requirements

| Component | Requirement |
|---|---|
| **Windows** | 10 or 11 (64-bit) |
| **American Truck Simulator** | 1.55 or newer |
| **Archipelago** | any recent release — [archipelago.gg](https://archipelago.gg) |
| **ATS DLCs** | only the state DLCs you want to include in your randomizer |

---

## 1. Install the APWorld

1. Download the latest `ats.apworld` from the [Releases](../../releases) page.
2. Place it in your Archipelago `worlds/` folder:
   ```
   C:\ProgramData\Archipelago\lib\worlds\    (standard install)
   ```
   or drag it onto the Archipelago Launcher.
3. Restart the Archipelago Launcher — **ATS** will appear in the game list.

---

## 2. Generate your game

Open the Archipelago Launcher → **Generate** and configure your YAML:

```yaml
name: YourName
game: ATS

ATS:
  # DLCs you own (set to true to include in the randomizer)
  dlcs_owned:
    Nevada: true
    Arizona: true
    Oregon: true
    Washington: true
    Idaho: true
    Montana: true
    Wyoming: true
    Utah: true
    Colorado: true
    New Mexico: true
    Kansas: true
    Nebraska: true
    Oklahoma: true
    Texas: true

  # Starting state (always unlocked at game start)
  starting_state: california

  # Goal: how many states must be completed to finish
  # deliver_in_n_states (default) or all_deliveries
  goal: deliver_in_n_states
  goal_states: 6

  # Death Link: when you crash heavily, all other Death Link players get a penalty
  death_link: false
```

A YAML generator is also available in the client app (menu → YAML Generator).

---

## 3. Install the ATS Archipelago client

Download **`ATS Archipelago Setup 0.1.0.exe`** from the [Releases](../../releases) page and run it.

The installer will:
- Create a Start Menu shortcut and a Desktop shortcut.
- Store your settings in `%APPDATA%\ats-archipelago-client\config.json`.

---

## 4. Configure the client

Launch **ATS Archipelago** from your desktop or Start Menu.

In the **Settings** panel (gear icon in the top-right), fill in:

| Field | Value |
|---|---|
| **AP Server URL** | `ws://localhost:38281` (local) or your host's address |
| **Slot name** | your player name in the Archipelago session |
| **Password** | leave empty if the room has no password |

Click **Connect** — the status dot turns green when connected.

---

## 5. Launch ATS and start playing

1. Open **American Truck Simulator**.
2. Load your save (or start a new game).
3. The client detects the game automatically via the telemetry SDK.

When the header shows **ATS: connected**, you're ready.

---

## Gameplay loop

1. **Accept a delivery job** in ATS — any destination is valid.
2. **Complete the delivery.** The client detects it automatically and sends the location check to the AP server.
   - Checks are only registered for cities in **states you have access to**.
   - Your starting state is always accessible. Other states require receiving a `StateAccess` item.
3. **Items received** from other players appear in the client dashboard:
   - `StateAccess` — unlocks a new state (you can now do deliveries there).
   - `ProgressiveSkill` — a skill upgrade (ADR, Long Haul, etc.) — apply it manually in-game.
   - `ADR` — hazardous materials class unlock — apply manually in-game.
   - `TrailerAuth` — trailer type authorization — apply manually in-game.
   - `Trucker's Luck` — filler item, no action needed.

> Items that affect your truck (skills, ADR, trailers) are tracked in the dashboard but rely on the **honor system** — apply them yourself in-game when you receive them.

---

## Dashboard

The dashboard is the main app window. It shows:

| Section | Description |
|---|---|
| **AP Status** | connection state, slot name, current goal progress |
| **States** | locked / unlocked map of all 15 states |
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

Only states whose DLCs you **own and enable** in your YAML will be included in your randomizer. You need the DLC installed in ATS to drive there.

---

## Troubleshooting

| Problem | Solution |
|---|---|
| Status dot stays red after filling AP settings | Check the server URL format (`ws://` not `wss://` for local servers). Verify your slot name matches exactly. |
| Delivery not registering as a check | The destination city's state must be unlocked (you need its `StateAccess` item first). |
| `ATS: disconnected` shown in header | ATS is not running or the telemetry SDK is not available. Make sure ATS is open and loaded into a save. |
| App opens but no window appears | Check `%APPDATA%\ats-archipelago-client\` — delete `config.json` to reset to defaults. |
| Client crashes on startup | Run as Administrator once to allow the telemetry SDK to initialize. |

---

## Uninstall

Use **Add or Remove Programs** → **ATS Archipelago**.
Settings and state files in `%APPDATA%\ats-archipelago-client\` are not removed automatically.
