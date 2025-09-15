# Custom Fluids

!!! abstract "Work In Progress"
    The contents found here are not completely documented, please consider submitting a Pull Request with additional information.

## Example Fluids
```javascript
// Basic thick fluid (lava-like) with red tint
event.create('thick_fluid')
 .thickTexture(0xFF0000) // (1)
 .bucketColor(0xFF0000)  // (2)
 .displayName('Thick Fluid'); // (3)

// Basic thin fluid (water-like) with cyan tint
event.create('thin_fluid')
 .thinTexture(0x00FFFF)  // (1)
 .bucketColor(0x00FFFF)  // (2)
 .displayName('Thin Fluid') // (3)
 .noBucket() // (4)
 .noBlock(); // (5)

// Fluid with custom textures
event.create('strawberry_cream')
 .displayName('Strawberry Cream')
 .stillTexture('kubejs:block/strawberry_still') // (6)
 .flowingTexture('kubejs:block/strawberry_flow') // (6)
 .bucketColor(0xFF33FF) 
 .gaseous(); // (7)

// Fluid with modified bucket item
event.create('taco_sauce')
 .thickTexture(0xff0000) // (1)
 .bucketColor(0xff0000)  // (2)
 .bucketItem.group('food'); // (8)
```

1. **Texture**: Defines the fluid's appearance (e.g., thick or thin).  
2. **Bucket Color**: Sets the color of the fluid's bucket.  
3. **Display Name**: Sets the fluid's in-game name.  
4. **No Bucket**: Prevents the creation of a bucket for the fluid.  
5. **No Block**: Prevents the creation of a fluid block.  
6. **Custom Textures**: Allows specifying still and flowing textures.  
7. **Gaseous**: Makes the fluid flow upward instead of downward.  
8. **Bucket Customization**: Modifies bucket properties (e.g., group).  


## FluidBuilder Methods
### Basic Properties

- **`displayName(name: TextComponent)`**  
  Sets the fluid's display name.

- **`rarity(value: string)`**  
  Sets fluid rarity. Values: 'common', 'uncommon', 'rare', 'epic'.

- **`color(color: int)`**  
  Sets the fluid color.

- **`bucketColor(color: int)`**  
  Sets the bucket color.

### Texture Methods

- **`thinTexture(color: int)`**  
  Creates water-like thin texture with the specified color.

- **`thickTexture(color: int)`**  
  Creates lava-like thick texture with the specified color.

- **`stillTexture(path: ResourceLocation)`**  
  Sets custom still texture path (e.g., 'kubejs:block/strawberry_still').

- **`flowingTexture(path: ResourceLocation)`**  
  Sets custom flowing texture path (e.g., 'kubejs:block/strawberry_flow').

- **`builtinTextures()`**  
  Uses built-in fluid textures.

### Fluid Behavior

- **`noBucket()`**  
  Prevents bucket item creation

- **`noBlock()`**  
  Prevents fluid block creation

- **`gaseous()`**  
  Makes fluid flow upward instead of downward. (Not a Mekanism gas!)

### Modded Properties
These can be used, but will only have an effect if a mod adds a purpose for them.

- **`luminosity(value: int)`**  
  Sets light level (default: 0).

- **`density(value: int)`**  
  Sets density (default: 1000).

- **`temperature(value: int)`**  
  Sets temperature (default: 300).

- **`viscosity(value: int)`**  
  Sets viscosity (default: 1000).

### Bucket Customization

- **`bucketItem`**  
  Allows chaining item builder methods to customize the bucket item.

## Texture Guidelines

### Still Texture
- Recommended size: 16x16 pixels
- For animated textures: 16x48 (3 frames), 16x80 (5 frames), or 16x240 (15 frames)
- Optimal frame counts: 3, 5, 15, 6, 10, or 30 for smooth animation 

### Flowing Texture
- Recommended size: 32x480 pixels with mcmeta animation
- Each frame should be 32x32 pixels
- Frames are vertically shifted by 1 pixel for flowing effect
- **Warning**: Creating flowing textures manually is not recommended due to complexity 

