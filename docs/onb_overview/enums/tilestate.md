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
    In v2.0, a Character which has AirShoes will also prevent the Tile from breaking.
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
    In v2.0, having AirShoes will also prevent the movement. This will not be the case in 
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
Tile. Then, the TileState will revert to `TileState.Normal`.

!!! tip "Grass Burn"
    In the official games, a Fire element attack will remove Grass even if no Entity is hit. 
    This is not the case in v2.0, but will be in future versions.

!!! tip "Grass Heal"
    In the official games, WoodBody Characters heal slowly over time while standing on a Grass 
    Tile. This does not happen in v2.0, but will in future versions.

### Hidden

A Tile with `TileState.Hidden` will not be drawn. You can see this in the official games during 
the fights with Duo, Nebula Gray, and Falzar.

Hidden Tiles are not walkable. They cannot change Team, and a Tile with `TileState.Hidden` cannot 
change states to any other state. They will be Hidden forever. They also cannot be highlighted.

### Holy

Entities standing on Tiles with the `TileState.Holy` state will take reduced damage from attacks.

Damage is halved and rounded down after all DefenseRules have run their `can_block_func`.

!!! tip "Damage Rounding and Impact"
    In v2.0, the round down means that 1 damage attacks will become 0 damage, which is 
    not BN behavior. This will be changed in a later version.

    Typically, attacks that lack the `Hit.Impact` flag will not have their damage reduced by 
    Holy Tiles. In v2.0, they will still have their damage reduced.

!!! tip "'Holy Aura' Strategy"
    A well-known strategy in the official games involves using a LifeAura combined with a 
    Holy panel to require a very high damage value to destroy the Aura. This behavior will 
    typically not be seen in ONB mods at this time because of the timing of damage reduction
    from Holy Tiles.

### Ice

Tiles with the `TileState.Ice` state will move any Entity when they step onto it in the same 
direction that they were moving in. For example, if the Entity moved to the right to step 
onto it, they would continue sliding right. The Entity will continue to slide until they 
land on a Tile with a different state, or the next Tile is inaccessible according to their 
`can_move_to_func`.

Entities that have FloatShoes or are AquaBody will not slide.

The slide is a MoveEvent that takes 4 frames to complete.

If an Entity is hit by an Aqua attack while on ice, it will be frozen. 

!!! tip "Elec Damage"
    In v2.0, Ice Tiles grant bonus damage to Elec attacks in the same way Grass does to 
    Fire. This is behavior from games before BN6, and will be removed in future versions.

### Lava

A Character or Obstacle that stands on a Tile with the `TileState.Lava` state will be damaged, 
and then the Tile will revert to `TileState.Normal`. 

If the Entity has FloatShoes, the Lava will not attack it. Because it does not attack, it also 
does not revert.

!!! tip "FireBody"
    In the official games, being FireBody would also avoid Lava attacks. This is not the 
    case in v2.0, but will be in the future.

The attack does 50 Null damage with the Hit.Flash flag.

!!! tip "Fire Lava"
    In the official games, Lava tends to do Fire damage. This is not the case in v2.0, 
    but may change in the future.

### Normal

Tiles in the `TileState.Normal` TileState have no special effects.

### Poison

Tiles in the `TileState.Poison` TileState will damage Characters standing on them. This 
uses a global counter, which counts for all Tiles even while in different TileStates. 
Every 7 frames on this counter, damage will be dealt.

An Entity with Floatshoes will not be attacked.

The attack does 1 Null damage with the Hit.Pierce flag.

!!! tip "Damage Timing"
    The damage timing in v2.0 does not match the official games. This will be made more 
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
    In v2.0, the timing may be different from the original games. It also uses the same 
    time for each Team's Volcano Tiles, which will change in future versions. 

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

#### Making Holes

Some attacks and effects might skip cracking a Tile and go straight to breaking it. 
These effects often crack a Tile if it couldn't break, for example if something was 
standing on the Tile. To cover for this case, you can set the state to Cracked, and then 
on the next line set it to Broken.