# Entity Class

The base class of *things* that appear on the field during battle. The player character, 
an explosion effect, a pillar of fire, an enemy—all of these are types of Entity.

In the below sections, you will read about each type of Entity in the engine:

* Artifact
* Spell
* Obstacle
* Character
* Player

Each of these types should be used in different scenarios, so be sure to understand 
each one and use the most appropriate. 

## "Scripted" Version

I left out some types of Entity from the above list. For each Entity type (and most non-Entity 
types), the engine has a second, very similar class:

* ScriptedArtifact
* ScriptedSpell
* ScriptedObstacle
* ScriptedCharacter
* ScriptedPlayer

For the most part, there is not much difference between each class and its "Scripted" 
counterpart. In these pages, I usually don't make the distinction and use them interchangeably,
but keep an eye out for when I specifically mention the Scripted versions. They sometimes 
have additional functionality and functions.

Generally, when you *create* an instance of the class, it will be a Scripted version.
When you are *given* an instance of the class, such as through the return value of an 
engine function, it will not be. When you define callbacks on the class, the `self` 
passed in will be the Scripted version, seen in the below [callbacks](#callbacks) section.

When in doubt, use `print` to check the type. The console will tell you if it's a 
Scripted version or not.

## Is-A Relationship

In object oriented programming, you will often see a base class that other classes 
inherit from. This means that those classes, those types of objects, have some things 
in common. 

An Artifact *is an* Entity. 
A Spell *is an* Entity.
An Obstacle *is an* Entity.
A Character *is an* Entity.
A Player *is an* Entity.
They all share traits with Entity.

For descendants of Entity, this means they have access to many of the same functionality. 
Everything outlined on this page, and more, applies to every other type of Entity.

I will often be general in wording and say something affects Entities, or something 
searches for Entities. You will also see this wording in the code. Know that that means 
the function, code, logic, or intent applies to all types of Entity.

Look out for this phrasing as you read.

## ID

The engine keeps a running tally of created Entities. Whenever a new Entity is created, 
it's assigned a unique ID in sequence. This ID can be used to identify the Entity without 
storing a reference to the Entity, and is required for a few functions.

```lua
local artifact = Battle.Artifact.new()
local spell = Battle.Spell.new(Team.Red)

local id_1 = artifact:get_id()
local id_2 = spell:get_id()

print(id_1)
-- This number will be 1 higher than id_1
print(id_2)
```

This can almost be used to determine what Entity is older, except that the ID is divorced 
from spawn order. An Entity with an older ID could be spawned before after with a newer ID.
Modders are encouraged to spawn their Entities in the same frame they are created, both 
to ensure no resources are wasted and to try to keep IDs in a chronological order.

## Accessible Class Members

Entities have their own, or store a reference to, some other engine class objects. 
Below are functions related to these.

Each of these classes will have their own page. Be sure to check them for what 
you can do with these objects.

### Field

An Entity has a Field that they are on. 

```lua
local field = ent:get_field()
```

`get_field` will fail if the Entity has not been spawned.

### Tile

An Entity has a single Tile on the Field that it stands on at a time.

```lua
local tile = ent:get_current_tile()
tile = ent:get_tile()
tile = ent:get_tile(Direction.Right, 1)
```

The `tile` returned from these functions can be `nil` if the Entity has 
not been spawned, or if the Entity is not on the Field. (Being absent from 
the field is unusual, so you will likely not have to code around it. But be 
aware of it.)

### HitProps

An Entity has HitProps.

```lua
local props = ent:copy_hit_props()
ent:set_hit_props(props)
```

Only Spells and Obstacles make relevant use of HitProps.

### Sprite

An Entity has a Sprite.

```lua
local sprite = ent:sprite()
```

### Animation

An Entity has an Animation.

```lua
local anim = ent:get_animation()
```

## Other Important Values

There are many other handy fields on Entity. 

### Facing

Every Entity is *facing* either Left or Right. Its Sprite will be drawn facing left 
or right based on this. An Entity's facing is a [Direction enum](../enums/direction.md) 
value, but only `Direction.Right` and `Direction.Left` are valid.

```lua
local facing = player:get_facing()
print("Facing right?", facing == Direction.Right)
```

You can also get the opposite of an Entity's facing using `Entity.get_facing_away`.

```lua
local facing_away = player:get_facing_away()
print("Not facing right?", facing_away == Direction.Right)
```

Most importantly, you can set an Entity's facing.

```lua
spell:set_facing(player:get_facing())
```

An Entity can only face Left or Right. Others values are ignored.

#### Default Facing

When an Entity is created, it has no facing. You should set its facing. If you don't, it 
will be given one when it is spawned, and it may not be what you wanted. 

If an Entity is spawned without having a facing set, its facing will be set to match the 
facing of the Tile it was spawned on. Tiles on the left side of the field have a facing of 
Direction.Right, and Tiles on the right side of the Field have a facing of Direction.Left.

Essentially, their facing will be set so that they face the center of the screen.

### Team

Entities have a Team, which is a [Team enum](../enums/team.md). For Entities which can 
be hit, this determines what attacks can hit them.

```lua
local team = player:get_team()
print("On Red Team?", team == Team.Red)
```

You can set an Entity's Team with `Entity.set_team`, which takes a Team as input.

```lua
local artifact = Battle.Artifact.new()
artifact:set_team(player:get_team())
```

You won't do this often, because you will give most Entities a Team when you 
create them.

!!! warning "Changing Player Teams"
    You will usually not want to change an Entity's Team after it has spawned. For 
    Players, this is even more true. A battle ends when one Team has no members left, 
    so swapping the Player's team might end the battle if doing so causes their old 
    Team to be empty.

#### Default Team

Most Entities are given a Team when you create them, but not all. An Artifact will 
default to `Team.Other`. Characters and Players created by a Mob package will default 
to the Team of the Tile they spawn on.

### Element

Every Entity has an Element, which is an [Element enum](../enums/element.md) value. 
See the page on Element for their effects.

You can set an Entity's Element with `Entity.set_element`, and fetch it with 
`Entity.get_element`.

```lua
player:set_element(Element.Fire)
local element = player:get_element()
-- true
print(element == Element.Fire)
```

By default, an Entity's Element is `Element.None`.

## Callbacks

Entities have some indexes which are used as callback functions.
They are often `nil` by default, but can be set to a function which the 
engine will call under certain conditions.

Many callbacks pass `self` in. Expect the same type that the Entity on the 
lefthand side of the `.` is, which may mean the Scripted variants.

Many of these callbacks only exist and can be set on the Scripted class 
versions.

### update_func

An Entity's `update_func` takes the Entity as a parameter. It will be 
called no more than once during a frame, and will be called alongside the 
Entity's update. 

It will not be called in a frame if the Entity will not update during that frame. 

```lua
ent.update_func = function(self)
    print("Update")
end
```

#### Delta Time

The update_func actually has one more input, a delta time `dt`, in milliseconds, 
since the last frame. It will nearly always be equal to `1/60`. I omit it here 
for two reasons:

1. It will be removed in the next engine update.
2. It encourages you to make logic that is not based on frame numbers and that 
may be vulnerable to floating point imprecision.

For point 2, the latter reason may be a non-issue in almost all cases. But it's 
more correct to use frame numbers as a basis for logic in an engine that is precisely 
based on frame timing. You are less likely to regret it.

If you need to count or time something, remember that this callback will run once 
every frame.

```lua
spell.delay = 5
spell.update_func = function(self)
    if self.delay == 0 then
        print("Did nothing for 5 frames")
    end

    self.delay = self.delay - 1
end
```

### on_spawn_func

The `on_spawn_func` takes the Entity as a parameter. The engine will call it when 
the Entity is successfully spawned. It will run only once in an Entity's lifetime.

```lua
ent.on_spawn_func = function(self)
    print("Spawned entity with ID "..self:get_id())
end
```

!!! note "Fact Check Needed"

    This notice will be removed once I check if it can run for an Entity which failed 
    to spawn.

### battle_start_func

The `battle_start_func` takes the Entity as a parameter. The engine will call it 
when battle starts. This will happen only once.

```lua
ent.battle_start_func = function(self)
    print("Battle started.")
end
```

This callback may run later than you would expect. Specifically, it will be called 
on the first frame the player gains control after clicking OK on the Custom Screen. 
This will be just as the "Battle Start" text disappears.

!!! note "Fact Check Needed"

    This notice will be removed once I check if it runs at the start of this frame,
    or the end of the previous one.

Because it runs only once, an Entity that is spawned after this will never have its 
callback run.

!!! note "Fact Check Needed"

    This notice will be removed once I check if it only runs for Entities which exist 
    as the battle is created. 

### battle_end_func

The `battle_end_func` takes the Entity as a parameter. It will run when the battle ends.
Specifically, when the text saying "Enemy Deleted" or similar begins to appear.

```lua
player.battle_end_func = function(self)
    print("Battle ended.")
end
```

### can_move_to_func

The `can_move_to_func` takes a Tile as paramter. It will be called whenever 
the Entity attempts a MoveEvent, and the destination Tile for the MoveEvent 
will be passed in.

The engine expects a boolean value to be returned. If `true` is returned, 
you are telling the engine that the movement to that Tile is OK and should 
be allowed to continue. If `false` is returned, you are telling the engine to 
reject the movement.

```lua
spell.can_move_to_func = function(tile)
    return tile:is_walkable()
end
```

A common mistake when making a projectile that should move across the screen 
is forgetting to define a `can_move_to_func`. Without it, the engine will 
believe that all MoveEvents should fail.

This callback will not be used for any type of Player. 

### delete_func

The `delete_func` takes the Entity as parameter. It will be called immediately 
when the Entity is marked for deletion by the `Entity.delete` function.

```lua
spell.delete_func = function(self)
    print("Deleted")
end

print("About to delete")
spell:delete()
print("Finished deleting")
```
The above code would output

```
About to delete
Deleted
Finished deleting
```

The `delete_func` will not be called more than once.

## Deletion

An Entity can be marked for deletion at the end of the frame by calling `Entity.delete`.

```lua
spell:delete()
```

This will cause the Entity's `delete_func` callback to run.

Once `delete` has been called, the Entity is put into a state where it is waiting to 
be removed from the field. The Entity "is deleted" already.

While deleted, certain actions cannot be taken on the Entity object. If you try, you'll 
see an error appear telling you that data is deleted. Your code should often have 
safeguards when handling Entity references because of this, especially if the reference 
is kept and accessed across frames.

```lua
--[[
    Runs every frame. `player` could be deleted by the next 
    time this runs, so check.
]]
spell.update_func = function(self)
    if player:is_deleted() then 
        return
    end

    print(player:get_current_tile():x())
end
```

At some point, the "deleted" Entity will be removed from the field.

!!! note "Missing Info"

    This notice will be removed once I check when exactly they are removed.

### erase

There is another function similar to `Entity.delete`. This is `Entity.erase`.

```lua
spell:erase()
```

My recommendation is to never call it, and to always use `Entity.delete` if you 
want to delete something. This is because `Entity.erase` will skip running an 
Entity's `Entity.delete_func` callback, which may have had important cleanup 
logic. 

One benefit to `Entity.erase` is that the Entity will not be drawn the frame 
it's called, unlike `Entity.delete`. But calling `Entity.delete` with a call to 
`Entity.hide` will accomplish the same, without compromising `Entity.delete_func`.