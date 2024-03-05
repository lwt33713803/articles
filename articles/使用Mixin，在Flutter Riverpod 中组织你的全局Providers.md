## [原文地址](https://medium.com/@ximya/organize-your-global-providers-in-flutter-riverpod-with-mixin-class-562ae2aa3376)

![预览](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*eokEijh_W2G0BLU0l828QQ.png)

你喜欢 Riverpord吗？

在最近的新项目中，我已经开始使用 Riverpod 扩展作为我的状态管理工具了。在此之前，我主要使用Provider 和 Getx 作为我的状态管理工具，我对 Riverpod 并不熟悉。然而我却对Flutter 最近热衷的 Riverpord 产生了兴趣。我做了个大胆的决定，在我的项目中使用 Riverpord。

慢慢的，我开开始喜欢 Riverpord。

当我在项目中着手使用 Riverpord 的时候，扩展包的*反应机制*以及基础功能基本符合我的需求。但是有一方面的体验让我感到不太能接受。

> 那就是状态管理是依附全局的

我并不是说全局变量不好。由于提供程序的状态是在 ProviderContainer 中管理的，所以它是全局的。

![预览](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*SBc0teAOKPu7PPH0VlQsmg.png)

就像Riverpod 官方文档说的那样，全局声明具有不可变性，所以他不会干扰应用程序的生命周期，当然也不会在编写测试代码的时候有什么问题。但是这样做也会存在一些问题，由于只全局的特性，导致任何页面都可以使用，从而导致了无法确定哪些页面使用了这些程序。

关于Riverpod全局声明挑选了几个 Issues 来说明下这种方式会带来什么样的问题；

需求1: 假如你的项目在使用 Riverpod，然后接到一下需求：

> 请根据主页上使用的 Providers 编写单元测试代码

如果你是刚加入这个项目，了解一个页面上使用了哪些状态和事件将会是个非常耗时的事情。

再者，如果你的项目设计多人团队协作，你不了解这个页面上具体提供了哪些程序，或者不清楚这些程序涵盖哪些范围，原本简单的需求，到了你这里可能变得复杂，再就是容易产生代码冗余。最终导致项目可维护性降低。

既然知道问题在哪里，解决它就好了，那就是明确 privide 的覆盖范围。



<iframe width="680" height="382" src="https://www.youtube.com/embed/gHbkIgDnDyg" title="The Riverpod &quot;Global&quot; Myth" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>



在上面视频中，介绍了一些既可以消除全局声明变量存在的一些问题，有可以明确的知道程序所覆盖的范围。

本文着重介绍两种方法

1. 本地变量
2. 类静态本地变量



#### 本地变量的方式

利弊：可以私有化，但是复用会相对复杂

```dart
final _uncompletedTodosCount = Provider<int>((ref) {  
  return ref.watch(todoListProvider).where((todo) => !todo.completed).length;  
});

part 'tool_bar3.dart'; // <- Separated into a part file

class Toolbar extends HookConsumerWidget {  
  const Toolbar({  
    Key? key,  
  }) : super(key: key);  
  
  @override  
  Widget build(BuildContext context, WidgetRef ref) {  
    return Material(  
      child: Row(  
        mainAxisAlignment: MainAxisAlignment.spaceBetween,  
        children: [  
          Text(  
            '${ref.watch(_uncompletedTodosCount)}', // <- Accessing the localized provider
          ),
         ...
         
      }
  }
```



### 类静态本地变量

```dart
abstract class HomeProviders {  
  HomeProviders._();  
  
  static final todoListFilter = StateProvider((_) => TodoListFilter.all);  
  
  static final uncompletedTodosCount = Provider<int>((ref) {  
    return ref.watch(todoListProvider).where((todo) => !todo.completed).length;  
  });

  ...
}
```

```dart
class Toolbar extends ConsumwerWidget {  
  const Toolbar({  
    Key? key,  
  }) : super(key: key);  
  
  @override  
  Widget build(BuildContext context, WidgetRef ref) {  
    return Material(  
      child: Row(  
        mainAxisAlignment: MainAxisAlignment.spaceBetween,  
        children: [  
          Text(  
            '${ref.watch(HomeProviders.uncompletedTodosCount)}', 
            // Accessing the provider through a static variable
          ),
         ...
         
      }
  }
```



### 结合 MIXIN使用

1. 状态 MIXIN

   这是混入类，提供了相关状态

   ```dart
   mixin class HomeState {  
     int uncompletedTodosCount(WidgetRef ref) => ref.watch(uncompletedTodosCountProvider);  
     
     List<Todo> filteredTodos(WidgetRef ref) => ref.watch(filteredTodosProvider);  
     ## 异步
     AsyncValue<Todo> todoAsync(WidgetRef ref) => ref.watch(todoProvider);
     
     ...
   }
   ```

   混入类 需要一个传入 **WidgetRef**，婚礼如雷本身并不存储 **WidgetRef**

2. 使用

   ![预览](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*NnDzP2etdA4iunhpg6owUA.png)

事件的混入,同样的，事件需要一个 WidgetRef 作为参数

```dart
mixin class HomeEvent {  
  void addTodo(  
    WidgetRef ref, {  
    required TextEditingController textEditingController,  
    required String value,  
  }) {  
    ref.read(todoListProvider.notifier).add(value);  
    textEditingController.clear();  
  }  

  void requestTextFieldsFocus(  
    {required FocusNode textFieldFocusNode,  
    required FocusNode itemFocusNode}) {  
  itemFocusNode.requestFocus();  
  textFieldFocusNode.requestFocus();  
  }
...

}
```

使用：

![预览](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*DBjy5ftgPwmzEKHFsTP61Q.png)

### 核心概念

![预览](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*Yief4VuybZWmY3Kvv5QaBw.png)

将原先比较无序的状态依赖变的统一

这就相当于为状态提供了统一的get 和 set方法。
