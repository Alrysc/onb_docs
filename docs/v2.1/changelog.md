# Changelog
* Folder and pack can be sorted using the Pause button (default Enter) 
* Folders can be copied with `CTRL+C` while editing
* Sea, Metal, and Sand TileStates added
    * Sea boosts damage of Aqua cards used on them, doubles Elec damage taken while on one, and roots Entities that step on them
    * Metal cannot be cracked or broken
    * Sand roots Entities that step on them and doubles Wind damage taken while on one
* Charge times are recalculated even without flinching
    * `Player.charge_time_func` and `PlayerForm.charge_time_func` can be used reliably as a result
* Changed parameters and names of charge time functions, see [Breaking Change: Charge Time](./breaking_changes.md#charge-time)
* Wood element Entities will heal over time while standing on a Grass Tile
* Fire element Entities will not take damage from Lava
* Elec damage is no longer doubled on Ice
* Grass Tiles are now removed by Fire, even if damage was not dealt to any Entities
* Holy Panel damage rounds up
* Bonus damage from weakness no longer combines multipicatively with other engine-handled sources of bonus damage
    * E.g. a Fire attack on a Wood Entity standing on Grass used to do x4 damage, but now correctly does +200% damage (x3)
        * This is accurate to later games in the series
* Movement no longer acts as if endlag is 1 frame when it's 0 frames
* Player animation for movement is 1f faster, which matches the actual move action
    * This should fix cases where the move animation started looping during a time freeze action
* Drag lasts the correct amount of time now
* `Hit.Bubble` no longer crashes, applies Bubble for 150 frames by default
* Pressing inputs during Stun, Freeze, and Bubble will cause them to end more quickly
* Status interactions with each other are overall more accurate
    * Some may cancel others, and will do so correctly
* Form changing will cancel various statuses
* Flinching can no longer be cancelled by using a card
* TFC cannot be performed until 10 frames into time freeze
* Charge is no longer used up when performing TFC
* Anger/FullSync now only consumed by boostable cards
* Time freeze actions show damage and modifiers
* Health turns red at low HP
* Mugshots no longer bounce
* Freedom battles always send data to the server, and turns can no longer end while the last enemy is dying
* Freedom battles read input during time freeze
* Card long descriptions are prioritized over short descriptions in battle
* Time Freeze actions can be marked uncounterable
* Time Freeze actions now call action_end_func
* `Field.find_nearest_characters` returns an ordered table
* `Battle.AlertSymbol.new` is available
* Access to `Left_Shoulder` and `Right_Shoulder` inputs
* `Hit.NoCounter` hitflag, prevents attack from countering
* `Hit.Confuse` hitflag, adds Confuse for 110 frames
* All Entities can use `battle_start_func` and `battle_end_func`
* Access to `Character.can_attack`
* Access to `Entity.set_counter_frame_range`
* Access to Entity. `is_stunned`, `is_frozen`, `is_rooted`, `is_blind`, `is_confused`, `is_bubbled`, and `is_dragged`
* Access to `Player.is_charging`
* Default turn duration is now correctly 512 frames (~8.5 seconds, down from 10 seconds)
* Access to `Battle.get_turn_count`, which returns the current turn number. 0.0 initially, then increases by 1.0 every Battle Start banner
* Access to many functions related to Custom Gauge manipulation 
    * See [Recommended Upgrades: CustGauge Manipulation](./recommended_upgrades.md#custgauge-manipulation)
* Form changing resets emotions
* New HitProps constructor and builder pattern
    * See [Recommended Mod Upgrades: New HitProps Constructor](./recommended_upgrades.md#new-hitprops-constructor)
* HitProps builder pattern allows custom durations for Stun, Freeze, Bubble, Blind, Root, Confuse, and Flash
* HitProps can have a second element, `element2`
* Mob can skip rewards screen with `Mob:no_rewards()`
* Obstacles are now deleted immediately when their health reaches 0, even during time freeze
    * This may break a few mods. See [Breaking Changes: Obstacles Delete At 0 Health](./breaking_changes.md#obstacles-delete-at-0-health)
* Entity can register new `Battle.UIComponent`, which acts as a SpriteNode
    * Add child nodes to draw to the screen in battle, anchored at 0,0 screen coordinates
    * Position is absolute and does not get flipped by perspective flip
* `Esc` behavior is now the Panic sequence, which is activated by pressing `Esc` three times
    * This means exiting battle is now three `Esc` presses instead of one
* Panic can be used to disconnect from a server
* The engine can now play SFX when dealing damage
    * See [Recommended Upgrades: Let Engine Handle Hit Sounds](./recommended_upgrades.md#let-engine-handle-hit-sounds)
* DefenseFrameStateJudge can suggest whether or not to spawn hit GFX
    * See [Recommended Upgrades: Suggest GFX Spawn](./recommended_upgrades.md#suggest-gfx-spawn)
* Hitbox/Spell `attack_func` and `collision_func` now receive an optional last parameter DefenseFrameStateJudge object, mainly for use with the above two features
* Obstacles can now be affected by Lava, but you must remove their FloatShoes, which are added by default
* Blocks are now sorted when loaded, which fixes a desync where the order of blocks could be wrong when sent to a remote player
* New `Hit.Cancel` flag, which is for internal use
    * Adding to flags does nothing, but it can be reacted to or removed in callbacks
    * Interally added during damage steps when certain flags are present
* FullSynchro is no longer removed accidentally in places it shouldn't be
* `ActionOrder.Immediate` now causes the last action added to go first, so queuing multiple actions with `ActionOrder.Immediate` will have reversed order now
* PvP battles can end in draws
* Obstacles can no longer be Bubbled
* Player movement animation has 1 frame shaved off the start
* Blind animation displays more correctly
* Fixed crash with `Field.find_nearest_characters` when pivot `Character` was not spawned
* ActionQueue now rejects a CardActions if it is already queued, as is proper
* AirShoes is no longer checked in places where only FloatShoes should have been checked instead
* Lava does not turn HP red
* Convenient `HitProps.has_flags` and `HitProps.has_element` for Lua, `has_element` checks both primary and secondary `Element`s at once
* New `AudioPriority.Immediate`, SFX replays if already playing and told to play with Immediate priority
* Certain statuses will pause a voluntary movement instead of allowing it to continue
* `Hit.PierceGround` flag, used to read from scripted content to do things, typically for semi-invulnerability that should be beat by attacks that hit or travel along the ground
* Added Lua bindings for AudioTypes that were missing bindings
* Music plays more consistently in SelectMobScene (it only played OnResume before, but now plays OnEnter as well)
* PlayerCustScene plays music
* Setting `Player.charge_time_func` or `PlayerForm.charge_time_func` (if a form is active) will also recalculate charge time
* Setting skip_time_freeze_intro true on CardProperties has the following changes:
    * No longer dims the screen
    * No longer displays names
    * Can no longer be used to counter a card in time freeze
    * Can no longer be countered by other cards in time freeze
* `DefenseFrameStateJudge.block_impact` and `.is_impact_blocked` are deprecated. They never did anything. Do not use them. Remove them if you have.
* `DefenseFrameStateJudge.block_collision`, prevents hitbox from colliding. Does nothing outside of a `DefensePriority.Always` `DefenseRule`.
* `DefenseFrameStateJudge.is_collision_blocked`, returns true if collision was previously blocked by a `DefenseRule`
* `Engine.get_viewport_size()`, returns vector containing width/height of game logic (480.0 X and 320.0 Y)
* `Field:tile_from_pixels(px, py)`, returns a Tile on the Field found at pixel location px, py
    * Returns `nil` if no Tile is at that location
* `Player.get_flinch_count`, returns number of times the Player has been "flinched"
    * Increments each time the player inflicted with Flinch, Stun, Freeze, Bubble, or Drag status
* Flinch duration adjusted slightly
* Fixed `Battle.BusterAction` attachment animation
* Reading email will let the server know you read the email
* Being stunned or frozen now plays the first flinch animation frame like Bubble does
* `PlayerForm.on_activate_func` and `.on_deactivate_func` now run after any pending movements finish
* Reward Rank penalties around getting hit are more accurate
* Fixed a crash that could happen when removing Entities on the same frame that they are spawned
* Fixed a crash that could happen with certain inputs to `math.random`
    * Cases that used to crash now provide a descriptive error message
* Hit.Confuse correctly adds the Confuse status when using default duration
* Music properly resumes when resuming certain scenes, such as the Mob Select scene
* `Entity.shake_camera` no longer shakes the Custom bar
* Homepages will respect background music path
* New title screen and startup sequence

There are also new server-related features. 

* Battle rewards can be granted, with limited functionality around card rewards
* HUD can be swapped with the PET icon
* Can send email to clients
* Can play PET ring SFX
* New Sprite API lets servers draw sprites on the client's screen
* Can assert asset type when providing assets
* Card mod packages received by client are added to mod partition for the duration of the session
* Battle results no longer have `runaway` bool, now use `reason` enum
* Tile custom property `shadow` can be set to `Never` to never have that tile be drawn in shadow
* Start and stop song functions