# TileState

`TileState` describes the state a Tile is in, and also affects its behavior 
and other Entities' and attacks' interactions with it.

The following TileStates are available:

* Broken
* Cracked
* DirectionDown
* DirectionLeft
* DirectionRight
* DirectionUp
* Empty
* Grass
* Hidden
* Holy
* Ice
* Lava
* Normal
* Poison
* Volcano
* Sea
* Sand
* Metal

The default TileState is `TileState.Normal`.

## Usage

You can change a Tile's state using `Tile.set_state`. 

```lua
local tile = player:get_current_tile()
tile:set_state(TileState.Ice)
```
You can inspect the current state of a Tile using `Tile.get_state`.

```lua
print(player:get_current_tile():get_state() == TileState.Normal)
```

Edge Tiles cannot have their state set. 

### Broken

A Tile with the `TileState.Broken` state cannot be stepped on by Players 
who do not have AirShoes. 

A Broken Tile will revert to `TileState.Normal` after 10 seconds (600 frames). 
Setting `TileState.Broken` on a Tile that is already broken will refresh its timer.

Trying to set `TileState.Broken` on a Tile which has Obstacles or Characters on it 
will fail. 


### Cracked

A Tile with the `TileState.Cracked` state will change to `TileState.Broken` when 
a Character which does not have FloatShoes steps off it.

!!! warning "AirShoes"
    In v2.1, a Character which has AirShoes will also prevent the Tile from breaking.
    This will not be the case in future versions.

!!! tip "Missing Info"
    Obstacles may be affected as well. This notice will be removed once I have confirmed.

A Tile that is in the `TileState.Broken` or `TileState.Empty` states cannot be set to 
Cracked.

### Directions

Tiles with `TileState.DirectionDown`, `TileState.DirectionLeft`, `TileState.DirectionRight`,
or `TileState.DirectionUp` states will move an Obstacle or Character in that direction when it 
steps onto it. Having FloatShoes will exempt the Entity from this movement.

!!! warning "AirShoes"
    In v2.1, having AirShoes will also prevent the movement. This will not be the case in 
    future versions.

The movement is an Involuntary MoveEvent that completes in 3 frames.

These types of Tiles might also be called "conveyor panels" by some BN players. They're created 
by ComingRoad and GoingRoad in BN6.

### Empty

Tiles with `TileState.Empty` are simliar to `TileState.Broken` in effect, with the exception 
that it does not revert to `TileState.Normal`. 

These types of Tiles might also be called "perma-holes" by some BN players, because they are
always a hole. Traditionally, these Tiles cannot be taken by the other Team, and they can never 
be set to a different TileState. This is not the case in ONB. If you want to make your mods 
feel more like BN, you should often check for this TileState.

### Grass

Tiles with `TileState.Grass` will add damage after a Fire element attack hits an Entity on that 
Tile. If a Fire element attack is made on the Tile, the Tile will revert to `TileState.Normal`, 
regardless of whether or not the attack dealt damage. 

A WoodBody Character will heal slowly over time while standing on a Grass Tile. The speed of
healing is 1 HP per 20 frames, or 1 HP per 180 frames while the Character has less than 10 HP.

### Hidden

A Tile with `TileState.Hidden` will not be drawn. You can see this in the official games during 
the fights with Duo, Nebula Gray, and Falzar.

Hidden Tiles are not walkable. They cannot change Team, and a Tile with `TileState.Hidden` cannot 
change states to any other state. They will be Hidden forever. They also cannot be highlighted.

### Holy

Entities standing on Tiles with the `TileState.Holy` state will take reduced damage from attacks.

Damage is halved and rounded up after all DefenseRules have run their `can_block_func`.

!!! tip "Damage and Impact"
    Typically, attacks that lack the `Hit.Impact` flag will not have their damage reduced by 
    Holy Tiles. In v2.1, they will still have their damage reduced.

!!! tip "'Holy Aura' Strategy"
    A well-known strategy in the official games involves using a LifeAura combined with a 
    Holy panel to require a very high damage value to destroy the Aura. This behavior will 
    typically not be seen in ONB mods as of v2.1 because of the timing of damage reduction
    from Holy Tiles.

### Ice

Tiles with the `TileState.Ice` state will move any Entity when they step onto it in the same 
direction that they were moving in. For example, if the Entity moved to the right to step 
onto it, they would continue sliding right. The Entity will continue to slide until they 
land on a Tile with a different state, or the next Tile is inaccessible according to their 
`can_move_to_func`.

Entities that have FloatShoes or are AquaBody will not slide.

The slide is a MoveEvent that takes 4 frames to complete.

If an attack hits an Ice Tile, it will have `Hit.Freeze` added to it.

### Lava

A Character or Obstacle that stands on a Tile with the `TileState.Lava` state will be damaged, 
and then the Tile will revert to `TileState.Normal`. 

If the Entity has FloatShoes or is FireBody, the Lava will not attack it. Because it does not attack, 
it also does not revert.

The attack does 50 Fire damage with the Hit.Flash flag.

!!! tip "Obstacles on Lava"
    In v2.1, Obstacles have FloatShoes by default. This means they will not be hit by Lava unless 
    they have their FloatShoes removed. In the future, Obstacles will be able to be hit by Lava by 
    default.

### Normal

Tiles in the `TileState.Normal` TileState have no special effects.

### Poison

Tiles in the `TileState.Poison` TileState will damage Characters standing on them. This 
uses a global counter, which counts for all Tiles even while in different TileStates. 
Every 7 frames on this counter, damage will be dealt.

An Entity with Floatshoes will not be attacked.

The attack does 1 Null damage with the Hit.Pierce flag.

!!! tip "Damage Timing"
    The damage timing in v2.1 does not match the official games. This will be made more 
    accurate in future versions.

### Volcano

Tiles in the `TileState.Volcano` TileState will attack once in a while on a global 
timer.

Every 2 seconds (120f), the Volcano will change animation states to flicker just before 
erupting. It will flicker for about 33f before attacking, at which point the 2 second timer
restarts and the attack is spawned.

The attack lasts as long as the erupted lava is visible, which is approximately 38f. It 
does 50 Null damage, with the Hit.Impact and Hit.Flinch flags.

!!! tip "Volcano Timing"
    In v2.1, the timing may be different from the original games. It also uses the same 
    time for each Team's Volcano Tiles, which will change in future versions. 

### Sea

Tiles in the `TileState.Sea` state will cause Characters that step onto them to be afflicted 
with the Root status for 20 frames. If the Entity is AquaBody or has FloatShoes, they will 
not be affected by this. Obstacles will also not be affected by this.

Elec element attacks on a Sea Tile will do bonus damage. 

Cards with the Aqua element will gain +30 damage if used on a Sea Tile. Afterwards, the 
Tile will revert to `TileState.Normal`.

FireBody Characters standing on Sea Tiles will take damage over time, as if they were standing 
on a Poison Tile. The damage is 1 HP per 7 frames, and can be avoided if the Entity has 
FloatShoes.

Sea Tiles expire and revert to `TileState.Normal` after 16 seconds (960 frames).

!!! tip "ToadSoul"
    Contrary to popular belief, there is no innate ability for AquaBody Entities to submerge 
    in Sea Tiles, like ToadSoul does. This is a special ability of ToadSoul.

### Sand

Tiles in the `TileState.Sand` state will cause Characters that step onto them to be afflicted 
by the Root status for 20 frames. Obstacles are immune to this.

!!! "Wind Bonus Damage"
    In v2.1, a Wind attack that hits a Sand Tile will revert the Tile to `TileState.Normal`,
    as well as deal bonus damage. The bonus damage may be incorrectly added, and should not 
    apply to Sand, but rather the SandPit from BN4. This will be corrected in a later version 
    if so.

### Metal

Tiles in the `TileState.Metal` state cannot be Cracked or Borken. This means calls like 
`tile:set_state(TileState.Cracked)` or `tile:set_state(TileState.Broken)` will do 
nothing if the current state is `TileState.Metal`. 

See [Breaking Metal Tiles](#breaking-metal-tiles) for cases where you may want to Crack 
a Tile that is Metal anyway, and official examples where this happens.

## Usage Tips

### Walkable Tiles

It's common to check for certain states when creating attacks that travel along 
the ground and would make sense to be stopped by holes, such as the Shockwave 
or WaveArm chips from the official games, or when creating enemies or Obstacles 
that should avoid holes. 

Because there are serveral other similar states, you should instead check `Tile.is_walkable()`.
It will return `false` if the Tile's TileState is Hidden, Broken, or Empty, or if 
the Tile is an edge Tile.

### Breaking Tiles

Common behavior of attacks that crack Tiles is to break a Tile if it's already cracked. 
This isn't always the case, but it does happen sometimes. For example, the GutsMan 
chip in BN3 will crack panels as shockwaves pass over them, but it will break panels 
that are already cracked. The ElecCross Beast chip charge in BN6 will crack panels, but 
will not break cracked panels.

If you want to break cracked panels, check if the Tile is already cracked, and then 
break it if so.

ONB will reset the broken Tile timer if you set the state again, which is unlike effects 
seen in the games. Remember to check if the Tile was already broken before trying, so you 
don't reset the timer.

#### Breaking Metal Tiles

The Metal TileState prevents cracking and breaking a Tile. This means calls like 
`tile:set_state(TileState.Cracked)` or `tile:set_state(TileState.Broken)` will do 
nothing if the current state is `TileState.Metal`. 

Some effects intend to *overwrite* Tiles, which is different flavor from *breaking* 
them. For example, in BN3, MetalMan, GutsMan, and BassGS don't affect Metal Tiles, 
but Geddon1 and Geddon2 do. Geddon1 cracks all Tiles, including Metal Tiles; Geddon2 
turns all Tiles into holes, but Metal Tiles are instead cracked. If you intend for 
this sort of behavior, you can change the Tile to `TileState.Normal` and then crack 
and break it, or only crack if it was Metal beforehand. 

#### Making Holes

Some attacks and effects might skip cracking a Tile and go straight to breaking it. 
These effects often crack a Tile if it couldn't break, for example if something was 
standing on the Tile. To cover for this case, you can set the state to Cracked, and then 
on the next line set it to Broken.
