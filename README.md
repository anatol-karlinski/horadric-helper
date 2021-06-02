# 💀 Horadric Helper 💀

[![Release Horadric Helper](https://github.com/meta-is-beta/horadric-helper/actions/workflows/release.yml/badge.svg?branch=main)](https://github.com/meta-is-beta/horadric-helper/actions/workflows/release.yml)

A JavaScript WebComponents library that allows you to display tooltips from ARPG games on you webstie using Html tags. See it in action on my [blog](https://meta-is-beta.com/?p=40).

**Currently supported games** (as of version 0.5 Beta)
- Path of Exile

## Table of Content
- [How-to TLDR](#how-to-tldr)
- [Installation](#installation)
- [Html Components](#html-components)
- [Html Component Props](#html-component-props)
- [Configuration object](#configuration-object)
- [Showcase sections](#showcase-sections)
- [Skills and Passives data](#skills-and-passives-data)
- [Contribution](#contribution)

## How-to TLDR
Short example of library usage.
More complex examples with explanations can be found in the rest of the documentation.

#### 1. Include `js` and `css` files on your site.

```html
<script src="https://cdn.jsdelivr.net/gh/meta-is-beta/horadric-helper@0.5/dist/poe/horadric-helper-poe.umd.min.js"></script>
<link rel="stylesheet" href="https://cdn.jsdelivr.net/gh/meta-is-beta/horadric-helper@0.5/dist/poe/horadric-helper-poe.css" />
```

#### 2. Add component to your website's Html.

```html
<p>
  <poe-item as-icon reference="Frosted Fishscale Gauntlets of Skill"></poe-item>
</p>
```

#### 3. Initialize config for referenced item.

```html
<script>
  let itemConfig = {
    reference: "Frosted Fishscale Gauntlets of Skill",
    iconUrl:
      "https://web.poecdn.com/image/Art/2DItems/Armours/Gloves/GlovesStrDex1.png",
    data: `
      Item Class: Gloves
      Rarity: Magic
      Frosted Fishscale Gauntlets of Skill
      --------
      Armour: 9
      Energy Rating: 9
      --------
      Requirements:
      Level: 2
      Str: 4
      Dex: 4
      --------
      Item Level: 5
      --------
      Adds 1 to 2 Cold Damage to Attacks
      6% Increased Attack Speed
    `,
  };

  window.HoradricHelper.applyConfig(itemConfig);
</script>
```

#### 4. Item should be displayed on the page.

![DangYo2](https://meta-is-beta.com/wp-content/uploads/2021/05/de7b6ac2f1887243b844b831f4263347.gif)

## Installation
To enable Horadric Helper you simply need to include JavaScript and CSS files on your website (same as any other js library).


You can get both `js` and `css` files from jsDelivr CND:

```url
https://cdn.jsdelivr.net/gh/meta-is-beta/horadric-helper@0.5/dist/poe/horadric-helper-poe.umd.min.js
https://cdn.jsdelivr.net/gh/meta-is-beta/horadric-helper@0.5/dist/poe/horadric-helper-poe.css
```
_You can specify version of the library by changing `@0.5` to desired version. You can also set it to `latest` to always get newest version._

**OR**

You can get them from this repo's `/dist/poe` folder.

**Notes**
- This library supports only newer browsers such as Chrome, Firefox or Opera. IE11 is **not** supported.
- There is also non-minified version of JS available under `horadric-helper-poe.umd.js`.
- There are source-map files for both minified and non-minified versions.

## Html Components
Package `horadric-helper-poe` contains 2 components:
- `<poe-item>` - This component can render equipment, gems, jewels, flasks, currency and maps
- `<poe-passive>` - This component can render passives, including ascendancy nodes

To activate components you need to assign `reference` prop to them.
This will allow you to later load configuration object for that reference.
*(More about this in [Configuration](#configuration-object) section.)*
```html
<poe-item reference="Headhunter">
```

**Notes**
 - Multiple components with the same reference can exist - any config applied to that reference will work for all matching components.
 - Components can be used as any other Html element - this is thanks to [Web Components
](https://developer.mozilla.org/en-US/docs/Web/Web_Components) standard.

## Html Component Props

You can apply props to set or change behaviour for individual components.
| Prop | Type | Description |
|:-------:|:----------:|:------|
| `reference` | `String` | **Required**. Name used to target components with the same reference when loading configs. See [Configuration](#configuration-object) section for details. |
| `classes` | `String` | Additional classes that will be applied to root component.|
| `popover‑classes` | `String` | Additional classes that will be applied to popover. |
| `label‑text` | `String` | By default items will be labeled by their name. You can override that with this prop and assign your own text. |
| `as‑text` | `Bool` | Display item as text. Showcase popover will appear on hover. (*This is the default settings*) |
| `as‑icon` | `Bool` | Displays item as icon with label. Showcase popover will appear on hover. |
| `as‑showcase` | `Bool` | Displays item as showcase. Showcase popover will **not** appear on hover. |
| `icon‑inside` | `Bool` | Show icon inside of showcase. (*Only works if `iconUrl` was provided in config*) |
| `icon‑outside` | `Bool` | Show icon outside of showcase. (*Only works if `iconUrl` was provided in config*) |
| `icon‑size` | `string` | Available values: `auto\|sm\|md\|lg\|xlg`. Allows to set size of icon. Default is `auto`. |
| `dim‑sections` | `String` | List of sections to be greyed-out. More about this in [Sections](#showcase-sections) chapter. |
| `hide‑sections` | `String` | List of sections to be hidden. More about this in [Sections](#showcase-sections) chapter. |

#### Examples
`Headhunter` as small icon, with `properties` section hidden.
```html
<poe-item
  reference="Headhunter"
  as-icon
  hide-sections="properties"
  icon-size="sm"
></poe-item>
```

`Beef` passive as showcase, with icon displayed inside.
```html
<poe-passive
  reference="Beef"
  as-text
  icon-inside
></poe-passive>
```

## Configuration object

When library is loaded it will register global object called `HoradricHelper`.
You can use `HoradricHelper.applyConfig` method to load either single `PoeConfig` object or array of `PoeConfig` objects.

### ``PoeConfig`` object

```typescript
type PoeConfig = {
  // Required
  // Reference name of components to which this config should apply to.
  // Eg: If you set this value to "Headhunter" this config will be
  // applied to all components with <poe-item reference="Headhunter">.
  reference: String;

  // Optional
  // Url of icon that will be displayed if any of icon props are set.
  iconUrl: String | null;

  // Required
  // Object which describes all properties of item or passive you want to display.
  // Exact structure of that object is described in 'PoeItem' and 'PoePassive' sections.
  // You can also use raw item string data copied from the game.
  // (To copy item's data to clipboard press Ctrl+C while hovering over an item in-game)
  data: PoeItem | PoePassive | String | null;
};
```
### `PoeItem` object
```typescript
type PoeItem = {
  // Required
  // Item's rarity
  rarity: "normal" | "rare" | "magic" | "unique" | "gem";

  // Required
  // Item's type
  type: "equipment" | "gem" | "jewel" | "flask" | "currency" | "map";

  // Required
  // Item's name
  name: String;

  // Optional
  // Name of item's base - eg. "Leather Belt" for Headhunter
  baseName: String | null;

  // Optional
  // List of influences on an item
  // Eg: ["elder", "shaper"]
  influences: ("crusader" | "warlord" | "hunter" | "redeemer" | "elder" | "shaper" | "replica")[] | null;

  // All sections are optional
  sections: {

    // Optional
    // List of item's properties text lines
    // Eg: ["Armour: 9", "Energy Rating: 9"]
    properties: String[] | null;

    // Item's level
    // Eg: "42"
    level: String | null;

    // List of item's requirement text lines
    // Eg: ["Dex: 12", "Str: 12"]
    requirements: String[] | null;

    // List of item's enchants text lines
    // Eg: ["Allocates Beef"]
    enchants: String[] | null;

    // List of item's implicits text lines
    // Eg: ["Has 1 Socket"]
    implicits: String[] | null;

    // List of item's modifiers text lines
    // Eg: ["Adds 1 to 2 Cold Damage to Attacks", "6% Increased Attack Speed"]
    modifiers: String[] | null;

    // List of item's gem description text lines
    // Only applies to items with type "Gem"
    // Eg: ["Supports any skill that has a duration."]
    gemDescription: String[] | null;

    // List of item's statuses
    // Eg: ["corrupted", "split"]
    statuses: ("corrupted" | "mirrored" | "split")[] | null;
  }

}
```
### `PoePassive` object
```typescript
type PoePassive = {
  // Required
  // Passives's name
  // Eg: "Beef"
  name: String;

  // Required
  // Passives's type
  // Eg: "notable"
  type: "basic" | "notable" | "keystone" | "ascendancy basic" | "ascendancy notable";

  // All sections are optional
  sections: {
    // List of passives's description text lines
    // Eg. for "Arcane Blessing":
    // [
    //   "50% increased Effect of Arcane Surge on you"
    //   "Gain Arcane Surge when you or your Totems Hit an Enemy with a Spell"
    // ]
    description: String[];
  }
};
```

### Examples of loading configs

#### 1. Item loaded from data object
As icon, with modified label text.
```html
<!-- Html --->
<body>
  <div>
    <poe-item
      reference="Stone of Lazhwar"
      as-icon
      label-text="Spell block amulet"
    ></poe-item>
  </div>
</body>

<!-- JavaScript --->
<script>

// Declaration of PoeItem object
let amuletdata = {
  rarity: "Unique",
  type: "Equipment",
  name: "Stone of Lazhwar",
  baseName: "Lapis Amulet",
  sections: {
    requirements: ["Level 5"],
    implicits: ["+22 to Intelligence"],
    modifiers: [
      "14% Chance to Block Spell Damage",
      "12% increased Cast Speed",
      "+45 to maximum Mana"
    ]
  }
};

// Declaration of PoeConfig object
let amuletConfig = {
  reference: "Stone of Lazhwar",
  iconUrl: "https://web.poecdn.com/image/Art/2DItems/Amulets/Amulet5Unique.png",
  // Assignment of PoeItem to PoeConfig
  data: amuletdata
};

// Loading config for <poe-item reference="Stone of Lazhwar"> component
window.HoradricHelper.applyConfig(amuletConfig);

</script>
```
<p align="center">
  <img src="https://meta-is-beta.com/wp-content/uploads/2021/06/Stone-of-Lazhwar2.png" width="800px"/>
</p>

#### 2. Item loaded from data string
As showcase, with icon inside and with item level hidden.
```html
<!-- Html --->
<body>
  <div>
    <poe-item
      reference="Goldrim"
      as-showcase
      icon-inside
      hide-sections="item-level"
    ></poe-item>
  </div>
</body>

<!-- JavaScript --->
<script>

// Declaration of PoeConfig object
let helmConfig = {
  reference: "Goldrim",
  iconUrl: "https://web.poecdn.com/image/Art/2DItems/Armours/Helmets/HelmetDexUnique2.png",
  // Assignemnt of raw item data copied from the game
  data: `
    Item Class: Helmets
    Rarity: Unique
    Goldrim
    Leather Cap
    --------
    Evasion Rating: 54 (augmented)
    --------
    Sockets: G-G-B-G
    --------
    Item Level: 60
    --------
    +35 to Evasion Rating
    10% increased Rarity of Items found
    +36% to all Elemental Resistances
    Reflects 4 Physical Damage to Melee Attackers
    --------
    No metal slips as easily through the fingers as gold.
  `
};

// Loading config for <poe-item reference="Goldrim"> component
window.HoradricHelper.applyConfig(helmConfig);

</script>
```
<p align="center">
  <img src="https://meta-is-beta.com/wp-content/uploads/2021/06/Goldrim2.png" width="600px"/>
</p>

#### 3. Passive loaded from data
As text, with icon outside.
```html
<!-- Html --->
<body>
  <div>
    <poe-passive
      reference="Magnifier"
      as-text
      icon-outside
      hide-sections="item-level"
    ></poe-passive>
  </div>
</body>

<!-- JavaScript --->
<script>

// Declaration of PoePassive object
let magnifierdata = {
  name: "Magnifier",
  type: "Notable",
  sections: {
    description: [
      "10% increased Area of Effect",
      "10% increased Area Damage",
      "+10% to Critical Strike Multiplier"
    ]
  }
};

// Declaration of PoeConfig object
let magnifierConfig = {
  reference: "Magnifier",
  iconUrl: "https://static.wikia.nocookie.net/pathofexile_gamepedia/images/2/2e/AreaDmgNotable_passive_skill_icon.png",
  // Assignment of PoePassive to PoeConfig
  data: magnifierdata
};

// Loading config for <poe-passive reference="Magnifier"> component
window.HoradricHelper.applyConfig(magnifierConfig);

</script>
```
<p align="center">
  <img src="https://meta-is-beta.com/wp-content/uploads/2021/06/Magnifier2.png" width="600px" />
</p>

## Showcase sections
- Showcases are split into sections such as `modifiers`, `implicits`, etc.
- You can chose which sections you want to load if you are using `data` as data source.
- You can also grey-out or hide specific lines or entire sections using `dim-sections` and `hide-sections` props.

### `PoeItem` sections
| Name    | Type     | Description |
|:-------:|:--------:|:------------|
| `itemLevel` | `String` | Item's level ([wiki](https://pathofexile.fandom.com/wiki/Item_level)) _(do not confuse with item's level requirement)_. |
| `requirements` | `String[]` | List of item's requirement text lines.  |
| `enchants` | `String[]` | List of item's enchants text lines ([wiki](https://pathofexile.fandom.com/wiki/Modifiers#Enchantments)). |
| `implicits` | `String[]` | List of item's implicits text lines ([wiki](https://pathofexile.fandom.com/wiki/Modifiers#Implicit_modifiers)). |
| `modifiers` | `String[]` | List of item's modifiers text lines ([wiki](https://pathofexile.fandom.com/wiki/Modifiers#Explicit_modifiers)) _(also known as **explicit modifiers**)_. |
| `gemDescription` | `String[]` | List of item's gem description text lines. |
| `statuses` | `String[]` | Avalible statuses: `corrupted`, `mirrored`, `split`.  |

### `PoePassive` sections
| Name    | Type     | Description |
|:-------:|:--------:|:------------|
| `description` | `String[]` | List of passive's description text lines. |

### Dimming and hiding sections
You can dim or hide ether entire sections or specific lines of showcase using `dim‑sections` and `hide‑sections` props. (Section names are in `kebab-case`)
#### Targeting entire sections
You can target entire sections by passing section names seprated by `;`.

**Format**
```js
hide-sections="section1;section2;section3"
```
or
```js
hide-sections="section1:all;section2:all;section3:all"
```


**Example**
```html
<!-- Hiding all implicits and requirements -->
<poe-item reference="Headhunter" hide-sections="implicits;requirements"></poe-item>

<!-- Dimming entire description -->
<poe-passive reference="Beef" dim-sections="description"></poe-passive>
```

<p align="center">
  <img src="https://meta-is-beta.com/wp-content/uploads/2021/06/HideSections2.png" />
</p>

#### Targetting specific lines
To target specific lines you can pass numbers of lines separated by `,` after section's name.

**Format**
```js
hide-sections="section1:1,2;section2:4,5,6;section3:1"
```

**Example**
```html
<!-- Hiding 1st and 2nd line of modifiers and 1st line of properties -->
<poe-item reference="Headhunter" hide-sections="modifiers:1,2;properties:1"></poe-item>

<!-- Dimming 3rd and 4th line of description -->
<poe-passive reference="Lethality" dim-sections="description:3,4"></poe-passive>
```

<p align="center">
  <img src="https://meta-is-beta.com/wp-content/uploads/2021/06/HideLines2.png" />
</p>

## Skills and Passives data
You can create any item or passive config yourself, but I have also created premade configs for skill gems and passives for you to use.

- In `/src/path-of-exile/tools/data` you will find two json files:
  - `gem-data.json` - This file holds premade configs for all skill gems in the game as of patch 3.14
  - `passive-node-data.json` - This file holds premade configs for all passives in the game as of patch 3.14
- In `/src/path-of-exile/tools/` you will find two script files:
  - `get-poe-gems-data.js` - This script can pull newest config data for all gems. It will produce a file simillar to `gem-data.json`
  - `get_poe_passive_node_data.py` - This script can pull newest passives data. It will produce a file simillar to `passive-node-data.json`


## Contribution
- Please file bug reports here, on github
- If you have any questions or suggestions feel free to message me at meta.is.beta@gmail.com

#### Local development

Installing depencencies
```sh
npm install
```

Running demo
```sh
npm run serve
```

Building production libraries to `/dist/`
```sh
npm run build
```

Running linter
```sh
npm run lint
npm run lint:fix
```

Running tests
```sh
npm run test:unit
```
