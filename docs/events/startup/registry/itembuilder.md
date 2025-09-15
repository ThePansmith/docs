# Custom Items

!!! abstract "Work In Progress"
    The contents found here are not completely documented, please consider submitting a Pull Request with additional information.

## Example Items
```javascript
StartupEvents.registry('item', event => {
    event.create('enchanted_crystal')
        .displayName('Enchanted Crystal')
        .rarity('uncommon')
        .glow(true)
        .maxStackSize(16)
        .useDuration(32)
        .tagItem('minecraft:piglin_loved')
        .burnTime(1600)
        .model('kubejs:item/enchanted_crystal');
});

    event.create('custom_sword', 'sword').tier('diamond').attackDamageBaseline(10.0)

    event.create('magic_steak')
		.food(food => { food
            .hunger(6)
            .saturation(6)
            .effect('minecraft:speed', 600, 0, 1)
            .removeEffect('minecraft:poison')
            .alwaysEdible()
            .fastToEat()
            .meat()
    })
```

## ItemBuilder Methods

### Item types
  - Unset/Default
    - 'basic'
  - Tools
    - 'sword', 'pickaxe', 'axe', 'shovel', 'shears', 'hoe'
  - Armor
    - 'helmet', 'chestplate', 'leggings', 'boots'

### Basic Properties

- **`displayName(name: TextComponent)`**  
  Sets the item's display name .

- **`maxStackSize(size: int)`**  
  Sets the maximum stack size (1-64) .

- **`maxDamage(damage: int)`**  
  Sets maximum durability for damageable items.

- **`rarity(rarity: string)`**  
  Sets item rarity. Values: 'common', 'uncommon', 'rare', 'epic' .

- **`fireResistant()`**  
  Makes item immune to fire and lava (like netherite tools).

- **`glow(glow: boolean)`**  
  Adds enchantment glint effect to the item, even if it is not enchanted.

- **`unstackable()`**  
  Sets maximum stack size to 1.

- **`useDuration(duration: int)`**  
  Sets how long the item takes to use (in ticks).

- **`useAnimation(animation)`**  
  Determines the animation of the item when used, e.g. eating food.

#### Tool Properties
    - `tier(toolTier: string)`
    Sets the tool tier. Default Values: 'wood', 'stone', 'iron', 'gold', 'diamond', 'netherite'
    - `modifyTier(tier => ...)` - Modifies the tool tier. Uses the same syntax as custom tool tiers. Refer to "Custom Tiers" for more details.
    - `attackDamageBaseline(damage: float)` - Sets the baseline attack damage. Only modify this if creating a custom weapon such as a Spear, Battleaxe, etc.
    - `attackDamageBonus(damage: float)` - Adds a bonus to the attack damage.
    - `speedBaseline(speed: float)` - Sets the baseline attack speed. Only modify this for custom weapon types.
    - `speed(speed: float)` - Sets the attack speed.

#### Food Properties

- **`food(consumer: Consumer<FoodBuilder>)`**  
  Configures food properties. Available FoodBuilder methods:
  - `hunger(value: int)`
  Sets hunger restoration
  - `saturation(value: float)`
  Sets saturation modifier
  - `alwaysEdible()`
  Can be eaten even when not hungry
  - `fastToEat()`
  Reduces eating time (sweet berries)
  - `meat()`
  Marks as meat (can feed to wolves)
  - `effect(effect: ID, duration: int, amplifier: int, probability: float)`
  Adds consumption effect
  - `removeEffect(effect: ID)`
  Removes effect on consumption

### Model & Texture

- **`model(model: ResourceLocation)`**  
  Sets the item model.

- **`texture(texture: ResourceLocation)`**  
  Sets the item texture.

- **`parentModel(parent: ResourceLocation)`**  
  Sets parent model for item.

### Tags

- **`tagItem(tag: ResourceLocation)`**  
  Adds tag to the item.

- **`tagBlock(tag: ResourceLocation)`**  
  Adds tag to the item's block (if applicable).

- **`tagBoth(tag: ResourceLocation)`**  
  Adds tag to both item and its block.

### Advanced Properties

- **`burnTime(ticks: int)`**  
  Sets fuel burn time in ticks.

- **`craftingRemainder(item: ItemProvider)`**  
  Sets the item that remains after crafting (e.g., bucket for milk) .

- **`tooltip(vararg lines: TextComponent)`**  
  Adds tooltip lines to the item.

- **`color(color: int)`**  
  Sets item color (for tinted items).

- **`enchantableValue(value: int)`**  
  Sets enchantability value.

- **`enchantmentGlint(glint: boolean)`**  
  Controls enchantment glint effect.

### NBT & Custom Properties

- **`nbt(nbt: CompoundTag)`**  
  Sets custom NBT data for the item.

- **`defaultInstance(consumer: Consumer<ItemStack>)`**  
  Modifies the default item instance.

### Equipment Properties

- **`equipmentSlot(slot: EquipmentSlot)`**  
  Sets the equipment slot.

### Special Item Types

- **`blockItem()`**  
  Marks as a block item.

- **`bucketItem()`**  
  Configures as a bucket item.


## Foodeaten
!!! note
	This is a server event.

ItemEvents.foodEaten(event => {
  const { player, item } = event;
  if (item.id === 'kubejs:magic_steak') {
    player.tell(Text.gold('Yummy Yummy!'));
  }
});