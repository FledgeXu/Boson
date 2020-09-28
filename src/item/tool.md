# 工具

在这节中，我们将学习如何添加一个新的工具，这里我们以一个新的稿子举例。

首先让我们观察一下继承树：

![image-20200928183235878](tool.assets/image-20200928183235878.png)

可以看到原版已经提供了4种工具对应的类，在这些工具之上还有一个`ToolItem`如果你想创建一个全新种类的工具，可以选择继承并实现这个类，实现的方法请参照原版的4个类。

接下来我们开始创建黑曜石稿子

```java
public class ObsidianPickaxe extends PickaxeItem {
    public ObsidianPickaxe() {
        super(ModItemTier.OBSIDIAN, 2, -3f, (new Item.Properties()).group(ModGroup.itemGroup));
    }
}
```

可以看到，在这里我们选择直接继承了`PickaxeItem`类，然后传入直接我们创建好的黑曜石的`ItemTier`，其余的参数大家请参考原版的实现。

然后就是注册

```java
public static final RegistryObject<Item> obsidianPickaxe = ITEMS.register("obsidian_pickaxe", ObsidianPickaxe::new);
```

添加材质和模型，这里就不再多加说明了。

打开游戏看看，你应该就能看到全新的黑曜石稿了。

![image-20200928183155484](tool.assets/image-20200928183155484.png)

[源代码地址](https://github.com/FledgeXu/BosonSourceCode/tree/master/src/main/java/com/tutorial/boson/tool)