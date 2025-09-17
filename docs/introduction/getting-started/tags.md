---
title: Tags
---

Minecraft uses tags to group items, blocks, fluids, and entity types together for various game mechanics. Tags are essentially collections of game elements that share common characteristics or purposes. They're used extensively throughout the game for: 

- **Recipes** (using item tags)
- **Game mechanics** (climbable blocks, mineable requirements)
- **Mod compatibility**
- **World generation**
- and more!

You can easily view an item/block's tags by holding it and using `/kubejs hand`. If advanced tooltips are enabled, it will also show an item's tags when hovered over. A list of minecraft's tags can be viewed [here](https://minecraft.wiki/w/Tag_(Java_Edition)#List_of_tag_types).

---
## Adding Tags
!!! note
    You do not have to manually define the tags themselves anywhere, using any of the following functions below will also register the tag.
### Tagging in Server Events
#### Item tags 
These tags are primarily used in recipes, and can also be used to influence how a item behaves when it is dropped, equipped, or used. 
```javascript
ServerEvents.tags('item', event => {
        event.add('forge:cobblestone', 'minecraft:diamond_ore') //(1)
        event.remove('forge:cobblestone', 'minecraft:mossy_cobblestone') //(2)
        event.removeAllTagsFrom('minecraft:stick') //(3)
        event.removeAll('forge:ingots/copper') //(4)
        event.add('mymodpack:completely_new_tag', 'minecraft:clay_ball') //(5)
        event.add('mymodpack:stones', '#forge:stone') //(6)
        event.add('minecraft:stone_pressure_plates', [
            'minecraft:stone_pressure_plate',
            'minecraft:polished_blackstone_pressure_plate'
        ]) //(7)
        event.add('mymodpack:stone_redstone_activators', [
            '#minecraft:stone_buttons',
            '#minecraft:stone_pressure_plates',
            'minecraft:lever'
        ]) //(8)
})
```

1. **Add to Tag**: Adds `minecraft:diamond_ore` to the `#forge:cobblestone` tag.  
2. **Remove from Tag**: Removes `minecraft:mossy_cobblestone` from the `#forge:cobblestone` tag.  
3. **Remove All Tags**: Clears all tags associated with `minecraft:stick`.  
4. **Remove All Entries**: Clears all items from the `#forge:ingots/copper` tag.  
5. **Create New Tag**: Creates a new tag `#mymodpack:completely_new_tag` and adds `minecraft:clay_ball` to it. New tags can use any namespace.
6. **Tag Nesting**: Adds the `forge:stone` tag as a child of the `#mymodpack:stones` tag.
7. **Array of Entries**: Adds `minecraft:stone_pressure_plate` and `minecraft:polished_blackstone_pressure_plate` to the new tag `#minecraft:stone_pressure_plates`.
8. **Array of Entries and Tags**: Adds the `#minecraft:stone_buttons` and `#minecraft:stone_pressure_plates` tags as children and adds `minecraft:lever` to the `#mymodpack:stone_redstone_activators` tag.

#### Block tags
Block tags influnce what a behaviors a block has, as well as seeing use in world generation.
```js
ServerEvents.tags('block', event => {
  event.add('minecraft:climbable', 'minecraft:tall_grass') //(1)
  event.add('minecraft:moss_replaceable', 'minecraft:bedrock') //(2)
    event.add('minecraft:jungle_logs', '#mushroom_grow_block') //(3)
})
```

1. Adds tall grass to the climbable tag. This does make it climbable!
2. Adds bedrock to the `minecraft:moss_replaceable` tag, making it replacable with moss blocks when a nearby moss block has bone meal applied to it. 
3. Adds the `jungle_logs` block tag as a child of the `c:stones` tag. 

### Tagging During Startup 
You can also add tags when said items/blocks are being registered.
```javascript
StartupEvents.registry('block', event => {
  event.create('example_block')
    .tagBlock('minecraft:mineable/pickaxe')
    .tagBlock('minecraft:needs_iron_tool')
    .tagBlock('my_namespace:my_custom_tag')
})

StartupEvents.registry('item', event => {
  event.create('custom_item')
    .tagItem('forge:ingots')
    .tagItem('my_namespace:special_items')
})
```

## Using tags in recipes
!!! warning
    Recipes use item tags, not block or fluid tags. Even if items representing those are blocks, like `minecraft:cobblestone`, it still uses an item tag for recipes.
Tags can be used in most places where items are supported, with the hash sign. Tags can't be used as a recipe output.

```js
event.shaped(
  Item.of('minecraft:chest', 4), [
    'LLL',
    'L L', 
    'LLL'
  ],{
    L: '#minecraft:logs'
  }
)

event.stonecutting('3x minecraft:stick', '#minecraft:planks')

```
