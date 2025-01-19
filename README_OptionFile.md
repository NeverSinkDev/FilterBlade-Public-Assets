# Option-Files

The .options files are a domain language designed by and for FilterBlade. It is used to specify the contents of the overview and customizer screens.

The most important one is the Customizer.options file.

Changes in this repository (master branch only) will be automatically synced to the FilterBlade private repository and vice versa. PRs accepted on Github for these files will be directled synced into our private repository will end up on FilterBlade during the next push.

When contributing, please verify your new option file is working correctly by uploading it on FilterBlade -> Advanced -> Upload filter -> Upload Option File, and checking the customizer sections you changed. Taking a look into the F12 browser dev tool console is also often helpful here.

Sections in this file are annoted with

![#f03c15](https://placehold.co/15x15/7CFC00/7CFC00.png) for "Safe to edit" - little to no technical risk.

![#f03c15](https://placehold.co/15x15/ffae42/ffae42.png) are usually safe to edit, but edge cases exist. We suggest contacting [discord](https://discord.com/invite/mye6xhF), if you want to submit a PR for those and need extra information.

![#f03c15](https://placehold.co/15x15/f03c15/f03c15.png) for "Massive impact on functionality" and are usually best left to FilterBlade developers

## General Syntax

Most commands end with open and close brackets, sometimes with parameters inside.

All commands end with a semicolon or curly brackets.

Parameters can be normal primitive types, such as strings, numbers or booleans, but also inline arrays:
```javascript
SomeCommand("hello world", true, 5, [true, false]);
```

![image](https://github.com/NeverSinkDev/FilterBlade-Public-Assets/assets/20803858/dd273204-8a4c-40fa-9088-2fa1ce609375)

We recommend using the following VS-Code extension for syntax highlight:

https://marketplace.visualstudio.com/items?itemName=NeverSink.filterblade-options-language

## ![#f03c15](https://placehold.co/15x15/7CFC00/7CFC00.png) Comments

Comments are only available with the usual "//" prefix, which will turn everything to the right of it into a comment which is ignored by the compiler.
Multi-line comments with e.g. "/*" are not supported.

```
// This is a comment
ThisIsACommand(); // This is another comment :)
/* This is NOT valid syntax */
```

## ![#f03c15](https://placehold.co/15x15/ffae42/ffae42.png) QuickUI

The most important command for the Customizer.

Dynamically generates 95% of the entire editing GUI, including rule-titles, color editing, itemLevel slider, tierLists and other UI elements.

An example line would be: `QuickUI([0.0, "Loreweave Rings", "uniques;recipeuniquerings"], "SD", ["ItemLevel"]);`

![image](https://github.com/NeverSinkDev/FilterBlade-Public-Assets/assets/20803858/0116ead3-1ab3-4222-bf2a-428abe9a6f23)

It receives the following parameters:

### ![#f03c15](https://placehold.co/15x15/f03c15/f03c15.png) Filter rule

This is either just the name of a local filter-query (see the "Searches" chapter below), or an array with 3 values that inlines a filter-query:

1. VERSION: New rules always have version "0.0". Usually, you do NOT need to change this. Increasing the first number will remove ALL user customizations done to this rule for ALL users. Required to handle major changes to the game and filter structure. The second number will only remove non-visual changes (e.g. not size and color).

2. NAME-ID: Unique name that identifies this query. Will be sometimes referred as 'filter-rule-name' Having the same name for different rules is not allowed. Changing the ID of a rule is also not allowed as this value is used for the save&load system to identify this rule. Meaning any edit will cause existing changes to not find their old rule any more and cause invalidations. This value is therefore very critical. If you want to change the name, use the "Title" parameter of the QuickUI instead.

3. FILTER-RULE: If you look into NeverSinks `.filter` files, you'll see that every rule the filter is anoted with a "type" and a "tier" tag, for example: `Show # $type->currency $tier->t1exalted` means the "type" of the rule is "currency" and "t1exalted" is its "tier". We use these tags here to uniquely identify a single rule in the filter by writing the type, then a semicolon, and then the tier of the rule, for example: `"currency;t1exalted"`. This "links" that rule to this GUI. Meaning the GUI will display the values of that rule, and editing the GUI will edit this rule.

### ![#f03c15](https://placehold.co/15x15/ffae42/ffae42.png) SHD buttons

SHD stands for "Show", "Hide" and "Disable" - the 3 buttons you can see on the left of the editor to toggle rules on and off.
Example: "SD" would generate a show and disable button. "S" only a show button. "SHD" all 3 buttons. "" would not generate any options.
The order of the letters does not matter. "SHD" and "HDS" are equivalent.
This parameter is ignored if the right button combination can be deferred from the filter (this only applies if "SH" or "SD" is specified).

Warning: Never use 'Disable' in full tierlists. Unmatched items (through disabled rules) in a tierlist will 'fall-through' and fall down into the pink "error" rule. Full tierlists (such as currency, uniques, divination cards) need to have every available basetype matched by a Show or Hide rule.

### ![#f03c15](https://placehold.co/15x15/ffae42/ffae42.png) List of identifiers

This is the fun part:

This parameter is an array of strings. Each string will generate one GUI for this rule.
Valid values include any filter-command, such as "ItemLevel" or "Corrupted".
The order of these values does not matter as they will be ordered automatically to improve consistency.
This list can be empty.

Additions are:
- BTM for BaseTypeMatrix
- ItemType for the ArmourType buttons (ES, Armour, Evasion, etc.)
- Stats_RareGear & Stats_All & Stats_Misc to allow adding more filtering with given limitations (idents for items with rarity, all idents, itents for items without rarity)
- RarityU, because "Rarity" will not generate a button for Unique rarity as it is rarely ever used. This command will generate that "Unique" button too.
- Certain pre-defined lists, such as "Jewels", which will generate CheckboxButtons for all Jewel BaseTypes.

Exceptions are:
- Classes: Use the specific name of class-lists instead. Most commonly used ones are: AllGearClasses, AllGearClassesPlusJewel, AllGearClassesPlusFlaskJewel
- HasExplicitMod: Use "Mods" instead to generate the special multi-list and operator GUI
- BaseType and other idents with long value lists. Use the TierList-config parameter instead.

Do not add additional filtering elements (such as itemlevel, corrupted...) to tierlists. as allowing the users to e.g. add "ItemLevel >= 60" to Uniques Tier 2 will cause all Tier 2 Uniques that don't fulfill this requirement to not be handled in the tierlist and all down into the pink "error" rule.

Otherwise, adding more options is usually okay, but be careful when removing options. The UI does not remove the changes that the users made here and users will be 'stuck' with invisible changes. Not even section-resets will help here as they are controlled by the UI elements. These users can (if they figure it out) use the "Review changes" module on "Advanced" to delete these old changes.

These UIs don't edit the filter directly, but rather "expose" properties of the filter in the GUI to allow users to edit them.
If you add an ItemLevel slider to a rule and that rule does filter by ItemLevel, the slider will display this value and allow the user to edit it.
On the other hand, if that rule does not filter by ItemLevel yet, the slider will be on "any" and the user can add this filering if they desire.

### ![#f03c15](https://placehold.co/15x15/7CFC00/7CFC00.png) Title

Title for the GUI. Can be anything. If ommited (null or empty string), the filter rule name / query-id will be used.

### ![#f03c15](https://placehold.co/15x15/f03c15/f03c15.png) TierList-config

For tierlist-UIs, this array is required to generate the UI for the baseType list.
The array has 3 elements:

1. Name of autocomplete-suggestions-list, such as "Resonators" or "Currency" or "Gear"
2. Config-Flags: Similar to the SHD parameter, this is a string with letters. The order does not matter. A = The "any" button will be displayed. O = the exact-match operator (==) will be used. D = allow duplicates within a tierlist, S = single-tier without drag&drop functionality.
3. Item-stat/identifier. Optional, defaults to "BaseType"

Can be empty if you don't want to generate a tierlist UI.

### ![#f03c15](https://placehold.co/15x15/7CFC00/7CFC00.png) Description

Optional. Generates a small description text for this rule.

## Sections and Grouping

### ![#f03c15](https://placehold.co/15x15/7CFC00/7CFC00.png) Chapter:

BIG groups of many accordions. Right now we have 6 chapters: General, Maps, EG, EG Rare, Campaign, Misc. Chapters do not have names or titles.
![image](https://github.com/NeverSinkDev/FilterBlade-Public-Assets/assets/20803858/0f317bd6-b516-4f4b-8aed-7a9f24584a58)

### ![#f03c15](https://placehold.co/15x15/7CFC00/7CFC00.png) Section:

Each Accordion (these large-blue-expandable-buttons you can click on to open their content) is a section. They have a title and an optional icon. If a section has an icon, all other sections within that same area should also have one for consistency sake - it just looks better. You can put one section into another, but not a 3rd into that. The GUI gets to narrow and many UI elements won't fit.
![image](https://github.com/NeverSinkDev/FilterBlade-Public-Assets/assets/20803858/78a47b6c-213d-4b2a-bf44-47dc0574d411)

### ![#f03c15](https://placehold.co/15x15/7CFC00/7CFC00.png) Box:

A tiny contains with darker background.
![image](https://github.com/NeverSinkDev/FilterBlade-Public-Assets/assets/20803858/84351286-7bc1-4e2e-8d6e-b69c699b3dff)

## Text & Title Commands:

### ![#f03c15](https://placehold.co/15x15/7CFC00/7CFC00.png) Title:

Used to divide chapters and groups of sections.

### ![#f03c15](https://placehold.co/15x15/7CFC00/7CFC00.png) SectionTitle:

Mostly used as the title for each chapter.

### ![#f03c15](https://placehold.co/15x15/7CFC00/7CFC00.png) Description:

Smaller texts for details.

## ![#f03c15](https://placehold.co/15x15/f03c15/f03c15.png) Advanced Commands:

Searches:
Slowly going deprecated, rarely used. WIP

## ![#f03c15](https://placehold.co/15x15/f03c15/f03c15.png) Custom Rules & New Tiers:

This is an advanced topic. For now we don't expect people to work with these advanced commands.

### Functions
Defines a GUI-function with parameters. This can be useful when having many rules in the same section that all share the same GUI structure.
Required for the "Add tier" functionality and custom rules to define how the GUI of new rules is supposed to look like.

`Function <funcName>($s, $shd = "SHD", $text) { <content> }`

Where <funcName> is an unique name you can define here. When using this func later, you do so by using this name.
After that, in the brackets, we define which parameters which function receives when called. You can access the values of these parameters by writing their name (including the $-sign) in the content again.
And <content> is any normal option file command - most commonly a single QuickUI, where you can use specify "$s" as filter rule (or search) instead of the actual value.
Same for the $shd param, where we have given it a default of "SHD" which is required for new rules.

After defining the Function, you can call it by writing its name just like any other command.

### Conditionals:

Custom rules and more. Conditionals are any new rule that is generated within FilterBlade and was not previously in the base filter.

They can be simple, such as `Conditional("?AncTrialOmensNewTier") { }` where "?AncTrialOmensNewTier" is the ID/Name of the new rule-builder.
All conditional IDs start with a questionmark. This setup is used on existing tierLists to add new custom tiers to it.

Note that this command will only generate the rule-builder, but we still need the GUI for it. See ElementAdder_Tier.

Conditionals can also be more complex.
They have a second parameter, which is a list of waypoint-IDs. Waypoint determine where in the filter these new rules are put.
We can expose this list to the user with the CondTargetSelection ident in the QuickUI param list.
We can also fill the curly brackets of the Conditional. This is the base-body of new rules generated from it and it can look like this: `Hide; Class Maps; SetFontSize 18; MapTier <= 14;`

### ElementAdder_Tier:

The ElementAdder_Tier command generates the GUI for new rules/tiers. It receives the name of the conditional as first parameter and the name of the function to generate the GUI with as second parameter.

A typical new-tier part of a tierlist looks like this:

```
Conditional("?SanctumRelicsNewTiers") { }
ElementAdder_Tier("?SanctumRelicsNewTiers", "SanctumRelicsUI");
```

with the GUI function defined above like this: `Function SanctumRelicsUI($s, $shd = "SHD", $text) { QuickUI($s, $shd, ["Rarity"], $text, ["SanctumRelics", "O"]); }`

Meanwhile, customn rules can look like this:

```
Section("Highlight specific maps") {
    Function ShowMapBaseTypeTier ($s, $shd = "SD", $title) { QuickUI($s, $shd, ["MapTier", "Quality", "Rarity", "Corrupted", "AreaLevel"], $title, ["Maps", "SO"]); }
    Conditional("?CShowMaps", ["c9.maps.decorators.all"]) { Show; Class Maps; BaseType == "Strand Map"; SetFontSize 44; SetBackgroundColor 180 70 50 220; PlayAlertSound 5 300; SetTextColor 0 0 0; MinimapIcon 2 Brown Square; PlayEffect Brown; }
    ElementAdder_Tier("?CShowMaps", "ShowMapBaseTypeTier");
}
```

#### Hide-tiers:

Most tierlists have the behavior of moving items into a new "Hide" rule when pressing the "X" button. This rule also needs to be generated using its own Conditional and ElementAdder_Tier.
While we can use the simple Conditional, the ElementAdder_Tier needs other parameters: We specify null for the UI function as it is always the same and simple. Then true to identify that this is a hide-rule for the tierList, then the name of the suggestions, similar to the tierLis config of the QuickUI, and finally the tierList-flags.

A typical hide tier looks like this:

```
Conditional("?SanctumRelicsNewTiersHidden") { }
ElementAdder_Tier("?SanctumRelicsNewTiersHidden", null, true, "SanctumRelics", "OH");
```

## ![#f03c15](https://placehold.co/15x15/ffae42/ffae42.png) TierListToolBar:

This command adds these buttons at the top of the section:

![image](https://github.com/NeverSinkDev/FilterBlade-Public-Assets/assets/20803858/b9307335-5e63-483b-9985-eefda47f4e27)

This command has multiple parameters, all of which are optional:

1. Economy Name: Corresponds with the PoE Ninja economy data file. This file needs to be generated first, making this a parameter for FB devs only.
2. Excluded Rules: If Economy is proviced, this is an array of Searches for rules that should be excluded from the Auto-Sort feature.
3. Excluded Items: Same as above, but for individual items/BaseTypes
4. Stat: Defaults to BaseType, usually not required.
5. BonusItemInfo Section Name: If Economy Name is not available, enter the name of the section from the BonusItemInfo.json here to display its content in the item tooltips.

Which buttons are generated is partically dynamic:

- If you can add new tiers (via ElementAdder_Tier), the "Add tier" button is generated
- If an economy name was specified, the "Show prices", "Auto-Sort" and "Configure" buttons are generated with the specified setup from the parameters.
- Locks & Lock all buttons are always generated, as well as Reset Tiers and Compact mode.
- If economyName or BonusItemInfo Section Name are specified and tags are present or you can filter by class, the UI filtering button is generated.

ONLY use this command in sections where actual tierlists with at least 2 tiers are present.

## BaseTypeMatrix (multile rules/tiers): WIP
