# Spell

Spells are nearly synonymous with hitboxes. They are what you will spawn 
on the field to do damage to something.

## Creating a Spell

Calling `Battle.Spell.new(Team)` will return a new Spell object. Like every 
other type of Entity, it will need to be spawned on the Field before it will 
do anything.

```lua
local spell = Battle.Spell.new(team)
```

The `team` variable in the above code snippet must be a Team enum value. 

## When to Use

A Spell is used to attack and deal damage. It's one of the two Entity types 
that is allowed to attack, alongside Obstacle.

## Attacking

We attack using a Spell by telling a Tile (typically its own) to queue an attack.
Usually you will see this done during its `update_func` logic.

```lua
spell.update_func = function(self)
    self:get_current_tile():attack_entities(self)
end
```

The above code snippet will, every frame the Spell `spell` is on the Field, queue 
an attack on the Spell's Tile to happen later in the frame, when attacks are 
processed.

It's important to understand that attacks will happen later in the frame, and 
not immediately when you call `Tile.attack_entities`. 

### Attack Targets

All Entities on the Tile will be checked for attacking. Entities which are on the 
same Team as the Spell will not be hit, unless the Spell is Team.Other. 

Only Characters, Obstacles, and Players can be hit by an attack.

### Attacking Multiple Times

The snippet above will queue an attack every frame, but this does not mean an 
Entity standing on the same Tile as a Spell will take damage rapidly. 

Once a Spell has [collided](#collision_func) with an Entity, further attacks 
on the same Tile **will not be queued**. You may still queue attacks on other 
Tiles which have not yet been hit by this Spell, but if you want to hit again, 
you will need to spawn additional Spells.

For example, see the code below. A Spell is created which does not attack itself, 
but instead spawns another Spell every few frames to do the attack. This allows 
you to repeatedly attack the same Tile.

```lua
function spawn_attack(field, team, props, tile)
    local spell = Battle.Spell.new(team)
    spell:set_hit_props(props)

    spell.update_func = function(self)
        self:get_current_tile():attack_entities(self)
        -- Only attack for one frame, and then delete. 
        self:delete()
    end

    field:spawn(spell, tile)
end

local field = player:get_field()
local team = player:get_team()
local tile = player:get_current_tile()
local props = -- Omitted HitProps creation

local spell = Battle.Spell.new(team)

local frame = 0
spell.update_func = function(self)
    if frame % 5 == 0 then 
        spawn_attack(field, self:get_team(), props, self:get_current_tile())
    end

    frame = frame + 1
end

field:spawn(spell, tile)
```

### On Queuing Attacks

You may have noticed in the above code that I called `Tile.attack_entities` and then 
immediately deleted the Spell used to attack. The attack was queued, so it won't 
happen until later in the frame. Then, by the time the attack happens, won't the Spell 
be deleted? Can it still attack?

The answer is "Yes". That queued attack will still go through later in the frame, so 
that Spell queuing an attack and then deleting itself will only attack once.

## attack_func

ScriptedSpells have an `attack_func` index that your code can define. It will 
run only if and when an attack made by the Spell dealt damage. If it was blocked 
by a DefenseRule, it will not run. 

It takes the Spell, the Entity that was hit, and the DefenseFrameStateJudge as 
parameter. Modders have taken to calling the second parameter `other`, so you will 
often see that name. I will also use it here.

```lua
spell.attack_func = function(self, other, judge)
    print("Spell "..self:get_id().." damaged Entity "..other:get_id())
end
```

## collision_func

ScriptedSpells have a `collision_func` index that your code can define. It will 
run only if and when an attack made by the Spell collided with the Entity. It 
could not run if, for example, an Entity is intangible after being hit with an attack 
that had the Hit.Flashing flag. If it does run, it will run before the `attack_func`, 
or may run even if the `attack_func` does not.

It takes the Spell, the Entity that was hit as parameter, and the DefenseFrameStateJudge. 
Modders have taken to calling the second parameter `other`, so you will often see that name. 
I will also use it here.

```lua
spell.collision_func = function(self, other, judge)
    print("Spell "..self:get_id().." collided with Entity "..other:get_id())
end
```

## Hitbox

There is another type, Hitbox, that is related to Spell. A Hitbox is a Spell. I 
briefly mention it here without much detail, mostly because I have never felt the 
need to use it. 

A Hitbox is a Spell which has a few preconfigurations. Notably, it's automatically 
set up to queue an attack and delete itself.

Overall, however, a Hitbox is more restrictive than a Spell, and often cannot be 
used alone to replicate attacks you can find in the official games.

* It has no "Scripted" version
    -  You cannot set indexes on it to contain your own data
* It can't be used with a graphic or animation
* The attack can only have one active frame (one frame where it can do damage)
* Setting the `collision_func` and `attack_func` indexes is more awkward

A Hitbox can be created in the same way as a Spell.

```lua
local hitbox = Battle.Hitbox.new(team)
```

You must use its `Hitbox.set_callbacks` function to set both the `collision_func` 
and the `attack_func` at the same time.

```lua
local hitbox = Battle.Hitbox.new(team)

local function collision_func(self, other, judge)
    print("Hitbox "..self:get_id().." collided with Entity "..other:get_id())
end

local function attack_func(self, other, judge)
    print("Hitbox "..self:get_id().." damaged Entity "..other:get_id())
end


hitbox:set_callbacks(attack_func, collision_func)
```

In the [Attacking Multiple Targets](#attacking-multiple-times) section of this page, 
there was a code snippet that would attack one Tile multiple times. In that particular 
instance, Hitbox could do the same in fewer lines.

```lua
function spawn_attack(field, team, props, tile)
    local hitbox = Battle.Hitbox.new(team)
    hitbox:set_hit_props(props)
    
    field:spawn(hitbox, tile)
end

local field = player:get_field()
local team = player:get_team()
local tile = player:get_current_tile()
local props = -- Omitted HitProps creation

local spell = Battle.Spell.new(team)

local frame = 0
spell.update_func = function(self)
    if frame % 5 == 0 then 
        spawn_attack(field, self:get_team(), props, self:get_current_tile())
    end

    frame = frame + 1
end

field:spawn(spell, tile)
```

### SharedHitbox

A SharedHitbox is in a similar place to Hitbox. You may see it used in especially 
old mods that replicate Sword chips, but it isn't required and I have never felt 
the need to use it.

You can create a SharedHitbox by passing in an Entity and a time, in milliseconds, 
that it will exist for. I'll be referring to that Entity as the SharedHitbox's "owner". 
The engine source code uses the same name.

```lua
local spell = Battle.Spell.new(team)
local shared = Battle.SharedHitbox.new(spell, 0.033)
```

!!! tip "Frames"
    Remember that 1 in-game frame is roughly equal to 0.0166 seconds. When in doubt, use 
    `frame_count / 60`. For example, `21/60` as the time if you want it to last for 21 frames.

A SharedHitbox will reference the HitProps and Team of its owner, as well as its `attack_func` and 
`collision_func`. When spawned, it will stay on the Field and continually attack its Tile 
until the specified amount of time passes. 


A SharedHitbox will stop attacking before its timer expires if the Entity used to 
created it is deleted.

!!! bug "No Deletion"
    If the Entity used to create the SharedHitbox is deleted before the timer expires, 
    the SharedHitbox may not be deleted automatically.