## Changelog (v2.0 -> v2.1.4)
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

## v2.1.5
* New title screen music, "Open Source Savior", an original composition by Mannerek with compositional assistance from TheMaverickProgrammer
* Drag can now never queue behind itself
* Fixed an issue around actions popping incorrectly
* TFC now correctly consumes x2 boosts
* Entities that were spawned late in the frame and attacked when spawned will correctly execute their attack
* Hidden Tiles are invisible again
* Returning an invalid `CardAction` when its meta says it will freeze time will now cause time freeze to begin
* Adjusted some music loop behavior
* New `--chatbox` command line arg to test textboxes. Example usage: `BattleNetwork.exe --chatbox --dialog my_script.txt`
* Session Emotion is correctly initialized when entering battle
* Fixed issue where using a card could sometimes let you use a time freeze card behind it before the first card executed
* Fixed `make_frame_data` crashes when given incorrect types
* Fixed issue where two simultaneous actions, where only one is time freeze, could cause time freeze to be skipped
* When multiple time freeze actions are used simultaneously, they now execute in separate action chains, and actionability is checked before the next chain begins
* `Entity.erase` no longer prevents the BattleScene from noticing an Entity was deleted (no more battle softlock)
* Card UI is now visible during time freeze, until the time freeze counter window ends
* Unloaded folder cards are reloaded when a load package signal is received from servers
* Folder paste now checks for exact card ID

## v2.1.6
* Updated Lua version to 5.4.6
* Added Netplay Delay config option and related battle screen widgets
  * Delay can go from 1f to 12f
  * Lowest delay between players is selected. Make sure you and your opponent agree on delay when changing it!
  * Default delay is 5f. Can select "Any" option to default to opponent's delay option
* Improved responsiveness and speed of downloads between peers and server
* Local PvP is now possible on remote servers. Play online with your friends nearby!
* Matchmaking scene now prevents you from battling yourself
* New ping widget in netplay. Smaller and more informative.
* Debug mode now draws current battle frame in all battle scenes. `BattleNetwork.exe -d` to see it.
* Regression fix: Non-edge Hidden Tiles are hidden again
* Fixed possible crashes when a server connection times out while battle packages are downloading or while in battle
* Fixed a potential desync related to Sea Tiles
* Fixed a crash related to shaders on certain platforms

### Servers
* Servers are now told when a client exits the battle scene, separate from when the client finishes fighting.
  * Set a callback to listen to this with `Net:on("battle_exit", function)`
    * The event has a `player_id` index
  * Results should still be given with `Net:on("battle_results", function)`, just as in earlier versions

## v2.1.7
* **The mods folder layout has changed!** 
  * Mods now extract into a `staged` folder inside of their mod folder. For example, `mods/my_mod.zip` now extracts to `mods/staged/my_mod` instead of `mods/my_mod`
* Changed how mods are hashed, which should keep hashes consistent across platforms
* New `CardHandle` class available for Lua scripting. With it, you can replicate things like BN6 HeatCross giving +50 damage to all Fire chips, or Otenko giving you a steadily-increasing damage boost. See [Recommended Upgrades](./recommended_upgrades.md) for examples.
* New `PropsMod` enum for use with `CardHandle`
* When your opponent in PvP uses a realtime card, you will now see the name and damage of the card, just like BN6
* New filter menu available in the pack. This is only available for pack view, not folder view.
  * After opening the sort menu in the pack, press the shoulder buttons to switch to the filter menu.
  * You can filter by specific code, specific ID, and you can filter out certain card classes or elements
  * You can filter out cards that are not in a server whitelist
  * There is a "Reset" option to remove all filters
  * Servers with whitelists automatically turn on a filter to hide cards that are not whitelisted
* You can now edit a folder even if it contains cards that are not in a server's whitelist
* Cards that are not whitelisted by a server are now colored differently and are given different descriptions, so you can tell which ones are not allowed
* Player mods that are not allowed based on server whitelists will have their names colored red.
* Many net features give more breathing room to keep FPS up and lessen full freezes
* Battle stops allowing hits and damage while last enemy is exploding
* Battle ending no longer awkwardly ends movement and actions
* Flatpak install overwrites the resources folder, which ensures updates install new or modified resources
* The busy spinner graphic is more lax in when it appears, and no longer appears over certain scenes. 
* Server sprite animations properly loop now
* Server asset caching has completely changed. This helps to reduce the filepath length of cached assets, which is important on Windows with its small max path length
* A progress bar now displays while assets are being downloaded from the server. This is per asset, not for all assets.
* Title screen now tells youe exactly what mod is being loaded, and has more accurate progress reporting
* Fixed bad packet being sent to server
* Server battles can no longer be launched if the player is in a menu. It will wait to start until the menu close.
* Server battles can no longer start while a textbox is open. It will wait to start until the textbox closes.
* Transferring areas on a server no longer blacks out the screen while loading the next map's assets.
* Excluded bots can no longer be interacted with, nor collided with
* Animated movement no longer snaps the actor back to their starting position when movement ends
* `CardMeta.secondary_element` is deprecated. It will still work for now, but please use the new `CardMeta.element2` instead.
* TFC timing is more accurate now, which removes any benefit the bar had. The bar is removed.

## v2.1.8 (Beta)
* New API for chip charge abilities. See [Recommended Upgrades](./recommended_upgrades.md) for API and examples.
* Players now have a `cust` stat, which affects how many cards are drawn on the card select screen
  * Default 5, min 2, max 8
  * `Player:get_cust_level()` - Returns cust stat after applying modifiers
  * `Player:get_base_cust_level()` - Returns cust stat before applying modifiers
  * `Player:set_base_cust_level(lvl)` - Sets base cust stat, which modifiers will be applied to. Min 2, max 8
* Player form mugshot now affects only the form select UI button. A separate function sets the emotion texture, which is no longer cut off.
  * `PlayerForm:set_emotions_texture_path(path)`
  * This should be a single emotion for now. Do not put in the long strip that contains all emotions. Use only the base emotion.
* `Entity:adopt_time_freeze()` - Time freezes an Entity if time is frozen. Useful for Entities (such as Obstacles like Sensor or Pawn) that are spawned during time freeze, but should not update until time resumes
  * Only works when an Entity has been spawned and has a Tile, so it's recommended to call during `on_spawn_func`
* `Field:is_time_frozen()` - Returns `true` if time is frozen.
  * Warning: Also returns true at the start of battle, up until the first turn begins. From then on, it's accurate.
  * Does not return `true` while decrossing (yet)
* Metadata set on a `CardAction` during `card_create_action` will be respected.
  * Recommended to do so by modifying `props`, then calling `action:set_metadata(props)`, which will preserve the card ID
  * A card ID is required for the name of a realtime acton to display when used by an enemy
* Changing charge color while charging should now reflect the new color immediately
* Charge graphic is stabilized (no more wobble) and animates better
* Timing of `PlayerForm.update_func` is changed to be closer to `Player.update_func`
* `PlayerForm` and `PlayerFormMeta` are unified
  * You can set indexes on a form you create with `player:create_form()` and then read these same indexes in callbacks
* Characters spawned after battle begins no longer have their time freeze actions ignored
* Delay frames are now correctly visible on turn 0, rather than updating only once the first handshake is received
* New stat: Movement speed, which affects how long you have to wait after moving before you can act again
  * Works with the stat modifier API using `Stat.move_speed`, which you can modify to a `MoveSpeed` enum value, seen below
* `MoveSpeed` enum:
  * `MoveSpeed.Normal` - Default, 7 frames of movement endlag
  * `MoveSpeed.Fast` - 4 frames of movement endlag. Seen on ShadowSoul in BN5.
  * `MoveSpeed.Slow` - 13 frames of movement endlag. Seen on KnightMan in BN5. Very slow.
* `Player:get_move_speed()` - Returns the current `MoveSpeed`, after applying modifiers
* `Player:set_base_move_speed(MoveSpeed)` - Sets the base `MoveSpeed`, which modifiers apply to
* `Engine.toggle_music(bool)` - Stops the music that's playing if `false`, or resumes paused music if `true`
* BugFrags are now shown in the overworld menu, as `Fragments`
* Health in battle moves much more quickly for players and enemies
* HP no longer syncs in PvP. Instead, if an HP desync is detected, your opponent's HP will turn yellow. HP being yellow guarantees you are desynced, but HP not being yellow doesn't mean you haven't desynced. 
* Inputs are processed more neatly during battle, consistent across all battle states
  * Buster is no longer released if you were holding Shoot when you entered Custom, but were not holding it when battle starts
  * Same as the above for time freeze, and card charge is the same
  * Inputs should now be readable during form transformations
* Card code in battle results now lines up with the card name
* Adjusted spacing of text for used card names
* Improved the menu transition when queuing a form change
* Card select widget (including emotion mugshot and player HP) and custom bar are drawn more consistently during battle
* Text from used cards during combat immediately disappears when the card select opens
* Reduced use of loading spinner around battles
* Reduced frame hiccup when starting a battle from the server
* Fixed damage display when using a card with x2 showing incorrect addition
* Fixed a bug where removing cards from the folder could refill a pack card with the same name, rather than the same ID
* Fixed a bug where a form emotion texture could stay even after undoing a queued transformation
* `BusterAction` uses the Player's animation instead of loading from the underlying animation path. This lets it play nice when animation files are changed for forms.
* Fixed an issue where card packages sent during battle rewards would wait until battle exit to load
* Added some time padding for packages to load before accepting rewards (the package attempts to load when A is pressed)
* Fixed a bug where an actor which had never been excluded could be included, letting it update extra times
