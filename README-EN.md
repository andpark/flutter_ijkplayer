# ijkplayer

[![pub package](https://img.shields.io/pub/v/flutter_ijkplayer.svg)](https://pub.dartlang.org/packages/flutter_ijkplayer)

ijkplayer for bilibili/ijkplayer, use flutter Texture widget.
Read this README and refer to example/lib/main.dart before using it.
The question of Android might not be able to run will be explained in detail.
The simulator is out of use, so please use the real machine for debugging.

- Android: I added the so Library of x86, but my voice decoding here is abnormal.
- iOS: The simulator library is added, but it has sound and no pictures.

Before using library, you can star and download the code to try the example.

## Install

[![pub package](https://img.shields.io/pub/v/flutter_ijkplayer.svg)](https://pub.dartlang.org/packages/flutter_ijkplayer)

pubspec.yaml

```yaml
dependencies:
  flutter_ijkplayer: ${latest_version}
```

## Build

Compilation options for ijkplayer:
https://github.com/CaiJingLong/flutter_ijkplayer_pod/blob/master/config/module.sh

or see bilibili/ijkplayer or ffmepg

### iOS

Because the libraries of some iOS codes are large, a pod-dependent ijkplayer library for hosting iOS is created.
The pod library is hosted in the GitHub repository at https://github.com/CaiJingLong/flutter_ijkplayer_pod
Instead of tar.gz or zip, we use tar.xz to compress. This compression format has high compression rate, but slow compression and decompression speed. Considering the way of high compression rate, we should use high compression rate.
If a friend is willing to provide CDN acceleration, you can contact me
IOS code comes from iOS code in https://github.com/jadennn/flutter_ijk
On this basis, rotation notification is added.

### Android

Now, use [GSYVideoPlayer](https://github.com/CarGuo/GSYVideoPlayer) `.so` library.

## Simple Example

```dart
import 'package:flutter_ijkplayer/flutter_ijkplayer.dart';


class HomePageState extends State<HomePage> {
  IjkMediaController controller = IjkMediaController();

  @override
  void initState() {
    super.initState();
  }

  @override
  void dispose() {
    controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Plugin example app'),
        actions: <Widget>[
          IconButton(
            icon: Icon(Icons.videocam),
            onPressed: _pickVideo,
          ),
        ],
      ),
      body: Container(
        // width: MediaQuery.of(context).size.width,
        // height: 400,
        child: ListView(
          children: <Widget>[
            buildIjkPlayer(),
          ]
        ),
      ),
      floatingActionButton: FloatingActionButton(
        child: Icon(Icons.play_arrow),
        onPressed: () async {
          await controller.setNetworkDataSource(
              'https://www.sample-videos.com/video123/mp4/720/big_buck_bunny_720p_20mb.mp4',
              // 'rtmp://172.16.100.245/live1',
              // 'https://www.sample-videos.com/video123/flv/720/big_buck_bunny_720p_10mb.flv',
//              "https://www.sample-videos.com/video123/mp4/720/big_buck_bunny_720p_1mb.mp4",
              // 'http://184.72.239.149/vod/smil:BigBuckBunny.smil/playlist.m3u8',
              // "file:///sdcard/Download/Sample1.mp4",
              autoPlay: true);
          print("set data source success");
          // controller.playOrPause();
        },
      ),
    );
  }

  Widget buildIjkPlayer() {
    return Container(
      // height: 400,
      child: IjkPlayer(
        mediaController: controller,
      ),
    );
  }
}
```

## Usage

### Usage of ijkplayer

```dart
IjkMediaController controller = IjkMediaController();
```

```dart
  var ijkplayer = IjkPlayer(
    mediaController: controller,
  );
```

### Usage of controller

#### DataSource

```dart
// network
await controller.setNetworkDataSource("https://www.sample-videos.com/video123/mp4/720/big_buck_bunny_720p_20mb.mp4");

// asset
await controller.setAssetDataSource("assets/test.mp4");

// file
await controller.setFileDataSource(File("/sdcard/1.mp4"));

// autoplay param
await controller.setNetworkDataSource("https://www.sample-videos.com/video123/mp4/720/big_buck_bunny_720p_20mb.mp4",autoPlay : true);

// or use play()
await controller.setNetworkDataSource("https://www.sample-videos.com/video123/mp4/720/big_buck_bunny_720p_20mb.mp4");
await controller.play();
```

#### control your media

```dart
// play or pause your media
await controller.playOrPause();

// play your media
await controller.play();

// pause your media
await controller.pause();

// stop
// Here I want to explain that ijkplayer's stop releases resources, which makes play unusable and requires re-preparation of resources. So, in fact, this is to go back to the progress bar and pause.
await controller.stop();

// seek progress to
await controller.seekTo(0); // double value , such as : 1.1 = 1s100ms, 60 = 1min
```

#### get media info

```dart
  // have some properties, width , height , duration
  VideoInfo info = await controller.getVideoInfo();
```

#### Observer for resource

Broadcasting changes in information outward in the form of streams, in principle the attributes ending with streams are monitorable.

```dart
// Callback when texture ID changes
Stream<int> textureIdStream = controller.textureIdStream;

// Play status monitoring, true is playing, false is pausing
Stream<bool> playingStream = controller.playingStream;

// When controller. refreshVideoInfo () is called, this method calls back, usually for the customization of the controller UI, so as to monitor the current information (playback progress, playback status, width, height, direction change, etc.).
Stream<VideoInfo> videoInfoStream = controller.videoInfoStream;

// Volume change, which should be noted here, refers to the volume change of the current media, not the volume change of the system.
Stream<bool> volumeStream = controller.playingStream;
```

#### release resource

```dart
await controller.reset(); // When this method is called, all native resources are released, but the dataSource is still available for resetting.

await controller.dispose(); // After this method call, the current controller is theoretically no longer available, resetting dataSource is invalid and may throw an exception.
```

## LICENSE

MIT