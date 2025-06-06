# Overview

All mods are written in the Lua programming language. The goal of this section is **not** to teach you Lua, especially as 
your first programming language, but to highlight basic usage and differences from other languages, as well as important features.
I will try to cover the bare minimum in order to understand the code examples and logic behind other guides in these 
docs.


Sometimes I can't help myself and might make a few suggestions on how to or how not to use things. If you're new to coding, I 
(with some bias) recommend reading them. If you're already familiar with other languages, they probably won't add anything.

I may also sometimes have short sections that relate to usage in ONB. I'll reiterate on these in other places in documentation 
specifically for ONB, so don't worry about skipping.

You can also follow along some of the sections here using some Lua interpreter. Some can be found online and used from your 
browser, such as [https://onecompiler.com/lua/](https://onecompiler.com/lua/).

## Learning

[Programming in Lua](https://www.lua.org/pil/contents.html) (PiL) does a much better job going over language features than I 
do. If you want to seriously learn the language, give it and the official [Lua manual and documentation](https://www.lua.org/manual/5.4/)
a read. If you want a second opinion, more examples, or similar information in different words, feel free to read from here 
and those links.

Don't let either those or these few pages be the end, either. There are plenty of Lua tutorials you can find even on 
YouTube. Look for them.

## Lua Features We Don't Get

While covered in the manual and other Lua learning resources, there are parts of the language we cannot use in ONB mods.
Some are mentioned here, but this is not an exhaustive list:

1. The String library
    - No checking for substrings, pattern matching, characters at a position, or indexing a String
2. Coroutines
3. Checking the date or time
    - If you could, you would likely desync whenever you did, anyway
4. File system access