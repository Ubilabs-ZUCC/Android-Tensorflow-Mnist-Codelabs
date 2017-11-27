# 1. 介绍

## 什么是 Mnist ？

&emsp;&emsp;在使用 TensorFlow 进行训练的时候，需要使用到大量的训练集。出于共享和开源的精神，开发者们专门针对 10 以内的手写数字制作了一整套训练集，叫做 NIST。

&emsp;&emsp;MNIST 是一个手写数字数据库，它有 60000 个训练样本集和 10000 个测试样本集。它是 NIST 数据库的一个子集。这个数字数据库的每一个数字图像的像素大小都是 28 * 28 像素。

&emsp;&emsp;![](/assets/1.png)

> 想了解更多关于 MNIST 的内容，请查看 [MNIST 官方网站](http://yann.lecun.com/exdb/mnist/)

&emsp;&emsp;这个 codelab 将引导你完成在 Android 应用程序中使用手写数字识别传递神经网络的过程。别担心，只需要几行代码。你还可以使用此 codelab 中涉及的技术来实现已经培训完毕的任何 TensorFlow 网络。

## 我们将要创造什么 ？

&emsp;&emsp;在这个 codelab 中，你将要使用现有的 Android 应用程序，并在其添加一个 TensorFlow 模型，最终使用设备的相机识别写在之上的单个数字。你将建立以下技能：

* 在你的应用程序中使用 TensorFlow 的 Android Java 和 NDK 库
* 在 Android 应用程序中导入一个预训练的 TensorFlow 模型
* 在 Android 应用程序中执行逻辑推理
* 在 TensorFlow 图中执行特定张量

&emsp;&emsp;![](/assets/2.png)

## 你将要需要什么 ？

* 一个运行 Lollipop（API 21，v5.0）系统且相机在 Camera2 API 支持下（在API 21中引入）的设备
* v2.3 或者更高版本的 Android Studio
* 包含 v26 或者更高版本的 SDK 编译工具

> 注意：这个实验室专注于在一个 Android 应用程序中使用一个已存在的 TensorFlow 模型。Android 部分的代码将和提供的代码很大程度相同，但我们将说明 TensorFlow 的部分和 Android TensorFlow 专用的部分。