# Playback

`Playback` enum values describe how an Animation should play. 

It has the following indexes:

* Once
* Loop
* Bounce
* Reverse

The default for an Animation is `Playback.Once`. 

## Usage

You can set an Animation's Playback type using `Animation.set_playback`.

```lua
local artifact = Battle.Artifact.new()
local anim = artifact:get_animation()
anim:set_playback(Playback.Loop)
```

TODO for all: Exact times end func plays, if callbacks play multiple times on 
loop/bounce

### Once

The default Playback for an Animation is `Playback.Once`. With this, an Animation 
will play once and then stop.

Completion callbacks will run on the game frame that the animation's last keyframe 
finishes its duration.

### Loop

`Playback.Loop` will cause an Animation to restart once it reaches its end.

Completion callbacks run at a similar time to `Playback.Once`, but will play again 
at the end of each loop. `on_frame` callbacks will also run again each loop, unless 
`Animation.on_frame` was called with `true` as the last parameter.

!!! bug "Repeated Callbacks"
    While looping, completion callbacks may run an additional time one game frame after 
    it runs. `on_frame` callbacks may also run again all at once when the loop happens.

!!! bug "Extra Callbacks"
    While looping, an `on_frame` callback set for the keyframe number that is one higher 
    than the Animation's final keyframe may run when the loop happens.

### Bounce

`Playback.Bounce` will cause an Animation to play until its end, then start playing in 
reverse.

!!! warning "No Bounce"
    `Playback.Bounce` may not function properly in v2.0.

### Reverse

`Playback.Reverse` will cause an Animation to start on its last frame and play until 
it reaches the first frame, and then stop.

When using this Playback, `on_frame` callbacks will not play in reverse. In other words, 
the first frame to play will still call the `on_frame` callbacks for frame 1. The Animation 
still proceeds from frame 1 to frame 2, but frame 1 is the last animation frame, and frame 2 
is the second to last animation frame, and so on.

Completion callbacks will run on the game frame that the animation's last keyframe 
finishes its duration.