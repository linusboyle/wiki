# xpra

xpra类似于screen和tmux，能够创建虚拟的X服务器，并保持X Server的持续运行，允许从本机或远程建立和断开与X Server的连接。在连接断开的时候，运行在xpra虚拟display里的程序会继续运行，而不会像ssh -X那样必须停止。

xpra完全可以替代一些远程桌面（当然，必须要基于X才行）。一般在远程服务器上创建一个xpra服务并运行一个xterm，就可以在本机打开各类图形软件。

创建一个新的display，并让xpra管理：`xpra start <display号>`或者`xpra start ssh:host:display`在服务器创建一个display。用--start参数指定在display里启动的程序。

用`xpra attach ssh/user@host:port/DISPLAY`连接xpra 服务，那么在虚拟display上的窗口都会在本地展示。终止本地的xpra进程就可以断开连接了。

xpra可以用来救急，但是服务器的显示效果一般不好，而且xpra会做一些压缩来提升性能。所以适合运行一些只有图形界面，但是不关心交互的程序（比如我跑的单点认证的Seafile挂载盘）