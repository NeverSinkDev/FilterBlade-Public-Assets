# Option-Files

The .options files are designed by and for FilterBlade. It's a custom programming language to specify the structure and content of FilterBlade modules.

The most important one is the Customizer.options file.

## General Syntax

Most commands end with open and close brackets, sometimes with parameters inside.

All commands end with a semicolon or curly brackets.

Parameters can be normal primitive types, such as strings, numbers or booleans, but also inline arrays:
```javascript
SomeCommand("hello world", true, 5, [true, false]);
```

![image](https://github.com/NeverSinkDev/FilterBlade-Public-Assets/assets/20803858/dd273204-8a4c-40fa-9088-2fa1ce609375)


There is a VS-Code Extension for Syntax Highlight:
https://marketplace.visualstudio.com/items?itemName=NeverSink.filterblade-options-language

## Comments

Comments are only available with the usual "//" prefix, which will turn everything to the right of it into a comment which is ignored by the compiler.
Multi-line comments with e.g. "/*" are not supported.

```
// This is a comment
ThisIsACommand(); // This is another comment :)
/* This is invalid syntax */
```

## QuickUI

Easily the most important command for the Customizer.
This single command generates 95% of the entire editing GUI, including rule-titles, color editing, itemLevel slider, tierLists, ...

An example line would be: `QuickUI([0.0, "Loreweave Rings", "uniques;recipeuniquerings"], "SD", ["ItemLevel"]);`

![image](https://github.com/NeverSinkDev/FilterBlade-Public-Assets/assets/20803858/0116ead3-1ab3-4222-bf2a-428abe9a6f23)


It receives the following parameters:

### Filter rule

This is either just the name of a local search (see the "Searches" chapter below), or an array with 3 values:

1. Version. New rules always have version "0.0". This version can be increased later. Increasing the first number will invaldate all user customizations done to this rule. This is required for breaking updates but has become relatively rare. The second number will only invalidate non-visual changes (e.g. not size and color).

2. Technical name. This is one way to give a name to a rule for e.g. the search bar, but it is mainly used as identification. Having the same name for different rules is not allowed. Changing the name of a rule after initial release is also not recommended. This value is used for the save&load system and therefore very critical.

3. TierTagSearch: Rules in the filter often have lines like `Show # $type->currency $tier->t1exalted`. This data can be used here to determine which rule the QuickUI is going to select. To use it, write the type (excluding "$type->"), then a semicolon, then the tier (including "$tier->"). In this example it would be `"currency;t1exalted"`.

### SHD buttons

SHD stands for "Show", "Hide" and "Disable" - the 3 buttons you can see on the left of the editor to toggle rules on and off.
You can leave this as an empty string to not generate any buttons, or set it to "SHD" to show all options.
The order of the letters does not matter. "SHD" and "HDS" ar equivalent.
This parameter is ignored if the right button combination can be deferred from the filter (this only applies if "SH" or "SD" is specified).

### List of identifiers

This is the fun part:

This parameter is an array of strings. Each string will generate one GUI for this rule.
Valid values include any filter-command, such as "ItemLevel" or "Corrpted".
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

### Title

Title for the GUI. Can be anything. If ommited, the filter rule name will be used.

### TierList-config

For tierlist-UIs, this array is required to generate the UI for the baseType list.
The array has 3 elements:

1. Name of autocomplete-suggestions-list, such as "Resonators" or "Currency" or "Gear"
2. Config-Flags: Similar to the SHD parameter, this is a string with letters. The order does not matter. A = The "any" button will be displayed. O = the exact-match operator (==) will be used. D = allow duplicates within a tierlist, S = single-tier without drag&drop functionality.
3. Item-stat/identifier. Optional, defaults to "BaseType"

### Description

Optional. Generates a small description text for this rule.

## Sections and Grouping

Chapter:

BIG groups of many accordions. Right now we have 6 chapters: General, Maps, EG, EG Rare, Campaign, Misc. Chapters do not have names or titles.
![image](https://github.com/NeverSinkDev/FilterBlade-Public-Assets/assets/20803858/0f317bd6-b516-4f4b-8aed-7a9f24584a58)

Section:

Each Accordion (these buttons that you can click on to open their content) is a section. They have a title and an optional icon. If a section has an icon, all other sections within that same area should also have one for consistency sake - it just looks better. You can put one section into another, but not a 3rd into that. The GUI gets to narrow and many UI elements won't fit.
![image](https://github.com/NeverSinkDev/FilterBlade-Public-Assets/assets/20803858/78a47b6c-213d-4b2a-bf44-47dc0574d411)

Box:

A tiny contains with darker background.
![image](https://github.com/NeverSinkDev/FilterBlade-Public-Assets/assets/20803858/84351286-7bc1-4e2e-8d6e-b69c699b3dff)

## Text & Title Commands:

Title:

Used to divide chapters and groups of sections.

SectionTitle:

Mostly used as the title for each chapter.

Description:

Smaller texts for details.

## Advanced Commands:

Searches:
Slowly going deprecated, rarely used. WIP

## Custom Rules & New Tiers:

This is an advanced topic. For now we don't expect people to work with these advanced commands.

### Functions
Defines a GUI-function with parameters. This can be useful when having many rules in the same section that all share the same GUI structure.
Required for the "Add tier" functionality and custom rules to define how the GUI of new rules is supposed to look like.

`Function <funcName>($s, $shd = "SHD", $text) { <content> }`

Where <funcName> is an unique name you can define here. When using this func later, you do so by using this name.
And <content> is any normal option file command - most commonly a single QuickUI, where you can use specify "$s" as filter rule (or search) instead of the actual value.
Same for the $shd param, where we have given it a default of "SHD" which is required for new rules.

After defining the Function, you can call it by writing its name just like any other command.

### Conditionals:

Conditionals are basically any new rule that is generated in FilterBlade and was not previously in the base filter.

They can be simple, such as `Conditional("?AncTrialOmensNewTier") { }` where "?AncTrialOmensNewTier" is the ID/Name of the new rule-builder.
All conditional IDs start with a questionmark. This simple setup is used on existing tierLists to add new custom tiers to it.

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

## TierListToolBar: WIP

## BaseTypeMatrix (multile rules/tiers): WIP
