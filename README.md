# How To Create a Self-Evolving Autonomous Offensive Security Agent

**BSides Kraków 2026** · Jyrki Huhta — Cyber Security Specialist, IQM Quantum Computers Oyj

![The room — the skeptic, me, and the optimist](docs/preview.png)

Six months of building **Molly**, an autonomous offensive security agent: what genuinely
works, what genuinely breaks, and the war stories from in between.

This repository is the presentation. It is one HTML file — clone it, open it, present it.

## What the talk argues

Two people react to AI in offensive security, and the talk puts both of them on stage.

> *"I don't have time for AI nonsense. I'm too busy — and it's basically just malware."*
> — the skeptic
>
> *"AI can solve any problem. No issues at all."*
> — the optimist

Then it takes **both of them completely seriously** and walks down each of their avenues
in turn.

The optimist's avenue is real. An agent is *systematic* — it runs the same disciplined
loop on every endpoint and never gets bored into skipping a step. It is *reproducible* —
every finding ships with the exact request and response. It is *self-forging* — it writes
the capability it was missing and ships it into the next run. And it *scales* — one
confirmed technique propagates to every instance.

The skeptic's avenue is equally real: hallucination, genuine danger, the legal position,
and the quiet problem of spoon-feeding the answer to a system you then credit with
finding it.

Both are right, so the design has to hold both at once:

> **Deterministic execution** to hold the hallucination down. **A Rust wall** — scope,
> armed/passive, rate-limit, audit — to keep the danger contained. The design isn't
> "trust the model." It's: *let it reason, but verify everything.*

Before any of that is worth building, there are three questions. The talk states them
and deliberately does not answer them:

- **Does this already exist?** This isn't a novel idea, and you can do it without a harness.
- **Is it just awfully complex?** A pentest is a rigid pipeline, not a chat. Maybe forcing
  it into a conversational loop is the whole problem.
- **Will it be too expensive?** Every reasoning step is a model call. You can't use most
  subscription models. Server infrastructure is not free.

## The running order

95 stops, in eleven movements.

| Stops | |
|---|---|
| 1–6 | The room — the skeptic, the optimist, and the decision to take both seriously |
| 7–10 | The efficiency case: systematic, reproducible, self-forging, scale |
| 11–16 | The danger case: hallucination, danger, legal, spoon-feeding — then the synthesis |
| 17–20 | Three doubts worth having before you build anything like this |
| 21–26 | Five design ideas, climbing: standalone, brain vs. hands, Rust safety, a shooting range, the forge |
| 27–31 | A short history: Igor (2014), AlphaGo (2016), GenAI arrives, Burp AI (2025) |
| 32–41 | How it actually started — Wintermute, a VPS, OpenRouter, the Factory |
| 42–73 | Inside Molly: the brain, the Rust gate, the loop, the five verdicts, the Armory |
| 74–78 | The running system: dashboard, capabilities, armory, and the Factory on video |
| 79–81 | Six months by the numbers, every process that runs unattended, the commit log |
| 82–88 | War stories |
| 89–95 | Five lessons, the close, and a slow flight around the whole thing |

## Six months, by the numbers

| | |
|---|---|
| 185 | days |
| 3,881 | commits — **54% written by the Factory**, not by me |
| 4,339 | lines of Rust, against 74,746 of Python |
| 18 | loops running unattended on the VPS |

Measured 2026-09-23 across `molly`, `molly-armory`, `meshwiki` and `deck`.

The part that is allowed to say *no* is 5% of the code — small enough that one person can
actually read all of it. That is the whole architectural bet.

The talk is also deliberately skeptical of its own big numbers. Volume is the easiest
thing to produce and the hardest thing to trust; stops 83 and 87 are there to make that
point at the presenter's own expense.

## The war stories

- **42 days** — I ran the 104 XBOW benchmarks so I'd have something to show here. Molly
  didn't solve them all at once, so I asked how long a full run would take. That was the answer.
- **cf.errors.css** — Cloudflare's own 404 stylesheet, found on every subdomain and
  dutifully filed as a new HIGH severity finding **1,561 times**.
- **"Quick container count might be slow"** — a real warning line, kept because nobody
  wrote it to be funny. A shortcut that warns you it might not be one: the entire talk in six words.
- **Booking.com** — we read the programme rules, found automated scanning prohibited, and
  disqualified ourselves before sending a single request. The one where the safety worked.
- **Vinted** — they paused their programme while Molly was running unattended. Nothing in
  the loop checks whether a target is still open between runs. I found out when I got banned.
- **Teaching itself to stop looking** — two dismissals retired a whole vulnerability class
  on a target, and "the endpoint didn't respond" counted as one. An agent told to learn
  from its mistakes, learning the wrong thing from noise.
- **Looking for help** — everyone I pitched it to thought it sounded great, and everyone
  already had a job. The author list across four repos is one human and eleven machines.

## What six months taught me

1. AI is an over-confident friend.
2. Consider your threat model carefully.
3. Think about deployment, development process, and concrete goals.
4. Question your assumptions on a regular basis.
5. Don't listen too hard to what other people say. Follow your own path.

## Showing it

```sh
git clone git@github.com:jyrkihuhta/bsideskrakow2026.git
cd bsideskrakow2026
open index.html          # macOS — or just double-click it
```

That is the whole install. There is no build step, no package manager and no network
access at runtime: the deck is one self-contained HTML file plus its media.

If the opening film does not start, serve the folder instead of opening the file
directly — browsers apply stricter autoplay rules to `file://` URLs:

```sh
python3 -m http.server 8000
# then open http://localhost:8000
```

Go full screen before you begin (`F11`, or `⌃⌘F` on macOS). The deck is built for 16:9
and is checked at 1280×720 and 1920×1080.

## Driving it

| Key | |
|---|---|
| `→` `space` `PageDown` · or click | next stop |
| `←` `PageUp` | previous stop |
| `s` | speaker notes for the current stop (see the last section) |
| `t` | the closing grand tour — a slow flight around the whole diorama |
| `o` | overview |
| `?` or `h` | key help |
| `1`–`9` | jump to one of the first stops |

Any stop can be deep-linked by index, zero-based: `index.html#88` opens straight on
stop 89. Useful for rehearsing one section, and for finding your place again if the
browser is restarted mid-talk.

## How it is built

No framework, no build step, no runtime dependencies. One `index.html` of about 130 KB
that opens straight off the filesystem.

### There are no slides

`#stage` gets a CSS `perspective`, and the entire talk lives inside one `#world` element
with `transform-style: preserve-3d`:

```css
#stage { perspective: 1400px; perspective-origin: 50% 44%; }
#world { position: absolute; top: 50%; left: 50%; transform-style: preserve-3d; }
```

The room, both avenues, the climbing column of ideas, the architecture board and the
fleet diagram are all anchored at fixed coordinates inside that one space, all of the
time. A "stop" is not a slide — it is a camera pose looking at part of it.

### The camera is one CSS transform

A stop is seven numbers:

```js
{px:-1500, py:2760, pz:-1400, yaw:0, pitch:6, dz:400, title:"Looking for help", ms:1700}
```

and moving the camera is one string assignment:

```js
world.style.transform =
  `translateZ(${s.dz}px) rotateX(${s.pitch}deg) rotateY(${s.yaw}deg)` +
  ` translate3d(${-s.px}px,${-s.py}px,${-s.pz}px)`;
```

Read it right to left. The negated `px/py/pz` slide the world until the thing you want to
look at sits at the origin — there is no camera object, you move the world past a fixed
viewer. Then `rotateY` and `rotateX` swing the viewpoint around it, and `translateZ`
dollies in.

That is the whole animation system. `#world` carries a CSS transition on `transform` and
each stop's `ms` sets its duration, so the browser interpolates the move. There is no
animation loop and no matrix maths in the file — the single `requestAnimationFrame` in
there is watching the opening clip's playhead so the title card can come up a beat before
it ends. It is also why the deck stays smooth with every one of the 95 stops' worth of
content live in the DOM the entire time: one property is animating, not ninety-five
elements.

### Everything that holds text counter-rotates

Text lying flat in a 3D space is unreadable the moment the camera is off-axis, so every
panel sits in a `.bb` billboard that undoes the camera's rotation:

```js
const bbT = `rotateY(${-s.yaw}deg) rotateX(${-s.pitch}deg)`;
```

Perspective still applies — further away is still smaller — but the face of every card
stays square to the audience.

`.bb` has `transform-origin: 0 0`, which matters more than it looks. With the default
`50% 50%` a card rotates about its own centre and drifts off the point it is anchored
to: invisible at ±15° of yaw, half a screen wide at −152°.

### Reveals are windows, not steps

Panels declare the stops they exist for:

```html
<div class="anchor rv" data-show="6-15,93-94">
```

`20-` means from stop 20 to the end; a bare `18` means that stop only. The windows are
parsed once and cached on the element, and the test is a plain range check, so what is on
screen is a pure function of the stop index. That is what makes `index.html#57` work —
jump anywhere and the world is already in the right state, with no steps to replay.

### The figures are jointed SVG

The stick figures are nested `<g class="j">` joints using `transform-box: view-box` with
`transform-origin` given in viewBox units, so each joint rotates about the anatomically
correct point:

```css
.thighL, .thighR, .upper { transform-origin: 160px 148px; }
.shinL { transform-origin: 146px 180px; }
```

Sitting down at the desk is ten rotations and one transition.

### Four things that cost me an evening

- **An SVG filter deletes axis-aligned lines.** A filter with the default
  `filterUnits="objectBoundingBox"` has an empty region on any zero-width or zero-height
  box, so a hand-drawn wobble on the architecture diagram silently erased every horizontal
  and vertical arrow — 14 of 29, including a whole five-phase row and the seven-stage
  pipeline. The wobble is baked into the path geometry now. Geometry always renders.
- **`transform` inside `@keyframes` replaces an element's transform,** it does not compose
  with it. A floating animation on a card centred with `translate(-50%,-50%)` threw it a
  quarter of a screen off the instant the animation started.
- **`getBoundingClientRect` is not usable for layout maths inside `preserve-3d`** — every
  measurement comes back with the current camera baked into it. Positions here are
  authored in world coordinates instead.
- **`<video>` inside a `preserve-3d` subtree flickers.** It is rasterised into whatever
  layer it lands in, so every camera move and every `backdrop-filter` in the scene can
  force its decoded frames to be re-composited. Pin it to its own compositor layer with
  `translate3d(0,0,0)` and `will-change: transform`, and stop the ancestor clip from
  defeating the promotion.

If you want to screenshot it: headless Chrome needs `--headless=new`, and `--disable-gpu`
switches off the 3D rendering entirely, so the whole thing comes out as garbage.

## What is in here

```
index.html          the presentation — 95 stops, ~130 KB, self-contained
media/              the four films and every still the deck references
docs/preview.png    the image at the top of this README

speaker-notes.js    optional, git-ignored — the presenter's notes, one per stop
```

## Credits and notes

- The AI-pentesting radar on stop 18 is **Wavestone's** "Radar of AI-Powered Pentesting
  Solutions", used as published and credited on the slide.
- The opening and interlude films were generated with MiniMax / Hailuo AI.
- **The speaker notes are not in this repository.** They live in a `speaker-notes.js`
  that sits next to `index.html` and is git-ignored. If the file is there, `s` shows the
  note for the current stop; if it is not, everything else behaves exactly the same.
- The films were re-encoded for distribution (the masters were 3528×2344 at 58 Mbps);
  they are visually identical at projector resolution and the repo is 20 MB instead of 93 MB.

© 2026 Jyrki Huhta. The talk, its text and its diagrams are mine; third-party material
is credited above.
