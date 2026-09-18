# Glossary

## Area vs Stage vs Room/Zone

- The term area is used to denote the collection of all stages that share the same set of area switch flags. For example, the Forest Temple dungeon stage (D_MN05), midboss room stage (D_MN05B) and boss room stage (D_MN05A) all share the same area switch flags.
- A stage in this game can house one or multiple rooms, of which not all have to be loaded into memory at the same time. It can also define properties shared across all rooms. A stage can load actors that are always potentially spawned, regardless of the room the player is in. For example, many doors in each dungeon are typically spawned in at all times, even while in neither of the rooms connected by said doors.
- A room is simply one room within said stage. In the decomp, this is alternatively also referred to as a zone at times. This document will always use the term room, even if the various zones within Hyrule Field, for example, don't look like "rooms".


## Flags

### Switch Flag

- 0x00 to 0x7F: Permanently saved area switch flags.
- 0x80 to 0xBF: Temporarily saved area switch flags that are reset upon entering a new area.
- 0xC0 to 0xDF: Temporarily saved room-specific switch flags. Even while remaining in the same area, these may be reset at some point. The exact conditions for this are currently unknown to me.
- 0xE0 to 0xEF: Temporarily saved room-specific switch flags that immediately get reset upon entering the room or respawning in the same room.

### Treasure Box Flag

Treasure Box Flags are mostly dedicated to determining whether a treasure chest has been opened or not. There are a few other actors that also make use of them, such as small keys that are not obtained via treasure chests, tears of light obtained in the Twilight or actors that can have a special map marker attached to them such as the monkeys in Forest Temple, Ooccoo, Telma's carriage during the escort mission, the big statue in Temple of Time or the sols in Palace of Twilight.

### Item Flag

- 0x00 to 0x7F: TBD
- 0x80 to 0x9F: Permanently saved item flags for each area. Always used for pieces of heart and for rupees that can only be collected once.
- 0xA0 to 0xBF: TBD
- 0xC0 to 0xCF: TBD

## Names

### Proc Name

This refers to the name of the actor's proc name by which it is spawned. The names of said enum are taken from d_procname.h in the decomp.

### File Name

This refers to the name of the file in which the actor's code is written.

### Object Name

This refers to the name by which the actor-related chunks in the dzs or dzr files - such as ACTR, SCOB, TRES or Door - refer to the actor that is intended to be spawned. Every object name then refers to a proc name and an additional argument (signed 8 bit integer).
Note that any actor may have multiple object names, possibly to aid in more clearly denoting different sub types of these actors. However, this does not always mean that the different name will actually change the actor itself. While there are many object names for treasure chests, they themselves do not affect the treasure chest that is spawned.
There are only some examples where the name used also affects an argument variable that is then handled by the actor itself. One such example is the Helmasaur and the Helmasaurus. If spawned via the object name "E_mm", the argument -1 is given to the actor, causing it to be a basic Helmasaur as seen in Lakebed Temple. If spawned via the object name "E_mm2", the argument 0x01 is given to the actor instead, causing it to be a great Helmasaurus as seen in City in the Sky.
There are also actors that do not have an object name, as they are never spawned via the actor-related chunks in the dzs or dzr files. One such example are the Toados spawned by the Deku Toad miniboss. Here, Deku Toad simply spawns them via their proc name id instead.


# Item Actors

## Treasure Chest

### Names

- Proc Name: PROC_TBOX
- File Name: d_a_tbox.cpp
- Object Names:
  - "tboxA0" -> argument: -1
  - "tboxA1" -> argument: -1
  - "tboxA2" -> argument: -1
  - "tboxB0" -> argument: -1
  - "tboxB1" -> argument: -1
  - "tboxB2" -> argument: -1
  - "tboxJ0" -> argument: -1
  - "tboxP0" -> argument: -1
  - "tboxB00" -> argument: -1
  - "tboxB01" -> argument: -1
  - "tboxW0" -> argument: -1
  - "tboxEL0" -> argument: -1
  - "tboxEL1" -> argument: -1

### Description

Actor used for all treasure chests that can be opened to receive an item.


### Parameters

| Variable | Bit mask | Bit shift | Bit length | Name | Description |
|---|---|---|---|---|---|
|Parameter|0xFF000000|24|8|Drop Event Number|Event that is to be played upon the treasure chest spawning or being dropped. If set to 0xFF, the default event is played when the chest spawns.|
|Parameter|0x00F00000|20|4|Chest Shape|Defines the shape of the treasure chest. If set to 0, it's a small wooden chest. If set to 1, it's a regular chest. If set to 2, it's a boss key chest.|
|Parameter|0x000FF000|12|8|Spawn Switch|Switch flag that causes this chest to be spawned.|
|Parameter|0x00000FC0|6|6|Treasure Switch|Treasure switch that is set to true upon this chest being opened. If the switch is already set to true upon this chest being spawned, it will already be in the opened state.|
|Parameter|0x0000003F|0|6|Function Type|Determines whether this chest has any special behavior. See Function Types below.|
|Angle X|0x000F|0|8|Switch Type|Determines behavior related to the observing of switch flags. See Switch Types below.|
|Angle Z|0xFF00|8|8|Item Number|Number of the item that the player recieves upon opening this chest.|
|Angle Z|0x00FF|0|8|Path Id|TBD|


### Function Types

- 0x00: The chest does not have any special behavior.
- 0x01: The chest does not appear until the observed spawn switch flag is set to true.
- 0x02: The chest does not appear until all enemies in the room have been defeated.
- 0x05: TBD
- 0x06: Determines behavior related to the chest on the pillar in the Tile Worm room of the Forest Temple.
- 0x07: TBD

### Switch Types

- 0x00: The treasure chest does not only observe the spawn switch at 0x000FF000 of the parameter but also the 3 following switch flags. This is used for the treasure chest on the pillar in the Tile Worm room of the Forest Temple, as that chest can be dropped into sixteen different directions based on the direction from which the player rolls against the pillar.
- 0x0F: If the function type is set to 0x01, the treasure chest will spawn upon the observed switch being set to true.


# Enemy Actors

## Deku Baba

### Names

- Proc Name: PROC_E_HB
- File Name: d_a_e_hb.cpp
- Object Names:
  - "E_hb" -> argument: -1

### Description

Basic Deku Baba enemy. Does not include the more aggressive Baba Serpent variant.

### Parameters

| Variable | Bit mask | Bit shift | Bit length | Name | Description |
|---|---|---|---|---|---|
|Parameter|0xFF000000|24|8|Defeat Flag|Is set to true upon defeat. Prevents creation of this actor if set to true. If this parameter is set to 0xFF, there is no defeat flag.|
|Parameter|0x00FF0000|16|8|Item Drop|Determines the item that is dropped upon destroying the Deku seed left behind by the Deku Baba. If set to 0xFF, there is no item drop.|
|Parameter|0x0000FF00|8|8|Player Search Range|Defines the search range for the player. The resulting range is X*100. If this parameter is set to either 0x00 or 0xFF, the search range is 500.|
|Parameter|0x000000C0|6|2|Stay Awake|If set to 1, the Deku Baba is permanently awake.|
|Parameter|0x00000030|4|2|Higher Attack Frequency|If set to 1, the attack cooldown is slightly lowered from a range of [30, 60] to [30, 50] frames.|
|Parameter|0x0000000F|0|4|Upside Down|Defines whether the Deku Baba is rotated so that it looks like it's hanging from the ceiling, or not. If set to 0x2, it is upside down.|


## Shadow Deku Baba

### Names

- Proc Name: PROC_E_YD
- File Name: d_a_e_yd.cpp
- Object Names:
  - "E_yd" -> argument: -1

### Description

Twilight realm variant of the Deku Baba.


### Parameters

| Variable | Bit mask | Bit shift | Bit length | Name | Description|
|---|---|---|---|---|---|
|Parameter|0xFF000000|24|8|~~Defeat Flag~~|Likely intended to be this actor's defeat flag. However, neither the switch flag check nor the setting of the switch flag are implemented.|
|Parameter|0x0000FF00|8|8|Player Search Range|Defines the search range for the player. The resulting range is X*100. If this parameter is set to either 0x00 or 0xFF, the search range is 500.|
|Parameter|0x000000C0|6|2|Stay Awake|If set to 1, the Shadow Deku Baba is permanently awake.|
|Parameter|0x00000030|4|2|Higher Attack Frequency|If set to 1, the attack cooldown is slightly lowered from a range of [30, 60] to [30, 50] frames.|
|Parameter|0x0000000F|0|4|Upside Down|Defines whether the Shadow Deku Baba is rotated so that it looks like it's hanging from the ceiling, or not. If set to 0x2, it is upside down.|



## Baba Serpent

### Names

- Proc Name: PROC_E_DB
- File Name: d_a_e_db.cpp
- Object Names:
  - "E_db" -> argument: -1

### Description

More aggressive and red-colored variant of the Deku Baba.


### Parameters

| Variable | Bit mask | Bit shift | Bit length | Name | Description|
|---|---|---|---|---|---|
|Parameter|0xFF000000|24|8|Defeat Flag|Is set to true upon defeat. Prevents creation of this actor if set to true. If this parameter is set to 0xFF, there is no defeat flag.|
|Parameter|0x00F00000|20|4|Stay Awake|If set to 1, the Deku Serpent is permanently awake.|
|Parameter|0x000F0000|16|4|Higher Attack Frequency|If set to 1, the attack cooldown is slightly lowered from a range of [30, 60] to [30, 50] frames.|
|Parameter|0x0000FF00|8|8|Player Search Range|Defines the search range for the player. The resulting range is X*100. If this parameter is set to either 0x00 or 0xFF, the search range is 500.|
|Parameter|0x000000FF|0|8|Upside Down|Defines whether the Baba Serpent is rotated so that it can also look like it's hanging from the ceiling, or not. If set to 0, it is not rotated. If set to 1, it is rotated but has extra behavior specific to the Ook boss fight. If set to 2, it is only rotated and behaves like a normal enemy otherwise.|




## Shadow Baba Serpent

### Names

- Proc Name: PROC_E_YH
- File Name: d_a_e_yh.cpp
- Object Names:
  - "E_yh" -> argument: -1

### Description

Twilight realm version of Baba Serpents. Funnily enough, these can exclusively be found in the first room in the left tower of Palace of Twilight. However, as they share the exact same model as the Shadow Deku Baba, they're typically not known to be their own enemy type.

### Parameters

| Variable | Bit mask | Bit shift | Bit length | Name | Description|
|---|---|---|---|---|---|
|Parameter|0xFF000000|24|8|Deactivation Flag (de facto)|While clearly intended to be a defeat flag, this actor only prevents being created if the observed switch flag is set. However, it does not set the flag to true by itself upon defeat. If set to 0xFF, there is no deactivation flag, which is the case for all Shadow Baba Serpents found in the game anyway.|
|Parameter|0x00F00000|20|4|Stay Awake|If set to 1, the Shadow Baba Serpent is permanently awake.|
|Parameter|0x000F0000|16|4|Higher Attack Frequency|If set to 1, the attack cooldown is slightly lowered from a range of [30, 60] to [30, 50] frames.|
|Parameter|0x0000FF00|8|8|Player Search Range|Defines the search range for the player. The resulting range is X*100. If this parameter is set to either 0x00 or 0xFF, the search range is 500.|
|Parameter|0x000000FF|0|8|Upside Down|Defines whether the Shadow Baba Serpent is rotated so that it looks like it's hanging from the ceiling, or not. If set to 0x1 or 0x2, it is upside down. There's no instance of a Shadow Baba Serpent existing in the game where this is set to something other than 0. There's also some extra code dedicated to this value being 0x1, possibly a holdover from the Baba Serpents? (TBD)|

## Keese

TBD

## Shadow Keese

TBD

## Rat

TBD

## Shadow Vermin

TBD

## Guay

TBD

## Kargarok

TBD

## Shadow Kargarok

TBD

## Shadow Beast

TBD

## Shadow Insect

TBD

## Bokoblin

### Names

- Proc Name: PROC_E_OC
- File Name: d_a_e_oc.cpp
- Object Names:
  - "E_oc" -> argument: -1


### Parameters

| Variable | Bit mask | Bit shift | Bit length | Name | Description|
|---|---|---|---|---|---|
|Parameter|0xFF000000|24|8|Bridge Discovery Flag|Defines the switch flag that will be set to true upon this Bokoblin detecting the player on the bridge in the North Bridge Room of the Forest Temple. This parameter is set to 0xFF on all Bokoblins outside of that specific room.|
|Parameter|0x00FF0000|16|8|Defeat Flag|Is set to true upon defeat. Prevents creation of this actor if set to true. If this parameter is set to 0xFF, there is no defeat flag.|
|Parameter|0x00000100|8|1|Increase Search Radius|Defines the Bokoblin's maximum search radius. If set to 0, the radius is 1200. If set to 1, the radius is 5000.|
|Parameter|0x000000FF|0|8|Special Behavior|Defines whether the Bokoblin has special behavior. The most commonly used values are 0x01 and 0xFF. Whatever difference there is between these appears to be minor. If set to 2, the Bokoblin will conduct the bridge check in the North Bridge room of the Forest Temple. Values 3 and 4 are related to the Bokoblins in the Totem Pole Monkey room.|
|Angle X|0xFF00|8|8|Bokoblin Type|Defines whether the Bokoblin is blue (40 HP) or red (220 HP). If set to 0x00 or 0xFF, it's blue. If set to any value in between, it's red.
|Angle X|0x00FF|0|8|Movement Range|Defines the Bokoblin's range of movement. The resulting range is X * 100.|


## Bulblin

TBD

## Shadow Bulblin

TBD

## Bullbo

TBD


## Walltula

TBD

## Skulltula

TBD

## Bombling

TBD

## Deku Like

TBD

## Big Baba

TBD

## Tile Worm

TBD

## Bomskit

TBD

## Goron

TBD

## Torch Slug

TBD

## Dodongo

TBD

## Tektite

TBD

## Beamos

TBD

## Toadpoli

TBD

## Toado

TBD

## Helmasaur / Helmasaurus

### Names

- Proc Name: PROC_E_MM
- File Name: d_a_e_mm.cpp
- Object Names:
  - "E_mm" -> argument: -1 (for basic Helmasaur)
  - "E_mm2" -> argument: 0x01 (for bigger Helmasaurus)

### Description

Actor both for a basic Helmasaur as found in Lakebed Temple and also for the greater Helmasaurus as found in City in the Sky.

### Parameters

| Variable | Bit mask | Bit shift | Bit length | Name | Description|
|---|---|---|---|---|---|
|Argument|0xFF|0|0|Helmasaur Type|If set to 0x01, this actor is a Helmasaurus instead of a Helmasaur.|
|Parameter|0x0000FF00|8|8|Defeat Flag|Is set to true upon defeat. Prevents creation of this actor if set to true. If this parameter is set to 0xFF, there is no defeat flag.|
|Parameter|0x000000FF|8|8|Player Search Range|Defines the search range for the player. The resulting range is X*10. If this parameter is set to either 0x00 or 0xFF, the search range is 1000 by default.|

## Lizalfos

TBD

## Dynalfos

TBD

## Skullfish

TBD

## Shell Blade

TBD

## Bari

TBD

## Chu

TBD

## Chu Worm

TBD

## Bombfish

TBD

## Puppet

TBD

## Imp Poe

TBD

## Poe

TBD


## Moldorm

TBD

## Bubble

TBD

## Poison Mite

TBD

## Ghoul Rat

TBD

## Gibdo

TBD

## Stalchild

TBD

## Stalfos

TBD

## Staltroop

TBD

## Stalhound

TBD

## White Wolvos

TBD

## Mini Freezard

TBD

## Freezard

TBD

## Chilfos

TBD

## Baby Gohma

TBD

## Young Gohma

TBD

## Armos

TBD

## Darknut

TBD

## Aeralfos

TBD

## Zant Mask

TBD

## Zant Hand

TBD

# Doors and Gates

## Normal Door

### Names

- Proc Name: PROC_DOOR20
- File Name: d_a_door_shutter.cpp
- Object Names:
  - "door" -> argument: -1
  - "ndoor" -> argument: -1
  - "tadoor" -> argument: -1
  - "yodoor" -> argument: -1
  - "nadoor" -> argument: -1
  - "l9door" -> argument: -1
  - "l7door" -> argument: -1
  - "bigdoor" -> argument: -1

Make sure not to mix up the object names of the doors in City in the Sky. The object name of the doors without loading transitions starts with a lower-case l ("l7door"), while the object name of the doors with loading transitions starts with an upper-case L ("L7door").

### Description

This door actor connects rooms within an area that do not require a loading transition.

### Parameters

| Variable | Bit mask | Bit shift | Bit length | Name | Description|
|---|---|---|---|---|---|
|Parameter|0x80000000|31|1|Is Message Door|Defines whether there's a potential dialogue event tied to opening the door.|
|Parameter|0x70000000|28|3|Back Light Influence|Defines the light's influence on the door while in the back room.|
|Parameter|0x0E000000|25|3|Front Light Influence|Defines the light's influence on the door while in the front room.|
|Parameter|0x01F80000|19|6|Back Room No|Defines the room at the backside of the door.|
|Parameter|0x0007E000|13|6|Front Room No|Defines the room at the frontside of the door.|
|Parameter|0x00001C00|10|3|Back Option|Defines whether the door is potentially locked or barred from the backside. See Front/Back Options below.|
|Parameter|0x00000300|8|2|Front Option|Defines whether the door is potentially locked or barred from the frontside. See Front/Back Options below.|
|Parameter|0x000000E0|5|3|Door Model|Defines the door model used. Please note that this does not allow for any door model in the game to be used anywhere, as the game loads the door model based on the area the player is in. This parameter only allows for the model to be changed if the area provides multiple door models. As such, this parameter is set to 0 for most doors. One example of this parameter not being 0 is the door in the Courtyard of Snowpeak Ruins that leads to the cannon balls in the west section, as it is possible to see through that door. Shutter doors and knob doors have different model pools.|
|Parameter|0x0000001F|0|5|Door Type|Defines the opening animation for the door.|
|Angle X|0xFF00|8|8|Debris Switch Flag|Defines the switch flag that indicates whether the door has already been opened before. Opening the door for the first time causes debris to fall if it has not been opened yet. If set to 0xFF, there's never any debris from the door.|
|Angle Z|0xFF00|8|8|Back Room Switch Flag|Defines the switch flag that indicates whether the door is unlocked or unbarred on the backside.|
|Angle Z|0x00FF|0|8|Front Room Switch Flag|Defines the switch flag that indicates whether the door is unlocked or unbarred on the frontside.|

### Event Parameters

The following parameters are listed separately as I have no real understanding of them. There are also bit mask collisions caused by these.

| Variable | Bit mask | Bit shift | Bit length | Name | Description|
|---|---|---|---|---|---|
|Angle X|0xFF00|8|8|Backside Stop Event|Defines the event called upon the door getting barred on the backside.|
|Angle X|0x00FF|0|8|Frontside Stop Event|Defines the event called upon the door getting barred on the frontside.|
|Angle X|0xFFFF|0|16|Message Number|Defines the dialogue event potentially triggered upon opening the door if the door is a message door.|




### Front / Back Option
- 0: The door is neither locked nor barred.
- 1: The door is barred if the corresponding switch flag is not set to true upon entering the room. The bars are lifted if the corresponding switch flag is set to true.
- 2: The door is locked if the corresponding switch flag is not set to true upon entering the room. The lock can be removed by using a key on it.
- 3: The door is barred if the corresponding switch flag is not set to true upon entering the room. Setting the corresponding switch flag to true while in said room will not lift the bars.

### Door Type
- 0x00: Shutter door that Link manually opens upwards.
- 0x01: Shutter door that Link manually opens sideways.
- 0x02: Shutter door from Palace of Twilight.
- 0x09: Knob door.
- 0x0A: Shutter door that Link manually opens upwards. Used in Hyrule Castle.
- 0x0C: Shutter door that opens by itself. Used in City in the Sky.

## Midboss Door

### Names

- Proc Name: PROC_L1MBOSS_DOOR
- File Name: d_a_door_shutter.cpp
- Object Names:
  - "L1Mdoor" -> argument: 0x01
  - "L2Mdoor" -> argument: 0x02
  - "L3Mdoor" -> argument: 0x03
  - "L4Mdoor" -> argument: 0x04
  - "L5Mdoor" -> argument: 0x05
  - "L6Mdoor" -> argument: 0x06
  - "L7Mdoor" -> argument: 0x07
  - "L8Mdoor" -> argument: 0x08
  - "L7door" -> argument: 0x0A
  - "L5door" -> argument: 0x0B

Make sure not to mix up the object names of the doors in City in the Sky. The object name of the doors without loading transitions starts with a lower-case l ("l7door"), while the object name of the doors with loading transitions starts with an upper-case L ("L7door").

### Description

While being called Midboss Door, this actor is not exclusively used as a door to and from midboss rooms. Instead, it is used as a door between any rooms with a loading transition.

### Parameters

TBD

## Door Stopper 2

### Names

- Proc Name: PROC_Obj_Stopper2
- File Name: d_a_obj_stopper2.cpp
- Object Names:
  - "dstop" -> argument: -1

### Description

Alternatively to defining the door bars in the parameters of a door itself, this actor can also be used to define door bars on a door that does not handle bars on its own. To do so, this actor is placed at the exact position of the door that is to be potentially barred.

### Parameters

| Variable | Bit mask | Bit shift | Bit length | Name | Description|
|---|---|---|---|---|---|
|Parameter|0x0000FF00|8|8|Event Id|Event that is to be played if the door gets either closed or opened. If set to 0xFF, there is no event.|
|Parameter|0x000000FF|0|8|Switch Flag|Switch flag that determines whether the door is to be barred or not.|


# Switch Object Actors

TBD



# Switch Logic Actors

## And Switch 2

### Names

- Proc Name: PROC_ANDSW2
- File Name: d_a_andsw2.cpp
- Object Names:
  - "AND_SW2" -> argument: -1

### Description

This actor serves to observe one or multiple switch flags and, based on their state and various other parameters, either set its target switch flag to true or to false.



### Parameters

| Variable | Bit mask | Bit shift | Bit length | Name | Description|
|---|---|---|---|---|---|
|Parameter|0xFF000000|24|8|First Observed Switch|Switch flag that is to be observed. If the Number parameter is greater than 1, this switch flag is merely the first of a row of switches which are all observed. If, for example, this parameter is set to 0xC1 and the Number parameter is set to 0x10 (16) all switch flags from 0xC0 to 0xCF (0xC0+0x0F) will be observed. 
|Parameter|0x00FF0000|16|8|Target Switch|Defines which switch flag is going to be set or unset by this actor.|
|Parameter|0x0000FF00|8|8|Logic Type|Defines which logic is used. See Logic Types below.|
|Parameter|0x000000FF|0|8|Number|Defines how many switch flags are being observed.|
|Angle X|0x00FF|0|8|Event Number|Defines which event is going to be played upon this switch being triggered. Should be set to 0xFF by default if no event is intended to be played. Otherwise, this could cause issues.|
|Angle X|0xFF00|8|8|Switch Answer|Defines a sequence of bits that the row of switch flags is compared to. The lowest bit is being compared to the first switch flag. See Logic Types below.|
|Angle Z|0x00FF|0|8|Timer|Defines a time delay between the positive check of the observed switch(es) and the target switch being set. 0xFF is interpreted as no delay. The resulting delay is X * 15 frames.|

### Logic Types

The logic type determines how the actor behaves. The behaviors it affects are...
- whether the actor can also set the target switch to false,
- the requirements for the switch flag check to be true, such as...
  - all switch flags needing to be true,
  - the switch flags having to be equal to the Switch Answer,
  - only one switch flag needing to be true,
- whether the actor can interrupt the delayed setting of the target switch if the observed switch flags no longer fulfil the desired condition.

|Logic Type|Can unset target switch|Switch Condition|Can Interrupt Trigger|
|---|---|---|---|
|0|False|All True|False|
|1|TBD|All True|True|
|2|False|Equal to Switch Answer|False|
|3|True|Equal to Switch Answer|True|
|4|False|At Least One True|False|
|5|TBD|All True|True|
|6|True|All True|True|


## Timer Object

### Names

- Proc Name: PROC_Obj_Timer
- File Name: d_a_obj_timer.cpp
- Object Names:
  - "ObjTime" -> argument: -1

### Description

This actor observes a switch flag and, if it's been set to true, sets it to false again after a defined amount of time passes.

### Parameters

| Variable | Bit mask | Bit shift | Bit length | Name | Description|
|---|---|---|---|---|---|
|Parameter|0x04000000|26|1|Play Timer Sound|Defines whether the timer plays the countdown sound. Note that no sound is played while the remaining time is greater than 20 seconds.|
|Parameter|0x01000000|24|1|Demo Stop|If the timer sound is enabled, this stops the timer during events. More accurately, it extends the countdown by 30 ticks every 30 ticks if an event is currently active. The timer sound will still play.|
|Parameter|0x00FF0000|16|8|Target Switch|Defines which switch flag is to be observed and unset upon the countdown reaching 0.|
|Parameter|0x0000FF00|8|8|Deactivation Switch|Defines the switch flag which, upon being set, disables this actor. As such, the countdown is then disabled.|
|Parameter|0x000000FF|0|8|Countdown Time|Defines the start time of the countdown. The resulting countdown is X * 15 frames.|


## Area Switch

### Names

- Proc Name: PROC_SWC00
- File Name: d_a_swc00.cpp
- Object Names:
  - "SwAreaC" -> argument: -1
  - "SwAreaS" -> argument: -1

### Definition

This actor defines an area which, upon being entered, triggers a switch flag to be set or unset.

### Parameters

| Variable | Bit mask | Bit shift | Bit length | Name | Description|
|---|---|---|---|---|---|
|Parameter|0xFF000000|24|8|Event Number|Defines the event to be played upon player detection. 0xFF is interpreted as no event.|
|Parameter|0x00100000|20|1|Scale Increase|If set to 1, the scale of the area is increased in a specific manner.
|Parameter|0x000C0000|18|2|Shape|Defines the shape of the observed area. If set to 0, the area is a box. If set to 3, the area is a cylinder.|
|Parameter|0x0000FF00|8|8|Activation/Deactivation Switch|Switch flag that shall either activate or deactivate this actor. If this is set to 0xFF, the actor is always active.|
|Parameter|0x000000FF|0|8|Target Switch|Switch flag to be set or unset by this actor.|
|Angle X|0x0F00|8|4|Logic Type|Defines the logic behavior of this actor. See Logic Types below.|
|Angle X|0x00FF|0|8|Condition|Defines an additional condition that must be fulfilled for successful player detection. If set to 0, the player must be riding Epona. If set to 2, the actor will check for Epona's position instead of Link's position. If set to 3, the player must neither be riding Epona nor be in Wolf form. If set to 4, the player must be carrying his lantern.|
### Logic Types

The logic type determines how the actor behaves. The behaviors it affects are...
- whether the switch defined at 0x0000FF00 of the parameter is an activation or a deactivation switch,
- whether the successful detection of the player causes the actor to set the target switch to true or to false,
- whether the unsuccessful detection of the player causes the actor to set the target switch to false,
- whether the actor is explicitly deleted upon its triggered event ending,
- whether the actor is already deleted upon creation if the target switch is either true or false.

| Logic Type | Active if ac/deac switch is... | Detection -> set target switch to... | No detection -> set target switch to... | Delete after Event | Prevent creation if target switch is...
|---|---|---|---|---|---|
|0x00|True|True|False|False|-|
|0x01|True|True|-|False|-|
|0x02|True|False|-|False|-|
|0x03|False|True|-|True|True|
|0x04|False|True|False|False|-|
|0x05|False|True|-|False|-|
|0x06|False|False|-|False|-|
|0x07|True|False|-|True|False|
|0x08|False|False|-|True|False|
|0x0F|True|True|-|True|True|


### Player Detection

TBD


## Treasure Box Switch

### Names

- Proc Name: PROC_TBOX_SW
- File Name: d_a_tboxSw.cpp
- Object Names:
  - "tbox_sw" -> argument: 0

### Description

This actor observes a treasure box flag and, if set to true, also set its target switch flag to true. An example of this can be found in the Bow Room of Goron Mines where opening the treasure chest to obtain the bow also sets another flag that changes the enemy pool in the Magnet Wall room from Tektites to Toadpolis.

### Parameters

| Variable | Bit mask | Bit shift | Bit length | Name | Description|
|---|---|---|---|---|---|
|Parameter|0x0000FF00|8|8|Target Switch|Defines the switch flag which will be set to true if the observed treasure chest flag has been set to true.|
|Parameter|0x0000003F|0|6|Treasure Box Flag|Defines the treasure box flag that is to be observed.