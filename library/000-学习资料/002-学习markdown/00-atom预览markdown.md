From: <http://www.jianshu.com/p/ad3e737e5dc2>

[Atom][1]是github开发的**开源跨平台**的编辑器，Atom的强大可以与大名鼎鼎的Sublime Text相媲美。因为使用过Sublime Text，所以用Atom上手很快。这篇文章主要介绍使用Atom写markdown。  
之前在项目开发中都是使用.doc文件作为接口文档的载体，但是在使用中并不能很好的对比接口修改；如果使用.txt文件，有没有醒目的格式。所以，最后我们选择语法简单，方便对比的markdown来作为接口文档的载体。markdown的语法不多做介绍，主要介绍下书写和解析markdown的编辑器。

   [1]: https://github.com/atom/atom

任何文本编辑器都可以书写markdown，但我们更期望能够在书写的时候能够即时的看到解析效果，方便对格式进行调整。

在mac os 下，我推荐Mou，界面简洁优雅，解析流畅，也许没有更好的了。

**Sublime Text**：有markdown preview插件支持，能够在浏览器里查看编译效果，但是并不是实时的，需要在修改后进行刷新或者，并不方便。  
**mardown pad**：能够很好的书写，并支持预览。但free版竟然不支持表格的解析！pro版可以选择解析的theme，如github等，是否能够解析表格不得而知。  
**Atom**：**推荐使用**。内置对markdown的支持，能够方便的进行解析预览。

1、打开任意.md文件(markdown源文件)  
2、  
**windows** : `ctrl + shift + p`  
**mac** : `command + shift + p`  
这条命令跟Sublime Text是一样的，打开命令输入框  
3、输入 `markdown preview toggle`(可以偷懒只输入`mdpt`，跟Sublime Text一样支持模糊匹配)  
4、按enter键即可看到预览，如图，左边编辑，右边实时预览。  


![][2]  


   [2]: http://upload-images.jianshu.io/upload_images/276844-f9d3c49656f0afd2.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240

命令输入框




![][3]  


   [3]: http://upload-images.jianshu.io/upload_images/276844-8760e860544540b4.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240

预览效果

其实，markdown preview是Atom的一个插件，查看步骤如下：  
1、  
**windows** : `ctrl + shift + p`  
**mac** : `command + shift + p`  
打开命令输入框，输入 `settings view view installed packages`  
2、在筛选输入框中，写`markdown`,即可看到。

![][4]  


   [4]: http://upload-images.jianshu.io/upload_images/276844-2a4cae4805cdccf8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240

markdown preview

下面我再介绍一个小插件，用来格式化从服务端请求来的json数据，`pretty json`。  
操作步骤：  
1、  
**mac** : `ctrl + shift + p`  
**windows** : `command + shift + p`  
打开命令输入框 `settings view install packages and themes`(也可以简单的输入`install package`)

2、在搜索框输入`pretty json`，点击**Packages**按钮，在搜索结果中，点击`Install`按钮即可安装。  


![][5]  


   [5]: http://upload-images.jianshu.io/upload_images/276844-9186cc4edf81cdc8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240

搜索`pretty json`


图中是我已经安装后的样子。  
3、选中一段从服务器下载的json，如


    {"result":{"error":0,"type":2000},"sync":{"newdata":null,"lastver":"0","newnum":"0"}}


4、  
**windows** : `ctrl + shift + p`  
**mac** : `command + shift + p`  
打开命令输入框 输入 `pretty json prettify`  


![][6]  


   [6]: http://upload-images.jianshu.io/upload_images/276844-d65e7a827ec4f4a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240

输入 `pretty json prettify`


5、点击确定，即可  


![][7]  


   [7]: http://upload-images.jianshu.io/upload_images/276844-a0042409c31126ab.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240

结果

Atom有更多的插件可以使我们的工作更加便捷，我们应该善于利用工具提高效率。
