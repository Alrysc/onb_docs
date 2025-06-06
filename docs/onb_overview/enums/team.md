# Team

All Entities have a Team, which decides what they can be attacked by 
and what they can attack.

There are three indexes:

* Red
* Blue
* Other

## Usage

You will usually pass a Team in when creating an Entity.

```lua
local spell = Battle.Spell.new(Team.Other)
```

It's uncommon to refer to a direct value. Similar to Direction, you 
will usually want to use a Team relative to another Entity's Team. For 
example:

```lua
function create_spell(player)
    local spell = Battle.Spell.new(player:get_team())
    spell:set_facing(player:get_facing())
end
```

This function will create a Spell that can hit `player`'s enemies (Characters 
and Obstacles not on the same Team).

Another common use is to find Entities which are not on a certain Team.

```lua
local field = player:get_field()
local team = player:get_team()

local all_enemies = field:find_characters(function(ent)
    return ent:get_team() ~= team
end)
```