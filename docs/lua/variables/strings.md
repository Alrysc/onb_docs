# Strings

Strings are words, letters, and so on. 

## Creating a String

You can create a String by wrapping any characters in quotation marks or single quotes.

```lua
local str = "Hello"
local str_2 = 'World'
local num_str = "123"
```

You can also wrap the characters with double brackets, but I won't cover that here.

## String Concatenation

You can't use math operators on a String, but you can still "add" them together using the 
concatenation operator, `..` (two dots).

You can put spaces between the operator and the Strings. I usually won't in my code and 
examples.

```lua
local player_name = player:get_name()
local str = "Player name is "

print(str..player_name)
```

You can also concatenate numbers, which will automatically be converted to a String.

```lua
local hp = player:get_health()

print("Player has "..hp.." HP")
```

## Escape

You'll notice that you can't use quotation marks in your String if you define the String 
using them. You can **escape** the character with a backslash to make it clear to the 
program that you aren't ending the String yet, and are instead literally using that character.

```lua
-- Hello "World"
local str = "Hello \"World\""
```

### Escape Characters

Typical escape characters are also present. If you don't know what those are or what that means, 
the only one you'll likely be using in your modding adventures is `\n`.

```lua
local str = "Hello\nWorld"
```
If you printed out the above String, you would see
```
Hello
World
```
`\n` creates a newline at that place in the String. It can be useful for formatting some debug prints 
in a way that's nicer to look at.

```lua
local hp = player:get_health()
local new_hp = math.max(0, hp - 200)

print("HP before: "..hp.."\nHP After: "..new_hp)
```

```
HP before: 1000
HP after: 800
```

## Equality

Strings are equal to another String with exactly the same characters in the same order.

```lua
local str = "Hello "
local str_2 = "Hello"
local str_3 = "Hello "

-- false
print(str == str_2)
-- true
print(str == str_3)
```

## Restrictions

In ONB, we don't have access to the String library. You will not be able to check for specific 
characters or their position, check for substrings or patterns, capitalize and lowercase, and 
so on.

## Warnings

When creating a String that will be displayed to a player, such as the name of a Player Character, 
or the name a chip, keep in mind that we are "restricted" to the characters that are valid for the 
font used. Unfortunately, accuracy to BN means that there are many fonts used for many different things, 
so keep this in mind as you assign Strings.

For example, in text boxes, you'll see periods just fine. But the font used to display a Player's name 
in battle does not have that character, so it can't be displayed properly.

Characters used that aren't in the font will often be shown as a tiny capital A.