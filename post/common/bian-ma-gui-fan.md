## Hello Window
Let's see if we can get GLFW up and running. First, create a .cpp file and add the following includes to the top of your newly created file.

让我们看一下GLFW是否能正确运行。首先创建一个a.cpp文件，然后把下面这些包含文件添加到刚刚创建问文件最上方：
```
#include <glad/glad.h>
#include <GLFW/glfw3.h>
```

Be sure to include GLAD before GLFW. The include file for GLAD contains the correct OpenGL header includes (like GL/gl.h) so including GLAD before other header files that require OpenGL does the trick.

确保在包含GLFW头文件之前包含GLAD头文件。GLAD头文件包含了所有正确的OpenGL头文件（比如GL/gl.h），所以要在包含其他依赖于OpenGL的头文件之前包含GLAD头文件。

Next, we create the main function where we will instantiate the GLFW window:

接下来我们要创建一个main函数，在这里我们将实例化GLFW窗口：
```
int main()
{
    glfwInit();
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
    //glfwWindowHint(GLFW_OPENGL_FORWARD_COMPAT, GL_TRUE);
  
    return 0;
}
```
In the main function we first initialize GLFW with **glfwInit**, after which we can configure GLFW using **glfwWindowHint**. The first argument of **glfwWindowHint **tells us what option we want to configure, where we can select the option from a large enum of possible options prefixed with **GLFW_**. The second argument is an integer that sets the value of our option. A list of all the possible options and its corresponding values can be found at [GLFW's window handling](http://www.glfw.org/docs/latest/window.html#window_hints) documentation. If you try to run the application now and it gives a lot of undefined reference errors it means you didn't successfully link the GLFW library.

在main函数中，我们首先用**glfwInit**来初始化了GLFW，然后我们又用**glfwWindowHint**来配置GLFW。**glfwWindowHint**的第一个参数是我们想要配置的选项，这些选项可以在一个以**GLFW_**为前缀的大枚举中找到。第二个参数是数值类型的，代表这个参数的值。所有可能的选项列表和它对应的值都能在[GLFW's window handling](http://www.glfw.org/docs/latest/window.html#window_hints)文档中找到。如果你现在试着去运行这个应用程序，并且产生了很多未知的引用错误，那意味着你没有成功的链接GLFW库。

Since the focus of this website is on OpenGL version 3.3 we'd like to tell GLFW that 3.3 is the OpenGL version we want to use. This way GLFW can make the proper arrangements when creating the OpenGL context. This ensures that when a user does not have the proper OpenGL version GLFW fails to run. We set the major and minor version both to 3. We also tell GLFW we want to explicitly use the core-profile. Telling GLFW explicitly that we want to use the core-profile means we'll get access to a smaller subset of OpenGL features (without backwards-compatible features we no longer need). Note that on Mac OS X you need to add **glfwWindowHint(GLFW_OPENGL_FORWARD_COMPAT, GL_TRUE);** to your initialization code for it to work.

因为本站所用的都是OpenGL3.3，所以我们告诉GLFW用的版本也是OpenGL3.3。这样GLFW会在创建OpenGL上下文的时候做出一些适当的调整，来确保用户在使用了错误的版本时不能运行。我们设置的最大和最小版本都是3。我们也明确的告诉GLFW我们使用core-profile，意味着我们只能访问使用OpenGL特性的一个小的子集（没有我们已不再需要的向后兼容特性）。顺便说明在Mac OS X上需要在你的代码中添加**glfwWindowHint(GLFW_OPENGL_FORWARD_COMPAT, GL_TRUE);**来保证它正常工作。

Make sure you have OpenGL versions 3.3 or higher installed on your system/hardware otherwise the application will crash or display undefined behavior. To find the OpenGL version on your machine either call glxinfo on Linux machines or use a utility like the [OpenGL Extension Viewer](http://download.cnet.com/OpenGL-Extensions-Viewer/3000-18487_4-34442.html) for Windows. If your supported version is lower try to check if your video card supports OpenGL 3.3+ (otherwise it's really old) and/or update your drivers.

确保你安装的OpenGL版本是3.3或者更高，否则你的应用程序会崩溃或者出现未定义的行为。在Linux机器上可以使用glxinfo命令来查看OpenGL版本，在windows上使用[OpenGL Extension Viewer](http://download.cnet.com/OpenGL-Extensions-Viewer/3000-18487_4-34442.html)工具查看。如果你的当前版本小于3.3，那么查看一下你的显卡是否支持3.3以上版本，然后更新你的驱动。

Next we're required to create a window object. This window object holds all the windowing data and is used quite frequently by GLFW's other functions.

下一步我们需要创建一个窗口对象。这个窗口对象持有所有的窗口操作数据，而且这个对象会在其他GLFW的函数中频繁使用。

```
GLFWwindow* window = glfwCreateWindow(800, 600, "LearnOpenGL", NULL, NULL);
if (window == NULL)
{
    std::cout << "Failed to create GLFW window" << std::endl;
    glfwTerminate();
    return -1;
}
glfwMakeContextCurrent(window);
```

The **glfwCreateWindow** function requires the window width and height as its first two arguments respectively. The third argument allows us to create a name for the window; for now we call it "LearnOpenGL" but you're allowed to name it however you like. We can ignore the last 2 parameters. The function returns a **GLFWwindow** object that we'll later need for other GLFW operations. After that we tell GLFW to make the context of our window the main context on the current thread.

函数**glfwCreateWindow**需要窗口的宽高分别作为他的前两个参数。第三个参数允许我们为这个窗口创建一个名字；现在我们叫他“LearnOpenGL”，当然你也可以用你喜欢的名字。我们先忽略后两个参数。这个函数会返回一个**GLFWwindow**对象，稍后我们会在其他GLFW的操作中需要它。创建完窗口我们就可以通知GLFW将我们窗口的上下文设置为当前线程的主上下文了。

### GLAD
In the previous tutorial we mentioned that GLAD manages function pointers for OpenGL so we want to initialize GLAD before we call any OpenGL function:

在前面的教程中我们提到GLAD为OpenGL管理函数的指针，所以我们要在调用OpenGL之前初始化GLAD。
```
if (!gladLoadGLLoader((GLADloadproc)glfwGetProcAddress))
{
    std::cout << "Failed to initialize GLAD" << std::endl;
    return -1;
} 
```
We pass GLAD the function to load the adress of the OpenGL function pointers which is OS-specific. GLFW gives us **glfwGetProcAddress** that defines the correct function based on which OS we're compiling for.

我们给GLAD传入的是加载系统相关OpenGL函数指针地址的函数。GLFW返回给我吗的是**glfwGetProcAddress**函数，这个函数定义了基于我们当前系统所使用编译器的函数。

### Viewport
Before we can start rendering we have to do one last thing. We have to tell OpenGL the size of the rendering window so OpenGL knows how we want to display the data and coordinates with respect to the window. We can set those dimensions via the **glViewport** function:

在我们开始绘制之前，我们还必须做最后一件事。我们需要告诉OpenGL我们要绘制的窗口的尺寸，这样OpenGL才能知道它要显示的窗口的大小坐标。我们可以通过**glViewport**设置这些数据：
```
glViewport(0, 0, 800, 600);
```
The first two parameters of **glViewport** set the location of the lower left corner of the window. The third and fourth parameter set the width and height of the rendering window in pixels, which we retrieve here from GLFW itself.

**glViewport**的前两个参数是窗口左上角的坐标。第三个和第四个参数是设置我们要绘制的这个窗口宽高的像素值。

We could actually set the viewport dimensions at values smaller than GLFW's dimensions; then all the OpenGL rendering would be displayed in a smaller window and we could for example display other elements outside the OpenGL viewport.

实际上我们也可以设置viewport的维度值比GLFW的维度值更小；这样所有OpenGL的绘制可能会被显示在更小的窗口中，这样我们也能显示其他元素在OpenGL viewport之外了。

Behind the scenes OpenGL uses the data specified via **glViewport** to transform the 2D coordinates it processed to coordinates on your screen. For example, a processed point of location (-0.5,0.5) would (as its final transformation) be mapped to (200,450) in screen coordinates. Note that processed coordinates in OpenGL are between -1 and 1 so we effectively map from the range (-1 to 1) to (0, 800) and (0, 600).

OpenGL幕后使用**glViewport**中定义的位置和宽高进行2D坐标的转换，将OpenGL中的位置坐标转换为你的屏幕坐标。例如，OpenGL中的坐标(-0.5, 0.5)有可能（最终）被映射为屏幕中的坐标(200,450)。注意，处理过的OpenGL坐标范围只为-1到1，因此我们事实上将(-1到1)范围内的坐标映射到(0, 800)和(0, 600)。

However, the moment a user resizes the window the viewport should be adjusted as well. We can register a callback function on the window that gets called each time the window is resized. This resize callback function has the following prototype:

无论怎样，当用户调整窗口大小的时候，viewport也应该被调整。我们可以一个回调函数，每当窗口大小改变的时候它都会被调用。窗口改变的回调函数有下面这些属性：
```
void framebuffer_size_callback(GLFWwindow* window, int width, int height);  
```
The framebuffer size function takes a **GLFWwindow** as its first argument and two integers indicating the new window dimensions. Whenever the window changes in size, GLFW calls this function and fills in the proper arguments for you to process.

这个帧缓存尺寸的函数第一个参数是一个**GLFWwindow**，另外两个整型表示了新窗口的宽高维度。每当窗口的尺寸改变的时候，GLFW都会调用这个函数，并且为这些参数填充上适当的值供你处理。
```
void framebuffer_size_callback(GLFWwindow* window, int width, int height)
{
    glViewport(0, 0, width, height);
}  
```
We do have to tell GLFW we want to call this function on every window resize by registering it:

我们还必须通过注册来告诉GLFW我们想在每次窗口改变大小的时候调用这个函数：
```
glfwSetFramebufferSizeCallback(window, framebuffer_size_callback); 
```
When the window is first displayed **framebuffer_size_callback** gets called as well with the resulting window dimensions. For retina displays width and height will end up significantly higher than the original input values.

当窗口第一次被显示的时候，**framebuffer_size_callback**会被一起调用，并且带着窗口相关的参数。对于视网膜屏幕来说，宽高的值都会比输入的大一些。

There are many callbacks functions we can set to register our own functions. For example, we can make a callback function to process joystick input changes, process error messages etc. We register the callback functions after we've created the window and before the game loop is initiated.

我们拥有很多可以注册的回调函数可以使用。例如，我们可以注册一个回调函数来处理摇杆输入，处理错误信息等等。我们需要在创建窗口之后，游戏主循环初始化之前来注册这些回调。

## 准备好你的引擎(Ready your engines)
We don't want the application to draw a single image and then immediately quit and close the window. We want the application to keep drawing images and handling user input until the program has been explicitly told to stop. For this reason we have to create a while loop, that we now call the **render loop**, that keeps on running until we tell GLFW to stop. The following code shows a very simple render loop:

我们不想让这个应用程序只绘制一个图形或者立刻的就关闭退出了。我们想让这个应用程序保持一直绘制，并且可以处理用户的输入，知道程序明确的告诉它应该退出。出于这个原因我们需要创建一个while循环，现在我们叫他**渲染循环**，它在我们告诉GLFW停止之前会一直运行。下面的代码展示了一个非常简单的渲染循环：
```
while(!glfwWindowShouldClose(window))
{
    glfwSwapBuffers(window);
    glfwPollEvents();    
}
```

The **glfwWindowShouldClose** function checks at the start of each loop iteration if GLFW has been instructed to close, if so, the function returns true and the game loop stops running, after which we can close the application.The **glfwPollEvents** function checks if any events are triggered (like keyboard input or mouse movement events), updates the window state, and calls the corresponding functions (which we can set via callback methods). The **glfwSwapBuffers** will swap the color buffer (a large buffer that contains color values for each pixel in GLFW's window) that has been used to draw in during this iteration and show it as output to the screen.

**glfwWindowShouldClose**函数在每个循环迭代开始之前都去检查GLFW是否被通知关闭，如果是这样，函数将会返回true，游戏循环就会停止运行，然后我们就可以关闭这个应用程序了。**glfwPollEvents**检查是否有事件被触发（比如键盘输入或者鼠标移动等），更新窗口的状态，然后调用相应的函数（可以通过回调函数设置）。**glfwSwapBuffers**将会交换在这个迭代中已经被使用的颜色缓存（指包含了GLFW窗口上的每个像素点颜色值的缓存），然后将它输出显示在屏幕上。  

**Double buffer**
When an application draws in a single buffer the resulting image might display flickering issues. This is because the resulting output image is not drawn in an instant, but drawn pixel by pixel and usually from left to right and top to bottom. Because these images are not displayed at an instant to the user, but rather via a step by step generation the result may contain quite a few artifacts. To circumvent these issues, windowing applications apply a double buffer for rendering. The front buffer contains the final output image that is shown at the screen, while all the rendering commands draw to the back buffer. As soon as all the rendering commands are finished we swap the back buffer to the front buffer so the image is instantly displayed to the user, removing all the aforementioned artifacts.

**双缓冲**
当一个应用程序用一个单缓冲去绘制的时候，产生的图像可能会有闪烁的问题。这是因为生成的图像并不是立即绘制的，它通常是一个像素一个像素从左到右自上而下绘制的。因此这些图像并不是在统一时刻显示给用户的，而是通过一步一步生成结果显示的，这可能会看起来不自然。想要规避这些问题，我们使用双缓冲来绘制窗口应用程序。在所有绘制命令向后缓冲绘制的时候，前缓冲包含了已经显示在屏幕上的最终图像。一旦所有的绘制命令都完成后，我们会交换前缓冲和后缓冲的内容，这样图像就会立刻显示给用户，就不会产生前面提到的那种不自然的感觉了。

### 最后一件事(One last thing)
As soon as we exit the render loop we would like to properly clean/delete all resources that were allocated. We can do this via the **glfwTerminate** function that we call at the end of the **main** function.

一旦退出了渲染循环，我们需要正确的清理和删除所有我们之前申请的资源。我们可以通过在**main**函数的最后调用**glfwTerminate**函数来实现：
```
glfwTerminate();
return 0;
```
This will clean up all the resources and properly exit the application. Now try to compile your application and if everything went well you should see the following output:

这个操作会清理掉所有资源并且正确的退出应用程序。现在试着去编译你的程序吧，如果正常的话你将会看到下面的输出窗口：
![](/assets/hellowindow.png)

If it's a very dull and boring black image, you did things right! If you didn't get the right image or you're confused as to how everything fits together, check the full source code [here](https://learnopengl.com/code_viewer_gh.php?code=src/1.getting_started/1.1.hello_window/hello_window.cpp).

如果你看到了一个啥也没有的黑色背景图片，那么你就做对了！如果你没有看到或者比较困惑怎么把这些代码合适的组合到一起，那么点击[这里](https://learnopengl.com/code_viewer_gh.php?code=src/1.getting_started/1.1.hello_window/hello_window.cpp)查看完整的源代码。

If you have issues compiling the application, first make sure all your linker options are set correctly and that you properly included the right directories in your IDE (as explained in the previous tutorial). Also make sure your code is correct; you can easily verify it by looking at the source code. If you still have any issues, post a comment below with your issue and me and/or the community will try to help you.

如果你在编译这个程序的时候遇到了问题，首先确保你连接器里的选项都设置正确了，还有你的IDE也正确的包含了相关的目录（之前的教程有解释）。同时也要确保你的代码是正确的；通过查看提供的源代码你可以非常容易的验证这些。如果你仍然还有问题，在下面发送你的问题给我，或者到社区里请求帮助。

### 输入(Input)
We also want to have some form of input control in GLFW and we can achieve this several of GLFW's input functions. We'll be using GLFW's **glfwGetKey** function that takes the window as input together with a key. The function returns whether this key is currently being pressed. We're creating a **processInput** function to keep all input code organized:

我们有时也想要从GLFW获得一些输入的控制权，这是我们可以用GLFW的一些输入函数来实现这些需求。我们可以使用GLFW的**glfwGetKey**函数，它需要使用window对象和一个按键作为输入。该函数会返回当前的按键是否被按下。我们可以创建一个**processInput**函数来保持这些代码的整洁有序:
```
void processInput(GLFWwindow *window)
{
    if(glfwGetKey(window, GLFW_KEY_ESCAPE) == GLFW_PRESS)
        glfwSetWindowShouldClose(window, true);
}
```

Here we check whether the user has pressed the escape key (if it's not pressed, **glfwGetKey** returns **GLFW_RELEASE**). If the user did press the escape key, we close GLFW by setting its **WindowShouldClose** property to true using **glfwSetwindowShouldClose**. The next condition check of the main while loop will then fail and the application closes.

这里我们检查用户是否按下了返回键(Esc)（如果没有按下，**glfwGetKey**将会返回**GLFW_RELEASE**。如果用户的确按下了返回键，我们将通过**glfwSetwindowShouldClose**使用把**WindowShouldClose**属性设置为 true的方法关闭GLFW。下一次while循环的条件检测将会失败，程序将会关闭。

We then call **processInput** every iteration of the render loop:

我们接下来会在每个渲染循环的迭代中调用**processInput**函数：
```
while (!glfwWindowShouldClose(window))
{
    processInput(window);

    glfwSwapBuffers(window);
    glfwPollEvents();
}  
```
This gives us an easy way to check for specific key presses and react accordingly every frame.

这就给我们一个非常简单的方式来检测特定的键是否被按下，并在每一帧做出处理。

### 渲染(Rendering)
We want to place all the rendering commands in the render loop, since we want to execute all the rendering commands each iteration of the loop. This would look a bit like this:

我们想让渲染命令在渲染循环中进行渲染，因此我们让所有的渲染命令在每个循环迭代中执行。看起来有点像下面这样：
```
// render loop
while(!glfwWindowShouldClose(window))
{
    // input
    processInput(window);

    // rendering commands here
    ...

    // check and call events and swap the buffers
    glfwPollEvents();
    glfwSwapBuffers(window);
}
```
Just to test if things actually work we want to clear the screen with a color of our choice. At the start of each render iteration we always want to clear the screen otherwise we would still see the results from the previous iteration (this could be the effect you're looking for, but usually you don't). We can clear the screen's color buffer using the glClear function where we pass in buffer bits to specify which buffer we would like to clear. The possible bits we can set are GL_COLOR_BUFFER_BIT, GL_DEPTH_BUFFER_BIT and GL_STENCIL_BUFFER_BIT. Right now we only care about the color values so we only clear the color buffer.

为了测试一切都正常工作，我们使用一个自定义的颜色清空屏幕。在每个新的渲染迭代开始的时候我们总是希望清屏，否则我们仍能看见上一次迭代的渲染结果（这可能是你想要的效果，但通常这不是）。我们可以通过调用glClear函数来清空屏幕的颜色缓冲，它接受一个缓冲位(Buffer Bit)来指定要清空的缓冲，可能的缓冲位有GL_COLOR_BUFFER_BIT，GL_DEPTH_BUFFER_BIT和GL_STENCIL_BUFFER_BIT。由于现在我们只关心颜色值，所以我们只清空颜色缓冲：
```
glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
glClear(GL_COLOR_BUFFER_BIT);
```
Note that we also set a color via glClearColor to clear the screen with. Whenever we call glClear and clear the color buffer, the entire colorbuffer will be filled with the color as configured by glClearColor. This will result in a dark green-blueish color.

注意，除了glClear之外，我们还调用了glClearColor来设置清空屏幕所用的颜色。当调用glClear函数，清除颜色缓冲之后，整个颜色缓冲都会被填充为glClearColor里所设置的颜色。在这里，我们将屏幕设置为深蓝绿色。

As you might recall from the OpenGL tutorial, the glClearColor function is a state-setting function and glClear is a state-using function in that it uses the current state to retrieve the clearing color from.

你应该能够回忆起来我们在 OpenGL 这节教程的内容，glClearColor函数是一个状态设置函数，而glClear函数则是一个状态使用的函数，它使用了当前的状态来获取应该清除为的颜色。

![](/assets/hellowindow2.png)

The full source code of the application can be found [here](https://learnopengl.com/code_viewer_gh.php?code=src/1.getting_started/1.2.hello_window_clear/hello_window_clear.cpp).

这个程序的完整源代码可以在[这里](https://learnopengl.com/code_viewer_gh.php?code=src/1.getting_started/1.2.hello_window_clear/hello_window_clear.cpp)找到。

So right now we got everything ready to fill the game loop with lots of rendering calls, but that's for the [next tutorial](https://learnopengl-cn.github.io/01%20Getting%20started/04%20Hello%20Triangle/). I think we've been rambling long enough here.

好了，现在我们已经做好开始在渲染循环中添加许多渲染调用的准备了，但是那要在[下一节](https://learnopengl-cn.github.io/01%20Getting%20started/04%20Hello%20Triangle/)学习了。我认为我们需要在这多复习一下。





