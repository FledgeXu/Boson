# 音效

在这节中，我们讲学习如何向给我的Mod添加音效，非常的简单，就让我开始吧。

```java
public class SoundEventRegistry {
    public static final DeferredRegister<SoundEvent> SOUNDS = DeferredRegister.create(ForgeRegistries.SOUND_EVENTS, Utils.MOD_ID);
    public static final RegistryObject<SoundEvent> meaSound = SOUNDS.register("mea", () -> new SoundEvent(new ResourceLocation(Utils.MOD_ID, "mea")));
}
```

就这两句话，就可以注册我们的音效了。

接下来我们来添加音效，在你的`Resource`目录下创建如下的文件和目录。

```
resources
├── META-INF
│   └── mods.toml
├── assets
│   └── boson
│       ├── blockstates
│       ├── lang
│       ├── models
│       ├── sounds
│       ├── sounds.json
│       └── textures
├── data
└── pack.mcmeta
```

在这里我们创建了一个`sounds.json`，这个文件的具体格式请参照Wiki。

内容如下

```json
{
  "mea": {
    "subtitle": "mea",
    "replace": "true",
    "sounds": [
      {
        "name": "boson:mea",
        "stream": true
      }
    ]
  }
}
```

请注意，最外面的键名和你之前`ResourceLocation`中第二个参数应该是一样的。然后我们在`name`中指定了具体的声音文件。请注意，Minecraft只允许载入ogg格式的音频文件。

接下来，我们将制作好的音频文件放入`sounds`文件内。

```
resources
├── META-INF
│   └── mods.toml
├── assets
│   └── boson
│       ├── blockstates
│       ├── lang
│       ├── models
│       ├── sounds
│       │   └── mea.ogg
│       ├── sounds.json
│       └── textures
├── data
└── pack.mcmeta
```

放置好的目录如上。

接下来就可创建物品来使用我们的音效了

```java
public class SoundTestItem extends Item {
    public SoundTestItem() {
        super(new Properties().group(ModGroup.itemGroup));
    }

    @Override
    public ActionResult<ItemStack> onItemRightClick(World worldIn, PlayerEntity playerIn, Hand handIn) {
        if (worldIn.isRemote) {
            worldIn.playSound(playerIn, playerIn.getPosition(), SoundEventRegistry.meaSound.get(), SoundCategory.AMBIENT, 10f, 1f);
        }
        return super.onItemRightClick(worldIn, playerIn, handIn);
    }
}
```

打开游戏试试吧，我们应该成功添加了音效。

[源代码](https://github.com/FledgeXu/BosonSourceCode/tree/master/src/main/java/com/tutorial/boson/sounds)

