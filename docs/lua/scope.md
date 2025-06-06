# Scope

Your code can only access data if that data is within its scope. 

## Indenting

While not required, please indent your code to indicate a new scope. It'll 
make it easier to read for you and everyone else, and it'll make it easy to 
identify each unique scope. You'll see plenty of examples of this through 
the code exmaples here and everywhere else.

## Blocks

A block of code creates a new scope. A new block exists in the space between 
something and its accompanying `end`. 

That's between:

* The parentheses and `end` of a function
* The `then` and `end` of a conditional
* The `do` and `end` of a `for` or `while` loop
* The `repeat` and `until` of a repeat-until

```lua
local y = nil

if true then 
    --[[
        This `x` only exists until this block closes.
        When it stops existing, we say it "went out of 
        scope".
    ]]
    local x = 10
    
    --[[
        This is a new block within the other one, 
        so it can still access `x`.
    ]]
    
    local function func()
        -- 10
        print(x)
    end
end

--[[
    Both print `nil`, because they exist nested in 
    a different scope.
]]
print(x)
print(func)
```

### Other Files

Each working file is its own block, unable to access anything local to any block 
of another file. 

```lua title="file1.lua"
local x = 10

include("file2.lua")

-- nil
print(y)
```

```lua title="file2.lua"
local y = 20

-- nil
print(x)
```

Here, even though `file2.lua` is read and executed by the program running `file1.lua`, and 
you might think `file2.lua`'s block is inside `file1.lua`, neither has access to each other's 
local variables.