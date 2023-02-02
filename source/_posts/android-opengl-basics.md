---
layout: post
title: Android OpenGL学习笔记
date: 2023/1/2
categories: Android
---

[OpenGL ES 2.0 for Android](https://www.amazon.com/OpenGL-Android-Quick-Start-Pragmatic-Programmers/dp/1937785343) 学习笔记。

不过发现OpenGL不靠笔记，全靠内力，所以后期复杂的内容就记不过来了。

<!--more-->

## Getting Started

现代GPU更新了算法，**每帧清屏重新绘制**比在上一帧基础上修改要更效率、更可靠。

`SurfaceView`是个`Surface`，屏幕上单独挖了个洞来高效率显示；`TextureView`是个`View`，继承了`SurfaceView`的功能且可以当成`View`来操作，代价是性能牺牲。

`GLSurfaceView.Renderer`中的代码会**在子线程执行**。主线程到子线程：`GLSurfaceView.queueEvent`。

```Kotlin
glSurfaceView.setEGLContextClientVersion(2)

// Sometimes necessary
// glSurfaceView.setEGLConfigChooser(8, 8, 8, 8, 16, 0)

glSurfaceView.setRenderer(AirHockeyRenderer(this))
glSurfaceView.withLifecycleOwner(this)
```

```Kotlin
override fun onSurfaceCreated(gl: GL10?, config: EGLConfig?) {
    GLES20.glClearColor(1f, 0f, 0f, 0f) // Alpha is disabled by default
}

override fun onSurfaceChanged(gl: GL10?, width: Int, height: Int) {
    GLES20.glViewport(0, 0, width, height)
}

override fun onDrawFrame(gl: GL10?) {
    // We must draw something, even if it’s only to clear the screen.
    // If we don’t draw anything, we’ll probably get a bad flickering effect.
    // This will wipe out all colors on the screen and fill the screen with the color
    // previously defined by our call to glClearColor().
    GLES20.glClear(GLES20.GL_COLOR_BUFFER_BIT)
}
```

## Defining Vertices and Shaders

Vertex, Vertices: 点。包含位置、颜色等信息。

用多个三角形去近似任意图形。描述时使用**逆时针（倒带）方向可以优化性能**。

使用`ByteBuffer.allocateDirect`定义不被垃圾回收影响的Native Memory。

```Kotlin
private val vertexData: FloatBuffer = ByteBuffer
    .allocateDirect(tableVerticesWithTriangles.size * BYTES_PER_FLOAT)
    .order(ByteOrder.nativeOrder())
    .asFloatBuffer()
    .put(floatArray)
```

Shader: 渲染管线单元。

Shader使用GLSL（OpenGL’s shading language）

### Vertex Shader

每个点运行一次，得到最后的点。OpenGL生成点、线和三角形。

坐标为(-1, 1), (-1, 1)。

```GLSL
// Simple Vertex Shader
attribute vec4 a_Position;
void main()
{
    gl_Position = a_Position;
    // gl_PointSize = 10.0;
}
```

`vec4`: x, y, z, w 坐标

`attribute`: 添加颜色信息，自带默认值（0, 0, 0, 1）。

默认有着最高精度`highp`（见Fragment Shader精度介绍）。

### Fragment Shader

计算得到上述生成的点、线和三角形中每个像素点的颜色。Fragment相当于Pixel。

```GLSL
// Simple Fragment Shader
precision mediump float;
uniform vec4 u_Color;
void main()
{
    gl_FragColor = u_Color;
}
```

`precision mediump float;`: 定义所有浮点数的精度。在`lowp`, `mediump`, `highp`中选择。

无默认精度（未定义的行为？），必须手动定义。

2023年1月13日更新：如果FragmentShader不定义精度，有的手机上会无法显示。

最高精度旨**在某些实现中未支持**。

`int`的默认精度是`mediump`。

`uniform`: 保持不变直至手动修改。无默认值，需要手动指定。

`vec4`: 这里是颜色`RGBA`。

## Compiling Shaders and Drawing to the Screen

1. 在`onSurfaceCreated`中获得两种Shader的String。
1. 编译两种Shader，得到对应的shaderObjectId(int)。

    - OpenGL内部并不抛异常，只是返回特定的值或通过`glGetError`读取。

1. 把Shaders合成为Program，得到programObjectId。
1. 检测Program状态。
1. 使用Program。
1. 获取变量Attribute和Uniform的Location值。
1. 设置变量值

    方法有很多，一些是：

    - `glVertexAttribPointer` + `glEnableVertexAttribArray`设置Attribute值。
    - `glUniform4f`设置Uniform值。

1. 绘制

    - `glDrawArrays`

## Adding Color and Shade

`GL_TRIANGLE_FAN`: 一次画四个三角形。

```GLSL
// New Vertex Shader
attribute vec4 a_Position;
attribute vec4 a_Color;
varying vec4 v_Color;
void main()
{
    v_Color = a_Color;
    gl_Position = a_Position;
    gl_PointSize = 10.0;
}
```

`varying`: 在一条线或三角形中，渐变颜色后，交给Fragment Shader。

```GLSL
// New Fragment Shader
precision mediump float;
varying vec4 v_Color;
void main()
{
    gl_FragColor = v_Color;
}
```

这样就不用手动设置Fragment Shader颜色了。

## Adjusting to the Screen’s Aspect Ratio

线性代数、矩阵相乘！

```Text
1 0 0 translateX     x     x + translateX
0 1 0 translateY  x  y  =  y + translateY
0 0 1 translateZ     z     z + translateZ
0 0 0 1              1     1
```

```GLSL
// New Vertex Shader
uniform mat4 u_Matrix;
...
void main()
{
    ...
    gl_Position = u_Matrix * a_Position;
    ...
}
```

使用`Matrix.orthoM`根据屏幕比例，缩放长边，使内容显示在中心的正方形中。

## Entering the Third Dimension

坐标处理流程：

1. Clip Space

    在`Vertex Shader`中的`gl_Position`首先被裁剪到[-w, w]的范围中。

    此时的坐标也被称为Homogenous Coordinates。

1. Perspective Division

    x, y, z = x / w, y / w, z / w

    w代表距离。距离越大，结果越小，结果越靠近原点，越像消失点。

    此阶段结束后，所有坐标都在[-1, 1]。多个Homogenous Coordinates会对应一个Normalized device coordinates。

    w不为1时，(x, y, z)并不是实际坐标，(x / w, y / w, z / w)才是。

1. Viewport Transformation

    和显示区域长宽相乘得到最终坐标Window coordinates。

给坐标增加各种矩阵，实现3D模拟。

## Adding Detail with Textures

之后书中的内容就复杂起来，记笔记也变得难了起来。
