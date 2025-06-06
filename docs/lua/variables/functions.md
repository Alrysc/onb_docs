# Functions

Functions are like reusable groups of code that can carry out a task. This is a more complicated 
type, so I encourage doing more reading than only this page. See the manual.

## Defining a Function

Functions are defined by the keyword `function`, followed by the name of the function and a set 
of parentheses. They are closed by the keyword `end`. 

Follow the same naming conventions as any other variable.

No `{ }` here, like other languages.

```lua
function my_func()

end
```

You can name incoming variables that are passed into the function with a comma separated list of 
names in the parentheses. I'll be calling these parameters, arguments, and inputs interchangeably.

```lua
function my_func(var_1, var_2, var_3)

end
```

No two functions can share the same name. It will be shadowed by the second same-name declaration, 
similar to other variables.

Note that this means there is no function overloading.

### Global and Local

Like other variables, a function can also be global or local. The `local` keyword applies just the 
same.

```lua
local function my_func()

end
```

Typical behavior of local vs. global variables applies.

## Usage

You can call, or invoke, a function by using its name and a set of parentheses.

```lua
function my_func()

end

my_func()
```

You can pass values in, which will be mapped, in order, to the parameters defined.

```lua
function my_func(var_1, var_2)
    print(var_1)
    print(var_2)
end

--[[
    Prints

    10
    20
]]
my_func(10, 20)
```

The `print` function that has been called in some examples is a global function that Lua provides 
for us. 

If you pass in a different number of parameters than the function defines in its parentheses, 
the function call will not fail. If you passed in more than defined, they will be ignored; 
if you passed in fewer than defined, the remaining will have a value of `nil`.

```lua
function print_vals(a, b, c)
    print(a)
    print(b)
    print(c)
end

--[[
    nil
    nil
    nll
]]
print_vals()

--[[
    1
    2
    nil
]]
print_vals(1, 2)

--[[
    1
    2
    3
]]
print_vals(1, 2, 3, 4, 5)
```

## As a Type

Functions are treated just like any other variable, and can be assigned to one.

```lua
function my_func()
    print("Hello World")
end

local func = my_func

--[[
    Hello World
    Hello World
]]
my_func()
func()
```

Functions can also be created inline with the keyword `function` followed by parentheses, 
which may contain parameters.

```lua
local my_func = function()
    print("Hello World")
end

-- Hello World
my_func()
```
Whether they are `local` or not will depend on the variable declaration.

## Return Value

Functions can have a return value. The function call will resolve to the return value. A value is returned
by putting the keyword `return` before it. All code after the `return` within the function will not run, and 
using `return` without having an `end` as the next line is considered an error.

```lua
function add(a, b)
    return a + b
end

local sum = add(1, 3)

-- 4
print(sum)
```

```lua
--[[
    Returns the sum of `a` and `b`.

    If `a` or `b` are larger than 10, 
    returns nil.
]] 
function add_small(a, b)
    if a > 10 or b > 10 then 
        return
    end

    return a + b
end

local sum = add(1, 3)
local sum_big = add(100, 300)

-- 4
print(sum)
-- nil
print(sum_big)
```

When a function has no return value, `nil` is returned. This can be if there is no `return` keyword 
in the code path, or if there is a `return` with no value after it.

## Closures

Functions create closures. Basically, anything in their scope when defined can be accessed within 
the function.

```lua
local x = 10

function print_x()
    print(x)
end

function print_y()
    print(y)
end

local y = 20

-- 10
print_x()

-- nil, because `y` is local and defined after print_y
print_y()
```

## Passing Data

Data may be passed by value or by reference, depending on the type.

Primitive types, like numbers and booleans, will be passed by value. 
The function receives a copy of the value and acts on it, leaving the 
original unaffected.

```lua
function add_1(a)
    a = a + 1
end

local a = 1

add_a(a)

-- 1
print(a)
```

Tables and other objects are passed by reference. Anything done to it 
inside the function will affect the original as well, because the function 
body is acting on the original.

```lua
function set_str(t)
    t.str = "Hello"
end

local my_table = {}

-- nil
print(my_table.str)

set_str(my_table)

-- Hello
print(my_table.str)
```

If you want to pass around a primitive and have it be affected in multiple places 
without making it global, this can be a way to achieve that.

```lua
--[[
    Accesses the "num" index on `t` and adds 1 to it.
]]
function add_1(t)
    t.num = t.num + 1
end

--[[
    Accesses the "num" index on `t` and multiplies it by 10.
]]
function mult_10(t)
    t.num = t.num + 1
end


local my_table = {
    num = 0
}

-- 0
print(my_table.num)

add_1(my_table)

-- 1
print(my_table.num)

mult_10(my_table.num)

-- 10
print(my_table.num)
```