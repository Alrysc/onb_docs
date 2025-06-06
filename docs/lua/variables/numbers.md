# Numbers

Lua uses **integers** and **doubles** as number values.

Technically, Lua only has **Numbers**. Integers are represented through a double, but for our purposes, 
it can be necessary to differentiate. Because of this, I will sometimes talk about integers and doubles 
as if they are separate types.

## Integers

An integer is a number, negative or positive, that does not have a decimal point.
We can perform normal arithmetic operations on them.

```lua
local counter = 0
local num = 100

counter = counter + 1
num = num * 2

local p = num ^ counter

local x = p % 10

x = x / 2
```

Unfortunately, Lua does not have a shorthand increment. No `counter++` for us.

### Bitwise Operations

When a number value has no decimal point, it's valid for bitwise operations. 

```lua
local counter = 1

-- 3
counter = counter | 2
print(counter)

-- 3
counter = counter | 2
print(counter)

-- 1
counter = counter & ~2
print(counter)
```

and so on. 

Note that `~` is used as NOT. It can also be used as XOR.

```lua
-- 0, 1, 1, 0
print(1 ~ 1)
print(1 ~ 0)
print(0 ~ 1)
print(0 ~ 0)
```
Similar to the differencee between a negative number and subtraction, use a space.

## Doubles

You can think of a double as a decimal number. 

```lua
local x = 1.7
x = x * 3
x = x + 2.34
```

If the decimal is `0`, it'll be treated as an integer.

```lua
-- No error
local x = 1.0
x = x | 2

-- This gets an error
x = 1.5
x = x | 2
```

### Conversions

If the result of an operation on an integer ends with a value that has a decimal, you'll 
have a double. That could be adding a double to an integer, or dividing by a number that 
doesn't divide perfectly.

```lua
local x = 5
x = x | 2
x = x + 1.2
-- x has a decimal, so this will throw an error.
x = x | 1
```

If you're unsure of the state of a number after doing some operations on it and you need 
an integer, `math.floor` or `math.ceil` will cut off the decimal. Prefer `math.floor` for 
calculations in your mods, since the original games would.

```lua
-- No error
local x = 1.5
x = math.floor(x) | 2
```