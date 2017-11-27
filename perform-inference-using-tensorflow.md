# 4. 使用 TensorFlow 进行推理

## 添加项目依赖

&emsp;&emsp;为了在项目中添加接口库和他们的依赖，我们需要添加 TensorFlow Android 接口库和 JAVA API。这些已经可以在 JCenter 使用，或者你可以在从 TensorFlow 源码 编译。

&emsp;&emsp;![](/assets/5.png)

1. 在 Android Studio 中打开 build.gradle。
1. 通过将 API 添加到 android 代码块中的 dependencies 代码块，从而将 API 导入项目。（注意：这不是 buildscript 代码块）。

### [build.gradle](https://github.com/theangels/Mnist_Codelab/blob/master/app/build.gradle)

``` gradle
dependencies {
    compile fileTree(include: ['*.jar'], dir: 'libs')
    compile 'com.android.support:appcompat-v7:26.0.0-alpha1'
    compile project(':openCVLibrary2411')
    compile 'org.tensorflow:tensorflow-android:1.3.1-alpha'
}
```

&emsp;&emsp;点击 Gradle sync 按钮，在 IDE 中使改变生效。

## 代码结构

&emsp;&emsp;关于 TensorFlow 的环境配置已经完成了，我们来看看代码的结构。整个项目的 Java 部分代码由 3 个包组成，androiddemo、finished 和 util。

&emsp;&emsp;androiddemo 包中的内容是我们要工作的地方；finished 包是代码完成时的样子，作为参考；util 包是项目中所用到的工具，同样不需要我们编辑。

&emsp;&emsp;androiddemo 包中包含两个 Java 类，MainActivity 和 TensorFlowImageClassifier。与 Android 系统进行界面交互以及调用 OpenCV 的部分位于 MainActivity 当中，这部分的相关代码已经帮我们完成；具体调用 TensorFlow API 的部分在 TensorFlowImageClassifier 当中，这部分需要我们补充。

## 手写数字识别神经网络

&emsp;&emsp;我们已经把在最后一部分描述的手写数字识别神经网络放在了项目的 assets 目录中，因此它可以供你使用。

## 添加推理代码

&emsp;&emsp;在 MainActivity.java 中，找到 `//TODO Declare input model variables here`，用以下成员字段覆盖。

## [MainActivity.java](https://github.com/theangels/Mnist_Codelab/blob/master/app/src/main/java/org/ubilabs/angel/androiddemo/MainActivity.java)

``` java
// 这部分已经存在，不要复制
private PermissionUtils.PermissionGrant mPermissionGrant = new PermissionUtils.PermissionGrant() {...};

/*
* TensorFlow
* */

//TODO Declare input model variables here
private static final int INPUT_SIZE = 28;
private static final String INPUT_NAME = "input";
private static final String OUTPUT_NAME = "output";

private static final String MODEL_FILE = "file:///android_asset/mnist_model_graph.pb";

// 这部分已经存在，不要复制
private TensorFlowImageClassifier classifier;
private Executor executor = Executors.newSingleThreadExecutor();
```

## [TensorFlowImageClassifier.java](https://github.com/theangels/Mnist_Codelab/blob/master/app/src/main/java/org/ubilabs/angel/androiddemo/TensorFlowImageClassifier.java)

&emsp;&emsp;在 TensorFlowImageClassifier.java 中，找到 `//TODO Add member here`，添加 TensorFlow 接口类。

``` java
// 这部分已经存在，不要复制
// Pre-allocated buffers.
private float[] outputs;

private TensorFlowInferenceInterface inferenceInterface;

// 这部分已经存在，不要复制
private TensorFlowImageClassifier() {
}
```
&emsp;&emsp;在相同的类下，找到 create 方法，在 `//TODO Add TensorFlowInferenceInterface instantiation here` 下添加接口实现。

## [TensorFlowImageClassifier.java](https://github.com/theangels/Mnist_Codelab/blob/master/app/src/main/java/org/ubilabs/angel/androiddemo/TensorFlowImageClassifier.java)

``` java
public static TensorFlowImageClassifier create(
        AssetManager assetManager,
        String modelFilename,
        int inputSize,
        String inputName,
        String outputName)
        throws IOException {
    TensorFlowImageClassifier c = new TensorFlowImageClassifier();
    c.inputName = inputName;
    c.outputName = outputName;

    // 从这里开始复制

    //TODO Add TensorFlowInferenceInterface instantiation here
    c.inferenceInterface = new TensorFlowInferenceInterface(assetManager, modelFilename);

    // 从这里结束复制

    int numClasses = (int) c.inferenceInterface.graph().operation(outputName).output(0).shape().size(1);
    Log.i(TAG, "Output layer size is " + numClasses);

    c.inputSize = inputSize;
    c.outputName = outputName;
    c.outputs = new float[numClasses];

    return c;
}
```

&emsp;&emsp;现在找到 recognizeImage 方法，添加代码调用 TensorFlow 识别图像。

## [TensorFlowImageClassifier.java](https://github.com/theangels/Mnist_Codelab/blob/master/app/src/main/java/org/ubilabs/angel/androiddemo/TensorFlowImageClassifier.java)

``` java
int recognizeImage(final float[] pixels) {
    int recognitions = 0;

    //从这里开始复制

    //TODO Add TensorFlow process code here
    // Log this method so that it can be analyzed with systrace.
    TraceCompat.beginSection("recognizeImage");

    // Copy the input data into TensorFlow.
    TraceCompat.beginSection("feed");
    inferenceInterface.feed(inputName, pixels, inputSize * inputSize);
    TraceCompat.endSection();

    // Run the inference call.
    TraceCompat.beginSection("run");
    inferenceInterface.run(new String[]{outputName}, false);
    TraceCompat.endSection();

    // Copy the output Tensor back into the output array.
    TraceCompat.beginSection("fetch");
    inferenceInterface.fetch(outputName, outputs);
    TraceCompat.endSection();

    for (int i = 1; i < outputs.length; i++) {
        if (outputs[i] > outputs[recognitions]) {
            recognitions = i;
        }
    }

    TraceCompat.endSection(); // "recognizeImage"

    //从这里结束复制
    return recognitions;
}
```

> 重要：如果你看到了 “无法找到标识 ...” 的警告，你将需要添加 import 语句到此文件中。Android Studio 能帮你做这个事情，把光标移动到红色错误文本上方，按下 Alt-Enter 键，选择 Import ...

1. 在 Android Studio 中按下 Run 按钮，等待项目编译
1. 你现在应该看到了在你的设备中识别了你手写的数字！

&emsp;&emsp;![](/assets/2.png)