# LockType

The `LockType` enum describes the condition under which a CardAction will be 
considered complete. 

It has the following indexes:

* Animation
* Async
* Sequence

The default is `LockType.Animation`.

## Usage

You will never use the LockType enum on its own. Instead, you will use a 
series of related functions to set the type on the CardAction. These 
functions refer to these types as a "lockout", but don't confuse them with 
another enum, `Lockout`.

### Animation

The CardAction will end when the associated Animation ends. This is the 
default, so you are unlikely to ever set this. But if you wanted to, you 
can use `make_animation_lockout`.

```lua
local action = Battle.CardAction.new(player, "PLAYER_IDLE")
action:set_lockout(make_animation_lockout())
```

### Async

The CardAction will do cleanup, free the user, and call its `CardAction.action_end_func` 
when the associated Animation ends, but it will continue calling its `CardAction.update_func` 
until a specified amount of time passes based on the value given to the Async lockout.

For example, see the code below:

```lua
local action = Battle.CardAction.new(player, "PLAYER_IDLE")
action:set_lockout(make_async_lockout(0.5))
```

`make_async_lockout` is called with a time in milliseconds. The above code gives 0.5 (30 frames).

Once the `PLAYER_IDLE` animation finishes, `action` will continue updating for 30 frames.

#### Lockout

The Async LockType's timer has an additional effect. Until it expires, CardActions of the associated 
LockoutGroup will not be performed.

!!! tip "Missing Info"
    This notice will be removed once I figure out if the CardAction is still queued or if it will 
    only be discarded once it should begin waiting to execute.