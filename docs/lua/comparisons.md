# Comparisons

You can compare two values with comparison operators.

1. <    (less than)
2. <=   (less than or equal to)
3. \>    (greater than)
4. \>=   (greater than or equal to)
5. ==   (equal to)
6. ~=   (not equal to)

Note that `~` is used for "not equal to" instead of `!` like other languages.

The result of these operators will be a `boolean`.

```lua
-- false, because true is not false
print(true == false)

-- true
print("Hello" == "Hello")

-- true
print(10 > 2)

local x = 10
local y = 8

-- true
print(x ~= y)

y = y + 2

-- false
print(x ~= y)
```