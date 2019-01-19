# HUDBT
---

  这个项目首先是得益于之前活跃在各大pt站的人的无私贡献以及帮助，在此感谢各位大佬。

  跟其他人不一样的是，这里没有做成全自动的机器人。弄这个的初衷就是发布的时候写简介太麻烦，然后就想一键发种。

  目前只是针对蝴蝶发布，项目还有很多需要改进的地方，因为也是边学边做，有的代码可能过于臃肿，希望大家提出指导意见。

---

## 说明：

    1. 由于半自动，采用的是ut的观察目录，所以所有的种子需要下载到指定目录，如：save_path = "E:\\发种测试"

    2. 种子下载之后ut自动下载，然后等待发布，需要有一个异步通信的机制：

    考虑过几个方法：最后采用的步骤：（拿服务器和客户端作比喻）

        * 开启服务器端，根据种子详情链接下载种子，获取种子名称，并且转换一下（蝴蝶不支持中文等），解析出种子的目录结构。

        * ut在完成时，可以启动程序hudbt.bat，开启一个客户端。

          在ut设置-->高级-->运行程序-->当下载完成时运行此程序，如："*\hudbt.bat" "%D" "%N" （*表示程序所在绝对路径，返回参数根据说明进行设置）

        * 两者通信，服务器端开始解析网页，获取发布需要的信息。原来用的是socket机制，后来改成中间文件，类似于ut的观察目录。

        * 上传种子到蝴蝶并且完成做种。

    3. 获取网页以及种子需要cookie信息，这里采用的是 selenium + chromedriver 一键获取，当然也可以手动获取或者别的方法。

    4. 后期为了支持并发，采用多线程，并且用tkinter写了一个界面。


## 目录文件以及功能说明：

    | HUDBT

        | icon
            | bitbug_favicon # tkinter图标

        | parser_origin # 用来解析网页获取发布信息
            | __init__
            | {site}.py # 对支持的站点进行解析
            | get_descr # 如果获取到豆瓣链接，简介自动生成（包含mediainfo和视频截图信息），否则使用源网页信息

        | client，很简单，当ut下载完后，会调用hudbt.bat文件进入项目所在目录，开启客户端在服务器的观察目录下写入一个txt文件，检测到了之后进行后续操作

        | config 对一些路径和常用项进行存储

        | get_cookies 使用selenium获取cookies，生成 pt_sites.json. 使用其他方法生成该文档需要参考一下存储的数据结构

        | get_mediainfo 根据一个视频文件的绝对路径获取能够用到的mediainfo

        | get_picture 根据视频文件的绝对路径获取视频截图并且上传到指定图床获取链接生成对应的bb_code

        | parser_torrent 根据一个种子文件的绝对路径获取种子的信息，如果有多个文件，返回目录和某一个视频文件的信息，如果单文件，两者都指向视频文件绝对路径

        | server_multi 直接运行采用单线程，但是加入了多线程机制，由于输入输出混乱，需要一个输入界面

        | tk_server 用于启动程序，开启线程时调用server_multi.

## 不足之处：

   其实不足的地方有很多，通信机制一般，有点绕，然后开启客户端程序时会弹出黑框一闪而过，最后就是所有的输入输出还是在控制台，调用ffmpeg截图的时候会有一堆东西输出。

   添加任务之后缺乏任务管理，后期会在界面上作一个任务管理。

## 测试环境：win10+ut2.2.1

## 使用：直接运行打开server_multi(单线程)，或者tk_server，会开线程执行(多线程)
