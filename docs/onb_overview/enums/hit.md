# Hit

The `Hit` enum makes up what I'll be calling "hitflags", values that are 
used as signals, or flags, that certain behavior should happen on a hit. 

It contains the following values:

* None
* Flinch
* Flash
* Stun
* Root
* Impact
* Shake
* Pierce
* Retangible
* Breaking
* Freeze
* Drag
* Blind
* Confuse
* Bubble
* NoCounter
* Cancel

[Impact](#impact) and [Cancel](#cancel) are flags with extra special behavior. Be sure to read their sections.

!!! tip "Statuses"
    Many of the flags here inflict statuses. In the future, I may split 
    some of the information here to another page for statuses.

    Statuses and certain reactions to hitflags are only applied when the 
    attack passes all DefenseRules without being blocked. If their 
    `attack_func` plays, then the flags have been fully applied.

## Bits

`Hit` values are set up so that they can be used as bit flags. This means you 
can use bitwise operations on them. Some of the following sections will show 
some examples of using these.

## Usage

When creating a `HitProps`, use functions with the names of the flags to add them.

```lua
local hit_props = HitProps.new()
    :dmg(10)
    :impact()
    :from(player:get_context())
```
(With the exception of `drag`, which actually uses the function name `drg`.)

Or, equivalently with the the old constructor, use `Hit` enum values when setting its `flags` 
field.

```lua
local hitprops = HitProps.new(
    -- damage
    10,
    -- flags
    Hit.Impact,
    -- element
    Element.None,
    -- context
    player:get_context(),
    -- Drag
    Drag.None
)
```

### Multiple Flags

You will often use `|` to combine multiple flags. This is done mostly in the old 
HitProps constructor, or when accessing the `HitProps.flags` field.

```lua
local hitprops = HitProps.new(
    10,
    Hit.Impact | Hit.Flinch | Hit.Flash,
    Element.None,
    player:get_context(),
    Drag.None
)
```

With the new `HitProps` constructor and its builder pattern, you can represent 
the above as 
```lua
local hit_props = HitProps.new()
    :dmg(10)
    :impact()
    :flinch()
    :flash()
    :from(player:get_context())
```

The above HitProps will make impact, cause the hit target to flinch, and also 
cause them to flash.

### Status Durations

Some statuses can have a duration set. You can do this using functions from the 
builder pattern:
```lua
local hit_props = HitProps.new()
    :dmg(10)
    :impact()
    :flinch()
    :flash()
    :stun(frames(150))
    :from(player:get_context())
```

The statuses that can have a duration set, along with their defaults if none is provided, 
are:

* stun (default 120 frames)
* freeze (default 150 frames)
* flash (default 120 frames)
* root (default 120 frames)
* blind (default 300 frames)
* confuse (default 110 frames)
* bubble (default 150 frames)

### Checking For Flags

You can use bitwise operations to inspect and manipulate hitflags.

```lua
local flags = Hit.Impact | Hit.Flinch | Hit.Flash

-- If flags has Hit.Flash
if flags & Hit.Flash == Hit.Flash then
    -- Remove Hit.Flinch
    flags = flags & ~Hit.Flinch
end

-- Another way to check if flags has Hit.Flash
if flags | Hit.Flash == flags then 

end
```

## Values

### None

`Hit.None` is the `0` value. It has no other behavior. Think of this 
as the absence of all other flags.

This also means you never have to explicitly use `Hit.None` if you have 
multiple flags.

```lua
-- true
print((Hit.None | Hit.Impact) == Hit.Impact)
```

### Impact

`Hit.Impact` is an extremely important flag. **When in doubt, add it!**
It determines a few crucial properties of an attack and how other things 
tend to react to it. 

Because everything is user-generated, things may not match exactly. I'll 
describe what it does in the official games as a frame of reference:

With Impact:

* Attacks will cause the target to flash white when hit
* Damage sounds will play
* Barriers will block the attack
* Shields will play a block sound and spawn a graphic, and reflect if able
* Damage dealt will be tallied for the Damage Judge to determine a winner when 
the round's time runs out

Put another way, Impact is used for attacks that really *hit* someone. Swords, 
fire attacks, bombs, etc. all use Impact. They may be blocked by defenses.

Attacks without Impact are often things that act as "HP drains". Anubis and 
GunDelSol are examples of these. Shield still blocks them, but they go through 
AntiDamage and Barriers.

When creating mods that react to damage, take care in checking for `Hit.Impact` 
where appropriate.

### Flinch

`Hit.Flinch` causes Players to enter their "PLAYER_HIT" animation state. Many 
attacks use this, but not all. It's common to strip it away using the SuperArmor 
NaviCust Part in the official games.

!!! tip "Missing Info"
    This notice will be removed once I check what the duration of the flinch
    animation is. It's somewhere around 18-24f.

#### Flinching Enemies

Characters that are not Players do not inherently have any reaction to `Hit.Flinch`.
When programming enemies which should flinch, such as Navi bosses, you will need 
to handle flinching yourself.

### Flash

`Hit.Flash` causes the hit Entity to become intangible for some time, while their 
sprite flickers between visible and invisible. Viruses and objects in the official 
games often strip this flag when hit.

While an Entity is intangible because of this flag, we say that they are "flashing".
The default duration is 120 frames, but can use custom durations.

While flashing, an Entity is intangible. Attacks will not make collision unless they 
have the `Hit.Pierce` flag. If they don't collide, the attack's `collision_func` will 
not play and no damage will be done. DefenseRules with the `CollisionOnly` DefenseOrder
also will not have their `can_block_func` play.

### Stun

`Hit.Stun` causes the hit Entity to become stunned. This will prevent them from making 
actions for a period of time, and their sprite will flash yellow. CardActions that are 
currently in use will immediately end, and any which are queued will be discarded. Objects
in the official games strip this flag when hit.

Stun lasts 120 frames by default, but can use custom durations. 

Stun can be cancelled early by an attack that flinches and flashes, or by an attack that 
adds Freeze or Confuse. If Drag is active, being hit by another Drag attack will end Stun.

!!! tip "Counter"
    Being counterhit will also stun. Being stunned in this way will have a duration 
    of 150 frames instead, no matter what the original attack had as the duration.

!!! tip "Stun Duration"
    In the official games, stuns often have different durations. For example, Thunder 
    stuns for 90 frames, JudgeMan stuns for 120 frames, and FlashBomb2 stuns for 150 
    frames. 

### Root

`Hit.Root` causes the hit Entity to become rooted to the spot. This will prevent them 
from moving, and their sprite will flash black. Objects in the official games strip this
flag when hit. Another name used for the inflicted status is "immobilze", but I will be 
using "Root" to match the code.

Root lasts 120 frames by default, but can use custom durations. It will be cancelled early if the 
Entity is intangible, such as after being hit by an attack that flashes.

!!! tip "Root Over Holes"
    In the official games, the Root status cannot be applied to an Entity whose Tile is
    a hole. This is not the case in v2.1. 

!!! tip "Missing Info"
    This notice will be removed when I confirm exactly how Root interacts with 
    MoveEvents. Do they fail to queue, or only fail to update? What happens if 
    you are rooted mid-movement?

### Shake

`Hit.Shake` causes a hit Entity's sprite to shake quickly. In the official games, 
this is seen whenever damage is dealt while time is frozen.

Shake is automatically applied to hits that happen during time freeze. If aiming 
for official game accuracy, this flag should never be manually applied by your 
code.

### Pierce

`Hit.Pierce` signals that an attack should pierce intangibility. This bypasses the 
intangibility granted by the flashing status and allows a collision and damage.

Because it's just a flag, be sure to check for it when you make certain intangible effects.
For example, a recreation of the Invis chip from the official games should allow attacks 
with `Hit.Pierce` to deal damage.

!!! tip "Other Pierces"
    There are other forms of intangibility found across the BN series. PopUp, from BN3, 
    should be pierced by a flag different from the one that pierces Invis and flashing. 
    This is not handled in v2.1. Future versions will have more flags and renamed flags 
    for more clarity.

### Retangible

`Hit.Retangible` signals that an attack should remove intangibility. This will typically 
be paired with `Hit.Pierce`. This will remove the flashing status.

When an attack has `Hit.Retangible` and `Hit.Flash`, expect intangible effects to end 
and then for the flashing status to be applied after.

Because it's just a flag, be sure to check for it when you make certain intangible effects.
For example, a recreation of the Invis chip from the official games should end its effect 
when hit by a Retangible attack.

### Breaking

`Hit.Breaking` signals that an attach should go through certain guards, destroy certain 
objects, and deal additional damage to a frozen Entity.

Following official games, every attack with the Break element should have this flag. Many 
objects are destroyed instantly, but not necessarily all of them, and some have special 
reactions to it. When in doubt, you will likely want to allow them to be destroyed by the 
Breaking flag.

Because it's just a flag, be sure to check for it based on the mod. A recreation of the 
Reflect chip from the official games should not block attacks with this flag, and a recreation 
of RockCube should be instantly destroyed by an attack with this flag. 

!!! tip "Breaking Vs. Break"
    A common misconception is that the Break element has the properties described above.
    In reality, the Break element on its own has no extra behavior; all Break element 
    attacks just so happen to have the `Hit.Breaking` flag.

    Many other attacks in the series have this flag while not being Break element. See 
    Sensor (Elec), EraseMan (Cursor), the charge shot used by ChargeCross (Fire), the 
    DustMan chip (Null), and more.

### Freeze

`Hit.Freeze` causes an attack to inflict the Freeze status. This will prevent the target 
from taking actions, and their Sprite will be lightened and have ice fragments overlaid. 
CardActions that are currently in use will immediately end, and any which are queued will 
be discarded.

The Freeze status lasts for 150 frames by default, but can use custom durations. It will 
be ended early when being hit by an attack with the `Hit.Breaking` flag, or an attack which 
flinches and flashes. It will also end early if it is replaced by the Stun, Bubble, Drag or 
Confuse statuses. Objects in the official games are immune to this status.

If an attack would Freeze, it will not cause Flashing.

This flag is rarely, if ever, used on attacks in the official games. ElementMan Ice and FreezeBomb, 
from BN2, may be the only possible examples, though BN2 treated the status differently.
When deciding to use it or not, keep in mind that the Freeze status is a very powerful 
one, and much stronger than Stun.

!!! tip "Ice Panel Freeze"
    The Freeze status can also be inflicted on an Entity if they take damage from an Aqua 
    attack while standing on an Ice panel. When freeze is inflicted in this way, it 
    does so by adding the `Hit.Freeze` flag to the attack.

### Drag

`Hit.Drag` causes the hit Entity to be forced into movement. This also inflicts the 
Drag status. Their current CardAction, if any, will immediately end, and any that are 
queued will be discarded.

If a `Drag` is not defined in the `HitProps`, this flag will effectively do nothing. 
Using a `Drag` with `Direction.None`, including `Drag.None`, as its movement direction will 
not cause any of the above described behavior. Negative distances will be treated as positive.

While under the Drag status, other statuses and reactions to certain hit flags will be
continually requeued, only finally applying properly once the status ends. For example, 
if a Character is under the Drag status, they can be hit by an attack with the Flinch, 
Flash, and Stun flags, and they will not flinch, start flashing, or be stunned until the 
Drag finishes.

Drag will continue to last several frames after movement stops. This means there is still 
time after movement stops where statuses are queued and not yet taking effect, and the 
Entity still cannot move or attack.

If Freeze or Bubble are applied at the same time as Drag, they will not apply, even after 
Drag ends. If Freeze, Bubble, or Stun are queued during Drag, but on a different frame than 
the Drag attack, they can be unqeueued by a Drag attack landing before the first Drag ends.

#### Drag Movement

The way Drag moves an Entity is described by the final parameter of `HitProps`, the 
`Drag`. If an attack will not do any movement, `Drag.None` can be used. Otherwise, 
use `Drag.new` and pass in a [Direction](./direction.md) to move in and a number of Tiles 
to travel. Movement will be extended if an Entity slides onto an Ice Tile.

```lua
local facing = player:get_facing()
local hit_props = HitProps.new()
    :dmg(10)
    :impact()
    :flinch()
    :flash()
    -- Push 1 Tile in the direction of `facing`
    :drg(Drag.new(facing, 1))
    :from(player:get_context())
```
Or, equivalently with the old constructor, 
```lua
local hitprops = HitProps.new(
    10,
    Hit.Impact | Hit.Flinch | Hit.Flash,
    Element.None,
    player:get_context(),
    -- Push 1 Tile in the direction of `facing`
    Drag.new(facing, 1)
)
```

Typically, you will use the facing Direction of the one creating the attack. Prefer using 
relative Directions like this, as in other places. If `Direction.None` is used, or the distance 
to travel is 0, no movement will occur and the `Hit.Drag` flag will effectively do nothing.
Negative distances will be treated as positive.

The movement from Drag is a MoveEvent which completes in 4 frames, using `ActionOrder.Immediate`. 
Another movement will be made until the entire distance has been travelled or the next Tile cannot be 
moved to, at which point the endlag begins.

#### Common Distances

In the official games, only two Drag distances are used: 1 panel, or the entire field. The Field 
considers itself 6 Tiles long, and because movement ends when the Entity can no longer move, and 
an Entity usually cannot move off onto the edges, something like the below code will do fine 
to push across the entire Field, but it may leave something to be desired.

```lua
local facing = player:get_facing()
Drag.new(facing, 6)
```

Suppose, someday, the Field in ONB is allowed to be longer than 6 Tiles. Then the above code will 
not hold up. Instead, you should consider using `Field.width` to determine how far to push.

```lua
local facing = player:get_facing()
local field = player:get_field()
Drag.new(facing, field:width())
```

#### Interactions with Objects

In the official games, Drag has a number of special interactions when hitting objects. Here are 
some examples:

* RockCube will slide until it can't move to the next panel even if the Drag would normally 
only move 1 space. See AirShot hitting RockCube.
* The big rock, which spawns on some fields, will not be moved at all by Drag.
* RockCube does not take damage from attacks that Drag, while something like BlkBomb does.

### Blind

`Hit.Blind` causes the hit Entity to become blinded. An icon will be drawn over them.
Players who are Blind will be unable to see Obstacles and Characters which are not on 
their Team. 

The Blind status lasts 300 frames by default, but can use custom durations. 

!!! tip "Blind Enemies"
    In official games, enemies with the Blind status behave differently than normal.
    Another page will have information on how the games tend to handle this.

    In ONB, naturally, we need to check for a Blind status and change logic based on it 
    to have this behavior. This can be done using `Entity.is_blind`.

!!! tip "Missing Info"
    This notice will be removed once I have confirmed where the Blind icon is drawn. Is it 
    on the "head" point of an animation frame, or around the height set by `Entity.set_height`?

### Confuse

`Hit.Confuse` causes the hit Entity to become Confused. An swirling duck animation will be drawn 
over them. Players who are confused will have their movement directions reversed.

The Confuse status lasts 110 frames by default, but can use custom durations.

If Confuse is applied, it will end Stun, Freeze, or Bubble. These statuses will also overwrite 
Confuse if they are applied while Confuse is active. If Confuse is applied at the same time 
as any of these statuses, Confuse will not be applied.

!!! tip "Confused Enemies"
    In official games,enemies with the Confuse status behave different than normal.
    Another page will have information on how the games tend to handle this.

    In ONB, naturally, we need to check for a Confuse status and change logic based on it 
    to have this behavior. This can be done using `Entity.is_confused`.

!!! tip "Missing Info"
    This notice will be removed once I have confirmed where the Confuse animation is drawn. Is it 
    on the "head" point of an animation frame, or around the height set by `Entity.set_height`?

### Bubble

`Hit.Bubble` causes the hit Entity to become Bubbled. The Entity will have a bubble drawn around 
them, and they will float up and down. Players will play their first Flinch animation frame for 
the duration.

The Bubble status lasts for 150 frames by default, but can use custom durations. It will be 
ended early by any attack with the `Hit.Impact` flag. If an `Element.Elec` attack hits an 
Entity while it is Bubbled, the attack will do bonus damage.

If Stun, Freeze, or Drag are applied at the same time, Bubble will not be applied.

!!! tip "Odd BubbleStar"
    BubbleStar behaves very oddly in the original games, where, when hitting on ice, it will 
    cause Bubble status only if hitting pointblank. Otherwise, it will freeze instead of bubbling.

!!! tip "BubbleWrap"
    The BubbleWrap chip in the original games has a similar weakness to Bubble, and can stack 
    with the Elec weakness. Capcom was smart and made it so BubbleWrap would stop ticking its 
    regeneration counter if you were Bubbled, to likely prevent cases where the BubbleWrap could
    come back and cause you to take extraordinary damage while Bubbled.

    This can be replicated in ONB by using `Entity.is_bubbled` to decide whether or not to tick 
    the regeneration counter.

### NoCounter

`Hit.NoCounter` makes an attack unable to counterhit. 

This flag is automatically added to attacks made during time freeze, and to attacks made by a 
Player performing their `charged_attack_func` or `normal_attack_func`.

### Cancel

`Hit.Cancel` is a special flag, not intended to be used by attacks (but if you do add it to your 
attacks, there will be no change). The flag is automatically added during to an attack's HitProps 
during hit checks, and the original HitProps are restored after hit checks. 

If the `Hit.Cancel` flag is still present after defenses, and the attack will apply Stun, Freeze, 
Bubble, Drag, or Flinch, the Cancel is allowed to process. Otherwise, it will be dropped.

In the case `Hit.Cancel` processes, the hurt Entity's CardActions, action queue, and movement will 
be cleared and cancelled. 

This is completely normal for Players and Navi-like enemies, and you don't have to do anything about 
it. But for enemies like viruses, which don't flinch or get their attacks interrupted by any of these 
flags in the original games, you may want to have a DefenseRule that strips the `Hit.Cancel` flag.
The `DefenseVirusBody` Defense, created by `Battle.DefenseVirusBody.new()`, will strip the `Hit.Cancel` 
flag, but any DefenseRule can.