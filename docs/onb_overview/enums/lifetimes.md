# Lifetimes

The `Lifetimes` enum is used as a specifier for the update behavior of a 
Component. Specifically, they decide the battle state required in order 
for it to update, as well as the moment within a game frame that it will
update.

It has the following values:

* Local
* Battlestep
* Scene


## Usage

A `Lifetimes` is used when creating a Component.

```lua
local component = Battle.Component.new(player, Lifetimes.Local)

player:register_component(component)
```

When talking about a Component, I will often say that it is a Local 
Component, or a Battlestep Component, etc., indicating which `Liftimes` 
was used in its creation.

## Differences

### Local

A Local Component's `update_func` runs during an Entity's `update_func`. 
If the Entity will not update that frame, neither will the Component. 

Usually, an Entity will only update during the battle startup period
(before the Custom Screen opens) and during the turn while time is not 
frozen.

Note that an Entity's `update_func` will not be called while the Entity 
is stunned , but a Local Component will still be updated on that frame.

### Battlestep

A Battlestep Component's `update_func` runs near the end of the game frame, 
after all Entities and Tiles on the Field have updated. The `update_func` 
will only be called during the turn, while time is not frozen.

Battlestep Components are updated in tandem with Scene Components, so a 
Battlestep Component registered before a Scene Component will update first, 
and vice versa.

### Scene

A Scene Component's `update_func` runs near the end of the game frame, 
after all Entities and Tiles on the Field have updated. The `update_func` 
will be called every frame. Scene Components are the only Components that 
always update.

## Choosing

Choose the `Lifetimes` to use taking into account the effect you want to 
achieve. You should think of this in terms of when the update will happen, 
and if that timing is appropriate for the effect.

### Local vs. Battlestep

A lot of the ideas you think of that could fit a Local Component might also 
fit a Battlestep Component, and vice versa. Even if the logic may play out 
similarly, there could be subtle differences in practical behavior based 
on which you choose. Below are some examples that highlight these.

#### Example: Field Checks

You may want to use a Component to check for certain Entities, or maybe 
TileStates, on the Field. 

With a Local Component, the update may be running before some other Entities 
have updated, and before other logic during the frame has completed. By using 
a BattleStep Component instead, the update will run closer to the end of the 
frame and would be more likely to pick up on more changes that happened during 
the frame.

Say you wanted to do something similar to ColonelSoul's ability in BN5, which 
causes objects to turn into soldiers. This would require you to observe the Field, 
remove certain Entities, and spawn new ones.

With a Local Component, the soldiers would spawn on the same frame you checked 
the Field, but any objects that spawned on the same frame would not have spawned 
in time for you to catch them in a Field check. With a Battlestep Component, you 
can catch the objects that were spawned that frame, but your new spawns will not 
appear until the end of the next frame.

#### Example: HP Bug

Effects like an HP Bug could be a Local or Battlestep Component, because then 
HP wouldn't drop during Custom or time freeze. 

Recall that HP Bugs in the official games do not delete you, but stop draining 
when you are at 1 HP. Here's a scenario where it can be game-changing to do 
this using a Local or Battlestep Component, due to the difference in when 
during the frame they update:

* You are at 2 HP, and when you update this frame your HP will drop to 1.
* A Spell's update this frame will attack your Tile.

With a Local Component, you will update, drop to 1 HP, then get hit by the Spell 
and be deleted. With a Battlestep Component, you will update, get hit by the Spell 
and possibly fall to 1 HP (if you had UnderShirt installed, or the attack did 1 damage), 
and then the Component updates and does not drop HP because you are at 1 HP. You 
survive.

!!! tip "Disclaimer"
    I don't know how this scenario will end in the official games. You will need to do 
    extra research to get things like this right, if you are aiming for accuracy.

## Scene

Scene Components are not used as often, because most effects' logic should not 
run all the time. 

Somewhere you might use a Scene Component comes from a special case with Barriers.
Barriers animate during time freeze. Since the graphic should be added as a 
SpriteNode, it would only animate during time freeze if its animation update was
handled by a Scene Component's update.

Because Scene Components also update during Custom and the Barrier should not 
animate during Custom (except the first Custom screen), a few extra updates 
using other Components or Entities can be used to determine that Custom is open 
and avoid updating the animation.