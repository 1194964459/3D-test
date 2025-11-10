# WebGL

参考：http://www.yanhuangxueyuan.com/WebGL/

## 绘制一个点
与OpenGL API相配合的是着色器语言GLSL，
与OpenGL ES API、WebGL API相互配合的是着色器语言GLSL ES。
OpenGL标准应用的是客户端，OpenGL ES应用的是移动端，WebGL标准应用的是浏览器平台。

着色器语言用于计算机图形编程，运行在GPU中。
**顶点着色器**定义了顶点的渲染位置和点的渲染像素大小，**片元着色**器定义了点的渲染结果像素的颜色值
着色器相关的代码，需编译处理，然后分别在GPU的顶点/片元着色器单元执行。

gl_Position、gl_FragColor等以 gl 开头的都是内置变量，不需要声明。

颜色、位置都是vec4变量

gl.drawArrays()方法的作用就是通知GPU执行着色器代码，然后根据着色器代码在Canvas画布上进行渲染绘制。

## 绘制一个矩形

