# Boolean Expressions

Like a math expression, which uses numbers, a boolean expression uses 
booleans. And, like a math expression, you can solve one down to a 
single value ("resolve" it).

```
1. 2 + 4 + 8
    - Add the 2 and 4
2. 6 + 8
    - Add the 6 and 8
3. 14
    - Final value
```

```
1. false or false or true
    - OR the `false` and `false`
2. false or true
    - OR the `false` and `true`
3. true
    - Final value
```

## Other Values

Booleans are not the only types that can be used in a boolean expression.

A value of any type, except for if the value is `nil` or `false`, will 
resolve to `true`. Both `nil` and `false` are treated as `false`.

```lua
-- true
print(1 and true)

-- true
print(0 and true)

local x = -1

-- true
print(x and "")

-- false
print(nil or false)
```

My code examples will often use this feature as a shorthand in conditions, 
but remember to check if something is explicitly == false if your logic 
should consider `nil` as a separate case.

## Short Circuit

Boolean expressions can be short circuited. This is when the program skips part 
of the evaluation because it knows the result of the expression is already determined. 

For example, 

```lua
local x = true and false and true
```
When executing this statement, the logic is more like

```
1. true and false and true
    - AND `true and `false`
2. false and --
    - It doesn't matter what is on the other side of the `and`.
    `false` ANDed with anything is `false`, so it doesn't check.
3. false
    - Final result
```

This is more obvious in more complex expressions.

```lua
--[[
    Return true if a and b are both greater than 0 
    and a is greater than b.
]]
function greater_above_zero(a, b)
    print("Check zero.")
    if a < 0 or b < 0 then 
        return false
    end
    
    return a > b
end

local check = greater_above_zero(1, 2) and greater_above_zero(2, 1)

print(check)
```

In the above code, when `greater_above_zero` is called, we expect a `print` call 
as well. `greater_above_zero` is called twice, so you might expect it to appear 
twice. However, if you run this, you will see

```
Check zero.
false
```

It only appeared once. Here's how the resolution looked:

```
1. greater_above_zero(1, 2) and greater_above_zero(2, 1)
    - Call greater_above_zero with 1 and 2, run that function.
    It printed and returned false.

2. false and --
    - The other side of the `and` is ignored because anything
    ANDed with `false` is `false`.
```

### Avoid Expensive Calls

An important aspect of short circuiting is that it can avoid more expensive, or additional, 
calls when they aren't necessary. Take the following code:

```lua
--[[
    Compare all elements in t1 to all elements in t2. 

    Returns true if they each contain exactly the same elements 
    as the other.
]]
function deep_compare_table(t1, t2)
    -- Implementation omitted for example
end


if player:get_element() == Element.None 
    and player:get_health() > 1000
    and deep_compare_table(player.t1, player.t2)
then
    print("All satisfied")
end
```

We only want that statement to `print` if the player's element is None, they have more than 
1000 health, and two tables contain the same elements. The latter operation is obviously 
much more expensive and can take many more computations than the former two. 

Because of short circuiting, that comparison is never even attempted if the element is **not** 
None, or if their health is **not** above 1000.

The code above can similarly be written as

```lua
function deep_compare_table(t1, t2)
    -- Implementation omitted for example
end


if player:get_element() == Element.None then
    if player:get_health() > 1000 then 
        if deep_compare_table(player.t1, player.t2) then
            print("All satisfied")
        end
    end
end
```
You can see why you might prefer to short circuit in one boolean expression instead.

### Avoid Erroneous Calls

Short circuiting can also be a useful technique for writing code that avoids making calls 
that would otherwise result in an error. In the ONB sections of these docs, you will see 
me use it often when checking Tiles.

```lua
--[[
    Return true if a < b. 

    a and b both must be numbers.
]]
function check_lesser(a, b)
    return a < b
end

--[[[
    Roll a random number between 1 and 2.

    If the number is 1, returns nil.
    If the number is 2, returns 2.
]]
function get_number()
    local r = math.random(1, 2)

    if r == 1 then 
        return nil
    end

    return r
end

local maybe_r = get_number()

local lesser = maybe_r and check_lesser(maybe_r, 4)

print(lesser)
```
In the above code, the result returned from `get_number` may be `nil`. It would be an error to call 
`check_greater` with `nil`, because you cannot compare a number to `nil`. 

In order to avoid this, I check first check if `maybe_r` is `nil`, and AND that with the result 
of `check_greater`. Recall that `nil` evaluates to `false`, and any other value that isn't `false` 
evaluates to `true`. Then, the resolution of this expression might look like:

```
1. maybe_r and check_greater(maybe_r, 4)
    - Substitute in the variable value
2. 2 and check_greater(2, 4)
    - Check boolean value of `2`
3. true and check_greater(2, 4)
    - Resolve the check_greater call
4. true and true
5. true
```

Or

```
1. maybe_r and check_greater(maybe_r, 4)
    - Substitute in the variable value
2. nil and check_greater(nil, 4)
    - Check boolean value of `nil`
3. false and --
    - `false` ANDed with any other value is `false`, so 
    there is no need to check the other side.
4. false
```

The order here matters. If the code was instead 

```lua
local lesser = check_lesser(maybe_r, 4) and maybe_r
```

`check_lesser` would have been called with `nil`, and an error would occur.
