# Character

Characters can take damage and use CardActions. They display their HP below their
sprite. They also have a "Rank". Deleting them affects final battle results.

## Creating a Character

Calling `Battle.Obstacle.new(Team, Rank)` will return a new Character object. Like 
every other type of Entity, it will need to be spawned on the Field before it will 
do anything.

```lua
local character = Battle.Character.new(team, rank)
```

The `team` variable in the above code snippet must be a Team enum value, and `rank` 
must be a Rank enum value. 

You will usually not directly call the function to create a Character, since it 
will be handled for you in Mob packages. 

### From Character ID

There is no loadable mod package specifically for Characters, but you can still define
a Character and give it a unique package ID. How to do this will be covered in a 
different section.

If you know a Character's ID, you can create that Character, calling its `package_init`
in the process, using that ID.

```lua
local character = Battle.Character.from_package(id, team, rank)
```

!!! warning "Mod Sync"
    When mods sync for PvP, a library that defines the Character with that ID might 
    not have been shared Be wary of using this in a context where the function could be 
    called in PvP.


## When to Use

You will typically use Characters when you want to create a more character-like 
Entity, something that has movement and attack patterns. Enemies are the most 
common Character uses.


### Character or Obstacle

When deciding whether to use Characters or Obstacles, keep the following in mind:

1. Characters can be counterhit, granting FullSynchro and a bonus towards Busting Level
2. Characters can be double- or triple-deleted, putting text on the screen and granting 
a bonus to Busting Level
3. Characters are made to be able to easily respond to Rank, to allow you to set different 
stats, colors, behavior, etc. for different versions of the same Character (like V1 and V2
bosses)

## Type Checking

You can test if an Entity is a Character. The `Battle.Character.from(entity)` function 
call will return a Character cast of the input Entity if it was a Character, or `nil` 
if it was not.

```lua
spell.attack_func = function(self, other)
    local p = Battle.Character.from(other)
    if p ~= nil then 
        print("Damaged a Character")
    else
        print("Damaged a different type of Character")
    end
end
```

Remember that a Player *is a* Character, so `Battle.Character.from(entity)` when 
`entity` is a Player will return a Character object, not nil.