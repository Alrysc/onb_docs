# Blocks

The `Blocks` enum contains indexes representing the supported NaviCust block 
colors. This includes the following:

* White
* Red
* Green
* Blue
* Pink
* Yellow

Only colors from BN6 are included. 

## Usage

You will use this enum to set the color of blocks in your Block packages.

```lua
function package_init(block)
    block:set_color(Blocks.White)
    -- Omitted the rest of package_init for example
end
```

If no color is set, a default of `Blocks.White` will be used.