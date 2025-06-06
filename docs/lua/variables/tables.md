# Tables

Tables are basically the backbone of Lua. A lot of strong behavior and logic will hinge 
on your understanding and usage of these.

They're like arrays and maps in one type. Using them like either will have their own 
sections here, but you can also do both at the same time.

This is a more complex type, so I recommend doing additional reading. A lot more can 
be done with tables that I don't touch on. See the manual.

## Creating a Table

Tables are created using a pair of curly braces `{}`.

```lua
local my_table = {}
```

You can construct a table and assign it data at the same time by separating data with
commas.

```lua
local my_table = {
    "Hello",
    "World",
    100,
    {}
}
```

Here, a table was created with data in integer indexes **staring at 1**, ***NOT*** 0.
The String `"Hello"` is stored at index 1, `"World"` at index 2, and the number `100` is 
stored at index 3. Finally, an empty table is stored at index 4.

### String Indexes

Strings can also be indexes. You can construct a table with data at String indexes.

```lua
local my_table = {
    str = "Hello",
    100
}
```

Here, a table was created with String index `"str"` containing `"Hello"`, and numerical index 
`1` containing `100`.

### Variable Indexes

You can also use brackets, which allow the usage of a variable as the index or a number outside 
of the sequential order.

```lua
local x = 10
local str = "Hello"

local my_table = {
    [20] = x,
    [x] = -13,
    [str] = 0,
    str = -1
}
```

## Adding and Accessing Data

You can add data to a table by storing it at an index, and access it using that index. These indexes
can be Strings or integers. They can also be other things, but you are very unlikely to want to do that, 
so I won't talk about it.

### Integer Indexes

Integer indexes are automatically assigned when constructing a table without explicitly declaring an 
index. In this case, they start at `1`. 

Data can be accessed and set using brackets `[]`, with the index inside.

```lua
local my_table = {
    "Hello",
    "World",
    100,
    {}
}

-- Hello
print(my_table[1])
-- World
print(my_table[2])
-- 100
print(my_table[3])
-- Pointer to the empty table
print(my_table[4])
-- nil
print(my_table[5])

my_table[5] = 2
-- 2
print(my_table[5])
```

You can also add data at numerical indexes by calling `table.insert`, passing in the table and 
the data to add. The data will be added after the last numerical index in sequence.

```lua
local my_table = {
    2, 
    4, 
    6
}

--[[
    Table is now equivalent to

    {
        [1] = 2,
        [2] = 4,
        [3] = 6,
        [4] = 8
    }
]]
table.insert(my_table, 8)
```

By adding one more parameter between the table to insert into and the value to insert, you can specify 
the index to insert at, as long as the index specified is at the end of the sequential numerical indexes 
or is a numerical index that has a value. This will push data at any numerical index after this point up.

```lua
local my_table = {
    2, 
    4, 
    6
}

--[[
    Table is now equivalent to

    {
        [1] = 2,
        [2] = 8,
        [3] = 4,
        [4] = 6
    }
]]
table.insert(my_table, 2, 8)
```



### String Indexes

Data at String indexes, like integer indexes, can be accessed with brackets `[]` and the index inside.

```lua
local my_table = {
    str = "Hello"
}

-- Hello
print(my_table["str"])

my_table["str"] = "World"
-- World
print(my_table["str"])

my_table["num"] = 2
-- 2
print(my_table["num"])
```

There's also a shorthand access that can be done using a dot.


```lua
local my_table = {
    str = "Hello"
}

-- Hello
print(my_table.str)

my_table.str = "World"
-- World
print(my_table.str)

my_table.num = 2
-- 2
print(my_table.num)
```

Most code examples will use this type of "dot" access.

## Nested Tables

As you might have noticed in the first example, a table can contain a table. Accessing 
members of this nested table works as expected.

```lua
function pr()
    print("Hello Nested World")
end

local my_table = {
    {
        [10] = 4
    },

    table = {print = pr}
}

-- 4
print(my_table[1][10])

-- Hello Nested World
my_table.table.print()
```

## Removing Data

To remove data, simply set the value to `nil`. 

```lua
local  my_table = {
    2,
    4,
    6
}

my_table[3] = nil
```

You may also call `table.remove` to remove from the end, or give it a number to remove a specific 
index and move all other numerical indexes down around it. `table.remove` will return the removed
value.

```lua
local  my_table = {
    2,
    4,
    6
}

--[[
    Table is now equivalent to

    {
        [1] = 4,
        [2] = 6
    }
]]
local removed = table.remove(my_table, 1)

--2
print(removed)
```

```lua
local  my_table = {
    2,
    4,
    6
}

--[[
    Table is now equivalent to

    {
        [1] = 2,
        [2] = 4
    }
]]
table.remove(my_table)
```

## Table Length

There's a special operator to count the number items in a table. `#` placed before the table 
will return the number of sequential integer indexes, starting at `1`, which have a value.

```lua
local my_table = {
    [0] = 0,
    2,
    4,
    8,
    [5] = 10,
    str_index = 12
}

--[[
    This prints 3.

    6 values are in the table, but one is a String index, 
    one is at index 0, and one is not in sequence by skipping 
    ahead by a number.
]]
print(#my_table)
```

## Nil Values

### Accessing
Accessing data at an index which does not have any data is safe. `nil` will be given 
as the value.

```lua
local my_table = {}

-- nil
print(my_table[2])
-- nil
print(my_table.string_index)
```

### Sequential Indexes

For certain operations, it will matter that data is stored in sequential integer indexes, 
starting at 0. A consequence of `nil` being considered empty and nonexistent is that it can 
break up a table.

```lua
local my_table = {
    2,
    4,
    6,
    8
}

my_table[4] = nil

--[[ 
    This will throw an out of bounds error, because index `4` is
    completely disconnected from the sequence.
]]
table.insert(my_table, 3, 6)
```

This matters for counting as well.

```lua
local my_table = {
    2,
    4,
    nil,
    8
}

--[[
    Prints `2`, not `3`.
]]
print(#my_table)
```

And iterating using `ipairs`, but not `pairs`.

```lua
local my_table = {
    2,
    4,
    nil,
    8
}

local visited_indexes = 0

for k, v in ipairs(my_table) do
    visited_indexes = visited_indexes + 1
end

-- 2
print(visited_indexes)

local visited_pairs_indexes = 0

for k, v in pairs(my_table) do
    visited_pairs_indexes = visited_pairs_indexes + 1
end

-- 3
print(visited_pairs_indexes)
```

## Objects

Tables can function similarly to objects in other languages. This is covered in 
[another section](../objects.md).