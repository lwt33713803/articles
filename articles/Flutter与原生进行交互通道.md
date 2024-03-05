### [原文地址](https://medium.com/codingmountain-blog/flutter-platform-channels-6e78c2fc75dc)

## Flutter 与原生代码进行交互

#### 完成本文后的思考 



![预览](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*xDEQ2BvXaEutx6ey6kssJA.png)

Flutter 对于构建跨端应用来说是非常好的解决方案。用Flutter你可以很轻松的构建漂亮的应用。但是为了使用原生api，你需要与本地平台进行通信。

但是Flutter 只能编译Dart代码，相应的，原生APP只能编译原生代码。所以Flutter和原生之间该如何通信呢？

这里卖个小关子，猜猜看

![预览](https://miro.medium.com/v2/resize:fit:1400/format:webp/0*1WIV4lK5gQNs_-09)

如果你猜是二进制，那么恭喜你猜对了。

Flutter通过二进制消息与其他平台进行交互。为了区分这些平台消息，便引进了通道的概念。那么通道是怎么在两种语言之间发送二进制数据的呢？

这里我们可以使用 BinaryMessenger 包装数据，然后将数据在两种语言之间传递。

>BinaryMessenger 类 是Flutter团队提供的工具类，用于与和原生语言的交互，该类还为传入信息注册来了相应的处理程序.

下面我们看一下具体的交互流程：

![预览](https://miro.medium.com/v2/resize:fit:1400/format:webp/0*zrs5Z-yYTqF7_a0V)

现在我们通过代码演示下具体的使用方式： 

```dart
import 'dart:convert'; 
import 'package:flutter/services.dart';
import 'dart:ui' as ui; 
class CustomBinaryMessenger {
  // 一个静态方法，用于发送二进制数据
  static Future<void> givenValue(String data) async {
    //创建一个存储二进数据的变量.
    final WriteBuffer buffer = WriteBuffer();
    // 将给定数据转换为utf-8字节.
    final List<int> utf8Bytes = utf8.encode(data);
    // 将unt8字节转换为 Uint8List
    final Uint8List utf8Int8List = Uint8List.fromList(utf8Bytes);
    //将 Uint8List 存入变量 buffer.
    buffer.putUint8List(utf8Int8List);
    // 获的要传递的最终数据.
    final ByteData message = buffer.done();
    // 通过message 类将数据通过 foo 通道进行发送.
    await Messenger().send('foo', message);
    return;
  }
}
// 一个实现了 BinaryMessenger 自定义 messenger 类 
// I am only handling send here for the sake of example
class Messenger implements BinaryMessenger {
  @override
  // 处理传入的平台消息，本例子会抛出一个不支持的错误.
  Future<void> handlePlatformMessage(
      String channel, ByteData? data, PlatformMessageResponseCallback? callback) {
    throw UnsupportedError("This platform message handling is not supported.");
  }
  @override
  // 使用 'ui.PlatformDispatcher'向平台发送一个二进制数据消息
  Future<ByteData?>? send(String channel, ByteData? message) {
    // 除了发送数据，这里还定了一个回掉
    ui.PlatformDispatcher.instance.sendPlatformMessage(channel, message, (data) {});
    return null;
  }
  @override
  // 设置一额个接受消息的处理函数，本例子本例子会抛出一个不支持的错误.
  void setMessageHandler(String channel, MessageHandler? handler) {
    throw UnsupportedError("Setting message handler is not supported.");
  }
}
```

### 下面是安卓端代码

```kotlin
class MainActivity : FlutterActivity() {
 override fun configureFlutterEngine(flutterEngine: FlutterEngine) {
        super.configureFlutterEngine(flutterEngine)
        // Configure the binary messenger to handle messages from Flutter.
        flutterEngine.dartExecutor.binaryMessenger.setMessageHandler("foo") { message, reply ->
        message?.order(ByteOrder.nativeOrder()) // Ensure proper byte order.
        val data = decodeUtf8String(message!!) // Decode the binary data to UTF-8 string.
        val x = message.toString() // Convert the message to a string for demonstration.
        // Display a Toast with the received message.
        Toast.makeText(this, "Received message from Flutter: $data", Toast.LENGTH_SHORT).show()
        reply.reply(null)
        }
        // Call the super method to finalize the FlutterEngine configuration.
        super.configureFlutterEngine(flutterEngine)
    }
    // Function to decode a ByteBuffer into a UTF-8 string.
    private fun decodeUtf8String(byteBuffer: ByteBuffer): String {
        return try {
            val byteArray = ByteArray(byteBuffer.remaining())
            byteBuffer.get(byteArray)
            String(byteArray, Charsets.UTF_8)
        } catch (e: Exception) {
            e.printStackTrace()
            ""
        }
    }
}
```

### 下面是 ios端代码

```swift
import UIKit
import Flutter
@UIApplicationMain
@objc class AppDelegate: FlutterAppDelegate {
  override func application(
    _ application: UIApplication,
    didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?
  ) -> Bool {
      let flutterViewController = self.window.rootViewController as! FlutterViewController
              
      // Configure the binary messenger to handle messages from Flutter.
      let binaryMessenger = flutterViewController.engine!.binaryMessenger
      binaryMessenger.setMessageHandlerOnChannel("foo", binaryMessageHandler: { [weak self] message, reply in
          // Ensure proper byte order.
           guard let message = message else {
               reply(nil)
               return
           }
           // Decode the binary data to UTF-8 string.
           if let data = String(data: message, encoding: .utf8) {
               let x = message.debugDescription // Convert the message to a string for demonstration.
               // Display an alert with the received message.
               let alertController = UIAlertController(
                   title: "Message from Flutter",
                   message: data,
                   preferredStyle: .alert
               )
               alertController.addAction(UIAlertAction(title: "OK", style: .default, handler: nil))
               flutterViewController.present(alertController, animated: true, completion: nil)
           }
           reply(nil)
      }
    )
      GeneratedPluginRegistrant.register(with: self)
    return super.application(application, didFinishLaunchingWithOptions: launchOptions)
  }
    // Function to decode a ByteBuffer into a UTF-8 string.
    private func decodeUtf8String(byteBuffer: FlutterStandardTypedData) -> String? {
            let byteArray = [UInt8](byteBuffer.data)
            return  String(bytes: byteArray, encoding: .utf8)
    }
}

```



## 效果展示

![预览](https://miro.medium.com/v2/resize:fit:1200/format:webp/1*NoZUZ3qlqYoIdC3Y-glFgQ.gif)



交互是双向的，你也可以将消息从平台传回 flutter.只要确保你实现了 Messenger的 setMessageHandler 方法，该方法用于解码本地平台回穿的数据。

安卓平台中使用：

```kotlin
flutterEngine.dartExecutor.binaryMessenger.send()
```

ios平台中使用：

```swift
flutterViewController.engine!.binaryMessenger.send(onChannel: <#T##String#>, message: <#T##Data?#>)
```



> 消息的传递是异步的，请确保用户界面会保持响应。

现在你可以随便在两者之前传递消息了。目前而言，虽然做到了消息传递，不用关系编码解码等一系列问题，但是就现在而言，代码还是会过于冗长，复杂性可维护性较差，我们应该怎么进一步优化呢？

平台通道，通道概念相信做过程序的都有所了解。他是一种封装，即你将 utf8 消息丢入通道，他会返回相应的二进制消息，反之依然，他对外隐藏了处理过程，你只需要知道相关通道能达到何种效果即可，方便简化了调用，减少了开发者负担。

> MethonChannel 系统通道之一，看他的代码你会发现他是如何处理相关数据的，每个通道只针对其对应的格式数据。



### 下面是作者的后续文章，原文分为两篇文章，这里合并为一篇处理。[介绍下 Flutter 平台的可用通道](https://medium.com/codingmountain-blog/flutter-platform-channels-6e78c2fc75dc)



上面篇幅介绍了Flutter 是如何与本地平台进行交互的，介绍了系统通道的概念，下面我们主要了解下系统提供了那些可用通道：

> Todo
>
> *Platform Channel is the construct that combines a channel name and a codec.For example, if we look at the* [*MethodChannel*](https://github.com/flutter/flutter/blob/12fccda598/packages/flutter/lib/src/services/platform_channel.dart#L253) *code, we can see it combining the codec and the channel name.*

**MessageCodec** 是用于消息解码/编码的一种描述，可以理解为一种描述类型，他定义了同种类型在不同语言之间的格式，它用于Flutter 与 本地代码消息的序列化反序列化，Flutter 本身提供了四种MessageCodec可用，他们分别是：

1. StringCodec 顾名思义处理字符串消息的 StringCodec
2. BinaryCodec  使用 ByteData 表示未编码的二进制信息。如果不需要编码/解码，就可以使用它。
3. JsonMessage：Json字符串消息的处理
4. StandardMessageCodec 顾名思义处理字符串的 StringCodec

> 你也可以定义自己的 MessageCodec实现，不过这需要在 Dart，安卓，ios 三种语言都实现才可以实现消息的交互。

![预览](https://miro.medium.com/v2/resize:fit:1160/format:webp/1*qwvnLdUWK2EMx28vnntU6w.png)

### 平台通道

Flutter中有三种通道类型可用，分别是：

1. **MethodChannel**

   方法调用的通道，用于 Flutter 和 平台之间方法的调用和值的返回

2. **EventChannel**

   事件通道，当你想从本地平台向 flutter 连续流数据时，就会用到它。它建立了从本机代码到 Dart 的单向通道，允许本机代码向 Dart 异步发送事件或数据流。如果您想接收连续数据，如传感器数据、实时更新、Wi-Fi 连接状态等，它将很有帮助。它用于从平台向 Flutter 传输数据流。

3. **BasicMessageChannel**

   它设计用于在 Dart 和本地代码之间双向收发异步消息。它可用于连续的双向通信。

相关的定义我们了解了。下面我们看看如何使用吧～



#### **MethodChannel**

它是一个命名通道，用于使用异步方法调用与 Flutter 应用程序通信。

Main.dart的 点击事件

```dart
      print(_counter);
      Future<int> new_count =
      MethodChannelCounter.increment(counterValue: _counter);
      print("102");
      var a = await new_count.then((value) => value);
      setState(() {
        _counter = a;
      });
```



Dart代码

```dart
import 'package:flutter/services.dart';
class MethodChannelCounter {
  // 创建一个名为 "methodChannelDemo" 的通道
  static MethodChannel methodChannel = const MethodChannel('methodChannelDemo');

  //在平台端定义一个自增方法，用于增加count的值并返回
  static Future<int> increment({required int counterValue}) async {
    // 平台端定义一个方法 increment 该方法接受一个 count 作为参数，
    final result = await methodChannel.invokeMethod<int>('increment', {'count': counterValue});
    
    // 返回从平台端接收到的返回值
    return result!;
  }

  //在平台端定义一个递减方法，用于减少count的值并返回
  static Future<int> decrement({required int counterValue}) async {
   // 平台端定义一个方法 decrement 该方法接受一个 count 作为参数，
    final result = await methodChannel.invokeMethod<int>('decrement', {'count': counterValue});
    
    // 返回从平台端接收到的返回值
    return result!;
  }

  // 定义一个方法接收耦合随机数作为返回值
  static Future<int> randomValue() async {
    // 在平台端定义一个随机数生成函数
    final result = await methodChannel.invokeMethod<int>('random');
    
    // 返回平台端返回的值
    return result!;
  }

  // 定义方法 'tryMe' (custom method) to interact with the native side
  static Future<int> tryMe() async {
    // 调用平台端的 tryMe 方法
    final result = await methodChannel.invokeMethod<int>('tryMe');
    
    // 返回平台端返回的值
    return result!;
  }
}
```



现在我们可以响应本地调用的方法了。

安卓端：

MainActivity.kt

```kotlin
package com.example.flutter_channel_demo

import io.flutter.embedding.android.FlutterActivity
import io.flutter.embedding.engine.FlutterEngine
import io.flutter.plugin.common.BinaryMessenger
import io.flutter.plugin.common.MethodChannel
import io.flutter.plugin.common.MethodCall

class MainActivity: FlutterActivity() {
    override fun configureFlutterEngine(flutterEngine: FlutterEngine) {
        super.configureFlutterEngine(flutterEngine)
      	// 注册通道信息
        methodChannelDemo(flutterEngine.dartExecutor.binaryMessenger)
    }
}

class methodChannelDemo(messenger: BinaryMessenger) :MethodChannel.MethodCallHandler{
    private val channelName = "methodChannelDemo"
    private val channel:MethodChannel

    init {
        channel = MethodChannel(messenger, channelName)
        channel.setMethodCallHandler(this)
    }

    override fun onMethodCall(call: MethodCall, result: MethodChannel.Result) {
        if(call.method=="increment") {
            val count: Int? = call.argument<Int>("count")
            if (count != null) {
                result.success(count+1)
            }else{
                result.success(111)
            }

        }
    }
} 

```



```kotlin
//MethodChannel to handle communication between Flutter and the platform.
MethodChannel(flutterEngine.dartExecutor, "methodChannelDemo")
    .setMethodCallHandler { call, result ->
        //call represents the incoming method call from Flutter
        //result represents result or response that you send back to Flutter after handling the method call.
        // Retrieve the 'count' argument from the method call, if provided.
        val count: Int? = call.argument<Int>("count")
        // Determine which method was called from Flutter.
        when (call.method) {
            // Handle the 'random' method call.
            "random" -> {
                // Generate a random number between 0 and 100 and send it back to Flutter as a success result.
                result.success(Random(System.nanoTime()).nextInt(0, 100))
            }
            // Handle the 'increment' and 'decrement' method calls.
            "increment", "decrement" -> {
                // Check if the 'count' argument is missing or invalid.
                if (count == null) {
                    // If the 'count' argument is missing or invalid, send an error result to Flutter.
                    result.error("INVALID ARGUMENT", "Invalid Argument", null)
                } else {
                    // If the 'count' argument is valid, perform the requested operation.
                    if (call.method == "increment") {
                        // Increment the 'count' and send the updated value to Flutter as a success result.
                        result.success(count + 1)
                    } else {
                        // Decrement the 'count' and send the updated value to Flutter as a success result.
                        result.success(count - 1)
                    }
                }
            }
            // Handle any other method calls that are not implemented.
            else -> {
                // Send a "not implemented" result to Flutter.
                result.notImplemented()
            }
          }
        }
```

![预览](https://miro.medium.com/v2/resize:fit:948/format:webp/1*amcRT_9DPv-FeeleslPU7w.gif)

### ios端代码

```swift
//FlutterMethodChannel to handle communication between Flutter and the platform.
FlutterMethodChannel(name: "methodChannelDemo", binaryMessenger: binaryMessenger)
    .setMethodCallHandler { call, result in
        //call represents the incoming method call from Flutter
        //result represents result or response that you send back to Flutter after handling the method call.
        // Retrieve the 'count' argument from the method call, if provided.
        let count = (call.arguments as? NSDictionary)?["count"] as? Int
        
        // Determine which method was called from Flutter.
        switch call.method {
        case "random":
            // Generate a random number between 0 and 100 and send it back to Flutter as a result.
            result(Int.random(in: 0..<100))
        case "increment", "decrement":
            if count == nil {
                // If the 'count' argument is missing or invalid, send a FlutterError back to Flutter.
                result(FlutterError(code: "INVALID ARGUMENT", message: "Invalid Argument", details: nil))
            } else {
                // If the 'count' argument is valid, perform the requested operation.
                let updatedCount = call.method == "increment" ? count! + 1 : count! - 1
                result(updatedCount)
            }
        default:
            // Handle any other method calls that are not implemented.
            result(FlutterMethodNotImplemented)
        }
    }
```



![预览](https://miro.medium.com/v2/resize:fit:1140/format:webp/1*gFyRjewlLX9RMFpFPzuKFw.gif)

