
# Custom Blocks

!!! abstract "Work In Progress"
    The contents found here are not completely documented, please consider submitting a Pull Request with additional information.

## Example Block
```javascript
StartupEvents.registry('block', event => {
    event.create('glowing_obsidian_glass')
        .displayName('Glowing Obsidian Glass')
        .mapColor('color_black')
        .soundType('glass')
        .hardness(2.0)
        .resistance(6.0)
        .lightLevel(0.8)
        .renderType('translucent')
        .textureAll('kubejs:block/glowing_obsidian_glass')
        .tagBlock('minecraft:mineable/pickaxe')
        .tagBlock('minecraft:needs_iron_tool')
        .waterlogged()
        .box(2, 0, 2, 14, 16, 14, true)
        .fullBlock(false);
});
```

## BlockBuilder Methods
### Core Properties

- **`displayName(value: TextComponent)`**  
  Sets the block's display name.

- **`mapColor(value: MapColor)`**  
  Sets the block's map color (1.20.1+ only). Use lowercase color IDs like 'color_light_green'.

- **`soundType(value: SoundType)`**  
  Sets the block's sound type (1.20.1+ only). Use sound type IDs like 'glass', 'wood'.

- **`hardness(value: float)`**  
  Sets block hardness (mining time). Higher values take longer to mine.

- **`resistance(value: float)`**  
  Sets explosion resistance. Higher values resist explosions better.

- **`unbreakable()`**  
  Makes block unbreakable (like bedrock). Sets hardness=-1, resistance=MAX_VALUE.

### Sound Type Shortcuts

Shortcut methods for common sound types:

- `noSoundType()`

- `woodSoundType()`

- `stoneSoundType()`

- `gravelSoundType()`

- `grassSoundType()`

- `sandSoundType()`

- `cropSoundType()`

- `glassSoundType()`

### Visual Properties

- **`lightLevel(value: float)`**  
  Sets light emission (0.0-1.0). 0.0=no light, 1.0=full light.

- **`renderType(type)`**  
  Sets rendering type.
	- `solid`: For fully opaque blocks
    - `cutout`: For textures with transparency (e.g., glass)
    - `translucent`: For semi-transparent textures (e.g., stained glass)

- **`color(tintIndex: int, color: Color)`**  
  Recolors specific texture layers.

- **`textureAll(texture: ID)`**  
  Textures all sides identically. Path format: 'namespace:block/texture_name'.

- **`texture(side: Direction, texture: ID)`**  
  Textures specific side individually.

- **`model(model: ID)`**  
  Specifies custom model. Path format: 'namespace:block/model_name'.

- **`defaultCutout()`**  
  Sets up cutout rendering for glass-like blocks.

- **`defaultTranslucent()`**  
  Sets up translucent rendering for semi-transparent blocks.

### Physical Properties

- **`opaque(value: boolean)`**  
  Sets whether block is opaque. Opaque blocks don't transmit light.

- **`fullBlock(value: boolean)`**  
  Sets full block rendering. Set false for custom hitboxes.

- **`box(x0, y0, z0, x1, y1, z1: float, scale16?: boolean)`**  
  Defines custom collision box. Multiple calls add multiple boxes. scale16=true uses 0-16 scale.

- **`noCollision()`**  
  Removes collision box. Entities can pass through.

- **`notSolid()`**  
  Marks block as non-solid. Affects rendering.

- **`slipperiness(value: float)`**  
  Sets slipperiness. Affects entity movement (default: 0.6).

- **`speedFactor(value: float)`**  
  Sets movement speed modifier. Affects player movement speed.

- **`jumpFactor(value: float)`**  
  Sets jump height modifier. Affects jump height.

### Functional Properties

- **`property(value: BlockProperty)`**  
  Adds blockstates. E.g., waterlogged, facing directions.

- **`requiresTool(value: boolean)`**  
  Requires tool for drops. Uses block tags to determine valid tools.

- **`waterlogged()`**  
  Enables waterlogging. Block can contain water.

- **`noDrops()`**  
  Disables block drops. No drops even with Silk Touch.

- **`noValidSpawns(value: boolean)`**  
  Disables mob spawning.

- **`suffocating(value: boolean)`**  
  Causes suffocation damage.

- **`viewBlocking(value: boolean)`**  
  Blocks player view.

- **`redstoneConductor(value: boolean)`**  
  Conducts redstone. True by default.

- **`transparent(value: boolean)`**  
  Sets transparency. Affects rendering.

### Tags

- **`tagBlock(tag: ID)`**  
  Adds tag to block.

- **`tagItem(tag: ID)`**  
  Adds tag to block's item.

- **`tagBoth(tag: ID)`**  
  Adds tag to both block and item.

### Advanced Features

- **`randomTick(callback: Consumer<RandomTickEvent>)`**  
  Sets random tick callback.

- **`item(callback: Consumer<ItemBuilder>)`**  
  Modifies block's item properties.

- **`setLootTableJson(json: Object)`**  
  Sets custom loot table. Direct JSON input.

- **`setBlockstateJson(json: Object)`**  
  Sets custom blockstate JSON. Direct JSON input.

- **`setModelJson(json: Object)`**  
  Sets custom model JSON. Direct JSON input.

- **`noItem()`**  
  Removes associated item. Player cannot hold/place.
