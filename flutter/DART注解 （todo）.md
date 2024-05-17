

**注解**也称**元数据**（**metadata**），用于在代码中添加的用于描述代码的额外的信息。

可以应用于**类**、**方法**、**属性**、**构造函数**、**函数参数**等。

它可以为代码提供更多的语义信息，使代码更加清晰易懂。

作用：一种用于在 **编译期** 或 **运行时** 获取有关程序结构的附加信息的方法，从而可以实现更高级的编程技巧，例如自动序列化和反序列化、依赖注入等。

元数据可以在 `library`、`class`、`typedef`、`type parameter`、 `constructor`、`factory`、`function`、`field`、`parameter` 或者 `variable` 声明之前使用，也可以在 `import` 或 `export` 之前使用。可使用反射在运行时获取元数据信息。

  

作者：tangbl93  
链接：https://juejin.cn/post/6962347092782104606  
来源：稀土掘金  
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

常用的注解：

目前Dart语言中内置注解一共三个

1. @override 重写
2. @Deprecated 自定义消息和过时时间的过时注解
3. @deprecated API已经过时，不建议使用

**再就是三方模块也会提供的一些注解：**

比如：@required 参数必须

@immutable 用于标记一个 **类** 为不可变

具体使用和作用还需要结合三方文档

### 自定义注解：

1. 创建一个类，并继承自Object

1. 类名通常以Annotation为后缀

2. 创建一个带有const构造函数的类

1. 类可以有任意数量的属性，但它们必须是编译时常量。

具体逻辑代码还需要根据场景来书写，这只是最基础的注解创建

```
class DesAnnotation {
  final String description;
  const DesAnnotation(this.description);
}
```

这就完了....

注解创建完成了，下面看看他的用法：

```
// 使用自定义注解注解类
@DesAnnotation('描述信息')
class MyClass {
  // ...
}
// 使用自定义注解注解函数
@MyAnnotation('描述信息')
void myFunction() {
  // ...
}
// 使用自定义元素据注解变量
@MyAnnotation('描述信息')
int myVariable;
```

注解使用除了设置外，还可以在代码中获取：

需要借助：反射（dart:mirrors库）或代码生成工具（如source_gen库）。

```
import 'dart:mirrors';

void main() {
  // 获取上面代码中 MyClass 的ClassMirror
  ClassMirror classMirror = reflectClass(MyClass);

  // 遍历MyClass的元数据
  for (var metadata in classMirror.metadata) {
    // 检查元数据是否为 DesAnnotation 类型
    if (metadata.reflectee is DesAnnotation) {
      // 获取 DesAnnotation 实例
      DesAnnotation annotation = metadata.reflectee;

      // 输出注解的描述
      print('MyClass annotation: ${annotation.description}');
    }
  }
}
```

由于dart:mirrors库在Flutter中不受支持，因此在Flutter应用程序中需要使用代码生成工具（如source_gen库）来读取和处理自定义注解。

注意的点：

- 注解必须是常量表达式

- 注解不能直接访问被注解对象的属性或方法

- 注解不会影响程序的执行

应用场景：

TODO