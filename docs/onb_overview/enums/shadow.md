# Shadow

Entities can have a shadow appear under them. Often this is done by having 
the shadow be part of the spritesheet, but ONB also supports setting a different 
one using `Entity.set_shadow`, which will stay on the ground when you change the 
Entity's elevation.

Using the `Shadow` enum, you can access shadow textures that are included in the 
engine, or remove one you have set. This includes the following:

* None
* Small
* Big

By default, `Shadow.None` is used.

## Usage

Set a shadow by giving a Shadow value to `Entity.set_shadow`, and then call 
`Entity.show_shadow` with a value of `true` to see it.

```lua
local artifact = Battle.Artifact.new()
artifact:set_shadow(Shadow.Small)
artifact:show_shadow(true)
```