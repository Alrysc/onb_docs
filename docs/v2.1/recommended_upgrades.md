# Recommended Mod Upgrades
The vast majority of mods do not need upgrades to work in the new version. However, there 
are many new features available that modders will want to use to improve their mods, including 
things that were previously impossible. 

Some of these recommendations are to warn about behavioral changes, which may affect how a mod 
works, but most likely leave it working. Other recommendations are for new things that can be added 
to mods to either make certain logic safer, or improve mods with new functionality. These 
are separated by section.

This page also covers usage suggestions and examples for new features.

## New Feature Possibilities

### Let Engine Handle Hit Sounds
v2.1 allows many mods to remove the hit sounds included in their mods. This has technically 
always been true because of the [`AudioType` enum](../onb_overview/enums/audiotype.md), but 
now the engine can take full control and even play different hit sounds depending on what 
an attack hit.

For most people, this means you don't have to provide hit sounds in most of your mods anymore, 
or handle playing them, unless you specifically want a custom hit SFX different from the default.

Note that the SFX will only play when the attack is not blocked, it does more than 0 damage, 
and it has the `Hit.Impact` flag. This should not be concerning and is likely what is intended 
by all mods regardless, as hit sounds act like this in Battle Network, too. If you want to play a 
hit sound and you know it doesn't fall under these situations, you may still want to call 
`Engine.play_audio`. Otherwise, you should be able to safely remove it and associated audio 
files from your mods.

#### Default Hurt Sounds
This is all because Entities now have "default hurt sounds", which will play after the `Spell.attack_func`
runs, if damage from an attack was not blocked, was > 0, and the attack had the `Hit.Impact flag`. 
There are three different defaults:

* The sound you hear when damaging an Obstacle in BN (default for all Obstacles)
* The sound you hear when damaging an enemy in BN (default for all other Entities)
* The sound you hear when the player is damaged in BN (default for only the local Player)

This means that, in PvP, you will hear a different sound when you get damage than you hear when 
you damage the enemy player.

#### Change Default Sound
You can change the default hurt sound for an Entity. This requires two function calls: one to 
set the new sound to use, and one to toggle whether or not your custom sound should be used.

```lua
local my_hurt_sfx = Engine.load_audio(_folderpath.."hurt.ogg")
function player_init(player)
    player:use_default_hurt_sfx(false)
    player:set_hurt_sfx(my_hurt_sfx)
end
```
This also works with the `AudioType` enum.
```lua
function player_init(player)
    player:use_default_hurt_sfx(false)
    -- Player will play the Explode sound whenever damaged
    player:set_hurt_sfx(AudioType.Explode)
end
```

You can also use `nil` as the hurt SFX to play no sound.

#### Override Damage Sound For Defense
Sometimes, you may want a DefenseRule to play a sound when the Entity gets hit. Often 
a hit sound won't play, because a DefenseRule usually blocks attacks, but this isn't 
always the case. Maybe your DefenseRule allows the user to still be damaged, but it creates 
some extra effect and you want a different hurt sound. 

This can be done using the judge's new `set_hit_sfx` function. This can override the 
sound that the Entity has set for their hurt SFX to play your own.

```lua
local my_hit_sfx = Engine.load_audio(_folderpath.."hit.ogg")
local defense = Battle.DefenseRule.new(10, DefenseOrder.CollisionOnly)
defense.can_block_func = function(judge, attacker, defender)
    judge:set_hit_sfx(my_hit_sfx)
end

player:add_defense_rule(defense)
```
The above DefenseRule will override the SFX to play after damage, so that instead of using 
the hurt Entity's set SFX, it will use the one set here. An `AudioType` enum value also works.
```lua
local defense = Battle.DefenseRule.new(10, DefenseOrder.CollisionOnly)
defense.can_block_func = function(judge, attacker, defender)
    judge:set_hit_sfx(AudioType.Explode)
end

player:add_defense_rule(defense)
```
You can also use a value of `nil` to prevent any sound from playing. 

A DefenseRule that runs after yours may set the SFX again, which will overwrite your change. 
The attacker has the final say in the damage sound, however. 

!!! tip "'hit' vs. 'hurt'"
    Make sure to notice that `Entity` uses `hurt` in `Entity.use_default_hurt_sfx` and 
    `Entity.set_hurt_sfx`, but `DefenseFrameStateJudge` uses `hit` in `DefenseFrameStateJudge.set_hit_sfx`.

#### Override Damage Sound For Attack
Sometimes, you may want an attack you make to play a different sound. You can still call 
`Engine.play_audio` in your `attack_func`, but this will play the sound at the same time as the 
enemy hit sound. 

Attacks have the final say in what SFX will play, since they can run logic after every other 
possible change was made. Because the `DefenseFrameStateJudge` is now passed into the `attack_func` 
and `collision_func` of Spells and other hitboxes, you can use its new `set_hit_sfx` function to 
determine what sound will play when damage is dealt. For example, 
```lua
local my_hit_sfx = Engine.load_audio(_folderpath.."hit.ogg")
spell.attack_func = function(self, other, judge)
    judge:set_hit_sfx(my_hit_sfx)
end
```
The above code will cause your own SFX to play. You can similarly use an `AudioType` enum value instead.
```lua
spell.attack_func = function(self, other, judge)
    -- Play the explosion sound
    judge:set_hit_sfx(AudioType.Explosion)
end
```

`nil` is also a valid value and will cause no sound to play. You might use this also in cases where
you want your non-Impact attack to make a hit sound, so you used `Engine.play_audio`, and want to 
ensure no other sound plays at the same time.

!!! tip "'hit' vs. 'hurt'"
    Make sure to notice that `Entity` uses `hurt` in `Entity.use_default_hurt_sfx` and 
    `Entity.set_hurt_sfx`, but `DefenseFrameStateJudge` uses `hit` in `DefenseFrameStateJudge.set_hit_sfx`.

### Custom Charge Times
v2.1 fixes charge time calculation, which means Player mods are free to have unique 
charge times. If you do not add one, the mod will continue to use the default 
provided by the engine.

This is done using the `charge_time_func`, both on Players and on PlayerForms.
The `charge_time_func` is passed a reference to the Player, and a number `level` 
representing the current Charge level, and expects a `frametime` to be returned. 
The `level` will be a number from 1 to 5, inclusive. Be sure to cover all cases.

```lua
local form = player:create_form()
player.charge_time_func = function(self, level)
    if level == 1 then
        return frames(50)
    end

    if level == 2 then 
        return frames(40)
    end

    -- etc.
end

-- Note that the form receives the Player, not itself, in the parameters, 
-- so standard practice of using `self` as the first parameter is incorrect.
form.charge_time_func = function(player, level)
    if level == 1 then
        return frames(100)
    end

    if level == 2 then 
        return frames(90)
    end

    -- etc.
end
```

Some tips for determining charge times:

* The default charge time is 90 frames at level 1, 80 at level 2, 70 at level 3, 60 at level 4, and 50 and level 5.
* If your charge time can be based on a formula, use one
    * For exmaple, the ONB default can be replicated in one line with `return frames(100 - 10 * level)`
* The official games often do not use linear charge times. For example, HeatCross in BN6 has times 
 60, 50, 40, 35, 30, for levels 1-5. Notice how having more levels past 3 has lesser effect.
* The returned time is applied *after* holding the Shoot button for 10 frames (when the graphic appears)
    * This means returning `frames(10)` actually means you need to hold B for 20 frames total to be fully charged
    * Sources on charge times for the official games may or may not count the 10 frames before holding Shoot. 
    Take this into consideration when taking data from other sources. 

If your mod changes the `Player.charged_attack_func`, consider also changing the `Player.charge_time_func`.

### Suggest GFX Spawn
In the original games, there are some Defenses which allow graphical effects to spawn even though 
the attack was blocked, and others that don't. Spells can simply spawn graphics wherever and whenever 
they want, but you can now have your DefenseRules suggest that they don't spawn a graphic. This is 
not foolproof, as mods needs to read this and decide to listen, but it allows for the option. 

This comes from `DefenseFrameStateJudge.hint_spawn_gfx`. If called, it will return `true` or `false`, 
based on whether or not it has been suggested that GFX should or shouldn't spawn. If called by passing 
in a boolean instead, it will set the return value to be given later.

```lua
local defense = Battle.DefenseRule.new(10, DefenseOrder.CollisionOnly)
defense.can_block_func = function(judge, attacker, defender)
    -- Ask attackers to not spawn GFX
    judge:hint_spawn_gfx(false)
end

player:add_defense_rule(defense)
```
With this DefenseRule, you might have an attacker like so:
```lua
spell.collision_func = function(self, other, judge)
    -- Stop if the judge has asked not to spawn GFX
    if not judge:hint_spawn_gfx() then return end

    -- Put code for spawning hit effect here
end
```

It's recommended to have your attacks listen to the DefenseFrameStateJudge.

### Access to Shoulder Buttons
v2.1 adds the `right_shoulder` and `left_shoulder` inputs to be read by mods. You 
can perform some logic based on these, and you might have some mods that were getting 
especially crowded using only the Special button. 

Note that the shoulders are typically used to open the Custom screen. Your logic may 
be performed at the same time the Custom screen opens if the Custom Gauge is full.

### Safer card_action_event
In v2.0, if you used `Character.card_action_event`, you had no way of knowing whether 
or not the Card would be added or would execute, or even could execute. You might 
have queued the action multiple times, causing it to happen more times than intended. 
With v2.1, this can be made somewhat safer because we can check whether or not the 
Character is available to perform an action using `Character.can_attack`.

`Character.can_attack` returns false if the Character already has a CardAction, or 
is flinching, stunned, frozen, or in the middle of Drag. By combining this with a 
check for `Entity.is_moving`, you can more consistently avoid queueing the action 
multiple times, since the frame after you queue the action `Character.can_attack` will 
return false until the action has either finished or been discarded.

```lua
player.update_func = function(self)
    local can_act = player:can_attack() and not player:is_moving()
    if can_act and player:input_has(Input.Pressed.Left_Shoulder) then 
        player:card_action_event(make_my_action(), ActionOrder.Voluntary)
    end 
end
```
If the above code did not make those checks, a player could repeatedly press the left shoulder
button and queue the action multiple times, or could even press it while stunned and have the 
action happen immediately when stun ends. Guarding the `card_action_event` with `can_attack` 
and `is_moving` prevents this.

### Hit.NoCounter
You can now make attacks that can never counterhit. In v2.0, this was able to be done using 
the raw value of the enum (2048), but now the enum value is accessible using `Hit.NoCounter`. 
If you used `2048`, consider updating to use the enum value, which is guaranteed to always 
work in the future.

### Check Confusion and Blind
In the original games, enemies would often act differently than normal if they were blind 
or confused. Modders can now check for these conditions with `Entity.is_confused` and 
`Entity.is_blind`.

According to K1rbyat1na, confused enemies in BN3/EXE3 acted the same as if they were rooted, 
so their movements will not happen. But in later games, confused enemies act the same as 
if they were blinded, which means, quoted:
"
1. They have a longer idle delay.
2. They can't choose the correct tile for an attack and choose it randomly (e.g., StarMan using Star Arrow).
3. They skip attacks that require moving to a specific tile (e.g., StarMan using Moon Dance).

Some bosses have unique features in the Blind state. For example, Blinded Forte always excludes tiles 
containing the player when using Shooting Buster so he never hits them.
"

You might consider following this for your mods to be closer to the source material.

### `battle_end_func` and `battle_start_func`
All Entities can use the `battle_start_func` and `battle_end_func` now. This is most likely 
to come up where modders have wanted to remove Spells from the Field when battle ends, or 
prevent enemies from spawning new attacks, which is common for official game behavior. 

### TimeFreeze action_end_func
In v2.0, time freeze CardActions did not call their `action_end_func`. This has been fixed. 
If your mods had certain cleanup that must be performed at the end, this could be a good 
place to move things.

### TimeFreeze Uncounterable
Time freeze actions can now be marked as uncounterable, using `CardMeta.counterable = false`.

Try not to abuse it too much. This is typically used for time freezes that happen as a 
result of some condition, like Guardian being hit, or an elemental attack appearing 
on the field and triggering ElementTrap, or a boss changing phases. Mods that have these 
sorts of actions likely already wanted to be uncounterable, and now they can be. Generally 
recommended to go with `skip_time_freeze_intro` being true.

### set_counter_frame_range
Instead of toggling counter on and off, you can now set a range of animation keyframes
for which an Entity will be counterable. This is based on the current animation.

```lua
action.execute_func = function(self, actor)
    actor:set_counter_frame_range(1, 4)
end
```

The above code is equivalent to 
```lua
action.execute_func = function(self, actor)
    action:add_anim_action(1, function()
        actor:toggle_counter(true)
    end)

    action:add_anim_action(4, function()
        actor:toggle_counter(false)
    end)
end
```
Note that this example is done specifically in the `execute_func`, which is the first time the 
user has entered the action animation, but you can still do this elsewhere. Don't forget to 
treat the incoming `user` for the `card_create_action` as if it may be different from the `actor`.
You should be targeting the `actor`, given in the parameters for the `execute_func` or returned 
from `CardAction.get_actor`.

### Breaking Metal Tiles
The Metal TileState prevents cracking and breaking a Tile. This means calls like 
`tile:set_state(TileState.Cracked)` or `tile:set_state(TileState.Broken)` will do 
nothing if the current state is `TileState.Metal`. 

Some effects intend to *overwrite* Tiles, which is different flavor from *breaking* 
them. For example, in BN3, MetalMan, GutsMan, and BassGS don't affect Metal Tiles, 
but Geddon1 and Geddon2 do. Geddon1 cracks all Tiles, including Metal Tiles; Geddon2 
turns all Tiles into holes, but Metal Tiles are instead cracked. If you intend for 
this sort of behavior, you can change the Tile to `TileState.Normal` and then crack 
and break it, or only crack if it was Metal beforehand. 

### Engine Alert Symbol
v2.1 adds access to `Battle.AlertSymbol.new()`, which creates an alert symbol, the 
double exclamation point icon that appears when taking weakness damage or performing 
TFC. If your mods have created one for themselves, you may consider using this instead. 

### Turn Counting
In v2.0, some mods tried to create attacks or special effects that activated only 
once per turn. This can now be done much more easily and with fewer holes by the 
new `Battle.get_turn_count`.

`Battle.get_turn_count` returns the current turn of the battle. It will return `0.0`
before the first turn, then `1.0` more every time the "Battle Start!" banner appears 
after exiting card select.

```lua
player.last_known_turn = 0.0
player.allow_special_attack = true

-- Set allow_special_component true if it's a new turn
local special_component = Battle.Component.new(player, Lifetimes.Scene)
special_component.update_func = function()
    local turn = Battle.get_turn_count()
    if turn ~= player.last_known_turn then
        player.last_known_turn = turn
        player.allow_special_attack = true
    end
end
player:register_component(special_component)

-- Do nothing if allow_special_attack is false
-- Otherwise, return special attack and set 
-- allow_special_attack false
player.special_attack_func = function(self)
    if not self.allow_special_attack then 
        return 
    end

    local action = Battle.CardAction.new(self, "PLAYER_IDLE")

    action.excute_func = function()
        self.allow_special_attack = false
    end

    return action
end
```

### New HitProps Constructor
HitProps can now be constructed and modified using a builder pattern.
This is best done using the new constructors, `HitProps.new()` or 
`HitProps.new(context)`.

Before:
```lua
local hit_props = HitProps.new(
    props.damage,
    Hit.Impact | Hit.Flinch | Hit.Stun,
    props.element,
    user:get_context(),
    Drag.None
)
```
Now:
```lua
local hit_props = HitProps.new()
    :dmg(props.damage)
    :impact()
    :flinch()
    :stun()
    :elem(props.element)
    :from(user:get_context())
```
You no longer need to remember the order of values to give to the constructor, 
and don't have to specify that you will have no Drag. If your attack did have 
Drag, you can decide to do
```lua
hit_props:drg(Drag.new(user:get_facing(), 1))
```

### Use element2
With the [new HitProps builder pattern](#new-hitprops-constructor), you can now 
add secondary elements.

```lua
local hit_props = HitProps.new()
    :dmg(props.damage)
    :impact()
    :flinch()
    :stun()
    :elem(props.element)
    :elem2(Element.Sword)
    :from(user:get_context())
```
This can be read with `hit_props.elem2`.

Be sure to take `elem2` into account in your DefenseRules, which should react the same 
to primary and secondary element.

### Use Status Durations
With the [new HitProps builder pattern](#new-hitprops-constructor), you can now 
add durations to certain statuses.

```lua
local hit_props = HitProps.new()
    :dmg(props.damage)
    :impact()
    :flinch()
    :stun(frames(90))
    :elem(props.element)
    :from(user:get_context())
```
Note that a status generally needs at least two frames of duration to take effect. 

The following statuses can be given durations in this way:

* stun (default 120 frames)
* freeze (default 150 frames)
* flash (default 120 frames)
* root (default 120 frames)
* blind (default 300 frames)
* confuse (default 110 frames)
* bubble (default 150 frames)

### CustGauge Manipulation
It's now possible to read and change the Custom Gauge's duration and speed.

* `Battle.get_cust_gauge_value`, which returns a double with value `0.0` at empty gauge and `1.0` or higher at full, as a percentage completion of the gauge
* `Battle.get_cust_gauge_time`, which returns a number in frames for the current gauge time (e.g. 256.0 when gauge is half full with default duration)
* `Battle.get_cust_gauge_max_time`, which returns a number in frames for the current max gauge time (e.g. 512.0 for default time)
* `Battle.get_default_cust_gauge_max_time`, which returns the default gauge time, 512.0 frames
* `Battle.set_cust_gauge_time`, takes in a frametime (like `frames(256)`) to set current gauge time to
* `Battle.set_cust_gauge_max_time`, takes in frametime to set max time to (current time is automatically readjusted to match the percentage of total time it had before, e.g. max time 512 and current time 256 will have current time 100 if you set max time to 200)
* `Battle.reset_cust_gauge_to_default`, sets max time to default time

## Behavior Changes (Be Aware)

### Using Hit.Drag
v2.1 makes Drag interactions much closer to source material. This reveals some 
possible mistakes modders have made with Drag attacks. Below are some nuances that 
may have been unknown before, and that modders should reconsider for attacks that 
inflict Drag:
1. Drag makes the hit Entity totally unable to act for over 20 frames after movement stops
2. Drag and Freeze are fully incompatible. If any attacks have Hit.Freeze and Hit.Drag, they 
   will never cause Freeze. If you have done this, consider removing Hit.Freeze.

### Drag Endlag
The Drag status now properly keeps Entities from acting for over 20 frames after movement 
stops. This is the Drag endlag, during which `Entity.is_moving` and `Entity.is_sliding` 
will return false, which was not the case in v2.0.

See [safer card_action_event](#safer-card_action_event).

### Movement Endlag
In v2.0, all movement had at least one frame of endlag. Endlag of `frames(0)` and 
`frames(1)` were identical. With v2.1, this is no longer the case. 

This will affect any MoveEvent that was using `frames(0)` for its endlag, which 
is most sliding Spells. There is no actual fix necessary, because modders likely 
intended their 0 frame endlag to really be 0 frames, but modders should be aware 
that projectiles may appear to move slightly more quickly. 

The actual effect on speed has to do with how long it takes to reach mutiple Tiles 
away. Things that moved two Tiles will reach the second Tile 1 frame faster now that 
the endlag isn't there. Moving three Tiles will reach the third Tile 2 frames faster, 
etc.

If you intended to have an extra frame on each Tile, you can use `frames(1)` as the 
endlag to replicate how `frames(0)` looked and moved in v2.0.

### Spawning Hit Effects
Some Spells will spawn hit effects in their `collision_func` or `attack_func`. Some 
mods spawn the effect on the Spell's Tile; others spawn on the hit Entity's Tile. 
Spawning on the Spell's Tile has always been less correct, because the Spell is moving 
and may have already reached the next Tile before the effect was spawned. This could 
mean the effect spawns behind the hit Entity, for example. 

Because of the [movement endlag change](#movement-endlag), this may be more noticeable
(but it could have happened in v2.0, too).
If a mod spawned the hit effect on the target's Tile, no change is necessary. If they 
spawned on the Spell's Tile, consider changing this.

### Deleting Moving Attacks
Some Spells will delete themselves in their `collision_func`. If the Spell is moving, 
it may have changed Tiles before the `collision_func` ran. This means it could have 
been drawn on the next Tile even though it hit on the previous Tile, because Entities 
still draw on the frame `Entity:delete()` is called.

This draw error could happen in v2.0, but is likely more noticeable now because of the 
[movement endlag change](#movement-endlag).

If you call `erase` in your `delete_func`, or if you called `erase` intead of `delete`, 
or if you call `hide` when you called `delete`, or if you call `hide` in the `delete_func`, 
this will never happen to your Spells. If you do not do any of these, consider adding one.
My recommendation is to call `Entity.hide` in your `delete_func`.

### find_nearest_characters Fix
In v2.0, `Field.find_nearest_characters` did not actually return an ordered list, so the 
first item was not necessarily the nearest Character. This is fixed by v2.1. 

If you have used `find_nearest_characters` and intended to target the nearest Character, 
no change is needed; you may notice that you are now actually getting the nearest Character.

If you have avoided `find_nearest_characters` to use your own solution, or because it did 
not work as expected, consider updating to now use it.

### Fire and Grass
Fire element attacks now remove Grass even if they did not hit an Entity. Mods that 
handled this interaction on their own in v2.0 can now remove their implementation, as
the engine will handle it.

### Grass, Lava Effects
Grass now heals Wood Entities. If your Player mods have implemented a sort of grass heal, 
you can now remove this and rely on the engine's. If you do not, your healing effect will
stack with the engine's.

Fire Entities are no longer harmed by Lava Tiles. If you have added FloatShoes to your 
Player mods only to recreate this interaction in v2.0, you can now remove the FloatShoes 
and remain immune to Lava.

### New TileStates
Because of the new TileStates (Sea, Metal, Sand), mods that attempted to change Tiles 
on the Field to any TileState (e.g. a mod that picks random TileStates each time) 
may want to be updated to support the additions.

Servers that send data for encounters tend to use hardcoded values based on the TileState 
enum (ezencounters does this). Any use of `TileState.Hidden` on your server encounters 
may now appear as Sea Tiles. See the repo for new values to adjust to.

### Double Hit Sound
Because [the engine now plays default SFX when damage is dealt](#let-engine-handle-hit-sounds), 
mods that called `Engine.play_audio` in their attacks' `attack_func`s are likely playing 
a second sound on top of the default. 

If you have a special sound to play that won't be used by the engine by default, use the 
DefenseFrameStateJudge that's now passed into the `attack_func` to set the sound with 
`DefenseFrameStateJudge.set_hit_sfx`, and remove the `play_audio` call.

Otherwise, if your sound is the sound that the engine will play anyway, remove your 
`play_audio` call. You can also remove the SFX audio file from the mod if it's not used 
elsewhere.