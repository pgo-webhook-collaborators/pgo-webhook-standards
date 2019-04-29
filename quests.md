# Quest

| Key           | Type                       | Required | Description                                                                                                                     |
|---------------|----------------------------|----------|---------------------------------------------------------------------------------------------------------------------------------|
| longitude     | float                      | Y        |                                                                                                                                 |
| latitude      | float                      | Y        |                                                                                                                                 |
| pokestop_id   | string                     | Y        | The unique identifier for the quest                                                                                             |
| pokestop_url  | string                     | N        |                                                                                                                                 |
| pokestop_name | string                     | N        |                                                                                                                                 |
| template      | string                     | N        | Representation of current quest campaign                                                                                        |
| target        | int                        | N        | Base condition ID. See [conditions](#conditions) table.                                                                         |
| worker_level  | int                        | N        | The level of the worker that sent the webhook. Needed for determining if the rewards would be different (some items are locked) |
| quest_type    | int                        | Y        | See [quest types](#quest types) table                                                                                           |
| updated       | int                        | Y        | epoch time representing when the quest was last scanned, in order to avoid duplicate messages within the common 24h time frame. |
| conditions    | array of condition objects | Y        | See [conditions](#conditions) table                                                                                             |
| rewards       | array of rewards objects   | Y        | See [rewards](#rewards) table                                                                                                   |

Some quests will have multiple or no conditions, some will have multiple or no rewards, so it all depends on the quest.

The following is a complete example of a quest object

This requires the player to catch 10 pokemon, but these pokemon have to specifically be
Zigzagoon (263), Linoone (264), Taillow (276), or Swellow (277) for the reward of 1000 stardust

Breakdown:
* `quest_type: 4` correlates to `Catch Pokemon` as listed in the [quest types](#quest types) table
*  The only condition has the `condition_type: 2`, which correlates to `Pokemon Category` as listed in the 
[conditions](#conditions) table
* `condition_amount` is `10`, which is the amount of this type you need to do (catch in this case)
* `monster_ids: [263, 264, 276, 277]` corresponds to the pokemon IDs that you can catch for this
* `reward_type: 3` correlates to the `Stardust` reward type
* `reward_amount: 1000` means that you would get 1000 of the reward, which in this case is stardust

```json
[
  {
    "type": "quest",
    "message": {
      "longitude": 37.7876146,
      "latitude": -122.390624,
      "pokestop_id": "0",
      "pokestop_url": "https://....",
      "pokestop_name": "Niantic HQ",
      "quest_type": 4,
      "updated": 1572241600,
      "conditions": [
        {
          "condition_type": 2,
          "condition_amount": 10,
          "monster_ids": [
            263, 264, 276, 277
          ]
        }
      ],
      "rewards": [
        {
          "reward_type": 3,
          "reward_id": 0,
          "reward_amount": 1000
        }
      ]
    }
  }
]
```

## Quest Types
These are the top-level quest type IDs that correspond to the type of quest that it is.

If anything else is needed for this quest (e.g. specificity) then a condition obejct is added to the conditions array
for that requirement

| Key                       | ID |
|---------------------------|----|
| Unknown Type              | 0  |
| First Catch of the Day    | 1  |
| First Pokestop of the Day | 2  |
| Multi-Part                | 3  |
| Catch Pokemon             | 4  |
| Spin Pokestop             | 5  |
| Hatch Egg                 | 6  |
| Complete Gym Battle       | 7  |
| Complete Raid Battle      | 8  |
| Complete Quest            | 9  |
| Transfer Pokemon          | 10 |
| Favorite Pokemon          | 11 |
| Autocomplete              | 12 |
| User Berry in Encounter   | 13 |
| Upgrade Pokemon           | 14 |
| Evolve Pokemon            | 15 |
| Land Ball Throw           | 16 |
| Get Buddy Candy           | 17 |
| Badge Rank                | 18 |
| Player Level              | 19 |
| Join Raid                 | 20 |
| Complete Battle           | 21 |
| Add Friend                | 22 |
| Trade Pokemon             | 23 |
| Send Gift                 | 24 |
| Evolve into Pokemon       | 25 |

## Conditions
This spec will cover most of the requirements, but when more information is needed, the appropriate extra field(s) 
should be included. These fields are defined per-type

Otherwise, these fields will not be checked

| Key              | Type   | Description                                                                   |
|------------------|--------|-------------------------------------------------------------------------------|
| condition_type   | int    | See [Conditions Type Array](#conditions-type-array)                           |
| condition_amount | int    | A generic number representing how many times something must be done or aquired|

```json
[
    {
      "condition_type": 1,
      "condition_amount": 1
    }
]
```

### Conditions Type Array
This came directly out of the protos to use as the baseline, and will not change after this

The `type` int field for every condition should represent one of these

Keep in mind that all values will be accepted, but not all will be valid, even values in this list

| Name                   | ID | Description                                                    |
|------------------------|----|----------------------------------------------------------------|
| Unset                  | 0  | The condition is not set                                       |
| Pokemon Type           | 1  | Catch a specific pokemon type                                  |
| Pokemon Category       | 2  | Catch a pokemon from a specific category (arbitrarily defined) |
| Weather Boost          | 3  | Catch a pokemon that's weather boosted                         |
| Daily Capture Bonus    | 4  | Get your daily pokemon capture                                 |
| Daily Spin Bonus       | 5  | Spin your daily pokestop                                       |
| Win Raid Status        | 6  | Win a raid                                                     |
| Raid Level             | 7  | Win a raid of a particular level                               |
| Throw Type             | 8  | Make a specific type of throw on a pokemon                     |
| Win Gym Battle Status  | 9  | Win a gym battle                                               |
| Super Effective Charge | 10 | Use a super effective charge move                              |
| Item                   | 11 | Use a specific item                                            |
| Unique Pokestop        | 12 | Spin a pokestop that you have not before (within record)       |
| Quest Context          | 13 |                                                                |
| Throw Type in a Row    | 14 | Make this specific throw type x times in a row                 |
| Curve Ball             | 15 | Make a curve ball                                              |
| Badge Type             | 16 | Get a badge of this level                                      |
| Player Level           | 17 | Get to a specific player level                                 |
| Win Battle Status      | 18 | Win a trainer battle                                           |
| New Friend             | 19 | Make a new friend                                              |
| Days in a Row          | 20 | Do some action x days in a row                                 |

### Conditions Detail
When the basic fields of the condition don't cover all use-cases, reference below

Some possibilities from the conditions type array were left out because they didn't seem useful to do anything useful

#### Pokemon Type (type `1`)
See [PA's type array](https://github.com/PokeAlarm/PokeAlarm/blob/master/locales/en.json#L1016) for a complete type
id/name correlation (minus the padded zeroes)

Type example - this would require you to catch 5 Normal, Flying, or Ground type monsters:
```json
{
  "condition_type": 1,
  "condition_amount": 5,
  "type_id": [1, 3, 5]
}
```

#### Pokemon Category (type `2`)
How they define a group of monsters without any particular relation

In this example you catch 5 Bulbasaur, Ivysaur, or Venusaur
```json
{
  "condition_type": 2,
  "condition_amount": 5,
  "monster_id": [1,2,3]
}
```

#### Raid Level (type `7`)
Do a raid of a specific level

In this example, you would need to do 5 raids of either level 1 or level 5
```json
{
  "condition_type": 7,
  "condition_amount": 5,
  "level": [1,5]
}
```

#### Throw Type (type `8`)
Make x amount of this type of throw

In this example, you must make an excellent throw
```json
{
  "condition_type": 8,
  "condition_amount": 1,
  "throw_id": 3
}
```
This can have one of the following

| ID | Name      |
|----|-----------|
| 0  | Normal    |
| 1  | Nice      |
| 2  | Great     |
| 3  | Excellent |

For `THROW_TYPE_IN_A_ROW`, you do the same but increase the `amount` field on the condition, like so
```json
{
  "condition_type": 14,
  "condition_amount": 5,
  "throw_id": 3
}
```

#### Item (type `11`)
Example: Use 5 razz berries
```json
{
  "condition_type": 11,
  "condition_amount": 5,
  "item_id": 701
}
```
See [item array](defs/items.md#item-array) for id options





## Rewards

This spec will cover most of the requirements, but when more information is needed, the specific key is attached to this
object from one of the appropriate sections below based on the `reward_type`

| Key                  | Type   | Required | Description                                                                                             |
|----------------------|--------|----------|---------------------------------------------------------------------------------------------------------|
| reward_type          | int    | Y        | See [the reward types array](#rewards-type-array) for options                                           |
| reward_id            | int    | Y        | A generic id representing an object, based on `reward_type`. NOTE: Is only required an an ID is needed. |
| reward_amount        | int    | Y        | A generic amount representing how many must be acquired or completed, based on `reward_type`            |

For example: the following will be the reward "Get 200 stardust"
```json
{
  "reward_type": 3,
  "reward_id": 0,
  "reward_amount": 200
}
```

### Rewards Type Array
This came directly out of the protos, and the `type` int field for every reward should represent one of these

Keep in mind that all values will be accepted, but not all will be valid, even values in this list

| Name              | ID |
|-------------------|----|
| Unset             | 0  |
| Experience        | 1  |
| Item              | 2  |
| Stardust          | 3  |
| Candy             | 4  |
| Avatar Clothing   | 5  |
| Quest             | 6  |
| Pokemon Encounter | 7  |


### Rewards detail
Rewards can have a multitude of keys based on the type of reward (see [Rewards Type Array](#rewards-type-array))

All types listed in the enum are valid, but only a few are useful, and even fewer need more details than what's already
been defined

Based on the reward type id, more keys may be needed in the reward object, see the below sections for these keys. 

#### Monster (type `7`):
This section should have most required fields as a normal monster webhook, excluding encounter information.

`reward_id` will represent the pokemon id in this instance

| Key        | Type | Required |
|------------|------|----------|
| reward_id  | int  | Y        |
| form       | int  | N        |
| gender     | int  | N        |
| costume_id | int  | N        |

Example of the reward object:
```json
{
    "reward_type": 7,
    "reward_id": 201,
    "reward_amount": 1,
    "form": 15,
    "gender": 3,
    "costume_id": 1
}
```

* In the case of the reward monster being a ditto, just send the pokemon id of ditto
* Along with these, the `shiny` field may also be added here for possible later use, as a `boolean` type
    * This is because nobody really knows what it's for. It is assumed this was used for Squirtle community day, when a 
        quest from a specific stop would guarantee a shiny for everybody that got the quest from that stop.

#### Candy (type `4`)
The only extra information that's needed is the ID of the monster that you would receive candy for

| Key       | Type | Required | Description           |
|-----------|------|----------|-----------------------|
| reward_id | int  | Y        | The ID of the monster |

```json
{
    "reward_type": 7,
    "reward_id": 201,
    "reward_amount": 1
}
```
* ID of `0` will assume the Rare Candy item