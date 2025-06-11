# ColorMode

The `ColorMode` enum values determine the way a Color will be applied to 
a Sprite.

The available ColorMode indexes are:

* Multiply
* Additive

The default is usually `ColorMode.Additive`. Descendent Sprites may sometimes 
be `ColorMode.Multiply` by default instead.

!!! tip "Missing Info"
    This notice will be removed once I've confirmed exactly when descendents 
    use Multiply as a default.

## Usage

You can use `Sprite.set_color_mode` to change a Sprite's ColorMode.

```lua
local artifact = Battle.Artifact.new()
local sprite = artifact:sprite()
sprite:set_color_mode(ColorMode.Multiply)
```

Doing that alone may cause the color of the Sprite to sharply change. You will 
usually follow a call to `set_color_mode` with a call to `set_color`.

```lua
sprite:set_color_mode(ColorMode.Multiply)

local red = Color.new(255, 0, 0, 255)
sprite:set_color(red)
```

### Additive

`ColorMode.Additive` will cause Colors to increase RGBA values. Generally, this 
is used to make a Sprite appear brighter. `Color.new(0, 0, 0, 255)` represents 
no change, and `Color.new(255, 255, 255, 255)` represents white.

### Multiply

`ColorMode.Multiply` will cause Colors to multiply RGBA values. Generally, this 
is used to make a Sprite appear darker. `Color.new(255, 255, 255, 255)` represents 
no change, and `Color.new(0, 0, 0, 255)` represents black.