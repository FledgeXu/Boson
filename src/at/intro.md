# Access Transformer
由于MCP名和SRG名的存在，在开发时访问一些私有方法和私用字段，修改final变量等在权限上不允许的一些事情将会变得棘手（需要考虑两种变量名的转换）

考虑以下情景：
```java
public class AMinecraftClass  {
    private AnotherMinecraftClass field_114514_l;
    ...
}
```
在开发环境里看起来是这样的：
```java
public class AMinecraftClass  {
    private AnotherMinecraftClass instance;
    ...
}
```
现在有一个需求，如果在没有此变量的Getter时，需要访问instance变量怎么办？（或在没有此变量的Setter时，需要修改instance变量怎么办？）

能这样吗？
```java
...
AMinecraftClass aMinecraftClassInstance = ...;
Field field = AMinecraftClass.class.getField("instance");
field.setAccessible(true);
AnotherMinecraftClass instance = field.get(aMinecraftClassInstance);
...
```
这样是不行的。虽然这样在开发环境中起效，但由于在生产环境中名称会被混淆，所以在生产环境中在getField方法被执行时会抛出NoSuchFieldException。

所以Access Transformer（简称AT，下文统称AT）出现了。它允许扩大可见性并修改类、方法和字段的最终标志。它允许模组制作者访问和修改他们无法控制的类中其他无法访问的成员。
**AT不能将类、方法和字段的最终标志变得严格（比如public->protected）**

那我们开始吧。首先请打开你的开发环境中的`build.gradle`文件，然后在第一个`minecraft`块中添加一句如下代码：
```Groovy
accessTransformer = file('path/file')
//path和file处分别要填上你的AT的配置文件的名字和路径，file必须为cfg文件。
//开发环境中，path可以在任意位置，但在非开发环境中，Forge就只会寻找JAR中的META-INF/accesstransformer.cfg并将它当做AT的配置文件。
```
这句代码设置了你的AT的配置文件路径。

## AT配置文件的编写
它的每一行（除了注释，注释在前面加#号）看起来像这样
(**在 Minecraft类上使用Access Transformer时，字段和方法必须用SRG名。**)：
#### 对于变量来说：
##### ```<权限修饰符> <全限定类名> <变量名>```
#### 对于方法来说：
##### ```<权限修饰符> <全限定类名> <方法名>(方法的参数)<返回值>```
### 权限修饰符
权限修饰符格式如下：
```<Java权限修饰符>(+f或-f)```

其中Java权限修饰符应该不用解释，就是```public```、```protected```之类的（```default```是Java的默认修饰符）。

```+f或-f```是可选项，分别代表去除或添加此类、变量、方法的final标志。
### 全限定类名
外部类直接表示为外部类的完全限定名称。

内部类是通过将外部类的完全限定名称和内部类的名称与分隔符$组合来表示的。

例如：
```net.minecraft.block.AbstractBlock```

又或者
```net.minecraftforge.event.entity.living.LivingEvent$LivingJumpEvent```
### 类型描述符
所有的基本类型都由一个大写字母表示：
- ```B```代表```byte```类型
- ```C```代表```char```类型
- ```D```代表```double```类型
- ```F```代表```float```类型
- ```I```代表```int```类型
- ```J```代表```long```类型
- ```S```代表```short```类型
- ```V```代表```void```类型
- ```Z```代表```boolean```类型

引用类型由类型的完全限定名称（分隔符为'/'）前面加一个'L'并在最后加一个';'表示。

数组在类型前加中括号表示，前面有多少个中括号，数组就有多少维。
## 实例（来源于Forge）
```
# 使net.minecraft.client.gui.ScreenManager.IScreenFavtory变得public
public net.minecraft.client.gui.ScreenManager$IScreenFactory

# 使net.minecraft.server.MinecraftServer里面的random变量的权限变为protected，并且将此变量变成非final变量。
protected-f net.minecraft.server.MinecraftServer field_147146_q #random

# 使net.minecraft.util.Util中的func_240979_a_(LJava/lang/String;)Ljava/util/concurrent/ExecutorService方法转为公开,
# 此方法输入一个String类型参数，返回一个java.util.concurrent.ExecutorService。
public net.minecraft.util.Util func_240979_a_(Ljava/lang/String;)Ljava/util/concurrent/ExecutorService; #makeExecutor

# 使net.minecraft.util.UUIDCodec中的 func_239776_a_(JJ)[I方法转为公开,
# 此方法接受两个long类型参数，返回一个int数组
public net.minecraft.util.UUIDCodec func_239776_a_(JJ)[I #leastMostToIntArray```