---
title: Custom Recipes
---

If a mod supports Datapack recipes, you can add recipes for it without any addon mod support! If a mod has a GitHub repository or other source code, you can find the relevant JSON files in `/src/generated/resources/data/<modname>/recipes/`. Otherwise, you may be able to find it by unzipping the mod's `.jar` file.

---

## Writing a custom recipe
Here's an example of adding a Farmer's Delight cutting board recipe, which defines an input, output, and tool taken straight from [their GitHub](https://github.com/vectorwing/FarmersDelight/blob/1.20.1/src/generated/resources/data/farmersdelight/recipes/cutting/cake.json). Note how the variables match between the datapack file and an `event.custom` recipe replicating it.


=== "Datapack File"

    ```json
    {
      "type": "farmersdelight:cutting",
      "ingredients": [
        {
          "item": "minecraft:cake"
        }
      ],
      "result": [
        {
          "count": 7,
          "item": "farmersdelight:cake_slice"
       }
     ],
      "tool": {
       "tag": "forge:tools/knives"
      }
    }   
    ```

=== "event.custom"

    ```javascript
    event.custom({
      type: 'farmersdelight:cutting',
      ingredients: [
        {
          item: 'minecraft:cake'
        }
      ],
      result: [
        {
          item: 'farmersdelight:cake_slice',
          count: 7
        }
      ],
      tool: {
        tag: 'forge:tools/knives'
      }
    });
    ```


### Looping 
Of course, this starts becoming powerful when you start to combine them with a [loop, function or otherwise](https://pastebin.com/raw/yy3i5L5a), allowing you to easily add tons of custom recipes without having to manage a single datapack file.

```js
let redstoneTransmute = (input, output) => {
    event.custom({
        "type": "tconstruct:casting_basin",
        "cast": { "item": input },
        "cast_consumed": true,
        "fluid": {
            "name": "thermal:redstone",
            "amount": 50
        },
        "result": output,
        "cooling_time": 30
    })
}

redstoneTransmute("minecraft:cobblestone", "minecraft:netherrack")
redstoneTransmute("minecraft:sand", "minecraft:red_sand")
// etc...
```

## Recipe Schemas
!!! WIP

!!! note
    This is being written with 1.20 currently being in the lead of packdev COLTS vote in mind, KubeJS 1.21 handles this differently. 

Now, if you have a recipe type that you use frequently, it might be worth writing your own **recipe schema** for it. Recipe schemas are sets of keys that define how a recipe is constructed. *How* this works is covered in more detail in the reflection chapter (for those unfamiliar with Java, please read that first), and assumes you have at least basic knowledge of how recipes work.

### Defining the Recipe Schema
To begin, you first need to define the schema you're going to use in your startup scripts.

#### What Goes In?
Let's use Extended Crafting's Combination Crafting as an example. It might seem complicated compared to some other recipe types, but it's manageable once you break it down. As a refresher, [here's how it works](https://blakesmods.com/wiki/extendedcrafting/blocks/crafting-core) if you haven't used Extended Crafting before.

Here's a datapack example:
```json
{
  "type": "extendedcrafting:combination",
  "powerCost": 400000,
  "input": {
    "item": "minecraft:iron_ingot"
  },
  "ingredients": [
    {
      "item": "minecraft:potato"
    },
    {
      "item": "minecraft:potato"
    },
    {
      "item": "minecraft:potato"
    },
    {
      "item": "minecraft:potato"
    },
    {
      "item": "minecraft:potato"
    },
    {
      "item": "minecraft:potato"
    },
    {
      "item": "minecraft:potato"
    },
    {
      "item": "minecraft:potato"
    }
  ],
  "result": {
    "item": "minecraft:stone"
  }
}
```

The field:

- An ItemStack `input` field

- An ItemStack `ingredients` array 

- An ItemStack `result` field

- An integer `powerCost` field 

Reviewing the Java implementation reveals an additional field:

- An optional integer `power_rate` field 


??? java
    ```java
  	  public static class Serializer implements RecipeSerializer<CombinationRecipe> {
  		  public static final MapCodec<CombinationRecipe> CODEC = RecordCodecBuilder.mapCodec(builder ->
  				  builder.group(
  						  Ingredient.CODEC_NONEMPTY.fieldOf("input").forGetter(recipe -> recipe.input),
  						  Ingredient.CODEC_NONEMPTY
  								  .listOf()
  								  .fieldOf("ingredients")
  								  .flatXmap(
	  									  field -> {
	  										var max = 48;
	  										var ingredients = field.toArray(Ingredient[]::new);
	  										if (ingredients.length == 0) {
	  											return DataResult.error(() -> "No ingredients for Combination recipe");
	  										} else {
	  											return ingredients.length > max
	  													? DataResult.error(() -> "Too many ingredients for Combination recipe. The maximum is: %s".formatted(max))
	  													: DataResult.success(NonNullList.of(Ingredient.EMPTY, ingredients));
	  										}
	  									},
	  									DataResult::success
	  							)
	  							.forGetter(recipe -> recipe.inputs),
	  					ItemStack.STRICT_CODEC.fieldOf("result").forGetter(recipe -> recipe.result),
	  					Codec.INT.fieldOf("power_cost").forGetter(recipe -> recipe.powerCost),
	  					Codec.INT.optionalFieldOf("power_rate", ModConfigs.CRAFTING_CORE_POWER_RATE.get()).forGetter(recipe -> recipe.powerRate)
	  			).apply(builder, CombinationRecipe::new)
	  	);
    }
    ```

After reviewing KubeJS's [schema](https://github.com/KubeJS-Mods/KubeJS/tree/2001/common/src/main/java/dev/latvian/mods/kubejs/recipe/schema) and [component](https://github.com/KubeJS-Mods/KubeJS/tree/2001/common/src/main/java/dev/latvian/mods/kubejs/recipe/component) classes, you can create the schema using `ItemComponents` and `NumberComponent`:

```js
const $RecipeSchema = Java.loadClass("dev.latvian.mods.kubejs.recipe.schema.RecipeSchema"); // (1)
const $ItemComponents = Java.loadClass("dev.latvian.mods.kubejs.recipe.component.ItemComponents");
const $NumberComponent = Java.loadClass("dev.latvian.mods.kubejs.recipe.component.NumberComponent");


StartupEvents.recipeSchemaRegistry(event => {
    event.register("extendedcrafting:combination", new $RecipeSchema( // (2)
        $ItemComponents.OUTPUT.key("result"),
        $ItemComponents.INPUT.key("input"),
        $ItemComponents.UNWRAPPED_INPUT_ARRAY.key("ingredients"),
        $NumberComponent.INT.key("powerCost").optional(500000).preferred("powerCost"), // (3)
        $NumberComponent.INT.key("powerRate").defaultOptional().preferred("powerRate")
    )
    );
})
```

1. Imports the `RecipeSchema` class, needed to write your own recipe schemas
2. Note that the order of the keys does not have to match the java code or any existing datapack files, so feel free to organize it into whatever way is most comfortable for writing with.
3. If not set, defaults to a value of 500000

### Using it
Now for the fun part: Actually using it!

```js
    event.recipes.extendedcrafting.combination(
        "kubejs:quantum_fluxed_eternium_heavy_plating",
        "gtceu:cryococcus_plate", ["2x redstone_arsenal:flux_plating", "3x kubejs:quantum_flux"],
        1600000, 160000
    )
```

