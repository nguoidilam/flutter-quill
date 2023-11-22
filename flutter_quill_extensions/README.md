# Flutter Quill Extensions

An extensions for [flutter_quill](https://pub.dev/packages/flutter_quill)
to support embed widgets like image, formula, video and more.

 Check [Flutter Quill](https://github.com/singerdmx/flutter-quill) for details of use.

 ## Table of Contents

- [Flutter Quill Extensions](#flutter-quill-extensions)
  - [Table of Contents](#table-of-contents)
  - [About](#about)
  - [Installation](#installation)
  - [Platform Spesefic Configurations](#platform-spesefic-configurations)
  - [Usage](#usage)
  - [Embed Blocks](#embed-blocks)
    - [Custom Size Image for Mobile](#custom-size-image-for-mobile)
    - [Custom Size Image for other platforms](#custom-size-image-for-other-platforms)
    - [Drag and drop feature](#drag-and-drop-feature)
  - [Features](#features)
  - [Contributing](#contributing)
  - [Acknowledgments](#acknowledgments)


## About

Flutter quill is a rich editor text. It'd allow you to customize a lot of things, 
it has custom embed builders which allow you to render custom widgets in the editor <br>
this is an extension to extend its functionalities by adding more features like images, videos, and more

## Installation

Before starting using this package, please make sure to install
[flutter_quill](https://github.com/singerdmx/flutter-quill) package first and follow 
its usage instructions.

```yaml
dependencies:
  flutter_quill_extensions: ^<latest-version-here>
```

<p align="center">OR</p>

```yaml
dependencies:
  flutter_quill_extensions:
    git: https://github.com/singerdmx/flutter-quill.git
    path: flutter_quill_extensions
```

## Platform Spesefic Configurations

>
> 1. We are using [`gal`](https://github.com/natsuk4ze/) plugin to save images.
> For this to work, you need to add the appropriate permissions
> to your `Info.plist` and `AndroidManifest.xml` files.
> See <https://github.com/natsuk4ze/gal#-get-started> to add the needed lines.
>
> 2. We also use [`image_picker`](https://pub.dev/packages/image_picker) plugin for picking images so please make sure follow the instructions
>
> 3. For loading the image from the internet, we need internet permission
>    1. For Android, you need to add some permissions in `AndroidManifest.xml`, Please follow this [link](https://developer.android.com/training/basics/network-ops/connecting) for more info, the internet permission included by default only for debugging so you need to follow this link to add it in the release version too. you should allow loading images and videos only for the `https` protocol but if you want http too then you need to configure your android application to accept `http` in the release mode, follow this [link](https://stackoverflow.com/questions/45940861/android-8-cleartext-http-traffic-not-permitted) for more info.
>    2. for macOS, you also need to include a key in your `Info.plist`, please follow this [link](https://stackoverflow.com/a/61201081/18519412) to add the required configurations
>
> The extension package also uses [image_picker](https://pub.dev/packages/image_picker) which also 
> requires some configurations, follow this [link](https://pub.dev/packages/image_picker#installation). It's needed for Android, iOS, macOS, we must inform you that you can't pick photo using camera in desktop so make sure to handle that if you plan on add support for desktop, this may change in the future and for more info follow this [link](https://pub.dev/packages/image_picker#windows-macos-and-linux) <br>
> 

## Usage

Before starting using this package you must follow the [setup](#installation)

Set the `embedBuilders` and `embedToolbar` params in configurations of `QuillEditor` and `QuillToolbar` with the
values provided by this repository.

**Quill Toolbar**:
```dart
QuillToolbar(
  configurations: QuillToolbarConfigurations(
    embedButtons: FlutterQuillEmbeds.toolbarButtons(),
  ),
),
```

**Quill Editor**
```dart
Expanded(
  child: QuillEditor.basic(
    configurations: QuillEditorConfigurations(
      embedBuilders: kIsWeb ? FlutterQuillEmbeds.editorsWebBuilders() : FlutterQuillEmbeds.editorBuilders(),
    ),
  ),
)
```

They both should be have a parent `QuillProvider` in the widget tree and setup properly <br>
Example:

```dart
QuillProvider(
  configurations: QuillConfigurations(
    controller: _controller,
    sharedConfigurations: const QuillSharedConfigurations(),
  ),
  child: Column(
    children: [
      QuillToolbar(
        configurations: QuillToolbarConfigurations(
          embedButtons: FlutterQuillEmbeds.toolbarButtons(
            imageButtonOptions: QuillToolbarImageButtonOptions(),
          ),
        ),
      ),
      Expanded(
        child: QuillEditor.basic(
          configurations: QuillEditorConfigurations(
            padding: const EdgeInsets.all(16),
            embedBuilders: kIsWeb ? FlutterQuillEmbeds.editorsWebBuilders() : FlutterQuillEmbeds.editorBuilders(),
          ),
        ),
      )
    ],
  ),
)
```

## Embed Blocks

As of version [flutter_quill](https://pub.dev/packages/flutter_quill) 6.0, embed blocks are not provided by default as part of Flutter quill. Instead, it provides an interface to all the user to provide there own implementations for embed blocks. Implementations for image, video and formula embed blocks is proved in this package

The instructions for using the embed blocks is in the [Usage](#usage) section

### Custom Size Image for Mobile

Define `mobileWidth`, `mobileHeight`, `mobileMargin`, `mobileAlignment` as follows:

```json
{
      "insert": {
         "image": "https://user-images.githubusercontent.com/122956/72955931-ccc07900-3d52-11ea-89b1-d468a6e2aa2b.png"
      },
      "attributes":{
         "style":"mobileWidth: 50; mobileHeight: 50; mobileMargin: 10; mobileAlignment: topLeft"
      }
}
```

### Custom Size Image for other platforms

Define `width`, `height`, `margin`, `alignment` as follows:

```json
{
      "insert": {
         "image": "https://user-images.githubusercontent.com/122956/72955931-ccc07900-3d52-11ea-89b1-d468a6e2aa2b.png"
      },
      "attributes": {
         "style":"width: 50; height: 50; margin: 10; alignment: topLeft"
      }
}
```



### Drag and drop feature
Currently, the drag and drop feature is not officially supported, but you can achieve this very easily in the following steps:

1. Drag and drop require native code, you can use any flutter plugin you like, if you want a suggestion we recommend [desktop_drop](https://pub.dev/packages/desktop_drop), it was origanlly developed for desktop but it has support for web as well Android (that is not the case for iOS)
2. Add the dependency in your `pubspec.yaml` using the following command:

    ```yaml
    flutter pub add desktop_drop
    ```
    and import it with
    ```dart
    import 'package:desktop_drop/desktop_drop.dart';
    ```
3. in the configurations of `QuillEditor`, use the `builder` to wrap the editor with `DropTarget` which comes from `desktop_drop`

    ```dart
    import 'package:flutter_quill_extensions/flutter_quill_extensions.dart';
    
    QuillEditor.basic(
          configurations: QuillEditorConfigurations(
            padding: const EdgeInsets.all(16),
             builder: (context, rawEditor) {
                return DropTarget(
                  onDragDone: _onDragDone,
                  child: rawEditor,
                );
              },
            embedBuilders: kIsWeb
                ? FlutterQuillEmbeds.editorsWebBuilders()
                : FlutterQuillEmbeds.editorBuilders(),
          ),
    )
    ```
4. Implement the `_onDragDone`, it depends on your use case but this is just a simple example
```dart
const List<String> imageFileExtensions = [
  '.jpeg',
  '.png',
  '.jpg',
  '.gif',
  '.webp',
  '.tif',
  '.heic'
];
OnDragDoneCallback get _onDragDone {
    return (details) {
      final scaffoldMessenger = ScaffoldMessenger.of(context);
      final file = details.files.first;
      final isSupported =
          imageFileExtensions.any((ext) => file.name.endsWith(ext));
      if (!isSupported) {
        scaffoldMessenger.showSnackBar(
          SnackBar(
            content: Text(
              'Only images are supported right now: ${file.mimeType}, ${file.name}, ${file.path}, $imageFileExtensions',
            ),
          ),
        );
        return;
      }
      // To get this extension function please import flutter_quill_extensions
      _controller.insertImageBlock(
        imageSource: file.path,
      );
      scaffoldMessenger.showSnackBar(
        const SnackBar(
          content: Text('Image is inserted.'),
        ),
      );
    };
  }
```

## Features

```markdown
## Features

— Easy to use and customizable
- Has the option to use custom image provider for the images
- Useful utilities and widgets
- Handle different errors
```

## Contributing

We welcome contributions!

Please follow these guidelines when contributing to our project. See [CONTRIBUTING.md](../CONTRIBUTING.md) for more details.

## Acknowledgments

- Thanks to the [Flutter Team](https://flutter.dev/)
- Thanks to [flutter_quill](https://pub.dev/packages/flutter_quill)
