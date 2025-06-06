# Useful Functions

Useful Lua functions for use in ONB are highlighted here. They still exist 
in other Lua environments, but this page will especially relate to their use 
in ONB.

Notice how some, specifically in the math and table libraries, are accessed as 
if they are parts of a global table named `math` and `table`, respectively.

## print

You will use the `print` function, not in finished code but as part of debugging. 
It will print the input data to the console (black) window that opens with the 
ONB executable.

`print` takes any type as parameter, and any number of inputs can be passed in. A newline
is implicitly generated.

```lua
-- Hello World
print("Hello World")

-- 100
print(10 * 10)

-- Hello World	10	Goodbye	table: 0x561832bbdf90
print("Hello World", 10, "Goodbye", {})
```

It can be relatively expensive to write to the console output, so you will want 
to make sure no calls to `print` are left in finished code.

## The math Library

The math library includes a few useful functions for us.

### math.min

Calling `math.min` will return the smaller of two numbers given as input.

```lua
local a = 10
local b = 20

-- 10
print(math.min(a, b))
```

### math.max

Calling `math.max` will return the larger of two numbers given as input.

```lua
local a = 10
local b = 20

-- 20
print(math.max(a, b))
```

### math.floor

`math.floor` will truncate the given input, returning the input number with the decimal 
portion removed. In other words, it will round down to the nearest whole number.

Importantly, this "converts" a double to an integer.

```lua
local a = 10
a = a / 3

-- 3.333333...
print(a)

a = math.floor(a)

-- 3
print(a)
```

For ONB mods, you may often use this to round down after division. You can't use a number 
with a decimal as a damage value without an error, so a `math.floor` call will get you a 
proper number.

### math.ceil

`math.ceil` will take the given input and round it up to the nearest whole number, and 
return the new number.

Importantly, this "converts" a double to an integer.

```lua
local a = 10
a = a / 3

-- 3.333333...
print(a)

a = math.ceil(a)

-- 4
print(a)
```

### math.random

`math.random` returns a random number. It can be called with either zero, one, or two inputs.

ONB seeds the random number generator for us, so you will never need to call `math.randomseed` 
(in fact, attempting to do so will fail),

#### Zero Inputs

Giving zero inputs in the call will generate a real number between 0 and 1, including 0 but 
not including 1.

```lua
local r = math.random()

--[[
    This may print 0, 0.3, 0.014, 0.999, etc.
]]
print(r)
```

#### One Input

Giving one input will generate an integer between 1 and the number given, inclusive.

```lua
local r = math.random(3)

--[[
    This may print 1, 2, or 3.
]]
print(r)
```

!!! danger "Crash!"
    In ONB v2.0, calling `math.random` with a single input will crash if the input 
    is `0`!

!!! bug "Unexpected Behavior!"
    In ONB v2.0, calling `math.random` with a single input when that input is negative
    will return numbers outside of the expected range.

#### Two Inputs

Giving two inputs will generate an integer between the first input and the second, inclusive.

```lua
local r = math.random(2, 4)

--[[
    This may print 2, 3, or 4.
]]
print(r)
```

!!! danger "Crash!"
    In ONB v2.0, calling `math.random` with two inputs will crash if the **second** input 
    is `0`!

!!! bug "Unexpected Behavior!"
    In ONB v2.0, calling `math.random` with two inputs when the **second** input is negative
    will return numbers outside of the expected range.

### Trig Functions

Various trigonometry functions, such as `math.sin` are present, and return values in radians. 
I will not cover them specifically here, since they have not seen much usage in ONB mods.

## The table Library

There are a few functions on `table`. `table.insert` and `table.remove` are covered in the 
[section on adding data to tables](variables/tables.md#adding-and-accessing-data) and the 
[section on removing data](variables/tables.md#removing-data), respectively.

Other functions exist, but will not be covered here.