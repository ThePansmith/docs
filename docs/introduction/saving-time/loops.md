---
title: Loops, Functions, and Regex
---

!!! abstract "Work In Progress"
    The contents found here are not completely done, please consider submitting a Pull Request to improve it.

# Regex, Loops, and Functions

After reading the previous chapters, you might have started thinking about custom recipes and items. You might have even considered how many recipes you would need to tweak and how much time it would take to write them individually, like datapacks. Thankfully, you don't have to do that! You have a lot of tools at your disposal to make your life easier and your code more compact.

---

## Arrays
Some events allow you to use an array instead of just a single item, enabling you to perform actions on multiple items rather than needing separate methods for each.

```js
JEIEvents.hideItems(event => {
    event.hide(["ae2:inscriber", "ae2:vibration_chamber"]) // (1)
})
```

1. Hides both the `ae2:inscriber` and the `ae2:vibration_chamber`

## Loops
Loops allow you to iterate over arrays and perform actions on each element. This is particularly useful when you have a list of items and want to apply the same logic to all of them.

Here's an example of using a `foreach` loop to define recipes for upgrading furnaces:

```js
const furnaces = [
    ["iron_furnace", "minecraft:iron_ingot", "minecraft:furnace"],
    ["copper_furnace", "minecraft:copper_ingot", "ironfurnaces:iron_furnace"],
    ["silver_furnace", "gtceu:silver_ingot", "ironfurnaces:copper_furnace"],
    ["gold_furnace", "minecraft:gold_ingot", "ironfurnaces:silver_furnace"],
    ["diamond_furnace", "minecraft:diamond", "ironfurnaces:gold_furnace"],
];

furnaces.forEach(([furnace, mat, base]) => { // (1)
    event.shaped(Item.of(`ironfurnaces:${furnace}`), [ // (2)
        "AAA",
        "ABA",
        "AAA"
    ], {
        A: mat,
        B: base,
    });
});
```

1. **Iteration**: The `foreach` loop iterates over each element in the `furnaces` array. Each element is destructured into `furnace`, `mat`, and `base`.
2. **Recipe Definition**: For each furnace, a shaped recipe for the output furnace (`furnace`) is defined using the provided material (`mat`) and base furnace (`base`).


## Functions
Functions allow you to reuse code by defining a function and repeatedly calling it with multiple arguments, even across different files! Knowing a little bit of [javascript](https://www.w3schools.com/js/default.asp) can go a very long way when working with functions.


```js
  let potting = (output, pottedInput) => { // (1)
    event.shaped(output, [
      'BIB',
      ' B '
    ], {
      B: 'minecraft:brick',
      I: pottedInput
    })
  }

  potting('kubejs:potted_snowball', 'minecraft:snowball') // (2)
  potting('kubejs:potted_lava', 'minecraft:lava_bucket')
  potting('minecraft:blast_furnace', 'minecraft:furnace')
```
1. Defines helper function `potting` with the elements of of `output` and `pottedInput`
2. Creating shaped recipes using the helper

### Helper Functions
Using the knowledge that you can call constants across different files, you could create a helper file with various functions in it and simply call them from anywhere.

```js
// priority: 9999

// The function being defined.
const donutCraft = (event, output, center, ring) => {
    return event.shaped(output, [
        "SSS",
        "SCS",
        "SSS"
    ], {
        C: center,
        S: ring
    });
};
```

```js
// Various usages of this function in various other files
donutCraft(event, "minecraft:weeping_vines", "occultism:spirit_attuned_gem", "minecraft:twisting_vines");
donutCraft(event, "minecraft:twisting_vines", "occultism:spirit_attuned_gem", "minecraft:weeping_vines");

donutCraft(event, Item.of("functionalstorage:void_upgrade", 4), "#functionalstorage:drawer", "minecraft:obsidian");

donutCraft(event, "sophisticatedbackpacks:stack_upgrade_starter_tier", "sophisticatedbackpacks:upgrade_base", "create:andesite_alloy");
donutCraft(event, "sophisticatedbackpacks:stack_upgrade_tier_1", "sophisticatedbackpacks:stack_upgrade_starter_tier", "create:brass_ingot");
```

### Applying knowledge 
Of course, functions aren't as rigid as the above examples imply. With a little bit of JavaScript knowledge, you can create pretty some nice things.

The function below is excerpted from [CABIN](https://github.com/ThePansmith/CABIN), and is used to generate the recipes used to turn machines into usable parts. If the fourth parameter is omitted, it defaults to creating a stonecutting recipe instead.

```js
const createMachine = (machineItem, event, outputIngredient, inputIngredient) => { // (1)
    machineItem = Ingredient.of(machineItem);
    outputIngredient = Item.of(outputIngredient);

    event.remove({ output: outputIngredient });
    if (inputIngredient) { // (2)
        inputIngredient = Ingredient.of(inputIngredient);
        event.custom({
            "type": "create:item_application",
            "ingredients": [
                machineItem.toJson(),
                inputIngredient.toJson()
            ],
            "results": (outputIngredient.isBlock() && outputIngredient.getCount() > 1) ? // (3)
                [
                    outputIngredient.withCount(1).toJson(),
                    outputIngredient.withCount(outputIngredient.getCount() - 1).toJson() // (4)
                ]
                :
                [
                    outputIngredient.toJson()
                ]
        });
    } else { 
        event.stonecutting(outputIngredient, machineItem); // (5)
    }
};
```

1. **Parameters**:
    - **`machineItem`**: The base ingredient (e.g., the machine being used).
    - **`event`**: The recipe event.
    - **`outputIngredient`**: The resulting item.
    - **`inputIngredient`** *(optional)*: The secondary ingredient.
2. Checks to see if the call has an `inputIngredient` and determines which code block should be run.
3. Checks the type and number of `outputIngredient`. If it passes, the first block is run; otherwise, the second block is run.
4. This is done because of how the `create:item_application` recipe works: The block in the first slot of the `item_application` array is the one that is placed in the world, while the others are dropped as items. Having them all in the first slot would cause the others to be voided.
5. Runs if the check for `inputIngredient` fails.

```js
// Using the function elsewhere
createMachine("computercraft:computer_normal", event, "computercraft:turtle_normal", "thermal:invar_gear");
createMachine("minecraft:obsidian", event, Item.of("minecraft:crying_obsidian", 2)); 
```


#### Callback Functions
Likewise, you can define functions that call other functions. For example:

```js
// Defining the functions
const andesiteMachine = (event, outputIngredient, inputIngredient) => {
    return createMachine("kubejs:andesite_machine", event, outputIngredient, inputIngredient);
}

const leadMachine = (event, outputIngredient, inputIngredient) => {
    return createMachine("kubejs:lead_machine", event, outputIngredient, inputIngredient);
}

// Using the function elsewhere
andesiteMachine(event, Item.of("create:mechanical_mixer", 1), "create:whisk");
leadMachine(event, Item.of("create:factory_gauge", 2));

if (Platform.isLoaded("createdeco")) { // (1)
    andesiteMachine(event, Item.of("create:mechanical_roller", 1), "createdeco:andesite_hull");
} else { // (2)
    andesiteMachine(event, Item.of("create:mechanical_roller", 1), "create:andesite_alloy_block");
}
```

1. Checks to see if the mod "createdeco" is loaded and determines what should be done.
2. Ifs, switches, and otherwise are not limited to just functions or mechanics!


## Regex
A **regex**, shorthand for "regular expression", is a pattern describing a certain amount of text. Essentially, it acts as a filter, allowing anything that matches to pass. Regex can be quite powerful, enabling you to search with surprisingly complex options. Getting into the nitty-gritty of regex is beyond this guide (consider looking [here](https://regexr.com/) for a starting place), but here are some practical examples:

```js
JEIEvents.hideItems(event => {
    event.hide(/kubejs:trial/); //(1)
    event.hide(/^sophisticatedstorage:limited.+barrel.+$/); //(2)
    event.hide(/^ad_astra:(steel|desh|ostrum|calorite)_(tank|engine)$/); //(3)
});
```

1.  Hides anything that matches the regex filter of: <br>`kubejs:trial` <br> So, anything that has kubejs:trial in its name, such as "**kubejs:trial**_cut_copper" or "**kubejs:trial**_copper_grate".
2.  Hides anything that matches the regex filter of: <br>`sophisticatedstorage:limited [arbitrary text] barrel [arbitrary text]` <br> So, any limited barrels.
3.  Hides anything that matches the regex filter of: <br>`ad_astra: [steel OR desh OR ostrum OR calorite] _ [tank OR engine]` <br> So, anything that matches any of those combos.