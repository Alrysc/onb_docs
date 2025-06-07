# Open Net Battle 

These docs contain information on creating mods for the Open Net Battle (ONB) engine, 
an engine recreating the mechanics of MegaMan Battle Network 6 where all content 
is user generated.

The current version of Open Net Battle is v2.0a (specifically, the fifth patch). Whenever 
I write something that says it's about v2.0, I mean the latest version. Certain things may 
be different in the next large engine update, and I'll be updating information here to 
reflect changes when they happen. 


## Topics Covered

These docs are in the early stages. So far, you'll find:

1. A summary of Lua features in the [Lua Primer tab](./lua/index.md)
2. Small usage tips for Entity class objects and summaries of their notable functions
3. Overviews of some of the C++ enums that have Lua bindings

I still plan to add at least:

1. Pages on the rest of the engine C++ objects that have Lua bindings, similar to 
the Entity pages
2. Pages on the rest of the values of engine enums
3. Overviews of the mod package types
4. Sections that cover setting up a new mod of each type
5. Sections dedicated to other tools that can help certain parts of modding and how
to use them
6. Sections covering common desired behavior, like making attacks that move
7. Sections that warn of logical pitfalls that could cause code to act in unexpected, 
less noticed ways
8. Sections on specific engine details, like the order of logic in a frame
9. Sections on details and behavior from the official games, to help make mods feel
more authentic (things like the typical number of chip codes, or how much each charge 
level tends to speed up charging)

## Other Help

You most likely got here through the official Discord server. If you didn't, I 
recommend you join it. You'll be around for updates, other players, the latest 
tools as the develop, and, most importantly, other modders like me who can help 
you figure stuff out. 