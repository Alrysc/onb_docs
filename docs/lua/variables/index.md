
# Variables

We can store information in variables and read from them later. A variable can be created in the following ways:

```lua
local my_local_var
local my_local_var_2 = 0
my_global_var = ""
```

Here, three variables have been declared. 

1. `my_local_var` is declared **local**, as you can see. This means that this variable can only be accessed within this scope. 
More on what "scope" means later. 

1. `my_local_var_2` is also declared `local`, but it is initialized with a value. That value is `0`, a number.

2. Lastly, `my_global_var` is declared and initailized to a value of `""` (an empty String), but has no `local` in front of it. This 
variable is **global** and can be accessed anywhere in the file, no matter the scope. 

Notice that the first variable here, `my_local_var`, is the only one declared and not given a value. It actually does have a value 
implicitly: `nil`, a keyword in Lua. You can think of it as "nothing". We could have also written `local my_local_var = nil` for 
the same effect. I recommend using `=` and explicitly assigning to `nil` if you do, for more consistency and to make sure you are 
giving it no value with intent.

You'll almost always be using `local` variables. They're easier to work with, generally, and also help you to avoid making mistakes 
that are harder to debug. Even better, they help you avoid making mistakes that are hard to notice.

Be wary of the scope a variable is defined in. Even if your variable is declared `local`, having it in the highest, broadest scope 
will leave it almost no different from a global variable.


## Types

Lua is dynamically typed. Unlike some other languages, where a variable declaration must also declare the type of value it will 
hold, a variable in Lua can contain any type of data the language supports. That includes the following:

1. Numbers (Kind of integers and doubles)
2. Strings
3. Booleans
4. Functions
5. Tables
6. Other userdata (in our case, C++ engine objects)
7. `nil`

Here's an example of a variable using each type:
```lua
-- Initialized to nil, no value
local var = nil

-- Set to an integer
var = 1

-- Divide the value in `var` and set `var` to new value.
-- This gets 0.5, a double
var = var / 2

-- Set to a String
var = "Hello World"

-- Set to boolean value `true`
var = true

-- Set to an empty table
var = {}

-- Set to a function that does nothing
var = function()

end

-- Set to a new Artifact, one of ONB's types. More on that in another section.
var = Battle.Artifact.new()
```

Other sections go over specifics for each type, except for userdata.

## Naming Variables

Variables can be named almost anything, with some exception. They:

1. Cannot start with a number (`1_var` is an invalid name)
2. Cannot start with most special characters (`!var` is invalid, but `_var` is valid)
3. Cannot be the same as a keyword

Variables named with different casing are distinct.

```lua
local x = 1
local X = 2

--[[
    1
    2
]]
print(x)
print(X)
```

Lua variable naming convention is to use [snake_case](https://en.wikipedia.org/wiki/Snake_case), where every word in the name is 
lowercase, and every word is separated by an underscore. I will be using snake case for most example code. A large majority 
of mods also stick to snake case.

You'll want to name your variables based on what they are and how they will be used. A billion `var_1`, `var_2` and so on isn't 
helping anyone, and neither is a variable named `number` that's actually a String. 

Be concise and and be precise, but don't be afraid to be verbose if you need to. Leave comments if it helps to clarify.

### Shadowing

If two variables in the same scope share a name, the variable in the smaller scope "shadows" the other. 
Attempting to access the veriable by name will climb up the code and scopes until it finds a match, and 
it'll find the closer match sooner.

```lua
local x = 10

function my_func()
    local x = 20

    -- 20, because that definition is found first
    print(x)
end

my_func()

--[[
    10, because my_func's `x` is enclosed in an inaccessible 
    scope and the other `x` is still reachable and unaffected
]]
print(x)
```
The previous definition is still accessible by name up until the statement declaring the 
new variable finishes. That means you can do this:

```lua
local x = 10

function my_func()
    -- The new `x` is initialize to a value based on the old `x`
    local x = x - 2

    -- 8
    print(x)
end

my_func()


-- 10
print(x)
```