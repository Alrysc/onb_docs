# Artifact

Artifacts are the lightest Entity type. They have the least logic associated
with them. 

## Creating an Artifact

Calling `Battle.Artifact.new()` will return a new Artifact object. Like every 
other type of Entity, it will need to be spawned on the Field before it will 
do anything.

```lua
local artifact = Battle.Artifact.new()
```

## When to Use

Artifacts should only be used for purely graphical effects. Tying any sort 
of logic to them beyond that is often a mistake, due to their update timing.

Other Entity types may only update during time freeze if they were spawned 
during that time freeze, and the next time time freezes they will not update 
during it. An Artifact, on the other hand, will continue to update during every 
time freeze. This is perfect for a graphical effect, so it can continue to animate, 
finish its animation, and remove itself without blocking the players' view for very 
long. But if you have any logic that affects the battole tied to it, the timing can 
be thrown completely off by time freeze beginning during it.