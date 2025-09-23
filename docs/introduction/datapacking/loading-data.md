---
title: Loading Data with KubeJS
---

# Loading Data with KubeJS
You can also use KubeJS to load assets from resource packs and data from datapacks!

---

## Loading Assets

[Resource pack](https://minecraft.wiki/w/Resource_pack) assets (custom textures, language files, etc.) can be placed in `kubejs/assets` to be loaded. This folder is loaded the same way as the `assets` folder in a resource pack. Assets in this folder will not appear as a separate resource pack and will be automatically loaded for every world.

If you already have a resource pack, you can simply copy the folder(s) from inside the resource pack’s `assets` folder into KubeJS’s `assets` folder.

## Loading Data

!!! note
    Consider looking into [JsonIO](jsonio.md) if you have to generate a generate a large amount of ([nonrecipe](custom-recipes.md)) datapack files, rather than manually writing them.

Datapack data can be placed in `kubejs/data` to be loaded. Data within this folder will not appear as a separate datapack. It is loaded the same way as the `data` folder in a datapack and will be automatically loaded for every world.

If you already have a datapack, you can simply copy the folder(s) from inside the datapack’s `data` folder into KubeJS’s `data` folder.


