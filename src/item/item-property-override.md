# 物品属性覆盖

物品属性覆盖（Item Property Override）是一个Minecraft原版的机制，用来实现比如弓箭根据不同的拉弓时间显示不同的弓的模型。我们用原版`bow.json`的模型文件，来解释一下什么是物品属性覆盖，更多的内容请参照原版的[WIKi](https://minecraft-zh.gamepedia.com/模型#.E7.89.A9.E5.93.81.E6.A8.A1.E5.9E.8B)中的「overrides」。

`bow.json`

```json
{ 
    ……省略内容……
    "overrides": [
        {
            "predicate": {
                "pulling": 1
            },
            "model": "item/bow_pulling_0"
        },
        {
            "predicate": {
                "pulling": 1,
                "pull": 0.65
            },
            "model": "item/bow_pulling_1"
        },
        {
            "predicate": {
                "pulling": 1,
                "pull": 0.9
            },
            "model": "item/bow_pulling_2"
        }
    ]
}
```

可以看到在`bow.json`中出现了一个叫做`overrides`的配置项，在这个配置项里出现了一对对的：

```json
{
  "predicate": {
    "pulling": 1
  },
  "model": "item/bow_pulling_0"
}
```

这里的内容非常容易理解。其中的`predicate`就是「条件」，当条件里的`predicate`（在游戏中定义会动态变化）的值大于等于1时，加载对应的模型，也就是`item/bow_pulling_0`。

而我们也正要实现一个类似的功能，在这节中我们要实现一个「魔法锭」会根据物品堆叠的数量动态的改变模型。

首先是物品，非常的简单。

```java
public class MagicIngot extends Item {
    public MagicIngot() {
        super(new Properties().group(ModGroup.itemGroup));
    }
}
```

注册物品：

```java
public static final RegistryObject<Item> magicIngot = ITEMS.register("magic_ingot", MagicIngot::new);
```

接下来就是重头戏，给我们创建的物品添加「Item Property Override」。

```java
@Mod.EventBusSubscriber(bus = Mod.EventBusSubscriber.Bus.MOD, value = Dist.CLIENT)
public class PropertyRegistry {
    @SubscribeEvent
    public static void propertyOverrideRegistry(FMLClientSetupEvent event) {
        event.enqueueWork(() -> {
            ItemModelsProperties.registerProperty(ItemRegistry.magicIngot.get(), new ResourceLocation(Utils.MOD_ID, "size"), (itemStack, clientWorld, livingEntity) -> itemStack.getCount());
        });
    }
}
```

可以看到，我们在`FMLClientSetupEvent`事件中，调用了`ItemModelsProperties.registerProperty`方法给我们的物品添加了一个`overrides`，这里的三个参数意义是，第一个是你要添加`overrides`的物品，第二个是这个`overrides`的名字，也就是你之后你要在模型文件的`predicate`里写的名字，第三个就是动态变化的值，这里我们直接调用了`itemStack.getCount()`函数返回了物品堆叠的数量。

接下去看我们的模型文件：`magic_ingot.json`：

```java
{
  "parent": "item/generated",
  "textures": {
    "layer0": "item/iron_ingot"
  },
  "overrides": [
    {
      "predicate": {
        "boson:size": 16
      },
      "model": "item/gold_ingot"
    }
  ]
}
```

可以看到这里我们设置了，在默认的时候显示铁锭的贴图，然后当物品的数量大于等于16之后显示金锭的贴图。

打开游戏你就能看见这个「魔法锭」了。

![image-20201009195911814](item-property-override.assets/image-20201009195911814.png)

![image-20201009195924247](item-property-override.assets/image-20201009195924247.png)

[源代码](https://github.com/FledgeXu/BosonSourceCode/tree/master/src/main/java/com/tutorial/boson/item_property_overrides)