# Comments

You can, should, and will add comments to your code. 

A comment is some text you can have in your code that will not be read by the program. 
That means you can leave notes for yourself or others who read your code, describe how 
certain functions behave and how they expect to receive data, and so on. 

You can also "comment out" some code to leave it in your file, but prevent it from 
being executed. This can be safer for you than deleting lines when testing.

You will usually want to write your comment above the code it relates to. While you 
can write it on the same line, your text will go off the screen faster that way, 
and it could be harder to convert your comment to a multiline comment later or remove it.

## Single Line

Comments in Lua start with two hyphens `--`. Every character after that in the line 
will not be read by the program.

```lua
-- x offset for the player
local x = 0
-- y offset for the player
local y = 0

local num = 20 -- 50

-- Crashes if this is negative
local countdown = 10
```

## Multiple Lines

You can comment multiple lines at once by surrounding a region of code with `--[[ ]]`.

```lua

--[[
    Multiplies `x` and `y` and returns the result.

    `x` and `y` both must be numbers.
]]
local function multiply(x, y)
    return x * y
end

--[[

This is a different implementation of the multiply function
that would multiply all values in a table. 

I didn't finish it, but it might be useful someday. Come back 
to finish it later.

local function multiply(number_table)

end

]]
```

## When To Comment

Different people might teach you to use comments more or less frequently, or for more 
or less specific code. For example, I would tell you not to do something like:

```lua
-- This is the player's offset
local player_offset = player:get_offset()
-- This is the x offset of the player
local x_off = player_offset.x
-- This is the y offset of the player
local y_off = player_offset.y
```

Very wordy, and the variable names already state what they are. A comment may not 
add much insight. 

You might want to ask yourself a few questions, like

1. Is the code simple enough that you can tell what it does by looking at it?
2. Is there any special behavior or requirement to the code that isn't obvious?
3. Will I forget what this does when I come back to fix a bug in a month?

For your ONB mods, I would encourage leaving comments where appropriate. You might 
just need to fix a bug and you've forgetten what the behavior should be. A new 
modder might look to your mod for inspiration and try to read its code. You might 
want to come back and update it with a new engine feature that makes the logic 
much cleaner. Help yourself and everyone else.