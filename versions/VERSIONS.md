# Keep Driving cheat table - every iteration

**Altxxr0** - https://github.com/altxxr0/KeepDrivingCE

Every .CT in this folder carries that credit in two places: a top row in the
cheat table that opens the repo when you tick it, and a credit block inside the
master script's header comment. v11 also shows it along the bottom of the
Windowed UI and the hitchhiker editor.

Each file is a complete, loadable .CT. They are cumulative: v11 contains
everything below it. Load **v11_layout_fix_CURRENT.CT** unless you want to see
how it got there.

Verified: every file in this folder parses and reports the entry count shown.

| file | size | entries | built | what changed |
|---|---|---|---|---|
| `v01_original_as_received.CT` | 58 KB | 114 | 20:07 | The table as first handed to me. Does NOT load: a { } pair inside the header comment closes it early, so the whole ENABLE section fails to compile. |
| `v02_compile_fix.CT` | 58 KB | 114 | 20:53 | Two characters changed - the header's { char* name ; int32 variable_id } became ( ... ) - which is all it took to make the master script compile and run. |
| `v03_tested_split.CT` | 61 KB | 113 | 22:37 | After testing every row in a live game: the 29 rows that never populate moved to a labelled BROKEN group, the measured-useless 2x and 4x speed scripts removed, working rows annotated with what they actually read. |
| `v04_auto_find_pointers.CT` | 71 KB | 114 | 23:01 | Adds AUTO-FIND POINTERS: walks the game's variable hash map and fills rows in immediately, with no gameplay. Revived gas_drain, gas_cost_mod and energy_cost_mod - situational went 11 of 40 to 14 of 40. |
| `v05_compact_mode.CT` | 75 KB | 115 | 07:37 | Adds COMPACT MODE (hides Cheat Engine's memory scanner) and stops the auto-finder forcing the Lua console open on every enable. |
| `v06_hitchhiker_editor.CT` | 85 KB | 116 | 08:22 | Adds HITCHHIKER EDITOR - a Lua window with a dropdown per seat that edits the save's [hitchhikers] section, so you can add, change or remove passengers. |
| `v07_hitchhiker_guards.CT` | 87 KB | 116 | 08:48 | The editor now refuses to write while Keep Driving is running (the running game overwrites the save on every autosave) and clamps levels to the game's own max of 3 instead of accepting nonsense like 100. |
| `v08_editor_instructions.CT` | 89 KB | 116 | 08:56 | Puts the numbered steps inside the editor window, plus a live readout that turns red and greys out the write buttons whenever the game is running. |
| `v09_two_ui_modes.CT` | 95 KB | 117 | 09:00 | Replaces the single COMPACT MODE toggle with two radio-style entries: MODE: Clean UI and MODE: Advanced. |
| `v10_windowed_trainer.CT` | 112 KB | 117 | 09:10 | Reworks those modes properly. MODE: Windowed UI now opens a trainer window holding the cheats themselves - script toggles, game speed, and the 12 core values each with Set and Freeze - all driving the real table rows. |
| `v11_layout_fix_CURRENT.CT` | 117 KB | 117 | 09:30 | CURRENT. Fixes the vanishing table: compact mode no longer shrinks the window (the fixed 524px scanner was squeezing the list to 0px), Advanced grows the window if the table would be too short, and the master entry's moAlwaysHideChildren flag is cleared so every option shows on load. |

## The two that are not mine

`_cheattables\` also holds tables other people made for older versions of the
game (1.0.0.1a and 1.0.1.2c). They are untouched and unrelated to this work.

## Where these live

The copies beside the game are the durable ones. The Downloads copy of the table
vanished once during this session - the whole folder is fine, but the .CT files
in it disappeared - so treat the game-folder copy as the real one.
