# Player

Players are Characters that are controlled by the one playing the game. 
They have their own mod package type, and additional stats and functions.

## Creating a Player

You cannot create a Player object. You will explicity be given a ScriptedPlayer
in the function parameters of the `player_init` in a Player mod file.

## Stats

In addition to health, Players have a few extra stats.

### Attack Level

Players have an attack level, which is an integer ranging from 1 to 5. By default, 
it is 1.

You can use `Player.get_attack_level` to get the current attack level, and 
`player.set_attack_level` to set a new one. 

```lua
local current_attack = player:get_attack_level()

-- 1
print(current_attack)

player:set_attack_level(current_attack + 10)

-- 5, because it's capped
print(player:get_attack_level())
```

A Player's attack level is often used in the formula to determine the damage their 
attacks will do.

### Charge Level

Players have a charge level, which affects how fast they can charge for their charged attack.
See [Charging](#charging) for specific speeds.

Charge level is an integer ranging from 1 to 5. By default, it is 1.

```lua
local current_charge = player:get_charge_level()

-- 1
print(current_charge)

player:set_charge_level(current_charge + 10)

-- 5, because it's capped
print(player:get_charge_level())
```

## Charging

While holding the Shoot button, a Player will begin to charge. There are three states 
to charging:

1. For the first 10 frames, there is no visual cue that charging has started
2. After the first 10 frames, a graphic appears
3. After the full charge time, the graphic changes and changes color

While in the third state, releasing the Shoot button will call the `charged_attack_func`.
 
It takes 110 frames to reach full charge. The Player cannot charge during another action.
Anything that would end an active action will also reset the charge. 

### Charge Speed

By default, it takes 100 frames to reach full charge at charge level 1. The higher the Player's charge
level, the less time it will take. Specifically, 10 frames are cut off for each level. The 
formula would be something like:

`110 - 10 * c`

where `c` is the charge level. Keep in mind that charge level will be a number from 1 to 5, 
hence the 110 frame time at level 1.

Officially, the charge time formula is slightly different from the above. 10 frames are factored 
into all charge times, because it takes that long for the charge graphic to appear. So in 
reality, the engine has a formula more like

`10 + (100 - 10 * c)`

Keep this in mind for scripted charge time formulas.

### Changing the Formula

You can determine the time it takes to charge by setting the Player's `charge_time_func`, 
both on Players and on PlayerForms. The `charge_time_func` is passed a reference to the Player, 
and a number `level` representing the current Charge level, and expects a `frametime` to be 
returned. The `level` will be a number from 1 to 5, inclusive. Be sure to cover all cases.

```lua
local form = player:create_form()
player.charge_time_func = function(self, level)
    if level == 1 then
        return frames(50)
    end

    if level == 2 then 
        return frames(40)
    end

    -- etc.
end

-- Note that the form receives the Player, not itself, in the parameters, 
-- so standard practice of using `self` as the first parameter is incorrect.
form.charge_time_func = function(player, level)
    if level == 1 then
        return frames(100)
    end

    if level == 2 then 
        return frames(90)
    end

    -- etc.
end
```

Some tips for determining charge times:

* The default charge time is 90 frames at level 1, 80 at level 2, 70 at level 3, 60 at level 4, and 50 and level 5.
* If your charge time can be based on a formula, use one
    * For exmaple, the ONB default can be replicated in one line with `return frames(100 - 10 * level)`
* The official games often do not use linear charge times. For example, HeatCross in BN6 has times 
 60, 50, 40, 35, 30, for levels 1-5. Notice how having more levels past 3 has lesser effect.
* The returned time is applied *after* holding the Shoot button for 10 frames (when the graphic appears)
    * This means returning `frames(10)` actually means you need to hold B for 20 frames total to be fully charged
    * Sources on charge times for the official games may or may not count the 10 frames before holding Shoot. 
    Take this into consideration when taking data from other sources. 

If your mod changes the `Player.charged_attack_func`, consider also changing the `Player.charge_time_func`.

## Built-In CardActions

ScriptedPlayers have three definable functions that can return CardActions, which 
will be called by the engine under certain circumstances.

### normal_attack_func

The `normal_attack_func` will run when the player presses the Shoot button 
while the Player is actionable. It takes the ScriptedPlayer as input and expects 
to return a CardAction.

A common CardAction to return is the Buster action, which replicates MegaMan's 
normal attacks in the official games when given `false` as the second parameter.

```lua
player.normal_attack_func = function(self)
    -- false to use normal attack graphics on hit, attack level as damage
    return Battle.Buster.new(self, false, self:get_attack_level())
end
```

### charged_attack_func

The `charged_attack_func` will run when the player releases the Shoot button after 
holding it for long enough to become fully charged. It takes the ScriptedPlayer as 
input and expects to return a CardAction.

A common CardAction to return is the Buster action, which replicates MegaMan's 
charged attacks in the official games when `true` is given as the second parameter.

```lua
player.charged_attack_func = function(self)
    --[[
        true to use charged attack graphics on hit and charged attack
        framedata, attack level * 10 as damage
    ]]
    return Battle.Buster.new(self, true, self:get_attack_level() * 10)
end
```

### special_attack_func

The `special_attack_func` will run when the player releases the Special button 
while the Player is actionable. It takes the ScriptedPlayer as input and expects 
to return a CardAction. 

The Special button is intended to parallel the B+Back input from the official 
games. 

```lua
-- Play the "PLAYER_HIT" animation and do nothing else
player.special_attack_func = function(self)
    local action = Battle.CardAction.new(self, "PLAYER_HIT")

    return action
end
```

## Inputs

You can read the player's inputs from their associated Player using the `Player.input_has` 
function, passing in the input to check for. These are represented by the [Input enum 
values](../enums/input.md).

Inputs come in three types:

1. Pressed, true only on the exact frame a button was pressed down
2. Held, true every frame the button is down after the first
3. Released, true on the exact frame a button was let up

Only one of the above types will be `true` at a time for one input.

!!! bug "Frame Step"
    In v2.1, the tracking for held button inputs continues to advance even while 
    the engine is paused. This means it's difficult, if not impossible, to read a 
    Pressed input while frame stepping.

```lua
if player:input_has(Input.Pressed.Shoot) then 
    print("Shoot button was pressed this frame")
end
```

```lua
local count = 0
spell.update_func = function(self)
    if player:input_has(Input.Held.Right) then 
        count = count + 1
        print("Holding Right for "..count.." frames")
    elseif player:input_has(Input.Released.Right) then 
        print("Player let go")
        self:delete()
    end
end
```

## Forms

Players have access to a form changing mechanic, a recreation of the Cross system 
in Battle Network 6. A player can choose forms that are added to the Player from the 
Custom screen, the same as BN6.

A Player can have up to 5 forms. You can add one by calling `Player.create_form` 
when the `player_init` runs. Calling it later will not add the form.

```lua
function player_init(player)
    local form_1 = player:create_form()
end
```

This creates a PlayerForm object. These will be detailed in another page.

## Type Checking

You can test if an Entity is a Player. The `Battle.Player.from(entity)` function 
call will return a Player cast of the input Entity if it was a Player, or `nil` 
if it was not.

```lua
spell.attack_func = function(self, other)
    local p = Battle.Player.from(other)
    if p ~= nil then 
        print("Damaged a Player")
    else
        print("Damaged a different type of Entity")
    end
end
```

Remember that a Player *is a* Character, so `Battle.Character.from(entity)` when 
`entity` is a Player will return a Character object, not nil.

### Checking For ScriptedPlayer

`Battle.Player.from(entity)` will return a ScriptedPlayer instead of a Player if 
the input Entity is exactly the Player type.

Because this is a Scripted type, this means you can access and set user indexes 
on the Player. This is commonly used for mods that change normal, charged, or 
special attacks.

This can be dangerous, however, because you may collide with the names of indexes 
the creator of that Player mod added to the Player. Take care when adding any. 
Try to use especially unique names, similar to when you choose a package ID name.