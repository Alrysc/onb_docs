# AudioPriority

`AudioPriority` values are used in calls to `Engine.play_audio` to describe 
how aggressively the audio will try to be played. 

Similar to the GBA games, ONB has a number of SFX channels. Using certiain 
AudioPriority could cause a sound to defer to another, not playing at all 
if channels are busy. The following priorities exist:

* Lowest
* Low
* High
* Highest

## Usage

`Engine.play_audio` takes an AudioPriority as its second parameter.

```lua
Engine.play_audio(SFX_HANDLE, AudioPriority.Low)
```

`SFX_HANDLE` in the above snippet must be an audio handle returned from a 
call to `Engine.load_audio`, or an [AudioType enum value](./audiotype.md).

### Lowest

`AudioPriority.Lowest` plays on a free channel, but other `Lowest` priority 
sounds will not play at the same time.

### Low

`AudioPriority.Low` plays the sound a free channel. It can play at the same 
time as other sounds. If you ever have issues where a sound doesn't play, using 
this priority will solve it.

### High

`AudioPriority.High` forces your sound to play, but other High priority can't play 
at the same time. This also means your sound won't play if another High priority sound 
is currently playing.

### Highest

`AudioPriority.Highest` tries to force your sound to play. 

!!! tip "Missing Info"
    I'm not sure what the difference between Highest and Low is in practice. This notice 
    will be removed once I've done more research.

## Usage Suggestion

I almost always use the Low piority. It practically guarantees that the sound will play.
If you are sure your code is supposed to be playing it but you're not hearing it, and you 
know it's loud enough to hear, you may want to change to Low priority.