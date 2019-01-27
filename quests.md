#Root Quest

| Key           | Type   | Description                         |
|---------------|--------|-------------------------------------|
| longitude     | float  |                                     |
| latitude      | float  |                                     |
| pokestop_url  | string |                                     |
| pokestop_name | string |                                     |
| updated       | int    | epoch time                          |
| conditions    | array  | See [conditions](#conditions) table |
| rewards       | array  | See [rewards](#rewards) table       |

```json
[
  {
    "type": "quest",
    "message": {
      "longitude": float,
      "latitude": float,
      "pokestop_url": string,
      "pokestop_name": string,
      "updated": epoch int,
      "conditions": [...],
      "rewards": [...]
    }
  }
]
```

## Conditions
This spec will cover most of the requirements, but when more information is needed, the detail field should be included

Otherwise, the detail field will not be checked

| Key           | Type   | Description                                                                   |
|---------------|--------|-------------------------------------------------------------------------------|
| type          | int    | See [Conditions Type Array](#conditions-type-array)                           |
| amount        | int    | A generic number representing how many times something must be done or aquired|
| detail        | object | See [Conditions Detail Sections](#conditions-detail) for further detail       |

```json
[
    {
      "type": int,
      "amount": int,
      "detail": {...}
    }
]
```

### Conditions Type Array
This came directly out of the protos, and the `type` int field for every condition should represent one of these

Keep in mind that all values will be accepted, but not all will be valid, even values in this list
```
enum ConditionType {
    UNSET = 0;
    WITH_POKEMON_TYPE = 1;
    WITH_POKEMON_CATEGORY = 2;
    WITH_WEATHER_BOOST = 3;
    WITH_DAILY_CAPTURE_BONUS = 4;
    WITH_DAILY_SPIN_BONUS = 5;
    WITH_WIN_RAID_STATUS = 6;
    WITH_RAID_LEVEL = 7;
    WITH_THROW_TYPE = 8;
    WITH_WIN_GYM_BATTLE_STATUS = 9;
    WITH_SUPER_EFFECTIVE_CHARGE = 10;
    WITH_ITEM = 11;
    WITH_UNIQUE_POKESTOP = 12;
    WITH_QUEST_CONTEXT = 13;
    WITH_THROW_TYPE_IN_A_ROW = 14;
    WITH_CURVE_BALL = 15;
    WITH_BADGE_TYPE = 16;
    WITH_PLAYER_LEVEL = 17;
    WITH_WIN_BATTLE_STATUS = 18;
    WITH_NEW_FRIEND = 19;
    WITH_DAYS_IN_A_ROW = 20;
}
```

### Conditions Detail
When the basic fields of the condition don't cover all use-cases, reference below

Some possibilities from the conditions type array were left out because they didn't seem useful to have any detail on

#### Pokemon Type (type `1`)
See [PA's type array](https://github.com/PokeAlarm/PokeAlarm/blob/master/locales/en.json#L1016) for a complete type
id/name correlation (minus the padded zeroes)
```json
{
  "type_id": [int]
}
```

#### Pokemon Category (type `2`)
How they define a group of monsters without any particular relation
```json
{
  "pokemon_id": [int],
  "name": string
}
```
* The `name` field is optional, and probably won't ever be filled

#### Raid Level (type `7`)
```json
{
  "level": [1-5]
}
```

#### Throw Type (type `8`)
```json
{
  "throw_id": int
}
```
This can have one of the following

| ID | Name      |
|----|-----------|
| 0  | Normal    |
| 1  | Nice      |
| 2  | Great     |
| 3  | Excellent |

For `WITH_THROW_TYPE_IN_A_ROW`, increase the `amount` field on the condition

#### Item (type `11`)
```json
{
  "item_id": int
}
```
See [item array](#item-array) for id options





## Rewards

This spec will cover most of the requirements, but when more information is needed, the `detail` field should be 
included

Otherwise, the `detail` field will not be checked

| Key           | Type   | Description                                                                           |
|---------------|--------|---------------------------------------------------------------------------------------|
| type          | int    | See [the reward types array](#rewards-type-array) for options                         |
| id            | int    | A generic id representing an object, based on `type`                                  |
| amount        | int    | A generic amount representing how many must be acquired or completed, based on `type` |
| detail        | object | See [the rewards detail section](#rewards-detail) for options                         |


```json
{
  "type": int,
  "id": int,
  "amount": int,
  "detail": {...}
}
```

### Rewards Type Array
This came directly out of the protos, and the `type` int field for every reward should represent one of these

Keep in mind that all values will be accepted, but not all will be valid, even values in this list
```
enum Type {
    UNSET = 0;
    EXPERIENCE = 1;
    ITEM = 2;
    STARDUST = 3;
    CANDY = 4;
    AVATAR_CLOTHING = 5;
    QUEST = 6;
    POKEMON_ENCOUNTER = 7;
}
```


### Rewards detail
Rewards details can have a multitude of keys based on the type of reward (see [Rewards Type Array](#rewards-type-array))

All types listed in the enum are valid, but only a few are useful, and even fewer need more details than what's already
been defined

#### Monster (type `7`):
This section should have most required fields as a normal monster webhook, excluding encounter information.
```json
{
    "pokemon_id": int,
    "form": int,
    "gender": int,
    "costume_id": int
}
```

* In the case of the reward monster being a ditto, just send the pokemon id of ditto
* Along with these, the `shiny` field may also be added here for possible later use, as a `boolean` type
    * This is because nobody really knows what it's for. It is assumed this was used for Squirtle community day, when a 
        quest from a specific stop would guarantee a shiny for everybody that got the quest from that stop.

#### Candy (type `4`)
The only extra information that's needed is the ID of the monster that you would receive candy for 
```json
{
  "pokemon_id": int
}
```
* ID of `0` will assume the Rare Candy item




## Item array
Once again, straight from protos. The int represents the ID
```
enum ItemId {
	ITEM_UNKNOWN = 0;
	ITEM_POKE_BALL = 1;
	ITEM_GREAT_BALL = 2;
	ITEM_ULTRA_BALL = 3;
	ITEM_MASTER_BALL = 4;
	ITEM_PREMIER_BALL = 5;
	ITEM_POTION = 101;
	ITEM_SUPER_POTION = 102;
	ITEM_HYPER_POTION = 103;
	ITEM_MAX_POTION = 104;
	ITEM_REVIVE = 201;
	ITEM_MAX_REVIVE = 202;
	ITEM_LUCKY_EGG = 301;
	ITEM_INCENSE_ORDINARY = 401;
	ITEM_INCENSE_SPICY = 402;
	ITEM_INCENSE_COOL = 403;
	ITEM_INCENSE_FLORAL = 404;
	ITEM_INCENSE_BELUGA_BOX = 405;
	ITEM_TROY_DISK = 501;
	ITEM_X_ATTACK = 602;
	ITEM_X_DEFENSE = 603;
	ITEM_X_MIRACLE = 604;
	ITEM_RAZZ_BERRY = 701;
	ITEM_BLUK_BERRY = 702;
	ITEM_NANAB_BERRY = 703;
	ITEM_WEPAR_BERRY = 704;
	ITEM_PINAP_BERRY = 705;
	ITEM_GOLDEN_RAZZ_BERRY = 706;
	ITEM_GOLDEN_NANAB_BERRY = 707;
	ITEM_GOLDEN_PINAP_BERRY = 708;
	ITEM_SPECIAL_CAMERA = 801;
	ITEM_INCUBATOR_BASIC_UNLIMITED = 901;
	ITEM_INCUBATOR_BASIC = 902;
	ITEM_INCUBATOR_SUPER = 903;
	ITEM_POKEMON_STORAGE_UPGRADE = 1001;
	ITEM_ITEM_STORAGE_UPGRADE = 1002;
	ITEM_SUN_STONE = 1101;
	ITEM_KINGS_ROCK = 1102;
	ITEM_METAL_COAT = 1103;
	ITEM_DRAGON_SCALE = 1104;
	ITEM_UP_GRADE = 1105;
	ITEM_GEN4_EVOLUTION_STONE = 1106;
	ITEM_MOVE_REROLL_FAST_ATTACK = 1201;
	ITEM_MOVE_REROLL_SPECIAL_ATTACK = 1202;
	ITEM_RARE_CANDY = 1301;
	ITEM_FREE_RAID_TICKET = 1401;
	ITEM_PAID_RAID_TICKET = 1402;
	ITEM_LEGENDARY_RAID_TICKET = 1403;
	ITEM_STAR_PIECE = 1404;
	ITEM_FRIEND_GIFT_BOX = 1405;
}
```