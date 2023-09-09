# FilterBlade-Public-Assets

Contains public custom files, such as extra hover descriptions for items listed on FilterBlade.

The files in this repository are used to add extra-data into the hover-descriptions on https://filterblade.xyz

For instance in this example, the extra-data is used to add drop locations into the unique tiering:

![image](https://user-images.githubusercontent.com/2942999/230211064-7a91428b-7e90-4d05-a273-2dae4444624d.png)

> See below how these descriptions are represented in the `bonusItemInfo.json` file.

## How to edit or add information

Descriptions are attached through JSON code. The top level property describes the tierlist names and then the description is added by naming the basetype:

```json
  "Incubator": {
    "items": {
      "Geomancer's Incubator": {
        "text": "Grants a random 6-link weapon or armour"
      }
    }
  }
```

In case of uniques, we can attach descriptions to the basetype itself or onto a specific unique type.

Uniques also have support for more proprties:

```json
  "Uniques": {
    "items": {
      "Infernal Sword": {
        "text": null,
        "items": {
          "Starforge":{
            "text": "Overridden in Exceptions. Drops from The Shaper.",
            "ruleLink": {
              "entryName": "Unique Shaper Sword",
              "text": "Link to exception rule"
            },
            "priority": -1
          },
          "Voidforge":{
            "hideInHoverBox": true
          },
          "Echoforge":{
            "text": "Drops from The Maven."            
          }
        }
      }
    }
  }
```

> In this example, we're attaching descriptions to the swords as seen below


### RuleLink

The "ruleLink" propoperty adds a link in the hoverBox that, once clicked, will jump the user to the specified rule.

The "text" property of the ruleLink object is the displayed text and has no technical meaning.

The "entryName" property needs to match a rule-ID specified in the OptionFile to identify the right rule.

### hideInHoverBox

The "hideInHoverBox" flag allows you to hide uniques in the hoverBox completely.

### Priority

The "priority" number specified the order of uniques in the tooltip. If ommited, a default value of 0 is used for all uniques that have no priority, or no bonus info entry at all.

If two uniques have the same priority, they are ordered by chaos price.

You can set negative priorities to put uniques at the bottom. This is most commonly used to put uniques that are not relevant for the tierList (because they have a special exception rule) at the bottom.

![image](https://github.com/NeverSinkDev/FilterBlade-Public-Assets/assets/20803858/d1e1b385-fc07-430c-b5cc-ab36243e6345)

## Tags

Each BaseType can also have "Tags":

```json
"Wild Crystallised Lifeforce": {
  "text": "Purple harvest crafting material",
  "tags": ["Harvest", "LeagueDrop"]
}
```

These tags need to first be declared on root level in the "itemTagDefinitions" dictionary like this:

```json
"itemTagDefinitions": {
  "Harvest": {
    "displayName": "Harvest",
    "iconPath": "assets/img/Icons/AccordionIcons/HarvestSeed.png",
    "description": "Drops from Harvest",
    "isHiddenByDefault": false,
    "group": "currency2"
  }
}
```

These tags will be displayed on the item in the FilterBlade tierlists and also allows for filtering in the GUI:

![image](https://github.com/NeverSinkDev/FilterBlade-Public-Assets/assets/20803858/47322344-3e59-4055-b46f-630859f7e358)

![image](https://github.com/NeverSinkDev/FilterBlade-Public-Assets/assets/20803858/a250bb49-5d61-4f2d-b247-ddefdb8073ed)

We'd welcome your pull request to help us populate and maintain these files!

### Quality Guidelines

- Only English
- Don't include any links
- Keep the bullet points as short as possible (<80 characters)
- Double-check for typos

## Deployment

Changes in this file autodeploy to: https://beta.filterblade.xyz

## License

MIT License. You can use this data however you wish and for all your POE project needs, just tell where it came from.
