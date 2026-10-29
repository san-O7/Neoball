# NEOBALL

Neon 8-ball arcade billiards for the browser. Two players, on one screen or online. Spin English, power charging, and a shot clock. Everything lives in a single `index.html` file with no build step.

## Quick start

1. Open `index.html` in a modern browser (Chrome, Edge, Firefox, Safari), or host it on any static site.
2. Choose **Local Play** for two players on one device, or **Online Play** to create or join a room.

Local play works offline. Online play needs an internet connection and a configured Firebase project (see below).

## How to play

Standard 8-ball rules:

- The table starts open. The first legal pot assigns solids or stripes.
- You keep shooting while you pot your own balls.
- A foul gives your opponent **ball in hand**. Fouls are: potting the cue ball, hitting nothing, hitting the wrong group first, or no rail contact after a non-potting hit.
- Once your group is cleared, the 8-ball is your target.
- Potting the 8-ball early, on the break, or with a scratch or wrong first contact loses the game.
- Running out the shot clock is a foul.

## Controls

| Action | Mouse / touch | Keyboard |
|---|---|---|
| Aim | Drag on the table (touch: swipe to rotate) | Arrow keys or A / D (hold Shift for fine aim) |
| Place cue ball | Drag the white ball when you have ball in hand | |
| English (spin) | Drag the reticle in the ENGLISH widget | |
| Power and shoot | Pull the POWER rail down and release | Hold Space to charge, release to shoot |
| Pause | PAUSE button | P or Esc |
| Mute | Volume slider in Settings | M |

## Online play

1. One player picks **Create Room**, sets the shot clock, aim assist, cloth and theme, then shares the 6-character code.
2. The other player picks **Join Room** and enters the code.
3. The host shoots first. If either player disconnects mid-match, the other wins.

Online matches are turn-synced: shots are broadcast, both clients simulate the same fixed-timestep physics, and the host's board state is the authority at the end of each turn. Pausing online only opens the menu; the match keeps running.

### Firebase setup

1. Create a project in the [Firebase console](https://console.firebase.google.com/) and add a **Realtime Database**.
2. Copy your web app config into the `firebaseConfig` object in `index.html` (search for `FIREBASE ROOM SYSTEM`).
3. Set your database rules so clients can only touch `rooms/`. A starting point:

```json
{
  "rules": {
    ".read": false,
    ".write": false,
    "rooms": {
      "$code": {
        ".read": true,
        ".write": true
      }
    }
  }
}
```

This is a permissive example. Tighten it before sharing the game widely, for example by validating data shape and expiring old rooms. The API key in a Firebase web config is not secret, so your rules are what protect the database.

If the console shows "Firebase SDK failed to load", the CDN scripts were blocked. The page tries Google, then jsDelivr, then cdnjs. Check for ad blockers, VPNs, or opening the file inside a sandboxed preview.

## Settings

Saved in your browser's `localStorage`:

- Audio volume
- Aim assist laser
- Cloth tint (four options)
- Particles and screen shake (defaults to off if your OS requests reduced motion)
- Local matches played

## Project layout

Everything is in `index.html`, in this order:

1. **CSS**: menus, HUD, control deck, mobile portrait and landscape layouts
2. **Markup**: menu panels, end screen, scoreboard, canvas, control deck
3. **Script**: storage, audio (Web Audio, no files), themes, constants, `Ball` class, rules and turn logic, effects, rendering, input, game loop, menu wiring, Firebase room system, keyboard and effects handlers

Notable design points:

- **Fixed 60 Hz physics timestep** with 8 sub-steps, so behavior is identical on any refresh rate and deterministic across online clients.
- **Pointer Events** for all mouse, touch and pen input, with a single active pointer.
- **Audio** is synthesized with the Web Audio API. There are no external assets apart from the Orbitron font.

## Known limitations

- There is no "legal break" rule (four balls to a rail).
- Online rematch is not supported; return to the menu and create a new room.
- Potting the 8-ball on the break is a loss rather than a re-rack.

## Browser support

Current versions of Chrome, Edge, Firefox and Safari, on desktop and mobile. Requires Canvas 2D, Pointer Events, and Web Audio.
