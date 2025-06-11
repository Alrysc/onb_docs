# Highlight

The `Highlight` enum describes the way a Tile should highlight.

The following indexes are available: 

* Solid
* Flash
* None

## Usage

Most commonly, you will call `Tile.highlight` and pass in a Highlight.

```lua
player:get_current_tile():highlight(Highlight.Solid)
```

This will cause the Tile to highlight for one frame. For a highlight that 
lasts longer, you will need to call it multiple frames in a row. Because of 
this, it's common to do it in some `update_func`.

You can also call `Spell.highlight_tile` and pass in a Highlight to cause the 
Spell's current Tile to be highlighted according to the Highlight every frame 
automatically.

### Solid

`Highlight.Solid` keeps the Tile lit up for every frame that it is called.

### Flash

`Highlight.Flash` works on a global timer, so using this Highlight for only one 
frame may often not light the Tile up at all. The Tile will light up for 4 frames, 
then stay normal for 4 frames, and repeat.

Because the flash highlight is on a global timer, you may start trying to highlight 
in the middle of the 4-on-4-off sequence.

If you need to have two different Flash highlights that are asynchronous, you will 
need to use `Highlight.Solid` with your own timer to determine when the highlight 
should be on and off.

### None

`Highlight.None` will cause there to be no highlight on the Tile.

## Hidden Tile

Tiles with `TileState.Hidden` as their state will never highlight.

## Priority

Calls to `Tile.set_highlight` for a particular Tile act as a request. If multiple 
requests are made in one frame, priority is given to the largest. The highest 
priority is `Highlight.None`, and the lowest is `Highlight.Solid`.