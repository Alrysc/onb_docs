# Tables As Objects

Tables can be used to act much like objects in other object oriented languages.
This is closely related to special syntax you can use for functions that are 
in a table.

Related reading: 
* [https://www.lua.org/pil/16.html](https://www.lua.org/pil/16.html)

## Calling Functions

When a table has a function at some index, the function can be called in the 
normal way, with a dot. But you can also call it using a colon `:` for extra 
behavior.

```lua
local my_table = {
    func = function()
        print("Func call")
    end
}

-- Normal way
my_table.func()

-- Other way
my_table:func()
```

When calling these functions with a colon, there is an impliict value passed into 
the function, being the lefthand side of the colon.

```lua
function func(param)
    print(param)
end

local my_table = {}
my_table.func = func

my_table.func()
my_table:func()
```

The above code outputs

```
nil
table: 0x55f82a449f40
```
or similar.

Despite putting nothing the parentheses, the function call using the colon received the 
table as input.

### self

When using the colon to call a function, you can say the table passes it**self** in. 
**self** is the standard name to use for this first parameter. You might notice it 
color differently in your code editor. 

Having itself passed in means we can safely modify members of the table.

```lua
--[[
    Adds `a` to the `num` index
]]
function add_to_num(self, a)
    self.num = self.num + a
end

local my_table = {
    num = 0,
    add_to_num = add_to_num
}

-- 0
print(my_table.num)

my_table:add_to_num(2)

-- 2
print(my_table.num)

-- Notice that the above call is identical to this.
my_table.add_to_num(my_table, 2)

-- 4
print(my_table.num)
```

## Defining Functions

There's special syntax for defining a function at some String index in a table.

```lua
local my_table = {
    num = 0
}

-- Normal
my_table.func_1 = function(self, a)
    self.num = self.num + a
end

-- Another way
function my_table.func_2(self, a)
    self.num = self.num + a
end

-- Another way that also implicitly has a "self" parameter
function my_table:func_3(a)
    self.num = self.num + a
end

-- 0
print(my_table.num)

my_table:func_1(1)
my_table:func_2(1)
my_table:func_3(1)

-- 3
print(my_table.num)
```

## In ONB

In the ONB sections, you will often see `self` in example functions, and you 
will nearly always call engine functions with a colon. When interacting with 
the C++ objects the engine provides, you can nearly think of them the same 
way as these tables.