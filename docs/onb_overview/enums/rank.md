# Rank

The Rank enum is used when creating a Character. These mostly cover the 
"versions" you see in the official games:

* V1
* V2
* V3
* SP
* EX
* Rare1
* Rare2
* NM

NM is an original Rank, which stands for "Nightmare". Future engine versions 
will add more Ranks.

Despite "EX" having a higher number than "SP", remember that SP is a higher 
rank in the official games.

## Usage

Rank does nothing on its own, but can be read from the `package_init` of a 
Character to branch logic, which can be helpful for setting different stats, 
palettes, behavior, etc.

```lua
function package_init(character)
    local rank = character:get_rank()

    --[[
        Adds a "damage" index on `character`, which this code will 
        read later when creating HitProps.
    ]] 
    if rank == Rank.V1 then 
        character:set_health(40)
        character.damage = 10
    elseif rank == Rank.V2 then 
        character:set_health(60)
        character.damage = 20
    else
        print("Unhandled Rank: "..rank)
        character:set_health(1)
        character.damage = 10
    end
end
```

Make sure your Character has stats set for any Rank. The above code does 
it with an `else` as a catch-all, but you can also set stats earlier in the 
code and then change them with each Rank.

If you don't set health, the Character may not appear in battle.

I will also reiterate that the `damage` index in the above code is not an 
official convention or supported index that does anything on its own. If 
you add one, you would need to read it later when you write other logic.