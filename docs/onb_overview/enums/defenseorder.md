# DefenseOrder

The `DefenseOrder` enum is used when creating a DefenseRule, and describes the time 
at which the DefenseRule will be checked against an attack.

It has the following indexes:

* Always
* CollisionOnly

## Usage

You must pass in a `DefenseOrder` when creating a DefenseRule.

```lua
local defense = Battle.DefenseRule.new(1, DefenseOrder.Always)
```

No DefenseRules react to an attack which could never hit the Entity, such as if the 
attack and the Entity are not Team.Other and not on the same Team, or if the Entity's 
hitbox is toggled off.

### Always

DefenseRules with the `DefenseOrder.Always` DefenseOrder will always run for an attack 
that is able to hit. These will run before `DefenseOrder.CollisionOnly`, and will run 
even if the Entity is intangible.

### CollisionOnly

DefenseRules with the `DefenseOrder.CollisionOnly` DefenseOrder will only run for attacks 
that collide. This means it will not run if the Entity is intangible.

An attack's `collision_func` will run just before these DefenseRules are checked.