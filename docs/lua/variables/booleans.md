# Booleans

Booleans have only two possible values: `true` and `false`.

```lua
local t = true
local f = false
```

## Operators

### AND

`and` will take a boolean on both sides and result in `true` if both inputs are `true`, or else 
it will be `false`.

We write the word, not `&&` like other languages.

```lua
-- true
print(true and true)
-- false
print(true and false)
-- false
print(false and true)
-- false
print(false and false)
```

### OR

`or` will take a boolean on both sides and result in `true` if one input is `true`, or else 
it will be `false`.

We write the word, not `||` like other languages.

```lua
-- true
print(true and true)
-- true
print(true and false)
-- true
print(false and true)
-- false
print(false and false)
```

### NOT

`not` will reverse a boolean in front of it.

We write the word, not `!` like other languages.

```lua
-- false
print(not true)
-- true
print(not false)
```

You can use multiple `not` in a row.

```lua
-- false
print(not not not true)
```

## Operator Precedence

Similar to the order of operations for math operators, boolean operators 
also have an order in which they resolve.


1. Parentheses
2. not
3. and
4. or

For their placement within all other operators, see the [manual](https://www.lua.org/manual/5.0/manual.html#2.5.5).

```
1. true and not true or false
    - Apply the `not`
2. true and false or false
    - `and` the `true` and `false`
3. false or false
    - `or` the false and false
4. false
    - Final result
```

When in doubt, use parentheses. They can make the expression easier to read, too.

```
1. (true and not true) or false
2. (true and false) or false
3. true or false
4. false
```

## Boolean Expressions

Booleans can be used in expressions. There's a lot more to say on this, so they 
are covered in a [different section](../boolean_expressions.md).
