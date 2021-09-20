# 食物

在这一节中我们将会在Minecraft世界中添加一个新的食物：黑曜石苹果，吃了这个苹果以后你可以回复饥饿，但是会中毒。和很多人想象的不一样，食物并不是单独的一个东西，对于Minecraft来说，食物只是一种特殊的物品而已。

同样的我们先来创建一个类，让这个类继承`Item`

```java
public class ObsidianApple extends Item {
    private static final Food food = (new Food.Builder())
            .saturation(10)
            .hunger(20)
            .effect(() -> new EffectInstance(Effects.POISON, 3 * 20, 1), 1)
            .build();

    public ObsidianApple() {
        super(new Properties().food(food).group(ItemGroup.FOOD));
    }
}
```

我们一行一行的解释。

我们创建了一个`Food`类型的变量，这个变量规定了这个这个食物的一些属性，比如：`saturation`方法设置了饱食度，`hunger`设置了回复的饥饿度，`effect`方法设置了吃食物时可能会有的药水效果，这里我们设置的效果是「中毒」，持续时间是60个Tick，药水等级一级。其中第二个参数代表触发效果的可能性（想想原版的生鸡肉），这里我们设置成`1`代表100%触发。

接下去就是构造方法，想必大家已经很熟悉了，唯一新的一点就是`.food(food)`，这个方法表明了物品是一个食物，最后我们把这个物品放在了「食物」创造模式物品栏里。

然后我们注册我们的食物，注册名是`obsidian_apple`:

```java
public class ItemRegistry {
    public static final DeferredRegister<Item> ITEMS = DeferredRegister.create(ForgeRegistries.ITEMS, Utils.MOD_ID);
    public static final RegistryObject<Item> obsidianApple = ITEMS.register("obsidian_apple", ObsidianApple::new);
}
```

然后添加模型`obsdian_apple.json`:

```json
{
  "parent": "item/generated",
  "textures": {
    "layer0": "boson:item/obsidian_apple"
  }
}
```

然后是材质`obsidian_apple.png`

<img src="food.assets/obsidian_apple.png" alt="obsidian_apple" style="zoom:300%;" />

拿出你的苹果试着吃吃看吧。

![image-20200427165949417](food.assets/image-20200427165949417.png)

[源代码](https://github.com/FledgeXu/BosonSourceCode/tree/master/src/main/java/com/tutorial/boson/food)

