# Field

The Field is what contains all battle elements. It manages them and updates them. 
All spawned Entities exist on the Field. 

You cannot create a Field object. There will be one for the battle, which is 
typically accessible through an Entity's `Entity.get_field` function.

```lua
local field = player:get_field()
```

You will go through the Field for spawning, finding Entities or Tiles, and 
more.

## Composition

Visually, the battlefield contains 18 Tiles, arranged in a 6x3 array. In reality, 
the Field has an additional 22 Tiles which are invisible, called edge Tiles. These 
are invisible Tiles which go along the perimeter of the stage. The left and right 
edge Tile columns are off the screen.

These extra Tiles mean that the top left of the typical playable area has coordinates 
`1,1` instead of `0,0`, and the bottom right is `6,3`.

Unless the Mob package overrides anything, the default state of the Field will 
have 9 Tiles on Red Team, 9 Tiles on Blue Team, and all Tiles except edge Tiles 
will have the `TileState.Normal` state.

## Perspective Flip

In PvP, as a result of being on `Team.Blue`, Player 2 sees the Field as if it was 
flipped. The colors of Tiles are flipped, so that Tiles that are `Team.Blue` appear 
red, and `Team.Red` appear Blue. Their character will look like they're facing right, 
even though `player:get_facing()` will return `Direction.Left`. The top left of the 
Field that they see is `6,1` instead of `1,1`.

This behavior is just like the official games. Because it is purely visual, this should 
not affect any of your logic.

In many places in these docs, I say to use relative Directions more often that absolute, 
such as using `Entity.get_facing` when you want Left or Right. If you don't, you may see 
certain things fall apart when you enter PvP and you're Player 2, in the same way you 
might if you turn your Player around, or have an enemy who is facing Left perform an action.

## Spawning

Entities can be spawned on the Field. This will make them "active" and part of the battle. 
They will be updated and can be found on the Field.

### Spawn Methods

There are two ways to spawn an Entity: with a Tile, or with Tile coordinates. 
Both go through the `Field.spawn` function, using different parameters.

`Field.spawn` returns an [EntityStatus](./enums/entitystatus.md). See that page 
for more details.

#### With Tile

If you have a Tile, you can spawn an Entity on it by passing in the Entity to 
spawn and the Tile to spawn it on.

```lua
local field = player:get_field()
local tile = player:get_current_tile()
local artifact = Battle.Artifact.new()

field:spawn(artifact, tile)
```

#### With Coordinates

If you have coordinates, you can use them to spawn an Entity on the Tile at those 
coordinates. Pass in the Entity to spawn, the X coordinate, and the Y coordinate.

```lua
local field = player:get_field()
local artifact = Battle.Artifact.new()

local x = player:get_current_tile():x()
local y = 1

field:spawn(artifact, x, y)
```

Be wary of using constant numbers, particularly for the X coordinate, as they may not 
always work as you want. For example, if you wanted to spawn something in the upper 
left of your area, you might use `1,1`, but someone on the opposing Team using the same 
logic would instead want `6,1`.

If the coordinates given do not match to an existing Tile, the Entity will not be 
spawned.

### Spawning a Spawned Entity

You should not call `Field.spawn` on an Entity which has already been spawned. This 
has odd behavior that can result in unexpected draw position, update order, and other 
effects. This comes from the fact that the Entity exists on two different Tiles at the 
same time.

An Entity's `Entity.on_spawn_func` will not play a second time.


## Entity Search

The Field can be used to find Entities matching a query.

You can compile a list of Entities on the Field which meet certain criteria. There 
are several functions that do this:

1. `Field.find_entities`
2. `Field.find_obstacles`
3. `Field.find_characters`
4. `Field.find_nearest_characters`

These functions each act and are used in similar ways. They each require a function to be 
passed in, which I will refer to as the "query function". The query function is used in the 
same way for each of these Field functions. Each Entity related to the name of the function 
is passed into the query function, one by one. If the query function returns `true` for an 
Entity, it will be added to a list that is finally returned at the end of the Field function 
call. Otherwise, it will be excluded from the final result.

Tiles are searched row by row, starting in the top left. **Edge tiles are not searched!**

!!! tip "Unsearchable Entities"
    In the future, Entities that have their hitboxes toggled off will not be tested 
    in any of these functions.

### find_entities

`Field.find_entities` requires a function to be passed in, which will be used as the query function.
The query function will be called once for every Entity on the Field. When the query function returns 
`true`, the Entity which it was run with will be added to a table to be returned once all Entities 
have been tested.

For example, say there are five Entities on the Field with the following names:

* "Mettaur", a Character
* "Mettaur", a Character
* "MegaMan", a Player
* "", a Spell
* "TimeBomb", an Obstacle

```lua
local field = player:get_field()

local function mettaur_query(entity)
    print("Testing Entity "..entity:get_name())
    return entity:get_name() == "Mettaur"
end

local mettaurs = field:find_entities(mettaur_query)

-- 2
print(#mettaurs)
```
With the above conditions and code, the following will be printed:

```
Testing Entity Mettaur
Testing Entity Mettaur
Testing Entity MegaMan
Testing Entity 
Testing Entity TimeBomb
2
```
The order may be different depending on the Tile each Entity is on.

`mettaur_query` was called 5 times, once for each Entity.

Note that `find_entities` is the only one in this set of functions that may 
test Spells and Artifacts. It's not common to want to search for these, and 
it is difficult to distinguish the two in Lua.

### find_characters

`Field.find_characters` works just like `Field.find_entities`, but only Characters 
will be tested.

Say there are five Entities on the Field with the following names:

* "Mettaur", a Character
* "Mettaur", a Character
* "MegaMan", a Player
* "", a Spell
* "TimeBomb", an Obstacle

```lua
local field = player:get_field()

local function mettaur_query(character)
    print("Testing Character "..character:get_name())
    return character:get_name() == "Mettaur"
end

local mettaurs = field:find_characters(mettaur_query)

-- 2
print(#mettaurs)
```
With the above conditions and code, the following will be printed:

```
Testing Character Mettaur
Testing Character Mettaur
Testing Character MegaMan
2
```
The order may be different depending on the Tile each Entity is on.

Remember that Players *are* Characters, so MegaMan appears here. The Spell and 
Obstacle are never tested at all.

### find_nearest_characters

`Field.find_nearest_characters` works just like `Field.find_characters`, but you 
also will pass in an Entity to pivot from as the first parameter. The returned table 
will be sorted based on each passing Character's distance to the pivot Entity.

The distance is determined based on `Tile.get_distance`. Characters are tested by 
the query function in the sorted order, so the closest will be used to call the 
query function first. 

The Characters are gathered in the same order `find_characters` tests them. 
The first Character in this order who has a given distance to the pivot Entity will appear 
earlier in the table compared to other Characters with the same distance. See below 
for an example:

Say there are four Entities on the Field with the following names and Tile coordinates:

* "Mettaur", a Character on Tile `4,1`
* "Mettaur2", a Character on Tile `5,2`
* "MegaMan", a Player on Tile `2,2`
* "Mettaur3", a Character on Tile `4,2`

```lua
local field = player:get_field()
local team = player:get_team()

local function enemy_filter(c)
    print("Testing Character "..c:get_name())
    local is_alive = not c:is_deleted() and c:get_health() > 0
    return is_alive and c:get_team() ~= team
end

local enemies = field:find_nearest_characters(player, enemy_filter)

local nearest_enemy = enemies[1]

if nearest_enemy then 
    -- Mettaur
    print(nearest_enemy:get_name())
else
    print("No enemies found")
end
```

```
Testing Character MegaMan
Testing Character Mettaur3
Testing Character Mettaur
Testing Character Mettaur2
Mettaur3
```
This print order is exact, based on the given Tile positions. 

Notice that `Mettaur` appears before `Mettaur2`, despite their Tiles both being a distance of 
3 away from MegaMan's Tile. Mettaur3 is the closest, because MegaMan was on the same team as 
`player`, and so was excluded from the returned table.

!!! bug "Wrong Order"
    In v2.0, `Field.find_nearest_characters` does not return ordered entries and 
    will instead be identical to `Field.find_characters`. This is fixed in later 
    versions.

### find_obstacles

`Field.find_obstacles` works just like `Field.find_entities`, but only Obstacles 
will be tested.

Say there are five Entities on the Field with the following names:

* "Mettaur", a Character
* "MegaMan", a Player
* "", a Spell
* "TimeBomb", an Obstacle
* "RockCube", an Obstacle

```lua
local field = player:get_field()

local function cube_query(obstacle)
    print("Testing Obstacle "..obstacle:get_name())
    return obstacle:get_name() == "RockCube"
end

local cubes = field:find_obstacles(cube_query)

-- 1
print(#cubes)
```
With the above conditions and code, the following will be printed:

```
Testing Obstacle TimeBomb
Testing Obstacle RockCube
1
```
The order may be different depending on the Tile each Entity is on.

### Common Search Requirements

There are a few checks that are common when searching for Entities.

#### Not Deleted

If an Entity was deleted in the same frame that you make your search, that Entity may be 
given to your query function and added to the final returned table. Because deleted Entities 
can cause issues when acted on, and generally you wouldn't want to do anything to them anyway, 
you will usually want to exclude them from your searches.

```lua
-- Return true if entity is not deleted.
local function query_all(entity)
    return not entity:is_deleted()
end

-- Table of all Entities which are not deleted
local all = field:find_entities(query_all)
```

#### No Health

It can also be common to exclude Entities which have 0 health. This can help to avoid Entities 
which are currently dying, but have not yet been removed. You might do this when replicating 
a chip like ProtoMan, who will not target enemies which are in the middle of dying.

```lua
-- Return true if entity is not deleted and has more than 0 health
local function query_all(entity)
    return not entity:is_deleted() and entity:get_health() > 0
end

-- Table of all Entities which are not deleted and have HP > 0
local all = field:find_entities(query_all)
```

#### Base On Team

You may want to make a chip effect that targets only Characters that are not on the user's Team, 
like many attacks from the official games would do. You can check against a Team in your query.

```lua
local field = player:get_field()
local team = player:get_team()

local function all_enemies(character)
    local alive = not character:is_deleted() and character:get_health() > 0
    return alive and character:get_team() ~= team
end

local enemies = field:find_characters(all_enemies)
```

Remember to take `Team.Other` into consideration. By checking if they are or are not on the 
player's Team, this accounts for `Team.Other` and the opposite Team.

#### Exclude User

You might want some effect to target all Characters, regardless of Team, as long as they are 
not the user of the effect. You can do this by comparing IDs.

```lua
local field = user:get_field()
local id = user:get_id()

local all_others = field:find_characters(user, function(c)
    local alive = not character:is_deleted() and character:get_health() > 0

    if not alive then 
        return false 
    end

    return c:get_id() ~= id
end)
```

When using a time freeze CardAction, the `user`'s ID may not be the same as the CardAction's 
Actor's ID. If you notice this, you can try checking both IDs. A trick I like to use is to 
make use of the fact that Lua's tables contain `nil` at unfilled indexes, and `nil` is falsey.
This means you can store `true` at an index matching each ID and check that against the Entity 
ID.

```lua
function card_create_action(user, props)
    local action = -- omitted

    action.execute_func = function(self, actor)
        local field = user:get_field()
        local excluded_ids = {
            [user:get_id()] = true,
            [actor:get_id()] = true
        }

        local all_except_user = field:find_characters(function(c)
            local alive = not c:is_deleted() and c:get_health() > 0
            return alive and not excluded_ids[c:get_id()]
        end)
    end

    return action
end
```

### No Results

In most use cases, you will want to check for and handle the case where the table returned by these
functions is empty. Because it's a table, you can easily check emptiness as normal. 

```lua
local target = field:find_characters(function(c)
    return not c:is_deleted() and c:get_health() > 200
end)

-- No first entry, so it's empty
if targets[1] == nil then 
    
end

-- Has 0 entries, so it's empty
if #targets == 0 then 

end
```

## Tile Search

You can use `Field.find_tiles` to gather Tiles. It works in a similar way to `Field.find_entities`.

You must pass in a function to use as a query, which takes a Tile as input. This query function 
will be called for every Tile on the Field, starting from `0,0` and working down to `7,4`, row by
row. When the query function returns `true`, the Tile that was passed in will be added to a table that
will be returned once all Tiles have been checked against the query function.

Unlike the `Field.find_` functions, edge Tiles are checked.

```lua
local field = player:get_field()

local function all_tile_query(tile)
    return true
end

local all_tiles = field:find_tiles(all_tile_query)

-- 40
print(#all_tiles)
```

### Common Searches

#### Exclude Edges

Since you usually want to ignore edge Tiles, it can be convenient to skip over edges 
when gathering Tiles.

```lua
local function no_edges(tile)
    return not tile:is_edge()
end


local non_edges = field:find_tiles(no_edges)

-- 18
print(#non_edges)
```

#### Based On Team

Some effects from the official games target only Tiles on the user's Team, such as Sanctuary.
Others target only enemy Tiles, like Meteors. You can compare the Tile's Team to the one 
to target or not target.


```lua
local field = player:get_field()
local team = player:get_team()

local function all_enemy_tiles(tile)
    return not tile:is_edge() and tile:get_team() ~= team
end

local function all_ally_tiles(tile)
    return not tile:is_edge() and tile:get_team() == team
end

local enemy_tiles = field:find_tiles(all_enemy_tiles)
local ally_tiles = field:find_tiles(all_ally_tiles)
```

Remember to take `Team.Other` into consideration. By checking if they are or are not on the 
player's Team, this accounts for `Team.Other` and the opposite Team.
