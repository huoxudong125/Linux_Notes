##vim 的安装

VI是LINUX下最老牌的编辑工具，而VIM则是VI的升级版本，LINUX系统必备啊

在命令行敲入`vi`后按`tab`键，可以看到目前系统中只安装了`vi`和`vim.tiny`。

>vim是从VI发展而来的一个文本编辑器，功能更强大。而vim.tiny是vim的精简版，所以，安装vim势在必行。

ubuntu系统：
普通用户下输入命令：
```
sudo apt-get install vim-gtk
```
centos系统：
普通用户下输入命令：
```
yum -y install vim*
```

***

安装完成之后，在命令行敲入`vi`，按`tab`键。
可以看到，已经有`vim`命令的存在。

安装成功。

##解决Ubuntu vim 中文乱码
打开vim的配置文件，位置在 `/etc/vim/vimrc` 在其中加入
```
set fileencodings=utf-8,gb2312,gbk,gb18030
set termencoding=utf-8
set encoding=prc
```

保存退出，此时vim就能正确显示中文了。

对于gedit，解决方法如下：

在终端中运行 gconf-editor 
在打开的界面中选择： apps->gedit-2->preferences->encodings 
在右边的 `auto_detected` 和 `shown_in_menu` 上点右键编辑，点 add，分别加入GB2312(或者是GB18030）,并点 UP按钮移动到第一位。关闭，就可以在gedit中显示中文了.

