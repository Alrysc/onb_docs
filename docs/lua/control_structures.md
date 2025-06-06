# Control Structures

These are covered more succinctly in [Programming in Lua](https://www.lua.org/pil/4.3.html).
If you read only one source, choose that one over this.

Each control structure involves some keywords, a [boolean expression](variables/booleans.md#boolean-expressions), 
and usually an `end`. They create a new scope, or [block](scope.md#blocks), as their body.

There are no switch statements.

## if

A `if` statement is made by the `if` keyword, a boolean expression, the `end` keyword, 
the body logic, and is closed by the `end` keyword.

```lua
if true then 
    -- Body logic
end
```

The code within the body will only run if the boolean expression resolves to `true`.

```lua
if 5 < 2 then 
    print("Run 1")
end

if 5 > 2 then 
    print("Run 2")
end
```
In the above code, the only output will be `Run 2`.

Notice that there are no `{}` like other languages, and parentheses around the 
boolean expression are not required.

### else

You can have a second body that will run only if the boolean expression resolves 
to false. Use the keyword `else` in place of `end`, and move the `end` to the end 
of the `else` body.

```lua
if 5 < 2 then 
    print("Run 1")
else
    print("No Run 1")
end

if 5 > 2 then 
    print("Run 2")
else
    print("No Run 2")
end
```
In the above code, the output will be:

```
No Run 1
Run 2
```

### elseif

You can have any number of extra blocks following an `if` that will only 
run if the previous condition was `false` and a new condition is met.

This can be done similar to `else`, but requires a `then` after.

```lua
if 5 < 2 then 
    print("Run 1")
elseif 5 > 2 then
    print("Run 1 else")
else
    print("Default")
end

if 5 > 2 then 
    print("Run 2")
elseif 5 ~= 5 then 
    print("Run 2 else 1")
elseif 5 == 5 then
    print("Run 2 else 2")
end
```

```
Run 1 else
Run 2
```

## while

A `while` loop can be created by using the keyword `while`, a boolean expression, 
the keyword `do`, and finally closing the body with `end`.

Until the expression resolves to `false`, the body will run over and over. If 
the expression resolves to `false` before running even once, the body will not 
run.

```lua
local counter = 3
while counter > 0 do
    print(counter)
    counter = counter - 1
end

local x = 1
while x ~= 1 do
    print("Not 1")
end
```

The output of the above code will be

```
3
2
1
```

You can also use the keyword `break` to exit the loop early. If `break` is used, 
the next line must be `end`.

```lua
local counter = 3
while counter > 0 do
    print(counter)
    if counter == 2 then 
        break
    end

    counter = counter - 1
end
```

The output of the above code will be

```
3
2
```

## repeat-until

You can `repeat` some body `until` a condition is met. Use the keyword `repeat`, 
followed by the body. Then close the body with the `until` keyword, followed by a 
boolean expression. 

Note that there is no `do` or `end`, like other loops.

Unlike `while`, `repeat-until` will always run the body at least once. The boolean expression 
is checked after the body runs instead of before.

```lua
local counter = 3
repeat
    print(counter)
    counter = counter - 1
until counter == 0

local x = 1
repeat
    print("Not 1")
until x == 1
```

The above code will have the following outupt:

```
3
2
1
Not 1
```

`break` can be used in the body in the same way as a `while` loop.

```lua
local counter = 3
repeat
    print(counter)

    if counter == 2 then 
        break
    end

    counter = counter - 1
until counter == 0

local x = 1
repeat
    print("Not 1")
until x == 1
```

```
3
2
Not 1
```

## for

A `for` loop is used to loop over a block of code a specified number of times. You can create 
a `for` loop by using the keyword `for`, defining a variable name and default value as the 
iterator, a number where the iterator will stop at, optionally a number to increment by 
after each iteration (the "step"), the keyword `do`, the body, and finally an `end`.

Conventionally, the iterator will be named `i`. If you have multiple `for` loops nested, you 
might move onto `j`, `k`, and other letters.

This one will be easier to understand visually than through that text.

```lua
for i=1, 3, 1 do
    print("Loop 1: "..i)
end

print("")

for i=1, 3 do
    print("Loop 2: "..i)
end

print("")

for i=4, 0, -2 do
    print("Loop 3: "..i)
end

print(i)
```

This outputs

```
Loop 1: 1
Loop 1: 2
Loop 1: 3

Loop 2: 1
Loop 2: 2
Loop 2: 3

Loop 3: 4
Loop 3: 2
Loop 3: 0
nil
```

There are a few things to notice here:

1. The variable name is not declared with `local`, but it is local to the
loop body. The `nil` print at the end proves that.
2. The default step is `1` when not provided.
3. No boolean expression is given for the end of the loop, only a number to stop at.
4. The loop ends when `i` is > the limit for a positive step, and ends when `i` 
is < the limit for a negative step.

The 4th point means the following code will run the body of the `for` loop once:

```lua
for i=1, 1 do
    print(i)
end
```

It also means that this loop's body will not run at all:

```lua
for i=1, -1 do
    print(i)
end
```

Separately, a step of `0` is considered an error.

You can use `break` in a `for` loop.

```lua
for i=1, 3, 1 do
    print("Loop: "..i)
    break
end
```

This outputs

```
Loop: 1
```

### ipairs

You can iterate over a table's numerical indexes using special `for` loop syntax. 
Instead of defining a incremented variable, a step, and so on, you give names for the 
**k**ey and **v**alue of each pair, and pass the table into a call to `ipairs`.

The `key` will be set to a key in the input table, and the `value` will be the value 
at the `key` index in the table.

```lua
local my_table = {
    2,
    4,
    8
}

for k, v in ipairs(my_table) do
    print("Key "..k.." has value "..v)
end
```
This will output 

```
Key 1 has value 2
Key 2 has value 4
Key 3 has value 8
```

When iterating with `ipairs`, the `key` will start at 1 and increase by 1 each iteration.
Once a `nil` value is encountered as the value, the loop ends.

```lua
local my_table = {
    2,
    4,
    nil,
    8
}

for k, v in ipairs(my_table) do
    print("Key "..k.." has value "..v)
end
```
This will output

```
Key 1 has value 2
Key 2 has value 4
```

Because `nil` was found at `my_table[3]`, the loop broke.

Note that, because only numerical indexes are visited, `ipairs` will never encounter 
a value at any other type of index, like a String index.

### pairs

You can iterate over all of a table's indexes using special `for` loop syntax. 
Instead of defining a incremented variable, a step, and so on, you give names for the 
**k**ey and **v**alue of each pair, and pass the table into a call to `pairs`.

Compare to `ipairs`.

```lua
local my_table = {
    str = "Hello",
    [-1] = -2,
    2,
    4,
    nil,
    8,
    str_2 = "World"
}

my_table.other_str = "!"

for k, v in pairs(my_table) do
    print("Key "..k.." has value "..v)
end
```

```
Key 1 has value 2
Key 2 has value 4
Key 4 has value 8
Key -1 has value -2
Key other_str has value !
Key str_2 has value World
Key str has value Hello
```

Notice that the order is not necessarily the same as the order in which key-value pairs were 
added.

### Other

There's more information about these types of `for` loops, but I won't cover them here. See 
the manual.