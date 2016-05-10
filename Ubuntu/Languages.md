在UBUNTU下实现这个也很简单，你不用去改系统的默认编码，也不用改Gedit的默认编码。

系统支持编码的修改如下：

1.
cat /usr/share/i18n/SUPPORTED
说明：查看系统支持的字符集，你需要注意的是支持字符集的格式，如对中文会有以下一些显示（我的系统如此，我不知是否普遍）
```
zh_CN.GB18030 GB18030
zh_CN.GBK GBK
zh_CN.UTF-8 UTF-8
zh_CN GB2312
```

2.
```
sudo vim /var/lib/locales/supported.d/local`
```
说明：打开系统字符集配置文件，将支持的中文字符集添加进去，格式如1中得到所示。

3.
```
sudo locale-gen
```
说明：更新。如果2中添加正确应该没有问题，如果出问题再次编辑2，后再3直至解决。如果正确此时应该可以使用VIM查看GBK编码的文件了，没有编码。但此时用Gedit还不可以，现在我们添加Gedit的字符集支持。

1.
gconf-editor
打开Gnome配置编辑器

2.
app/gedit/preferences/encodings

修改键值
auto_detected
添加入GBK，GB2312，GB18030

如果操作成功现在Gedit也没乱码了。

对于播放器中的乱码建议将播放器重置，重新添加所有音乐。


***

设置中文环境也比较简单，需要三步。  
##第一步，安装中文包：

sudo apt-get install language-pack-zh language-pack-zh-base  
有朋友留言反馈到应该执行下面的命令，我现在已经用Ubuntu12.10，未测试  

sudo apt-get install language-pack-zh-hant language-pack-zh-hans

##第二步，配置相关环境变量：

sudo vim /etc/environment

在文件中增加语言和编码的设置：

LANG="zh_CN.UTF-8"  
LANGUAGE="zh_CN:zh:en_US:en"

我的配置文件显示如下：  

PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games"  
LANG="zh_CN.UTF-8"  
UNZIP="-O CP936"  
ZIPINFO="-O CP936"

##第三步，重新设置本地配置：

sudo dpkg-reconfigure locales


另外就是要有一个支持utf8的终端软件，可参考文档SSH Secure Shell Client的替代方案，这是解决ssh命令行登录和sftp文件传输的。我喜欢用SecureCRT，sz和rz命令异常好用，当然，这只是客户端和服务器之间的交互。服务器与服务器之间还是scp来得方便。


