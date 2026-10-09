# xagna architecture

The script layer for the xAGNA NPC animation overhaul. It adds the mod's motions to the game additively and replaces no vanilla file.

![stack](img/xagna-architecture.png)

## Files

`xagna_core.script` holds the kept animation entries, the PDA into-overlays, the standing-posture deltas, and the leveled logger.
`xagna_mcm.script` registers the MCM Log level control and sets the logger threshold from it.
`zzz_xagna_overrides.script` applies the edits at `on_game_start` and runs last so its edits win.

## How it works

The layer ships none of the three table overrides, so vanilla `state_lib`, `state_mgr_animation_list` and `state_mgr_scenario` load whole.
`copy_table` merges the kept entries into the animation table and a field write sets the standing postures.
No override means it coexists with other NPC animation mods and survives a base-game update.
The PDA and medical clips use xAGNA-only motions the mod's own OMF carries.
The logger buffers lines and flushes them to its own `xagna.log` at the level the MCM Log level picks, WARN by default.

## OMF assets

The mod carries the actor OMF set.
`stalker_animation_new.omf` is a byte-identical copy of `stalker_animation.omf`, kept as a motion-reference alias.
Models whose `.ogf` motion_refs point at the `stalker_animation_new` name load their animations through it, so dropping it leaves those models without motions.
Chemzs reports custom NPC models and modpacks depend on it.
A search of the installed loose `.ogf` found no dependent.
That search did not cover models inside packed modpack archives or packs not installed here.
It stays despite the duplication.
