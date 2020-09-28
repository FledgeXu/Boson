# 方块模型和材质

我们已经成功地创建了一个方块，但是这个方块还很丑，就是一个紫黑块而已。在这一节中，我们就要来为这个方块添加模型和材质。

首先我们创建一些文件夹，创建成功后的目录如下显示:

```
resources
├── META-INF
│   └── mods.toml
├── assets
│   └── boson
│       ├── blockstates
│       ├── lang
│       ├── models
│       │   ├── block
│       │   └── item
│       └── textures
│           ├── block
│           └── item
└── pack.mcmeta
```

然后我们在`blockstates`文件夹下，创建一个和你物品注册名同名的json文件，这里我们要创建`obsidian_block.json`

内容如下:

```json
{
  "variants": {
    "": { "model": "boson:block/obsidian_block_model" }
  }
}
```

这个文件其实就是方块状态和具体要使用的模型的映射表，如果你还不清楚什么是方块状态，请向前翻阅。

这里我们没有方块状态，所以写了`"": { "model": "boson:block/obsidian_block_model” }`，将默认模型设置成了`obsidian_block_model.json`。

接下来我们在`models/block`下创建`obsidian_block_model.json`，内容如下:

```json
{
  "parent": "block/cube_all",
  "textures": {
    "all": "boson:block/obsidian_block_texture"
  }
}
```

可以看到，和物品模型相比，只是继承的东西不太一样，至于具体模型文件的格式请参考[Wiki](https://minecraft-zh.gamepedia.com/index.php?title=模型&variant=zh)。在模型里，我们调用了`obsidian_block_texture.png`作为我们的材质。

接下来让我们在`textures/block`下添加我们的材质，同样地请注意材质文件的比例是1:1，并且最好不要大于32x32像素。

<img src="modelandtextures.assets/obsdian_block.png" alt="obsdian_block" style="zoom:300%;" />



这时启动游戏，你应该就可看见我们的方块有对应的材质和模型了。

![image-20200428181816541](modelandtextures.assets/image-20200428181816541.png)

**整个加载过程为：获取游戏中方块的状态，在Blockstates相对应的映射表里获取模型，根据模型加载材质。**

但这时你会发现我们方块相对应的物品还没有材质，接下来我们就要解决这个问题。

在`models/item`下创建和我们BlockItem注册名同名的json文件，这里是`obsidian_block.json`

```json
{
  "parent": "boson:block/obsidian_block_model"
}
```

对，你没看错，就一句话，我们直接继承相对应的方块模型就行了。

![image-20200428182406212](modelandtextures.assets/image-20200428182406212.png)
