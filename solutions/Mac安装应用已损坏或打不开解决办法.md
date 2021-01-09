# Mac打开应用提示已损坏怎么办 

[Mac打开应用提示已损坏怎么办 Mac安装软件时提示已损坏怎么办](http://www.pc6.com/edu/168719.html)

**sudo xattr -r -d com.apple.quarantine ,然后输入个空格，再将应用程序目录中的软件拖拽到命令后面，按回车后输入密码执行，比如需要打开sketch应用的命令是**

```bash
sudo xattr -r -d com.apple.quarantine /Applications/sketch.app/
```

**打开应用提示已损坏是因为新系统的原因。新的系统为了加强安全机制，默认不允许用户自行下载安装应用程序，只能从Mac App Store里安装应用。**

# Mac打开应用提示打不开

1.**因为它来自身份不明的开发者**

2.**无法打开“xxx”,因为Apple无法检查其是否包含恶意软件**

解决方法均如下：

设置增加任何来源选项：

```bash
sudo spctl --master-disable
```

打开系统偏好设置 » 安全性与隐私，若显示任何来源，大功告成

在“系统偏好设置—安全性与隐私—通用—允许从以下位置下载的应用”选择“仍要打开”

> 苹果 macOS Catalina 系统打开软件出现：无法打开“XXXX”，因为Apple无法检查其是否包含恶意软件。怎么解决?
>
> 这是新系统 macOS Catalina 版本才有的提示，其实这个提示跟10.13、10.14出现的：“XXXX”已损坏，打不开。您应该将它移到废纸娄。是一样的，到了 Catalina 系统上变了提示文字而已。。。

