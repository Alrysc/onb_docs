# Obstacle

Obstacles are non-Characters which can take damage. Unlike Characters, they 
do not display their HP, and they don't count towards any battle results.


## Creating an Obstacle

Calling `Battle.Obstacle.new(Team)` will return a new Obstacle object. Like every 
other type of Entity, it will need to be spawned on the Field before it will 
do anything.

```lua
local obstacle = Battle.Obstacle.new(team)
```

The `team` variable in the above code snippet must be a Team enum value. 

Don't forget to give it a health value after creating one, or else it will not 
be able to get hit. The first call to `Obstacle.set_health` will also set its max
health.

```lua
local obstacle = Battle.Obstacle.new(team)

-- 0
print(obstacle:get_max_health())

obstacle:set_health(200)

-- 200
print(obstacle:get_max_health())
```

## When to Use

Things like RockCube or Anubis are often called "objects", or maybe "summons" 
by Battle Network players. These are the sorts of things you would replicate 
using an Obstacle.

Their use is not limited to just those, however. Remember, an Obstacle is just 
something that can take damage that isn't a Character. You might use them for 
detecting a hit on a different Tile, or an attack that can be destroyed, like 
PropellerBomb from BN4, or CutMan's scissors which block attacks.

## Obstacle Limit

BN players may remember that certain objects they spawn have a limit. You can 
only have two RockCubes on your field before trying to spawn a third will 
destroy the first, or you might want to use Prism and BlkBomb at the same time, 
but trying to throw the BlkBomb will destroy the Prism. The limit is based on 
a sort of "type" of object, where certain objects fit into the type that allows 
2 slots, and others fit into the 1 slot type. Others still, like FlashBomb from 
BN6, can be thrown without ever affecting other field objects.

These limits do not exist and are not enforced in ONB. Modders have created 
importable scripts to add the behavior to your Obstacles, with a short an opt-in 
process.This will be talked about in another section. Until then, you can find me 
(Alrysc) on the ONB Discord server and ask about my ObstacleInfo library.

## Attacking

Obstacles are allowed to attack and deal damage in the same way as Spells. See 
similar sections on the [Spells page](./spell.md).

## Type Checking

You can test if an Entity is an Obstacle. The `Battle.Obstacle.from(entity)` function 
call will return an Obstacle cast of the input Entity if it was an Obstacle, or `nil` 
if it was not.

```lua
spell.attack_func = function(self, other)
    local ob = Battle.Obstacle.from(other)
    if ob ~= nil then 
        print("Damaged an Obstacle")
    else
        print("Damaged a different type of Entity")
    end
end
```