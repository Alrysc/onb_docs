# EntityStatus

The `EntityStatus` enum is a status returned by calls to `Field.spawn` to describe 
the results of the spawn.

It has the following values:

* Queued
* Added
* Failed

## Usage

You can inspect the return value of `Field.spawn`.

```lua
local spell = Battle.Spell.new(player:get_team())
local status = player:get_field():spawn(spell, 3, 1)

-- true
print(status == EntityStatus.Queued)
```

You are unlikely to use this value very often, because it won't give useful information 
in most use cases. You should structure your code such that you know whether or not 
the Entity will successfully spawn, such as ensuring you don't pass in nonsensical 
coordinates.

### Queued

`Field.spawn` returns `EntityStatus.Queued` if the call was made while the Field 
was updating. Nearly all of your spawn calls will return this one.

### Added

`Field.spawn` returns `EntityStatus.Added` if the call was made while the Field 
was not updating and the Entity to spawn was successfully added.

### Failed

`Field.spawn` returns `EntityStatus.Added` if the call was made while the Field 
was not updating and the Entity to spawn was not successfully added. This happens 
if the coordinates to spawn at did not match an existing Tile, for example:

```lua
local spell = Battle.Spell.new(player:get_team())
local status = player:get_field():spawn(spell, 100, 1)
```
The `status` value would be `EntityStatus.Queued` if called during the update, or 
`EntityStatus.Failed` otherwise.