FLUTTER 打包 （MACOS 的DMG 和 Window的exe）

准备工作：

使用mac的应该都接触过 DMG，DMG 长的大致如下：

![](https://cdn.nlark.com/yuque/0/2024/png/97592/1715955030512-fa59c2d4-e904-40ad-b324-358d8b357833.png)

其中包含：

1. 一个app主体
2. 一个 application的快捷方式
3. 一个背景图
4. 一个网页

- APP主体，通过 flutter build macos 构建即可
- 创建APPLICATION 快捷方式

- 在应用程序目录，右键制作替身即可。

- 下面是主要操作：

- 打开**磁盘工具** - 这是mac自带程序，程序坞里查找
- 依次：文件-> 新建映像->空白映像

然后出现以下页面：

大小：比app包大一点就好

名称：app名称

其他不做修改

![](https://cdn.nlark.com/yuque/0/2024/png/97592/1715957457848-6c4e5a19-d221-4066-afd7-200a4cc1bc73.png)

- 最后存储。

然后在磁盘工具新建的 映像上 右键，

![](https://cdn.nlark.com/yuque/0/2024/png/97592/1715957683554-71372b0e-d229-4a15-bcc9-98cf1e62bf0f.png)

点击这个在 访达中显示，会出现下面窗口：

![](https://cdn.nlark.com/yuque/0/2024/png/97592/1715957786486-d491e9c6-682b-489f-a39e-22680dc217c0.png)

然后把之前准备东西，拖到窗口中

1. APP本地
2. Application 快捷
3. 背景图

调整下站位，右键空白处，设置背景图即可。

![](https://cdn.nlark.com/yuque/0/2024/png/97592/1715959018599-4c8ffdab-9814-4e01-8781-f82b4b90d2b6.png)

这时候打包出来的较大，还需要进行压缩操作

具体流程：磁盘工具 -> 映像 -> 转换；

具体页面如下：

![](https://cdn.nlark.com/yuque/0/2024/png/97592/1715959125116-ffebd092-933b-4741-baae-ced18a7c8eef.png)