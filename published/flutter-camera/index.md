## Instalando os pacotes

```yml
path:
camera:
path_provider:
uuid: ^2.0.4
image_crop: ^0.3.2
```

## Inicializando os Widgets

```dart
void main() {
  WidgetsFlutterBinding.ensureInitialized();

  if (Platform.isIOS) {
    runApp(IOSApp());
  } else {
    runApp(AndroidApp());
  }
}
```

## Obtendo as câmeras disponíveis no Flutter

```dart
import 'package:camera/camera.dart';

takePicture() async {
    final cameras = await availableCameras();
    final firstCamera = cameras.first;

    Navigator.push(
        context,
        MaterialPageRoute(
            builder: (context) => TakePictureView(
                camera: firstCamera,
            ),
        ),
    ).then((imagePath) {
        cropPicture(imagePath);
    });
}

```

## Obtendo a câmera no Flutter

```dart
import 'package:camera/camera.dart';
import 'package:flutter/material.dart';
import 'package:path/path.dart';
import 'package:path_provider/path_provider.dart';
import 'package:uuid/uuid.dart';

class TakePictureView extends StatefulWidget {
  final CameraDescription camera;

  const TakePictureView({
    Key key,
    @required this.camera,
  }) : super(key: key);

  @override
  _TakePictureViewState createState() => _TakePictureViewState();
}
```

## Inicializando a câmera no Flutter

```dart
CameraController _controller;
Future<void> _initializeControllerFuture;

@override
void initState() {
    super.initState();
    _controller = CameraController(
        widget.camera,
        ResolutionPreset.high,
    );

    _initializeControllerFuture = _controller.initialize();
}

@override
void dispose() {
    _controller.dispose();
    super.dispose();
}
```

## Exibindo a imagem da câmera

```dart
...
FutureBuilder(
    future: _initializeControllerFuture,
    builder: (context, snapshot) {
        if (snapshot.connectionState == ConnectionState.done) {
            return CameraPreview(_controller);
        } else {
            return Center(
                child: CircularProgressIndicator(),
            );
        }
    },
),
...
```

## Salvando a foto

```dart
...
Future<String> takePhoto() async {
    try {
        await _initializeControllerFuture;

        final uuid = Uuid();
        final fileName = '${uuid.v4()}.jpg';
        final path = join(
            (await getTemporaryDirectory()).path,
                fileName,
            );

        await _controller.takePicture(path);
        return path;
    } catch (ex) {
        print(ex);
        return "";
    }
}
...
```

## Resultado final

```dart
import 'package:camera/camera.dart';
import 'package:flutter/material.dart';
import 'package:path/path.dart';
import 'package:path_provider/path_provider.dart';
import 'package:uuid/uuid.dart';

class TakePictureView extends StatefulWidget {
  final CameraDescription camera;

  const TakePictureView({
    Key key,
    @required this.camera,
  }) : super(key: key);

  @override
  _TakePictureViewState createState() => _TakePictureViewState();
}

class _TakePictureViewState extends State<TakePictureView> {
  CameraController _controller;
  Future<void> _initializeControllerFuture;

  @override
  void initState() {
    super.initState();
    _controller = CameraController(
      widget.camera,
      ResolutionPreset.high,
    );

    _initializeControllerFuture = _controller.initialize();
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  Future<String> takePhoto() async {
    try {
      await _initializeControllerFuture;

      final uuid = Uuid();
      final fileName = '${uuid.v4()}.jpg';
      final path = join(
        (await getTemporaryDirectory()).path,
        fileName,
      );

      await _controller.takePicture(path);
      return path;
    } catch (ex) {
      print(ex);
      return "";
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text("Nova Imagem"),
        centerTitle: true,
        backgroundColor: Colors.transparent,
        elevation: 0,
      ),
      body: FutureBuilder(
        future: _initializeControllerFuture,
        builder: (context, snapshot) {
          if (snapshot.connectionState == ConnectionState.done) {
            return CameraPreview(_controller);
          } else {
            return Center(
              child: CircularProgressIndicator(),
            );
          }
        },
      ),
      floatingActionButton: FloatingActionButton(
        child: Icon(
          Icons.camera,
        ),
        onPressed: () {
          takePhoto().then((path) {
            Navigator.pop(context, path);
          });
        },
      ),
    );
  }
}
```


<div role="main" id="blog-s1-flutter-15c15f0b7777b4675fad"></div>
<script type="text/javascript" src="https://d335luupugsy2.cloudfront.net/js/rdstation-forms/stable/rdstation-forms.min.js"></script>
<script type="text/javascript"> new RDStationForms('blog-s1-flutter-15c15f0b7777b4675fad', 'UA-48664517-12').createForm();</script>
