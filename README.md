# Moonputt

Moonputt is a mini-golf game set in space. You putt a ball across a star chart
toward the cup. Planets and black holes pull on the ball, so most good shots
curve. A round is 9 holes, and every course is randomly generated.

The whole game is one file, `moonputt.html`. It has no build step, no
dependencies to install and no server. Open it in a browser and play.


## Running it

1. Double-click `moonputt.html`, or drag it into a browser window.
2. That's it.

It works in current versions of Chrome, Edge, Firefox and Safari, on desktop and
on phones. When you're online it loads two fonts from Google Fonts: Bricolage
Grotesque for headings and JetBrains Mono for numbers. Offline, it falls back to
system fonts and plays the same.


## How to play

**Aim and putt with a mouse or finger.** Press anywhere on the playfield, drag
backwards like a slingshot and let go. The ball goes in the opposite direction
of your drag, and a longer drag gives more power. While you aim, the game shows:

- a ring around the ball that fills up with power (green for soft, yellow for
  medium, red for hard), plus the power as a percentage
- a dotted line showing the first 0.55 seconds of the ball's path, already
  bent by gravity. A red dot at the end means the ball would hit a planet or
  fall into a black hole.

**Keyboard controls**

| Key | What it does |
| --- | --- |
| Left / Right | Turn the aim |
| Up / Down | More or less power |
| Shift + arrows | Fine adjustment |
| Space or Enter | First press shows the aim at 40% power, second press putts |
| Escape | Cancel the aim |

**Sinking the ball.** The ball drops when it reaches the cup moving slowly.
If it arrives too fast it rolls over the cup, and the game says "Lipped out".

**When a putt ends.** A putt is over when the ball:

- **comes to rest.** It can stop on a planet's surface, and you putt again from
  there.
- **drops into the cup.** The hole is finished.
- **leaves the map.** "Lost in space": one penalty stroke, and the ball goes
  back to where you last putted from.
- **falls into a black hole.** One penalty stroke, back to your last spot.
- **is still moving after 15 seconds.** "Stuck in orbit": one penalty stroke,
  back to your last spot.

Penalties work like golf's stroke-and-distance rule: the putt counts, you add
one penalty stroke, and you play again from the same place.

**Stroke limit.** A hole costs at most 10 strokes. If you reach 10 without
sinking the ball, the hole is marked "Picked up" and scored as 10.


## Scoring

Each hole has a par, the number of strokes a good player should need. Your
score on a hole is named the way it is in golf:

| Strokes compared with par | Name |
| --- | --- |
| 1 stroke total | Hole in one |
| 3 under | Albatross |
| 2 under | Eagle |
| 1 under | Birdie |
| equal | Par |
| 1 over | Bogey |
| 2 over | Double bogey |
| 3 over | Triple bogey |

The top bar shows your running total against par: `E` for level ("even"),
`−2` for two under, `+3` for three over.

After each hole you see a scorecard marked the way paper golf cards are:

- circle: one under par
- double circle: two or more under
- square: one over par
- double square: two or more over

After hole 9 the final card shows your total. Your best round (compared with
par) is remembered in this browser.


## What's on the playfield

- **Tee:** the dashed circle where the ball starts each hole.
- **Cup and flag:** the green glowing target. The number on the flag is the
  hole number.
- **Planets:** solid. The ball bounces off them or rolls on them. Each one is
  labelled with a golf-themed name (Mulligan, Divot, Dogleg and so on) and its
  surface gravity in g. Planets range from 0.8 g to 1.4 g.
- **Gravity rings:** the faint dashed circles around each planet and black
  hole. They mark where the pull drops to three strengths. The farther out the
  rings sit, the stronger that body pulls.
- **Black holes:** these appear from hole 5 on. They are small but pull very
  hard. Pass close to one to swing your shot around it. Touch the centre and
  the ball is gone.
- **Star-chart grid:** decoration in the style of an astronomer's chart, with
  right ascension in hours along one edge and declination in degrees along the
  other.
- **To pin:** the distance from the ball to the cup, in AU (astronomical
  units). In this game 1 AU is 500 map units.

On a phone held upright, the playfield is turned 90° so the long side of the
map runs up the screen. The text stays upright.


## Courses

Each course has a number from 1000 to 9999, shown next to the title. The same
number always builds the same 9 holes, so a course can be replayed exactly.
Use "Replay course" on the final scorecard to play it again, or "New course" in
the top bar for a random one. If you press "New course" mid-round, it asks you
to press again to confirm.

The holes get harder as you go:

| Holes | Planets | Black holes |
| --- | --- | --- |
| 1 | 1 | 0 |
| 2–3 | 2 | 0 |
| 4–5 | 3 | 0 on hole 4, 1 on hole 5 |
| 6–7 | 4 | 1 |
| 8–9 | 5 | 2 |

From hole 3 on, each hole has a 40% chance of one extra planet. The first
planet on every hole is placed roughly between the tee and the cup, so a
straight putt never works.

Par depends on how crowded a hole is. Planets count 1 each and black holes
count 2 each. A total of 1 makes par 2, a total of 2 to 4 makes par 3, and
anything more makes par 4.


## How the code is organised

Everything is inside `moonputt.html`, in this order:

1. **`<style>`:** colours, fonts and layout. All colours are named at the top
   (for example `--saffron` for the ball and `--flag` for the flag). The game
   reads these names, so changing a colour there changes it in the playfield
   too.
2. **HTML:** the top bar (title, hole, par, strokes, round score, distance to
   pin, sound and new-course buttons), the playfield `<canvas>`, the hint line,
   the message bubble and the scorecard panel.
3. **First `<script>`, the game rules (`MP`):** physics, course generation and
   the rules for when a putt ends. It does not touch the page, so you can run
   it on its own to test or tune levels.
   - `step()` moves the ball one tick: it adds the gravity of every body, then
     handles bounces and rolling on planets.
   - `judge()` decides whether the putt is over (sunk, lipped out, at rest,
     lost, swallowed or stuck in orbit).
   - `layout()` places the tee, cup, planets and black holes for one hole.
   - `genLevel(seed, hole)` builds a hole from the course number and works out
     its par.
4. **Second `<script>`, the game itself:** drawing, input, sound, the score
   bar, the scorecards and moving from hole to hole.
   - `resize()` fits the 1600 × 1000 map to the screen and turns it on
     portrait screens.
   - `update()` runs the physics in fixed steps of 1/240 of a second, so the
     ball flies the same way at any frame rate.
   - `draw()` paints each frame: background stars, chart grid, gravity rings,
     planets, black holes, cup, ball trail, aim guide, ball and confetti.
   - `shoot()`, `settle()` and `finishHole()` handle a putt from start to
     finish.
   - All sounds are made in code with the Web Audio API. There are no sound
     files.


## Settings you can change

These numbers are at the top of the first `<script>`:

| Name | Value | What it controls |
| --- | --- | --- |
| `HOLES` | 9 | Holes per round |
| `BALL_R` | 9 | Ball radius |
| `HOLE_R` | 20 | Cup radius. Bigger is easier. |
| `CAPTURE_V` | 560 | Fastest speed at which the ball still drops. Higher is easier. |
| `MAX_V` | 1150 | Ball speed at 100% power |
| `MAX_STROKES` | 10 | Strokes before a hole is picked up |
| `MAX_FLIGHT` | 15 | Seconds before a moving ball counts as stuck in orbit |
| `OOB` | 180 | How far past the map edge the ball can go before it's lost |

Distances are in map units (the map is 1600 × 1000) and speeds are in map
units per second.

In the second `<script>`, `PREVIEW_T` (0.55) sets how many seconds of the path
the aim guide shows. Raise it to make the game easier.

In `layout()` you can change planet sizes, gravity strength (the `R(800, 1400)`
range), black hole pull (`gm: 4.2e6`) and how many of each appear per hole.


## Saved data

The game stores two values in your browser's local storage, and nothing else:

- `moonputt-muted`: whether sound is off
- `moonputt-best`: your best round compared with par

Nothing is sent anywhere. If local storage is blocked, as in some private
windows, the game still works but won't remember those two things.
