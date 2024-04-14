### FLUTTER 视频开发
我们平常说的 视频格式其实包含三个部分：
- 视频编码、
- 音频编码、
- 容器格式

#### 编码 包含两个方面：编码和解码

「视频编码」指将动态的图像信息转化为二进制数据；其逆过程称为「视频解码」
解决编码后多组不同类型的的数据的存储、传输问题，将他们按照一定的规律组织起来，这种组织方式即是「容器格式」。

#### 常见视频后缀（容器格式）
- mp4,3gp ,asf,avi,flv,m2v,m4v,mkv,mov,mpeg,mpg,ogv,rm,rmv,ts,vo,webm,wmv,mts,mx,wtv

#### 视频异或处理
1. 异或一种简单加密方式，将视频二进制数据与密钥异或，得到加密后的视频。播放的时候，将视频与密钥异或，即可得到原视频。
2. 异或实现方式：1. 启用本地代理 2. 操作缓存文件

#### 涉及到的 FLUTTER 扩展
1. MPV播放器 https://pub.dev/packages/media_kit
2. FVP https://pub.dev/packages/fvp 基于 libmdk 开发，支持自定义解码器
3. 视频控制器 https://pub.dev/packages/video_player


##### 资源
1. 测试视频下载地址：https://filesamples.com/categories/video
2. https://github.com/0voice/audio_video_streaming/blob/main/article/012-%E5%B8%B8%E8%A7%81%E9%9F%B3%E8%A7%86%E9%A2%91%E7%BC%96%E7%A0%81%E6%A0%BC%E5%BC%8F.md



##### 其他
- “软解”就是通过软件让CPU进行视频解码处理；而“硬解”是指不依赖于CPU硬件资源，通过专用的设备（子卡）单独完成视频解码