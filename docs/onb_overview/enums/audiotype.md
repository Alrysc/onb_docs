# AudioType

You can play sounds in ONB using `Engine.play_audio`. You will mostly use it with 
sound effects that are in your mods, but you can also access the sound effects 
bundled with the engine by using the AudioType enum.

AudioType has the following indexes:

* CounterBonus
* DirTile
* Fanfare
* Appear
* AreaGrab
* AreaGrabTouchdown
* BusterPea
* BusterCharged
* BusterCharging
* BubblePop
* BubbleSpawn
* GuardHit
* Cannon
* Counter
* Wind
* ChipCancel
* ChipChoose
* ChipConfirm
* ChipDesc
* ChipDescClose
* ChipError
* CustomBarFull
* CustomScreenOpen
* ItemGet
* Deleted
* Explode
* Gun
* Hurt
* PanelCrack
* PanelReturn
* Pause
* PreBattle
* Recover
* Spreader
* SwordSwing
* TossItem
* TossItemLite
* Wave
* Thunder
* ElecPulse
* Invisible
* ProgramAdvance
* LowHP
* DarkCard
* PointSFX
* NewGame
* Text
* Shine
* TimeFreeze
* Meteor
* Deform

## Usage

You can use an AudioType in the same place you would use an audio handle in 
a call to `Engine.play_audio`.

```lua
Engine.play_audio(AudioType.Shine, AudioPriority.Low)
```

It's possible that these sound files may change quality or volume in the future, 
or may be removed or swapped for other sounds.