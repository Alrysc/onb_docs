# CardClass

`CardClass` indexes describe a card's class. A card's preview color 
changes with the CardClass, and certain effects may target certain classes. 

The following CardClass indexes are available:

* Standard
* Mega
* Giga
* Dark

By default, `CardClass.Standard` is used.

## Usage

A CardClass can be set on a CardProperties object's `card_class` index. Usually, this is 
set in the `package_init` of a card mod.

```lua
function package_init(package) 
    local props = package:get_card_props()
    props.card_class = CardClass.Standard
    -- Omitted the rest of the package_init for the example
end
```

## Copy Limits

In the official games, each class would have an associated limit within your folder. For 
example, in Battle Network 6, you could only have up to 5 Mega chips in your folder, and 
only 1 Giga chip without additional modifiers.

Currently, ONB does not enforce these limits. However, in the future, expect to see user-
created servers that do, or that may control limits in other ways based on their CardClass.

## Effects

A card's CardClass can be read by code in certain places. Nothing in the engine does, but 
user-created mods might change behavior based on them. For an official example, see the 
Beat NaviCust Part, which will activate when the opponent uses a Mega or Giga class chip. 

Currently in ONB, we can only reasonably act on the CardClass in the `filter_hand_step`. 
This would allow, for example, an effect like GaiaSword that only consumes Standard chips 
in your hand.

## Dark

Dark chips only appeared in a meaningful capacity in BN4 and BN5. ONB takes more of a BN5 
approach with Dark chips, as they can be put into your folder. 

Currently, ONB replicates the following Dark chip behavior:

* Hovering a Dark chip in the Custom window will dim the screen, lower music volume, 
and play a sound
* A Dark chip appearing in the Custom window will cause the cursor to jump to it 
when Custom is opened

!!! bug "Music Volume"
    If you leave a battle using the Esc key while the cursor is on a Dark chip, the music 
    volume will not return to its original volume.

A few more behaviors may be replicated in future versions.

### Expectations

In the official games, Dark chips inflict the user with a bug, and may also inflict whatever 
they hit with a bug as well, under certain conditions. Using any during a battle will also make
you permanently lose 1 max HP when the battle ends, up to some maximum. 

Due to the nature of ONB, you shouldn't necessarily expect either of those to be true. All 
card mods are made by modders, so it's up to them to add bugs and so on. What a card mod 
can't do is reduce max HP, which you should especially expect from the nature of ONB. But 
a server might be able to track that you have used a Dark chip while on the server and do 
something, such as lowering max HP, based on it. That would only apply to that server, and 
the server must be programmed to do such things.