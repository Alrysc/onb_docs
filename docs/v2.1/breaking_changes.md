# Breaking Changes
There are very few breaking changes in v2.1. Some behavior changes may cause your mods to 
misbehave, but still ideally function. See [Recommended Upgrades](./recommended_upgrades.md).

The changes on this page are known to cause runtime or loading errors every time, or to 
cause some code to simply do nothing at all. You'll want to fix them.

## Charge Time
In v2.0, the `Player.charged_time_table_func` or `PlayerForm.calculate_charge_time_func` 
could be used to affect charge times. These two functions have been renamed, and their 
parameters were changed to now pass in the Player and the charge level, rather than just 
the level. You will need to make the following changes:

* Rename `charged_time_table_func` to `charge_time_func`, no `d`!
* Change the parameters to `(self, level)`, e.g. `player.charge_time_func = function(self, level)`
* Rename `calculate_charge_time_func` to `charge_time_func`, same as Player
* Change the parameters to `(player, level)`, e.g. `form.charge_time_func = function(player, level)`

Before:
```lua
local form = player:create_form()
player.charged_time_table_func = function(level)
    return frames(100 - 10 * level)
end

form.calculate_charge_time_func = function(level)
    return frames(80 - 5 * level)
end
```

After:
```lua
local form = player:create_form()
-- No more 'd' in name or 'table', extra parameter
player.charge_time_func = function(self, level)
    return frames(100 - 10 * level)
end

-- Name matches Player's function, extra parameter
form.charge_time_func = function(player, level)
    return frames(80 - 5 * level)
end
```

For other examples using these functions, see [Custom Charge Times](./recommended_upgrades.md#custom-charge-times).

## Obstacles Delete at 0 Health
In v2.0, some mods attempted to set the health of an Obstacle to 0, outside of any combat 
events. As of v2.1, setting health to 0 immediatelly calls `delete` on the Obstacle. This 
is to allow Obstacles to correctly be deleted during time freeze.

This means the following v2.1 code
```lua
local obstacle = Battle.Obstacle.new(Team.Other)
obstacle:set_health(0)
```
is now essentially equal to doing this in v2.0:
```lua
local obstacle = Battle.Obstacle.new(Team.Other)
obstacle:set_health(0)
obstacle:delete()
```

### Reasons Mods May Have Done This, and Fixes
Some mods may have done this to avoid hit detection for their Obstacle. Obstacles have 
0 health by default, however, so this may not have done anything. You can remove the line
and it will likely work as intended. If it does not, consider using `Obstacle.toggle_hitbox`
to remove its hitbox. In v2.5, this will also cause the Obstacle to be unsearchable on the Field, 
however, which could be unintended. You can use other methods to track and search the Obstacle if 
you need.

Other mods may have set their own Obstacle's health to 0 to achieve some behavior. For example, 
the `BlkBomb` chip may have been implemented using a DefenseRule that set its health to 0 immediately 
when hit by a Fire attack. This isn't exactly accurate behavior, however, because it would cause 
the bomb to explode during time freeze if your logic is related to deletion. Consider instead flagging 
that the bomb has been hit by Fire, blocking all further damage if it has been, and handling the deletion 
and explosion on the bomb's following update.