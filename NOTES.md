# 概述
- 优势
    - 使用H5标准的canvas，基于浏览器使用webgl
    - 显示和操作三维图形
    - 用于开发三维客户界面，三维网页游戏，将互联网的海量数据进行三维可视化
    - 语言、工具、平台简单
    - 跨平台、轻松发布（传统三维图形程序使用c,c++等语言开发，并未特定平台编译成二进制可执行文件）
    - webgl的技术规范继承自免费、开源的OpenGL标准
    - webgl从Opengl的特殊版本Opengl ES中派生而来，用于嵌入式极端及、智能手机、家用游戏机设备等（2003-2004），轻量级以及渲染能力强（添加了新特性同时从Opengl移除了许多陈旧无用的就特性）
- 起源
    - 个人计算机上是哦那个最广泛的两种三维图形渲染技术：Direct3D和OpenGL
    - 关系图![关系图](webgl_opengl.png)
    - openGL从2.0版本支持可编程着色器方法，并被opengl es2.0继承，并成为webgl 1.0的核心部分
    - 着色器方法，简称着色器，使用类似C的编程语言
    - 着色器语言
        - OpenGL着色器语言-- GLSL
        - OpenGL ES着色器语言 -- GLSL ES
        - WebGL使用ES语言
    - 2011年发布webgl规范的1.0版本
- 程序结构
    - html - javascript - glsl es

# 入门
- canvas是什么？
    - 没有webgl，JavaScript只能在\<canvas\>上绘制二纬图形
    - 有了webgl，JavaScript能在\<canvas\>上绘制三维图形
- 最短的webgl程序：清空绘图区
    - 获取canvas容器
    - 获取上下文context：2d/3d
    - 调用绘制函数
    - 准备：
        - 引入一些专为webgl准备的含数据：webgl-util.js/webgl-debug.js/cunon-utils.js
    - 代码
        ```js
        var canvas = document.getELementById('canvas');
        // 参数2知名是否报错在控制台
        var gl = getWebGLContext(canvas); // 借助utils工具获取上下文，该方法处理了不同浏览器之间的兼容性，
        // webgl继承自OpenGL,所以遵循opengl颜色分量取值范围从0到1
        // clear颜色设置一次后会存储在缓存区，得到：gl.COLOR_BUFFER_BIT
        gl.clearColor(0,0,0,1); // 指定清空canvas的颜色 rgb取值【0.0-1.0】 a取值【0.0-1.0】
        gl.clear(); // 清空canvas
        // 清空画布实际上就是在清空--颜色缓冲区（color buffer)
        ```
    - 缓冲区继承自OpenGL的多基本缓冲区模型，比二位绘图上下文复杂的多
    - 其他缓冲区：颜色缓冲区，深度缓冲区，模板缓冲区
    - 如果不设置clearColor，缓冲区默认值：
        缓冲区名称|默认值|相关函数|非默认时的参数
        -|-|-
        颜色缓冲区|（0，0，0，0）|gl.clearColor(r,g,b,a)|gl.COLOR_BUFFER_BIT
        深度缓冲区|1.0|gl.clearDepth(depth)|gl.DEPTH_BUFFER_BIT
        模板缓冲区|0|gl.clearStencil(s)|gl.STENCIL_BUFFER_BIT
- 绘制一个点
    - canvas坐标系统：左上角为远点（x-向右，y-向下）
    - 单位：像素
    - webgl处理的是三维图形，具有三维坐标
    - 坐标原点在（0，0，0）在canvas的中心位置
    - 用矩形代替圆点，cause矩形比圆简单
    - 暂未使用"缓冲区对象"
    - 【PS】要使用webgl绘图就必须使用着色器
    - 依赖于着色器绘图机制，强大，但复杂，不是简单一条命令就能操作它工作的
        - 【顶点着色器】：描述定点特性的程序（尺寸、位置）
        - 【片元着色器】：进行逐片元处理过程如光照的过程（颜色）。
        - 片元：一个webgl属于，可将其理解为像素（图像的单元）
        - 必须两种着色器
    - 实际场景需要可考虑：光照、观察视角等对场景的影响，着色器可以高度灵活的完成这些工作，提供各种渲染效果。
    - 浏览器webgl接口调用-->在WebGL系统逐顶点操作+逐片元操作-->渲染到颜色缓冲区--->显示到浏览器
    - 【颜色缓冲区】的内容会自动显示在浏览器canvas上
    - 【扩展】如何从文件中加载着色器程序
    - 【WebGL系统】由两个部分组成：顶点着色器和片元着色器
        - 顶点着色器先执行，然后传入片元着色器，再执行片元着色器
    - 【initShader做了什么？】在初始化着色器之前，定点着色器和片元着色器都是空白的，initShader的将JavaScript写的字符串形式的着色器代码传给WebGL系统，并建立着色器。着色器运行在WebGL系统中，而不是JS程序。
- 顶点着色器
    - 控制点的位置和尺寸
    - GLSL ES是一种强类型语言，指明变量类型，所以变量赋值必须按类型复制
    - 必须包含一个main()函数, 关键字void表示无返回值，无参数
    - 内置变量
        类型 | 变量 | 描述 | 必选
        - | - | - | -
        vec4 | gl_Position | 表示定点位置 | 是
        float | gl_PointSize | 表示点的尺寸（像素数）| 否，默认1.0
        类型 | 描述 
        - | -
        vec4 | 表示由4个浮点数组成的矢量, 分量取值范围【0.0-1.0】
        float | 表示浮点数
    - 【齐次坐标】由四个分量组成的坐标（0.0，0.0，0.0，1.0）称为齐次坐标[x,y,z,w]===[x/w,y/w,z/w] w>=0（作用：提高处理三维数据的效率），当第四个分量为1.0时候，“前三个分量可以看作坐标值”
    - 【矢量】x,y,z坐标值 -vec4()-->vec4
    - 【三维图形系统】计算过程，通常使用齐次坐标来表示顶点的三维坐标
- 片元着色器
    - 控制点的颜色
    - 作用：处理片元，使其显示在屏幕上
    - 必须包含一个main()函数，【同顶点】
    - 内置变量
        类型 | 变量 | 描述 | 必选
        - | - | - | -
        vec4 | gl_FragColor | 指定片元颜色（RGBA格式）| 不知道
- gl.drawArray(mode, first, count):一个强大的函数，可会知各种图形，规范如下
    参数 | 描述
    - | -
    mode|指定绘制方式，可接收常量符号：gl.POINTS，gl.LINES,gl.LINE_STRIP,gl.LINE_LOOP,gl.TRIANFLES,gl.TRIANGLE_STRIP,gl.TRIANGLE_FAN
    first|指定从那个顶点开始绘制（整数型）（0开始）
    count|指定会只需要多少个顶点（整数型）
    补充|顶点着色器执行的次数为count次，每次处理一个顶点
- WebGL坐标系统
    - webgl处理的是三维图形，所以他使用三维坐标系统（笛卡尔坐标系-右手定则）
    - webgl坐标 <--转换--> canvas坐标
- 绘制一个点（第二版，非硬编码可扩展），借助两个方法：
    - attribute变量：传输与顶点相关的数据（GLSL ES变量，只有顶点着色器可以使用它）
    - uniform变量：所有顶点统一的数据（与顶点无关）
    - 声明一个变量存储位置：attribute vec4 a_Postion; gl_Postion = a_Postion;
    - 从JavaScript向顶点着色器中attribute变量传值
        函数名|参数|描述
        -|-|-
        gl.vertexAttrib1f|location,v0|制定传输给attribute变量的四个分量的值
        gl.vertexAttrib2f|location,v0,v1|制定传输给attribute变量的四个分量的值
        gl.vertexAttrib3f|location,v0,v1,v2|制定传输给attribute变量的四个分量的值
        gl.vertexAttrib4f|location,v0,v1,v2,v3 |制定传输给attribute变量的四个分量的值
    - 函数命名规范遵循OpenGL:基础函数(gl.vertextAttrib)+参数个数(1)+参数类型(f)
    - 函数名后 + v，如：gl.vertexAttrib4fv表示接受数组作为参数
- 鼠标点击绘制一个点
     - 相应鼠标事件
     - 鼠标每次点击的位置都需要记录？WebGL使用的是颜色缓冲区。系统中绘制操作实际是在有颜色缓冲区进行绘制，绘制结束后，系统将缓冲区的内容显示在怕屏幕上。此时你的缓冲区会被重置。【重置：是默认操作】。因此我们需要将每次鼠标位置都记录。
- 改变点的颜色
    - 计算机系统通常使用RGB格式来表示颜色，当加入α（透明度）后，称之为RGBA格式
    - 动态修改点的颜色，将变量传入片元着色器中：使用uniform变量
    - uniform变量，用u_前缀开始
    - 使用片元着色器，要用uniform变量或者varying变量
    - 使用precision设置精度限定，范围以及该精度，mediump等
    - 从Javascript向顶点着色器中uniform变量传值
        函数名|参数|描述
        -|-|-
        gl.uniform1f|location,v0|制定传输给attribute变量的四个分量的值
        gl.uniform2f|location,v0,v1|制定传输给attribute变量的四个分量的值
        gl.uniform3f|location,v0,v1,v2|制定传输给attribute变量的四个分量的值
        gl.uniform4f|location,v0,v1,v2,v3|制定传输给attribute变量的四个分量的值

# 绘制与变换三角形
- 绘制多个点
    - 构成三维模型的基本单位是---三角形
    - 上章，点击绘制点逻辑，将点存储在一个数组中，再循环遍历数组，每次遍历向着色器传入一个点，并绘制
    - 但是对由多个顶点组成的图形，如三角形来说，你需要一次性将图形的顶点全部传入顶点着色器，才能将图形绘制出来-------webgl提供另一种机制----缓冲区对象--一次性想着色器传入多个顶点数据---是webgl系统中的一块内存区域----一次性填充大量顶点数据---供顶点着色器使用
- 绘制三角形
- 【*】移动、旋转、缩放（用矩阵简化变换）

# 高级变换与动画基础
- 平移后，再旋转
- 动画

# 颜色、纹理
- 将非坐标数据传入顶点着色器
- 彩色三角形
- 在矩形表面贴上图像
- 使用多幅纹理

# OpenGL着色器语言(GLSL ES)
- 语法

# 三维世界
- 立方体由三角形构成
- 视点、视线
- 可视范围（正射类型）
- 可视空间（透视投影）
- 处理对象前后关系
- 立方体

# 光照
- 光照原理
- 运动物体光照效果
- 点光源

# 层次模型
- 多个简单模型组成复杂模型
- 着色器和着色器程序对象

# 高级技术
- 鼠标控制物体旋转
- HUD(平视显示器)
- 雾化(大气效果)
- 绘制图形的点
- α混合
- 切换着色器
- 渲染到纹理
- 绘制阴影
- 加载三维模型
- 相应上下文丢失

# 补充
- webgl中无需交换缓冲区
- glsl es1.0内置函数
- 投影矩阵
- webgl/opengl 左手还是右手坐标系？
- 逆转置矩阵
- 从文件中加载着色器
- 世界坐标系和本地坐标系
- webgl浏览器设置

