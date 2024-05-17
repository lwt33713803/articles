future 和 streams 关系

类似于 一个单一值 和 单一值迭代器的关系

dart ..称为级联操作符，可以实现对一个对象的连续调用,然后将原对象返回.

```
/// 初始化控制器
_controller = VideoPlayerController.network(list[0]['video_url'])
      ///设置视频循环播放
      ..setLooping(true)
      ///设置监听
      ..addListener(() {
        setState(() {
        });
      })
      ///初始化
      ..initialize().then((_) async {
        ///初始化完成更新状态,不然播放器不会播放
        setState(() {
          playOrPauseVideo();
        });
      }).catchError((err) {
        ///播放出错
        print(err);
      });
```

...用来拼接集合，如List，Map等

```
//这里组合后 list就变成[ 'a', 'b', 'c','d', 'e', 'f']
var list2 = ['d', 'e', 'f'];
var list = ['a', 'b', 'c', ...list2];
//这里组合后map就变成{'a': 'a', 'b': 'b','c': 'c', 'd': 'd'}
var map2 = {'a': 'a', 'b': 'b'};
var map = {...map2, 'c': 'c', 'd': 'd'};
```

在dart中的yield应用于方法或函数中，起到暂停的作用，加上关键字sync*后，则返回一个Iterior类型。而Iterior的 成员取决于方法内有多少个yield暂停.

```
group('keyword yield and sync* Test ', () {
    test('Length test', () {
      Iterable<String> getItems() sync* {
        yield 'hello';
        yield 'world';
      }

      print(getItems().length); // 2
    });
  });
```

使用迭代器相对于for遍历，可以有效去控制内存。

```
  test('Foreach Iterable', () {
    // 假设这是从文件中读取一个单词出来
    String readFromFile(int index) => ['hello', 'wolrd'][index];
    Iterable<String> getItems() sync* {
      for (var index = 0; index < 2; index++) {
        yield readFromFile(index);
      }
    }

    getItems().map(prints);
    // hello
    // world
  });
```

果把函数中的yield比作是一个元素的话，那么yield*就是把一个整个 元素集拿过来到这个函数中进行一个一个迭代。

yield* 迭代器嵌套的时候获取内部迭代器元素插入，相当于将另一个可迭代对象的所有元素插入到当前生成器函数的执行流中。

**yield**用于产生单个值，而**yield***用于将另一个可迭代对象的所有值插入到当前生成器函数的执行流中。

```
test('Test for sync* yield*', () {
    Iterable<String> getEmojiWithTime(int count) sync* {
      Iterable<String> getItems() sync* {
        yield 'hello';
        yield 'world';
      }

      yield* getItems();//
      yield '!';
    }

    print(getEmojiWithTime(10).join(' ')); // hello world !
  });
```

在一个函数内使用async*, yield和await，那么这个函数必然返回一个Stream<T>对象

简单来说，**FutureOr<T>** 表示一个可以是 **T** 类型的对象，也可以是一个异步返回 **T** 类型对象的 Future。

**sync 同步同步同步同步同步同步同步同步同步**

**sync***:

- **sync***用于定义同步生成器函数，它可以产生一个迭代器（Iterable），用来生成一系列的值。这些值在生成器函数的执行期间被惰性地生成。
- 在**sync***函数内部，使用**yield**关键字来产生序列中的每个值。每当执行到一个**yield**语句时，生成器函数会产生一个值，并暂停执行，直到下一个值被请求。
- **sync***函数可以被直接调用来获取一个迭代器，并且迭代器可以被用于遍历生成的值序列。

**sync**:

- **sync**关键字用于定义同步函数，与普通函数的行为类似，它们会立即执行并返回一个值，而不是生成一系列的值。
- **sync**函数不能包含**yield**关键字，因为它们不是生成器函数，不能产生多个值。它们的执行流程是同步的，不会暂停等待。

**sync***用于定义生成器函数，可以产生一系列的值，而**sync**用于定义普通的同步函数，只能返回单个值。

**async***:

- **async*** 用于定义异步生成器函数，它产生一个迭代器（AsyncGenerator），用来异步地生成一系列的值。这些值在生成器函数的执行期间被惰性地生成。
- 在 **async*** 函数内部，使用 **yield** 关键字来产生序列中的每个值。与 **sync*** 类似，每当执行到一个 **yield** 语句时，生成器函数会产生一个值，并暂停执行，直到下一个值被请求。
- **async*** 函数可以被直接调用来获取一个异步迭代器，并且异步迭代器可以被用于异步地遍历生成的值序列。

**async**:

- **async** 用于定义普通的异步函数，它返回一个 **Future** 对象，表示异步操作的结果。异步函数内部可以使用 **await** 关键字来等待其他异步操作的完成。
- 异步函数可以包含多个 **await** 表达式，这些表达式将导致异步函数的执行暂停，直到被等待的异步操作完成并返回结果。

**async*** 用于定义异步生成器函数，可以异步地生成一系列的值，而 **async** 用于定义普通的异步函数，返回一个 **Future** 对象表示异步操作的结果。

## async 和await 处理异步的关键字

在 Dart 中，构造函数的初始化列表使用冒号 **:** 开始，后面跟着初始化语句

```
 /// Creates a new streaming request.
  StreamedRequest(super.method, super.url)
      : _controller = StreamController<List<int>>(sync: true);
```

StreamController 可以为你生成一个 Stream，并提供在任何时候、任何地方将事件添加到该 Stream 的方法