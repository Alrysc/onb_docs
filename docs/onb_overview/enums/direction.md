# Direction

The Direction enum has values for each direction. It also has a few 
useful functions for manipulating them.

It has the following indexes:

* None
* Up
* UpRight
* Right
* DownRight
* Down
* DownLeft
* Left
* UpLeft

Additionally, the following indexes have function values:

* join
* reverse
* unit_vector

## Usage

The actual directions on Direction will mostly be used for an Entity's facing 
direction, the direction for something to move in, or the direction to pivot 
from a Tile when calling `Entity.get_tile` or `Tile.get_tile`.

```lua
-- Tile to the right of the player
local tile = player:get_tile(Direction.Right, 1)
```

It's less common to refer to exactly Left or Right, because those directions may
mean different things for player 1 and player 2. Try to keep Left/Right references 
relative.

```lua
local facing = player:get_facing()
-- Tile in front of the player
local tile = player:get_tile(facing, 1)
```

### join

`Direction.join` will return the combination of two input Directions. Up and 
Right becomes UpRight, for example.

`Direction.join` can help when using relative directions. If you want to get 
the Tile that's in front of the player and one up, for example, you can do this:

```lua
local facing = player:get_facing()
local joined_direction = Direction.join(facing, Direction.Up)
local tile = player:get_tile(joined_direction, 1)
```

Note that it is called with a period instead of a colon.

The alternative is to travel across two different Tiles, which also necessitates a 
check for `nil` on the first `get_tile` call.

### reverse

`Direction.reverse` will reverse an input Direction, returning its opposite. Left 
becomes Right, Up becomes Down, and so on.

`Direction.reverse` can help when using relative directions. If you want to get the 
Tile that's behind the user, for example, you can do this:


```lua
local facing = player:get_facing()
local reverse_facing = Direction.reverse(facing)
local tile = player:get_tile(reverse_facing, 1)
```

Note that it is called with a period instead of a colon.

Also remember that the above intention can also be done using `Entity.get_facing_away`
and skipping the reverse. Regardless, it'll be a useful tool, like when you don't have 
an Entity to get the facing direction of.

### unit_vector

`Direction.unit_vector` will return a Vector representation of an input Direction. 
Right becomes (1, 0), UpRight becomes approximately (0.707, -0.707), Left becomes (-1, 0), 
and so on.

You are unlikely to use this very often.