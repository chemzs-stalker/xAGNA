# xagna architecture

The script layer for the xAGNA NPC animation overhaul. It adds the mod's motions to the game additively and replaces no vanilla file.

![stack](img/xagna-architecture.png)

## Statistics

The pack re-animates 2087 motions and adds 14 new action motions and 2 new cover motions.

## Files

`xagna_core.script` holds the data: the kept animation entries, the PDA-draw variant specs, the standing-posture deltas, and the added states. This is the file Chemzs edits to change motions; it carries no logic beyond returning those tables.
`xagna_mcm.script` registers the Behavior controls (the hello, approach-PDA, commander-PDA, and idle-PDA chances, and the wounded stand-up toggle) and the Logging controls, and exposes the chances to the rest of the mod.
`xagna_log.script` is the leveled logger and the animation sampler, writing to `xagna.log` and `xagna_animations.log`.
`zzz_xagna_overrides.script` applies the data at `on_game_start` and runs last so its edits win.

## How it works

The layer ships none of the table overrides, so vanilla `state_lib`, `state_mgr_animation_list` and `state_mgr_scenario` load whole, and a field write merges the mod's entries in. No override means it coexists with other NPC animation mods and survives a base-game update.

A standing calm NPC near the actor draws a PDA on the idle scheme's roll. A commander you walk up to greets you with a beckon and may then draw a PDA, each on its own independent roll, so the two never fire as one fixed pair. A recovering wounded NPC plays a get-up animation instead of snapping upright. Every chance is an MCM slider, and 0 disables that behavior.

The approach PDA cannot be gated in the animation table, because an into-sequence plays whole on state entry. So `zzz_xagna_overrides.script` keeps `talk_default` and `choose` vanilla, builds PDA-carrying variant states from the live vanilla entries, and a wrap of `state_mgr.set_state` rolls once per approach and redirects the NPC to the matching variant.

With the latest demonized exes the mod sets `npc_relaxed_idle_mode` so each relaxed NPC holds its own torso idle instead of the player's safemode pose. On an older exe the cvar is absent and the mod leaves it alone.

## OMF assets

The mod carries the actor OMF set.
`stalker_animation_new.omf` is a byte-identical copy of `stalker_animation.omf`, kept as a motion-reference alias.
Models whose `.ogf` motion_refs point at the `stalker_animation_new` name load their animations through it, so dropping it leaves those models without motions.
Chemzs reports custom NPC models and modpacks depend on it.
A search of the installed loose `.ogf` found no dependent.
That search did not cover models inside packed modpack archives or packs not installed here.
It stays despite the duplication.
