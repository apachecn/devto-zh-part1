# 查找视频时长反应自然

> 原文：<https://dev.to/saadbashar/finding-video-duration-react-native-456f>

[![React Native Video](img/01435f9e5f6e54d7abd1aef3ff0be355.png)T2】](https://res.cloudinary.com/practicaldev/image/fetch/s--kbbuYv-E--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://petapixel.com/assets/uploads/2012/05/dslrvideo_mini.jpg)

在我最近的项目中，当客户要求我设置视频的时间限制时，我陷入了困境。我以为这很简单，我只要调用一个内置函数，然后嘣！会有用的！但不幸的是，我找不到这样一个函数来调用。

首先，为了拍摄视频，我使用了一个名为[react-native-image-picker](https://github.com/react-community/react-native-image-picker)的包，该包具有内置选项，可以在录制视频时设置视频的持续时间限制，这非常棒。但不幸的是，当用户从设备图库中选择和上传视频时，它没有任何这样的选项来设置时间限制。那就是我被卡住的地方。

然后我发现了另一个很棒的 react-native 包，它提供了任何媒体文件的元数据。这个包叫做 [react-native-media-meta](https://github.com/mybigday/react-native-media-meta) 。我只需给出包的文件路径，它就会返回给我特定媒体文件的元数据。所以现在对我来说一切都变得简单了。我试着做了以下事情:

1.  从 react-native-image-picker 选项获取媒体的文件路径。
2.  将路径发送到元数据包。
3.  取回也包括视频持续时间的元数据。
4.  最后利用条件做任何我想做的事。

但是我又卡住了！react-native-image-picker 只返回 android 的文件路径，不返回 ios 的文件路径。所以它只适用于安卓系统。最终我发现我必须修改 uri 子串来获得 ios 的实际路径。幸运的是，react-native-image-picker 支持 ios 和 android 的 uri。然后，我终于能够使用 react-native-image picker 找出视频长度。

# 下面给出了工作示例代码:

```
// react-native-image-picker options
const options = {
  title: '', // specify null or empty string to remove the title
  cancelButtonTitle: 'Cancel',
  takePhotoButtonTitle: 'Record Video', // specify null or empty string to remove this button
  chooseFromLibraryButtonTitle: 'Upload Video', // specify null or empty string to remove this button
  cameraType: 'back', // 'front' or 'back'
  thumbnail: true,
  durationLimit: 300, // 5 mins - Works only when you are recording
  allowsEditing: true,
  mediaType: 'video', // 'photo' or 'video'
  videoQuality: 'high', // 'low', 'medium', or 'high'
  storageOptions: { // if this key is provided, the image will get saved in the documents/pictures directory (rather than a temporary directory)
    skipBackup: true, // image will NOT be backed up to icloud
    path: 'images' // will save image at /Documents/images rather than the root
  }
};

// after you take the video...
ImagePicker.showImagePicker(options, (video) => {
      const path = video.path; // for android
      const path = video.uri.substring(7) // for ios
      const maxTime = 300000; // 5 min
      MediaMeta.get(path)
        .then((metadata) => {
          if (metadata.duration > maxTime ) {
            Alert.alert(
              'Sorry',
              'Video duration must be less then 5 minutes',
              [
                { text: 'OK', onPress: () => console.log('OK Pressed') }
              ],
              { cancelable: false }
            );
          } else {
            // Upload or do something else
          }
        }).catch(err => console.error(err));
    }); 
```

如果有更好的选择，请告诉我:)