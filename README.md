# FilterBlade-Public-Assets

Contains public custom files, such as extra hover descriptions for items listed on FilterBlade.

The files in this repository are used to add extra-data into the hover-descriptions on https://filterblade.xyz

For instance in this example, the extra-data is used to add drop locations into the unique tiering:

![image](https://user-images.githubusercontent.com/2942999/229589583-253bbc85-e5dc-46d5-9968-d6d08225ecc8.png)

> See below how these descriptions are represented in the `bonusItemInfo.json` file.

## How to edit or add information

Descriptions are attached through JSON code. The top level property describes the tierlist names and then the description is added by naming the basetype:

```json
  "Incubator": {
    "items": {
      "Geomancer's Incubator": {
        "text": "Grants a random 6link weapon or armour"
      }
    }
  }
```

In case of uniques, we can attach descriptions to the basetype itself or onto a specific unique type:

```json
  "Uniques": {
    "items": {
      "Infernal Sword": {
        "text": null,
        "items": {
          "Starforge":{
            "text": "Overridden in Exceptions. Drops from The Shaper."
          },
          "Voidforge":{
            "text": "Overridden in Exceptions. Drops from The Uber Elder."
          },
          "Echoforge":{
            "text": "Drops from The Maven."
          }
        }
      }
    }
  }
```

> In this example, we're attaching descriptions to the swords as seen above

We'd welcome your pull request to help us populate and maintain these files!

### Quality Guidelines

- Only English
- Don't include any links
- Keep the bullet points as short as possible (<80 characters)
- Double-check for typos

## Deployment

Changes in this file autodeploy to: https://beta.filterblade.xyz
