
Dart 中的子节

[https://juejin.cn/post/6913851705561972744#heading-24](https://juejin.cn/post/6913851705561972744#heading-24)

二进制

```
00101010
```

这个8位字节有一个值，在这种情况下，值是42

在Dart中可以将任何二进制数据表示为一个整数列表

```
List<int> data = [102, 111, 114, 116, 121, 45, 116, 119, 111, 0];
```

在Dart中，int类型默认为64位值。这就是八个字节

这里是数字42，这次显示的是64位，供你参考

```
0000000000000000000000000000000000000000000000000000000000101010
```

Uint8List是一个整数的列表，列表中的值每个只有8位。值的范围从0到255

## 将List<int>转换为Uint8List

Uint8List是dart:typed_data的一部分，Dart的核心库之一。要使用它，请添加以下导入。

**import**'dart:typed_data';

使用 fromList 方法将之前的 List<int> 列表转换为 Uint8List

```
List<int> data = [102, 111, 114, 116, 121, 45, 116, 119, 111, 0];
Uint8List bytes = Uint8List.fromList(data);
```