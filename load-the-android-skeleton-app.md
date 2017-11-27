# 3. 读取 Android 应用程序骨架

## 这个应用程序是什么？

&emsp;&emsp;这个应用程序骨架包含一个 Android 应用程序，它从设备的相机中获取帧，标注出 28 * 28 像素的框，并在主活动的视图中呈现。

## UI 控制

&emsp;&emsp;![](/assets/2.png)

* 白色方框表示待识别的区域，只有白色方框内的图像就加入识别。
* 左上角为当前识别出来的数字，第二行的滚动条控制识别轮廓的粗细，针对不同粗细的笔写的数字。

## 这些额外的代码是什么？

&emsp;&emsp;应用程序代码包括一些需要在本机 TensorFlow 和 Android Java 之间进行连接的帮助器。他们实现的细节并不重要，但你应该了解他们做的是什么。

&emsp;&emsp;MainActivity.onCameraFrame(...)

&emsp;&emsp;这个应用程序骨架使用 Android OpenCV 调用本地相机帧，一旦授权可以使用相机，并将调用此方法，并在主活动中绘制 28 * 28 像素的矩形框作为识别区。

&emsp;&emsp;MainActivity.doRecognize(...)

&emsp;&emsp;这个应用程序骨架获取传入图矩阵，并转换成一维数组。把表单 [0xRRGGBB] 转化成 [0.0,1.0] 的浮点数组，形式为 [r, g, b, r, g, b ...]。

&emsp;&emsp;TensorFlowImageClassifier.recognizeImage(...)

&emsp;&emsp;这是我们要工作的地方，调用 TensorFlow 识别图像并返回其代表的数字。