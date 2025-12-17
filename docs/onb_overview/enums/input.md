# Input

There are three sets of Input values:

1. Pressed
2. Held
3. Released

The following indexes are on each of them:

* Use
* Shoot
* Special
* Left
* Right
* Up
* Down
* Left_Shoulder
* Right_Shoulder

Access by using Input, one of the three types, and then one of the inputs.

```lua
Input.Pressed.Use
Input.Held.Shoot
Input.Released.Special
```

## Usage

Input enum values are only used as the parameter for `Player.input_has`. 
Its usage is described on the [Player page](../entities/player.md).