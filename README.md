# G.R.O.O.T.
**Guarded Resource Orchestration & Operation Terminal**

A system that safely coordinates access to shared resources — exactly what the
Producer–Consumer problem is about. This project is an interactive, stage-by-stage
learning game for an Operating Systems mini-project.

> Two workers. One shared buffer. Keep the system stable.

---

## How to run

1. Unzip the folder.
2. Open `index.html` in a modern web browser (Chrome, Edge, Firefox or Safari).
3. Click **Start the Mission**.

No server, install or build step is needed. Fonts load from Google Fonts when you
are online; offline, the page falls back to your system fonts and still works.

---

## Folder structure

```
groot/
├── index.html            Page structure: the 12 stages + inline SVG icons
├── README.md
├── css/
│   ├── base.css          Design tokens, layout, top/bottom bars, stage system
│   ├── components.css    Shared pieces: buffer slots, IN/OUT pointers, status bubbles
│   ├── stages.css        Each stage: hero, cast, learn, door, scenarios, results, logic…
│   ├── control-room.css  The main simulation dashboard
│   └── ambient.css       Floating background layer + reduced-motion rules
└── js/                   Loaded in this order:
    ├── core.js           Shared helpers + the GROOT event bus
    ├── ambient.js        Interactive floating background (canvas)
    ├── learn.js          "Learn the Rules" guided mini-simulation
    ├── door.js           "One Door, Two Workers" mutex story
    ├── simulation.js     Producer–Consumer engine + Control Room UI
    └── stages.js         Stage-by-stage reveal controller (loads last)
```

The scripts are plain scripts (not ES modules) and share one namespace,
`window.GROOT`, so the project runs straight from the file system.

---

## The journey (12 stages)

| Chapter | Stages |
|---|---|
| Mission | Mission briefing |
| Cast | Meet the Cast · The Story (want / need / conflict) |
| Learn | Learn the Rules (4 gated steps) · One Door, Two Workers · Different Jobs |
| Play | Control Room · Scenario Challenges |
| Results | Live shift report + session badges |
| Logic | Three variables · Pseudocode · Debrief + credits |

**Navigating:** Next / Back buttons, the chapter bar, or the ← / → keys.
Some stages wait for you (for example, create a package before the next rule
unlocks). Every stage has a direct link, e.g. `index.html#/play`, which is handy
when presenting.

**Floating background:** packages, buffer slots, semaphore tokens, locks and
code fragments drift behind the content. Move the pointer to part them, click
empty space for a ripple, and watch them react to the game (new packages float
up, waits glow amber, a race condition flashes red). The ✦ button in the top bar
turns it off. It stays still if your system asks for reduced motion.

---

## The synchronization model

- Bounded circular buffer with **5 slots**
- `empty = 5` (free slots), `full = 0` (ready items), `mutex` unlocked

```
Producer                     Consumer
produce(item)                wait(full)
wait(empty)                  wait(mutex)
wait(mutex)                  remove item from buffer
add item to buffer           signal(mutex)
signal(mutex)                signal(empty)
signal(full)                 consume(item)
```

A worker that calls `wait()` on a semaphore at 0 sleeps until the matching
`signal()` wakes it. The Control Room checks after every event that
`empty + full + in-transit = 5` (exactly `empty + full = 5` whenever no worker is
between a `wait()` and its matching `signal()`), and shows a developer warning if
that is ever broken. The unsafe "mutex experiment" runs on a staged copy, so it
never corrupts the real simulation.

---

## Editing

- **Team names:** open `index.html`, search for `EDIT`, replace the placeholders.
- **Speeds:** `SPEED_MS` near the top of `js/simulation.js`.
- **Background density / feel:** the "Tunables" block in `js/ambient.js`.

---

**Project:** Producer–Consumer Problem · **Course:** Operating Systems
Built as an interactive learning simulation.
