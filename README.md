# KeepDrivingCE

A Cheat Engine table for **Keep Driving v1.3.1.1d**, with a windowed trainer UI, an
automatic pointer finder, and a hitchhiker editor.

**[Download the latest release](https://github.com/altxxr0/KeepDrivingCE/releases/latest)**

Every row in this table was tested against a running game. Where something does not
work, it says so rather than shipping a dead checkbox — see
[What does not work](#what-does-not-work).

---

## Requirements

| | |
|---|---|
| Game | Keep Driving **v1.3.1.1d** (`KeepDriving.exe`, 30,260,736 bytes) |
| Cheat Engine | 7.x — developed and tested on **7.7** |

The table finds its own addresses by scanning code patterns, so it survives game
restarts. It is tied to this build of the game; other versions will not match.

## Getting started

1. Start Keep Driving, then open Cheat Engine and attach it to `KeepDriving.exe`.
2. Load `KeepDriving1.3.1.1d.CT`.
3. Tick **Keep Driving v1.3.1.1d — ACTIVATE THIS FIRST**. Nothing else works until
   this is on.
4. Tick **AUTO-FIND POINTERS**. Rows fill in immediately.
5. Optionally tick **MODE: Windowed UI** for the trainer window.

---

## Features

### Windowed UI, or the plain table

Two entries near the top choose how you drive the cheats. They behave like radio
buttons — tick one and the other unticks. The cheat table stays open in both.

- **MODE: Windowed UI** — a trainer window holding the cheats themselves: the script
  toggles, game speed, and the twelve core values each with a box, a **Set** button
  and a **Freeze** tick. The memory scanner is hidden so the table sits behind it.
- **MODE: Advanced UI** — no trainer window, just the normal cheat table with the
  scanner, for working the address list directly.

Nothing is duplicated: every control in the window drives the matching cheat table
entry, so **Freeze** is Cheat Engine's own freeze. The window refreshes once a second
and will not overwrite a box you are part way through typing into.

### Automatic pointer finder

The main script can only fill a row once the game happens to *read* that variable,
which is why so many rows used to sit at `0`. **AUTO-FIND POINTERS** does not wait.

It captures the stats object, walks the variable hash map inside it, and writes each
variable's live address straight into the slot the main script allocated:

```
object + 0x48   -> hash map          map + 0x08 -> bucket mask
map + 0x10      -> bucket array      16 bytes per entry
entry + 0x00    -> RValue*           entry + 0x0C -> (varid + 1), bit 31 clear
RValue: value at +0, kind at +12     (0 = number, 2 = array)
```

It resolves **23 of the 52 variables the moment a save loads**, with no gameplay at
all, and re-runs every 2 seconds so rows re-aim by themselves after a load or a
restart.

### Hitchhiker editor

Add, change or remove passengers. A window with one dropdown per seat listing all 15
hitchhikers, a level box each, and Write save / Re-read / Empty car.

The roster, in id order:

| | | | |
|---|---|---|---|
| 0 suit | 1 hippie_girl | 2 mechanic | 3 hurricane |
| 4 punk | 5 dog | 6 kid | 7 songwriter |
| 8 stranger | 9 idiot | 10 convict | 11 sleeper |
| 12 hiker | 13 bride | 14 couple | |

It edits the **save**, not live memory, and that is deliberate: the game owns a
`scr_load_hhs` routine that rebuilds each passenger's name, portrait, skills and perks
from those fields when a save loads. Poking the in-memory `slot_hh` array would set
the id and leave all of that derived state stale.

**The order matters:** close the game → write → start the game → Continue. While the
game is running it overwrites the save on every autosave, so the editor refuses to
write and tells you why. Every write leaves a `.bak` beside the save.

### Core values

All twelve verified working — they aim, read, write and freeze, and the written
values were confirmed in `savedata.ini` after a save:

Money · Gas · Gas tank capacity · Energy · Energy max · Energy max when rested ·
Car health · Car health max · Skill points · Day · Hour · Minute

Freezing Clock-Hour and Calendar-Day stops time and quest deadlines — verified: the
dash clock stops while fuel keeps draining.

### Game speed

`0.25x`, `0.5x`, `Normal 1x`. See the limitation below before expecting more.

---

## What does not work

Measured, not assumed. This is the part most tables leave out.

**Speeding the game up does nothing.** Cheat Engine's speedhack is set correctly, but
the game will not run faster than real time. Measured against fuel burn per wall-clock
second on the same road segment:

| | measured | |
|---|---|---|
| `0.25x` | 0.30× normal | works |
| `4x` | 0.99× and 1.00×, twice | **no effect** |

The 2x and 4x entries were removed rather than left in as decoration.

**26 of the 40 situational rows never populate.** They are kept in a labelled
`=== BROKEN ===` group rather than hidden, each annotated with where it was tested.
23 of them were tested with their own screen open and the value visibly on display —
a shop tooltip reading 11.25, a garage quoting 55, a glovebox showing 5/5 — and the
row still read 0. Two of them hold arrays rather than numbers, so a Double row could
never have worked.

This is not a hook problem. An instrumented log of every variable id the game asks for
showed **37 of the 52 ids are never requested at all**, so those names are not what the
game uses for those values. A second capture point built against the slot-lookup return
value did not help either: 6.8 million lookups went past it and only gas, energy and
car health ever matched.

**Encounter → Threat is intermittent.** It aimed in 3 of 5 road events. When it does
aim, writing to it works — setting 1 collapsed a six-icon event to one, setting 0 ended
it. After the event the captured pointer goes stale and reads garbage, so unfreeze it
when the event ends.

**A few rows were never reachable** during testing: the four Work rows and the two
Exploration rows, because no work site or explore site came up.

---

## Versions

Every iteration has its own [release](https://github.com/altxxr0/KeepDrivingCE/releases)
with a download, and is also in [`versions/`](versions/) with a changelog in
[`versions/VERSIONS.md`](versions/VERSIONS.md). They are cumulative — v11 contains
everything below it.

| | |
|---|---|
| [v01](https://github.com/altxxr0/KeepDrivingCE/releases/tag/v01) | the table as originally received — **does not load** |
| [v02](https://github.com/altxxr0/KeepDrivingCE/releases/tag/v02) | the compile fix |
| [v03](https://github.com/altxxr0/KeepDrivingCE/releases/tag/v03) | tested, working/broken split, dead speed scripts removed |
| [v04](https://github.com/altxxr0/KeepDrivingCE/releases/tag/v04) | auto-find pointers |
| [v05](https://github.com/altxxr0/KeepDrivingCE/releases/tag/v05) | compact mode |
| [v06](https://github.com/altxxr0/KeepDrivingCE/releases/tag/v06) | hitchhiker editor |
| [v07](https://github.com/altxxr0/KeepDrivingCE/releases/tag/v07) | hitchhiker editor guards |
| [v08](https://github.com/altxxr0/KeepDrivingCE/releases/tag/v08) | editor instructions + live indicator |
| [v09](https://github.com/altxxr0/KeepDrivingCE/releases/tag/v09) | two UI modes |
| [v10](https://github.com/altxxr0/KeepDrivingCE/releases/tag/v10) | windowed trainer |
| **[v11](https://github.com/altxxr0/KeepDrivingCE/releases/tag/v11)** | **current** — layout fix |

### Notable fixes along the way

- **v02** — the original would not compile at all. A `{ }` pair inside the header
  comment closed it early, because Cheat Engine's block comments do not nest, so the
  whole `[ENABLE]` section was parsed as code. Two characters fixed it.
- **v04** — the auto-finder revived three rows the hook could never reach:
  `gas_drain`, `gas_cost_mod` and `energy_cost_mod`. Gas drain was verified end to
  end: setting it to `0` took fuel burn from 0.0349 gas/s to exactly 0.0000 while
  driving.
- **v07** — the editor used to write the save while the game was running, which looks
  like it did nothing, because the running game overwrites the file on its next
  autosave. It now refuses, and clamps levels to the game's own maximum of 3.
- **v11** — the cheat table could vanish on load. Compact mode had shrunk the Cheat
  Engine window, and the scanner panel is a fixed 524 px tall, so the address list was
  squeezed to **zero height**. On top of that the master entry carried
  `moAlwaysHideChildren`, hiding every option beneath it. Both fixed.

---

## Credits

**Altxxr0** — https://github.com/altxxr0/KeepDrivingCE

Every `.CT` here carries that credit as a top row that opens this repo when ticked, and
as a block inside the main script's header comment.

Licensed under the GPL v2 — see [LICENSE](LICENSE).
