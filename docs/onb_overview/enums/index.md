# Enums

Enums are not an actual Lua type, but they are a C++ type, and the engine 
gives access to them.

## What Are Enums

You can think of an enum as a table with String indexes which hold number 
values. The number value usually isn't important, but the String is. Its 
name will tell you what it should be treated as.

They're a way to keep code looking less arbitrary and more readable when 
you need logic to respond to certain signals. For example, take the following 
code:

```lua
function do_something(thing, a, b)
    if thing == 0 then 
        return a + b
    elseif thing == 1 then 
        return a * b
    elseif thing == 2 then 
        return a / b
    end
end


-- 2
print(do_something(2, 10, 5))
```

When you call `do_something`, it's a little less clear what the intent is. 
Future code changes might add or remove values, and now the `2` that's given 
here might have a different meaning.

With enums, we can essentially assign these values a name. Here's how something 
else could look:

```lua
local things = {
    add = 1,
    multiply = 2,
    divide = 3
}

function do_something(thing, a, b)
    if thing == things.add then 
        return a + b
    elseif thing == things.multiply then 
        return a * b
    elseif thing == things.divide then 
        return a / b
    end
end

-- 2
print(do_something(things.divide, 10, 5))
```

Now it's much more clear what the intent is behind each value. When working 
with the values given in Lua, the first `do_something` function, which checked 
`thing == 0` etc., checking against exact numbers, would still work when given the 
new `things.add` and so on, but remember the reasons why we wouldn't refer to the 
exact value.

Say we had updated the "enum" values in a month to now be 

```lua
local things = {
    add = 1,
    subtract = 2,
    multiply = 3,
    divide = 4
}
```

Now the function which checked exactly == 2 and multiplied is incorrect. The 
version that checked for `things.multiply` will still work as expected.

## Enums In ONB

The pages in this section describe the engine enums we have access to from Lua 
when modding. There are a couple things you should notice about them:

1. They always use capitalized names
2. Accessing is just like a Lua table with String indexes
3. Their indexed values are (almost) always numbers

The first point is a reminder that you may want to avoid capitalizing your variable 
names. snake_case is perferred. Imagine you had code like this:

```lua
local Direction = player:get_facing()
```

You've now shadowed the actual `Direction` enum and can't access it in this scope 
anymore. You won't be able to test against `Direction.Left` and so on because now 
`Direction` refers to a number value, which you can't index without error.

The third point is a reminder that trying to print values will not be that useful.

```lua
local facing = player:get_facing()

-- Prints 2... What does that mean?
print(facing)
```

Because of this, you might instead want to use comparisons to check.

```lua
local facing = player:get_facing()

print("Facing left? "..(facing == Direction.Left))
```

I won't include the number values of any enums here, because you mostly shouldn't 
use them for reasons described above. You can find them in the engine source if 
you really need them.