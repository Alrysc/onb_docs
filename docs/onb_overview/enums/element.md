# Element

Entities and HitProps have Elements. They determine what Entities take bonus 
damage from, what HitProps deal bonus damage to, and what special interactions 
on a Tile the HitProps might have.

Card mod packages separately can have an Element set to display an icon. This 
can be programmed to be a different Element than the damage dealt by an attack, 
and sometimes is in official games.

The Elements available in v2.1 match the ones you'll find in BN6:

* None
* Fire
* Aqua
* Elec
* Wood
* Sword
* Break
* Cursor
* Wind
* Summon
* Plus

A few more will be added in the future.

Typically, you would say a Navi that is Fire Element is, or has, FireBody. An attack 
that has the Fire Element can be said to deal Fire damaga. I'll be using that wording 
throughout these docs.

The None element is often referred to as Null. I'll mostly do the same.

## Interactions

### None

An Entity with NullBody has no special interactions and does not take additional damage 
from attacks of any other element.

An attack that deals Null damage has no special interactions.

### Fire

An Entity with FireBody takes bonus damage from Aqua attacks and does not trigger damage 
from Lava Tiles. They also take damage while standing on Sea Tiles, as if it was poison.

An attack that deals Fire damage will deal bonus damage to WoodBody Entities. If the Tile 
an Entity is standing when they get hit is in the Grass TileState, they will take bonus damage.
Attacks made on Tiles in the Grass TileState will cause the Tile to revert to `TileState.None`.

### Aqua

An Entity with AquaBody takes bonus damage from Elec attacks. Additionally, an Entity with 
AquaBody will not slip when moving to a Tile that is in the Ice TileState.

An attack that deals Aqua damage will deal bonus damage to FireBody Entities. If the Tile 
an Entity is standing when they get hit is in the Ice TileState, the TileState will be set 
to `TileState.None`, and they will be frozen. If the attack passes over a Tile in the 
`TileState.Volcano` state, the Tile will revert to `TileState.None`

!!! tip "Lava"
    In the official games, an Aqua attack passing over Lava and Volcano Tiles will set 
    them to normal. This does not happen in v2.1, but will in the future.

!!! tip "Sea"
    Contrary to popular belief, it is not true that all AquaBody Characters submerge 
    themselves in Sea Tiles as if they were under the Mole or PopUp chip effect. 
    This is an ability of ToadSoul and certain viruses, not AquaBody.

### Elec

An Entity with ElecBody takes bonus damage from Wood attacks. 

An attack that deals Elec damage will deal bonus damage to AquaBody Entities and Entities 
under the Bubble status. If the Tile an Entity is standing when they get hit is in the 
Sea TileState, they will take bonus damage.

### Wood

An Entity with WoodBody takes bonus damage from Fire attacks. If the Entity is a 
Character, they will also heal over time while standing on Grass Tiles. This healing 
is 1 HP per 20 frames, but slows down to 1 HP per 180 frames if the Character has 
less than 10 HP left.

An attack that deals Wood damage will deal bonus damage to ElecBody Entities. 

### Sword

An Entity with SwordBody takes bonus damage from Break attacks.

An attack that deals Sword damage will deal bonus damage to WindBody Entities. 

### Break

An Entity with BreakBody takes bonus damage from Cursor attacks.

An attack that deals Break damage will deal bonus damage to SwordBody Entities. 

!!! tip "Pierce Guard and Freeze Break"
    A common misconception is that the Break element is what allows an attack to 
    go through shields, break objects, or deal bonus damage to a frozen target. This 
    is not the case. It's a certain hitflag that acts as a signal for this to happen.

    All Break element attacks in the official games happen to have this flag, but 
    many other attacks also do.

### Cursor

An Entity with CursorBody takes bonus damage from Wind attacks.

An attack that deals Cursor damage will deal bonus damage to BreakBody Entities. 

!!! tip "Breaking Traps"
    In BN6, Cursor element attacks also remove active traps set by the target. 
    This is not an automatic effect in ONB, since traps are UGC. When making traps, 
    it's recommended to keep this interaction intact.

### Wind

An Entity with WindBody takes bonus damage from Sword attacks.

An attack that deals Wind damage will deal bonus damage to CursorBody Entities. 

!!! tip "Barriers/Auras"
    In BN6, any Wind element attack will remove a Barrier or Aura. This is not an 
    automatic effect in ONB, since they are UGC. When making Barrier or Aura effects, 
    it's recommended to keep this interaction intact.

### Summon and Plus

The Summon and Plus Elements do nothing as a Body or attack damage.

As a consequence of Element appearing in multiple contexts, the engine allows you to 
set these as the Element of an Entity or as a damage Element. Regardless, I will recommend 
here and elsewhere to never do that. 

The official games use these Elements only for its icon on a chip, which you can do separately
with the mod package. If you use this in other places, mods aiming for accuracy to the source 
material might have unexpected conditions.

Mechanically, they are essentially a Null that isn't actually Null.