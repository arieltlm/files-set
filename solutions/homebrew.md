# Homebrew 安装


下面这个命令比较快好使，选择中科大的；

```bash
/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)"
```

[苹果M1 Mac上怎样使用Homebrew？](https://www.jianshu.com/p/ef3d53a9f0b0)

[Apple Mac M1芯片 使用问题汇总](https://www.jianshu.com/p/c1c1e0e52391)

[M1 Mac安装 Homebrew](https://blog.csdn.net/csdn2314/article/details/110952637)这个方法我没有使用，因为初始已经按照原来的方法装在usr路径下了，用了上面两个链接的方法处理了；

***
下面这个我开始使用的，也好使——
【引用-侵权可删】：[mac下镜像飞速安装Homebrew教程-知乎](https://zhuanlan.zhihu.com/p/90508170)

Homebrew是一款包管理工具，目前支持`macOS`和`linux`系统。主要有四个部分组成: `brew`、`homebrew-core` 、`homebrew-cask`、`homebrew-bottles`

提前设置`homebrew-core`镜像源并执行：

```bash
# 中科大
HOMEBREW_CORE_GIT_REMOTE=https://mirrors.ustc.edu.cn/homebrew-core.git

/bin/bash -c "$(curl -fsSL https://cdn.jsdelivr.net/gh/ineo6/homebrew-install/install.sh)"
```



原来的源raw.githubusercontent.com特别慢，科学上网才行，so切换

在安装过程中，遇到了

```bash
Error: 
  homebrew-core is a shallow clone.
  homebrew-cask is a shallow clone.
```

此时将可能之前安装的删掉再安装/更新就行了：

```bash
 rm -rf /usr/local/Homebrew/Library/Taps/homebrew/homebrew-core
 rm -rf /usr/local/Homebrew/Library/Taps/homebrew/homebrew-cask
```

如果命令执行中卡在下面信息（如提示有差异，请反馈给我）：

```bash
==> Tapping homebrew/core
Cloning into '/usr/local/Homebrew/Library/Taps/homebrew/homebrew-core'...
```

请Control + C中断脚本执行如下命令：

```bash
cd "$(brew --repo)/Library/Taps/"
mkdir homebrew && cd homebrew
git clone git://mirrors.ustc.edu.cn/homebrew-core.git
```

cask 同样也有安装失败或者卡住的问题，解决方法也是一样：

```bash
cd "$(brew --repo)/Library/Taps/"
cd homebrew
git clone https://mirrors.ustc.edu.cn/homebrew-cask.git
```

成功执行之后继续执行前文的安装命令:

```bash
/bin/bash -c "$(curl -fsSL https://cdn.jsdelivr.net/gh/ineo6/homebrew-install/install.sh)"
```

最后看到==> Installation successful!就说明安装成功了



**中科大源**：

```bash
git -C "$(brew --repo)" remote set-url origin https://mirrors.ustc.edu.cn/brew.git

git -C "$(brew --repo homebrew/core)" remote set-url origin https://mirrors.ustc.edu.cn/homebrew-core.git

git -C "$(brew --repo homebrew/cask)" remote set-url origin https://mirrors.ustc.edu.cn/homebrew-cask.git

brew update

# 长期替换homebrew-bottles
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles' >> ~/.bash_profile
source ~/.bash_profile
```

注意`bottles`可以临时设置，在终端执行下面命令：

```bash
export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles
```



**清华大学源**：

```bash
git -C "$(brew --repo)" remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/brew.git

git -C "$(brew --repo homebrew/core)" remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-core.git

git -C "$(brew --repo homebrew/cask)" remote set-url origin https://mirrors.tuna.tsinghua.edu.cn/git/homebrew/homebrew-cask.git

brew update

# 长期替换homebrew-bottles
echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.tuna.tsinghua.edu.cn/homebrew-bottles' >> ~/.bash_profile
source ~/.bash_profile
```

**恢复默认源**：

```bash
git -C "$(brew --repo)" remote set-url origin https://github.com/Homebrew/brew.git

git -C "$(brew --repo homebrew/core)" remote set-url origin https://github.com/Homebrew/homebrew-core.git

git -C "$(brew --repo homebrew/cask)" remote set-url origin https://github.com/Homebrew/homebrew-cask.git

brew update
```

`homebrew-bottles`配置只能手动删除，将 `~/.bash_profile` 文件中的 `HOMEBREW_BOTTLE_DOMAIN=https://mirrors.xxx.com`内容删除，并执行 `source ~/.bash_profile`。