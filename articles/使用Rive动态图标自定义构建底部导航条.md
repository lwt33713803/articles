### [原文地址](https://medium.com/flutter-community/build-a-custom-bottom-navigation-bar-in-flutter-with-animated-icons-from-rive-13651bc80629)

## 结合 Rive 动态图标库构建自定义底部导航条

#### 完成本文后的思考 - TODO

1. 与lottie动画比较利弊在哪里？

2. 扩展并实现一个其他动画。

   

今天我将用 Flutter 然后结合 Rive 的动态图标库构建一个动态底部导航条，具体效果预览图如下：

![效果图](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*rZh4nphSSS0QzBmGpK5JVA.gif)

> 作者也提供了视频版本，喜欢看视频的可以去：https://www.youtube.com/watch?v=jq-GVjzezBQ

### 项目初始化
**首先**我们需要初始化一个项目，具体细节不在赘述，**然后**创建一个 assets 目录用于存放我们后续用到的动画图标文件。

动画图标下载地址：[动态图标](https://rive.app/community/1298-2487-animated-icon-set-1-color/)

下载后将文件重命名为：animated-icons.riv 放到 assets 目录。然后在确保在 pubspec.yaml 引用了该文件。

**最后**下载 Rive 动画 [SDK](https://pub.dev/packages/rive)。

### RiveModel

创建一个 RiveModel class，这个 class 包含：`src`, `artboard`, 和 `stateMachineName`.

```dart
class RiveModel {
  final String src, artboard, stateMachineName;

  RiveModel({
    required this.src,
    required this.artboard,
    required this.stateMachineName,
  });
}
```

现在你可能还不明白这里的 `artboard`, 和 `stateMachineName`是做什么的？

没关系，现在让我们回到  [Rive图标](https://rive.app/community/1298-2487-animated-icon-set-1-color/) 然后通过 Remix（页面右侧）进入动画编辑界面，编辑页面如下：

![预览](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*qmyLQx6E0j2xz0vT1vnsHQ.png)

在编辑页面主窗口，你会看到每个图标左上角都会有一个自己的 名字，这个名字就是 artboard 的值，同时，每个 artboard 都会和一个状态机。当前选择的图标状态机名称为：TIMER_Interactivity，这个状态机有两个状态： `idle` and `active`，这些状态就是我们控制动画的关键。

然后我们创建一个变量，名为：bottomNavItems，用于记录底部导航栏所有项。

```dart
List<RiveModel> bottomNavItems = [
  RiveModel(
      src: "assets/animated-icons.riv",
      artboard: "CHAT",
      stateMachineName: "CHAT_Interactivity"),
  RiveModel(
      src: "assets/animated-icons.riv",
      artboard: "SEARCH",
      stateMachineName: "SEARCH_Interactivity"),
  RiveModel(
      src: "assets/animated-icons.riv",
      artboard: "TIMER",
      stateMachineName: "TIMER_Interactivity"),
  RiveModel(
      src: "assets/animated-icons.riv",
      artboard: "BELL",
      stateMachineName: "BELL_Interactivity"),
  RiveModel(
      src: "assets/animated-icons.riv",
      artboard: "USER",
      stateMachineName: "USER_Interactivity"),
];
```

下面我们将开始补觉有意思的部分了。

### 底部导航栏

在 main.dart 中创建 BottonNavWithAnimatedIcons 

```dart
import 'package:flutter/material.dart';

const Color bottonNavBgColor = Color(0xFF17203A);

class BottonNavWithAnimatedIcons extends StatefulWidget {
  const BottonNavWithAnimatedIcons({super.key});

  @override
  State<BottonNavWithAnimatedIcons> createState() =>
      _BottonNavWithAnimatedIconsState();
}

class _BottonNavWithAnimatedIconsState
    extends State<BottonNavWithAnimatedIcons> {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      // TODO: Botton Nva Bar
    );
  }
}
```

这里我门不会使用系统的 BottomNavigationBar 组件，所以我们使用下面代码替换 上面的  ``` // TODO: Botton Nva Bar ```

```dart
bottomNavigationBar: SafeArea(
  child: Container(
    height: 56, //TODO: In Future remove the height
    padding: const EdgeInsets.all(12),
    margin: const EdgeInsets.symmetric(horizontal: 24),
    decoration: BoxDecoration(
      color: bottonNavBgColor.withOpacity(0.8),
      borderRadius: const BorderRadius.all(Radius.circular(24)),
      boxShadow: [
        BoxShadow(
          color: bottonNavBgColor.withOpacity(0.3),
          offset: const Offset(0, 20),
          blurRadius: 20,
        ),
      ],
    ),
    // TODO: Animated Icons,
  ),
```

现在你可以看到下面效果了

![预览](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*DoRd5pgrULCexU6lZ3TknQ.png)

有了背景，下面我们要做的就是把图标放上。这里我们使用的是 Row 组件，并通过 List.generate 来构建 Row 组件的children的值。图标的宽高我们统一设置为 36。通过 RiveAnimation.asset 来获取图标。然后用以下代码替换上面的 ```  // TODO: Animated Icons ```

```dart
child: Row(
  mainAxisAlignment: MainAxisAlignment.spaceBetween,
  children: List.generate(
    bottomNavItems.length,
    (index) => SizedBox(
      height: 36,
      width: 36,
      child: RiveAnimation.asset(
        bottomNavItems[index].src,
        // TODO: Mention Artboard
      ),
    ),
  ),
),
```

现在你可以看到下面的效果：

![预览](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*lvLoAj1g9Ma5LfgP5duXqw.png)

出现上面原因不要慌，这是因为 animated-icons.riv 包含了所有的图标，这里我们需要手动指定下,用以下代码替换上面的```// TODO: Mention Artboard```

```dart
artboard: bottomNavItems[index].artboard
```

现在正常了：

![预览](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*P0VKqYq4fFsQLRISt5bLaQ.png)

### 控制动画

这时候你会发现动画现在并不受控制，为了让动画收到我们的控制，我们需要一个控制器。我们创建一个 riveOnInIt 函数，我们创建一个 StateMachineController，//todo

当控制器设置完成，你可能会想知道我们要通过什么来控制动画的播放与否。这里我们再次回到 [Rive编辑器](https://rive.app/community/1298-2487-animated-icon-set-1-color/) ，在 input 的 tab下，你会看到一个名字为 active 选中框，当设置 active 为true的时候，动画就会被触发。这就是我们用来控制动画的方法。Rive 提供了三种input 类型，`Numbers`, `Boolean`, 和 `Trigger`.

Rive sdk Controller 提供了 findInput 方法，该方法用于通过 name 查找相关的 input 信息。具体代码如下：

```dart
void riveOnInIt(Artboard artboard, {required String stateMachineName}) {
    StateMachineController? controller =
        StateMachineController.fromArtboard(artboard, stateMachineName);

    artboard.addController(controller!);
    controllers.add(controller);

    riveIconInputs.add(controller.findInput<bool>('active') as SMIBool);
  }
```

这时候你的代码会出现一些错误，那时因为我们还需要定义  `controllers` and `riveIconInputs`. 

```dart
List<SMIBool> riveIconInputs = [];
List<StateMachineController?> controllers = [];
int selctedNavIndex = 0;
```

在这里，我门存储的 controllers 这是为了在不用的时候可以注销。

selectedNavIndex 是用来存储当前选中的项id，以此用来确定播放那个动画。

回到 RiveAnimation ，在 onInit 调用 riveOnInIt，并传入需要的参数。

```dart
onInit: (artboard) {
  riveOnInIt(artboard, stateMachineName: riveIcon.stateMachineName);
},
```

### 触发动画

最后我们需要定义一下触发动画方式，这里我们用 GestureDetector，当item 被点击 的时候，我们通过将 input 设置为 true 来触发动画，动画执行大约1秒钟，我们需要在执行完成后，用 Future.delayed 来结束动画播放。代码如下：

```dart
GestureDetector(
  onTap: () {
    riveIconInputs[index].change(true);//这里记录了所有的 input ，通过相应的下标，我们就可以设置相应的 true和false
    Future.delayed(//设置延迟1s 后将input 重新设置为false
      const Duration(seconds: 1),
      () {
        riveIconInputs[index].change(false);
      },
    );
    setState(() {
      selctedNavIndex = index;//设置状态，触发页面重新构建
    });
  },
  child: SizedBox( .... ),
)
```

### 选中标识

当然选中的时候我们需要标识下哪一个被选中，下面就是选中后显示的状态标识：

```dart
class AnimatedBar extends StatelessWidget {
  const AnimatedBar({
    super.key,
    required this.isActive,
  });

  final bool isActive;

  @override
  Widget build(BuildContext context) {
    return AnimatedContainer(
      duration: const Duration(milliseconds: 200),
      margin: const EdgeInsets.only(bottom: 2),
      height: 4,
      width: isActive ? 20 : 0,
      decoration: const BoxDecoration(
        color: Color(0xFF81B4FF),
        borderRadius: BorderRadius.all(Radius.circular(12)),
      ),
    );
  }
}
```

最后，修改  List.generate 的child，即每项的child，将选中标识置于icon上方，通过 selctedNavIndex 的值判断 AnimatedBar的显示与否，这样所有代码就完成了。

### 在文章最后完整代码如下：（相比原文代码有所修改，直接复制该代码到main即可运行）

我的环境如下：通过 iphone15 模拟器运行。

```
flutter doctor
Doctor summary (to see all details, run flutter doctor -v):
[✓] Flutter (Channel stable, 3.16.7, on macOS 14.2.1 23C71 darwin-arm64, locale zh-Hans-CN)
[✓] Android toolchain - develop for Android devices (Android SDK version 34.0.0)
[✓] Xcode - develop for iOS and macOS (Xcode 15.0)
[✓] Chrome - develop for the web
[✓] Android Studio (version 2022.3)
[✓] VS Code (version 1.85.1)
[✓] Connected device (4 available)
[✓] Network resources

• No issues found!
```



```dart
import 'package:demo_bottom_button/RiveModel.dart';
import 'package:flutter/material.dart';
import 'package:rive/rive.dart';

void main() {
  runApp(const MyApp());
}

const Color bottomNavBgColor = Color(0xFF17203A);

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return const BottomNavWithAnimatedIcons();
  }
}

class BottomNavWithAnimatedIcons extends StatefulWidget {
  const BottomNavWithAnimatedIcons({super.key});

  @override
  State<BottomNavWithAnimatedIcons> createState() =>
      _BottomNavWithAnimatedIcons();
}

class _BottomNavWithAnimatedIcons extends State<BottomNavWithAnimatedIcons> {
  List<SMIBool> riveIconInputs = [];
  List<StateMachineController?> controllers = [];
  int selectedNavIndex = 0;
  List<String> pages = ["Chat", "Search", "History", "Notification", "Profile"];

  List<RiveModel> bottomNavItems = [
    RiveModel(
        src: "assets/animated-icons.riv",
        artboard: "CHAT",
        stateMachineName: "CHAT_Interactivity"),
    RiveModel(
        src: "assets/animated-icons.riv",
        artboard: "SEARCH",
        stateMachineName: "SEARCH_Interactivity"),
    RiveModel(
        src: "assets/animated-icons.riv",
        artboard: "TIMER",
        stateMachineName: "TIMER_Interactivity"),
    RiveModel(
        src: "assets/animated-icons.riv",
        artboard: "BELL",
        stateMachineName: "BELL_Interactivity"),
    RiveModel(
        src: "assets/animated-icons.riv",
        artboard: "USER",
        stateMachineName: "USER_Interactivity"),
  ];

  void riveOnInIt(Artboard artBoard, {required String stateMachineName}) {
    StateMachineController? controller =
        StateMachineController.fromArtboard(artBoard, stateMachineName);
    artBoard.addController(controller!);
    controllers.add(controller);
    riveIconInputs.add(controller.findInput<bool>('active') as SMIBool);
  }

  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(
        colorScheme: ColorScheme.fromSeed(seedColor: Colors.deepPurple),
        useMaterial3: true,
      ),
      home: Scaffold(
        appBar: AppBar(
          title: const Text("DEMO"),
        ),
        body: Center(
          child: Text(pages[selectedNavIndex]),
        ),
        bottomNavigationBar: SafeArea(
          child: Container(
            height: 66,
            padding: const EdgeInsets.all(12),
            margin: const EdgeInsets.symmetric(horizontal: 24),
            decoration: BoxDecoration(
                color: bottomNavBgColor.withOpacity(0.8),
                borderRadius: const BorderRadius.all(Radius.circular(14)),
                boxShadow: [
                  BoxShadow(
                      color: bottomNavBgColor.withOpacity(0.3),
                      offset: const Offset(0, 20),
                      blurRadius: 20)
                ]),
            child: Row(
              mainAxisAlignment: MainAxisAlignment.spaceBetween,
              children: List.generate(bottomNavItems.length, (index) {
                final riveIcon = bottomNavItems[index];
                return GestureDetector(
                  onTap: () {
                    riveIconInputs[index].change(true);
                    Future.delayed(
                      const Duration(seconds: 1),
                          () {
                        riveIconInputs[index].change(false);
                      },
                    );
                    setState(() {
                      selectedNavIndex = index;
                    });
                  },
                  child: Column(
                    children: [
                      AnimatedBar(isActive: selectedNavIndex == index),
                      SizedBox(
                        height: 36,
                        width: 36,
                        child: Opacity(
                          opacity: selectedNavIndex == index ? 1 : 0.5,
                          child: RiveAnimation.asset(
                            riveIcon.src,
                            artboard: riveIcon.artboard,
                            onInit: (artBoard) {
                              riveOnInIt(artBoard,
                                  stateMachineName: riveIcon.stateMachineName);
                            },
                          ),
                        ),
                      ),
                    ],
                  ),
                );
              }),
            ),
          ),
        ),
      ),
    );
  }
}

class AnimatedBar extends StatelessWidget {
  const AnimatedBar({super.key, required this.isActive});

  final bool isActive;

  @override
  Widget build(BuildContext context) {
    return AnimatedContainer(
      duration: const Duration(microseconds: 200),
      margin: const EdgeInsets.only(bottom: 2),
      height: 4,
      width: isActive ? 20 : 0,
      decoration: const BoxDecoration(
        color: Color(0xFF81B4FF),
        borderRadius: BorderRadius.all(Radius.circular(12)),
      ),
    );
  }
}
```

