
使用mac的应该都接触过 DMG，DMG 长的大致如下：

![](https://cdn.nlark.com/yuque/0/2024/png/97592/1715955030512-fa59c2d4-e904-40ad-b324-358d8b357833.png)

那么，应该如何创建一个DMG映像安装包呢？

首先我们分析下一个DMG文件主要需要什么：

1. 一个app主体 
2. 一个 application的快捷方式
3. 一个背景图
4. 一个网页(快捷链接)

##### APP主体，通过 flutter build Macos 构建即可
##### APPLICATION 快捷方式的话通过以下步骤创建：

- 在应用程序目录，右键制作替身即可。

制作DMG 映像，并打包相关文件：

这里主要用到的是Macos的磁盘工具。

- 打开**磁盘工具**
- 依次打开：
	- 文件-> 新建映像->空白映像

然后出现以下页面：

![](https://cdn.nlark.com/yuque/0/2024/png/97592/1715957457848-6c4e5a19-d221-4066-afd7-200a4cc1bc73.png)

主要修改下面的几个参数：
	大小：比app包大一点就好
	名称：app名称
	其他不做修改

#### 最后存储

映像创建完成，这时候还需要把我们的app 和相关文件打包进去。

还是在磁盘工具的页面上新建的映像上 右键：

![](https://cdn.nlark.com/yuque/0/2024/png/97592/1715957683554-71372b0e-d229-4a15-bcc9-98cf1e62bf0f.png)

点击这个在 访达中显示，会出现下面窗口：

![](https://cdn.nlark.com/yuque/0/2024/png/97592/1715957786486-d491e9c6-682b-489f-a39e-22680dc217c0.png)

然后把之前准备东西，拖到窗口中

1. APP本地
2. Application 快捷
3. 背景图

最后根据需求，调整下位置。
最后右键空白处，设置背景图即可。

![](https://cdn.nlark.com/yuque/0/2024/png/97592/1715959018599-4c8ffdab-9814-4e01-8781-f82b4b90d2b6.png)

这时候打包出来的较大，还需要进行压缩操作

具体流程：磁盘工具 -> 映像 -> 转换；

具体页面如下：

![](https://cdn.nlark.com/yuque/0/2024/png/97592/1715959125116-ffebd092-933b-4741-baae-ced18a7c8eef.png)