#### 打造逆天 iTerm

1. 首先下载 [iTerm2](https://www.iterm2.com/)

2. 安装iTerm的一个深色语法主题

    下载 [Seti theme for iTerm]( https://github.com/willmanduffy/seti-iterm ) 放到桌⾯,  
    打开iterm2 -> preferences -> profiles -> colors -> colors presets -> import 选中刚才文件夹中的seti.itermcolors文件

3. 让命令行变色

    命令行打开`.bash_profile`文件

    - open ~/.bash_profile
    - vim ~/.bash_profile

    然后把下面的代码粘贴进去

    ```bash
    export CLICOLOR=1
    export LSCOLORS=GxFxCxDxBxegedabagaced
    export PS1='\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\
    [\033[00m\]\$ '
    ```

4. 找到喜欢的终端字符图案，比如下面小手枪，新建个文件如 welcome.txt (下面要用)

    ```
    ,--^----------,--------,-----,-------^--,
    | |||||||||   `--------'     |          O
    `+---------------------------^----------|
        `_,-------, _________________________|
        / XXXXXX /`|     /
       / XXXXXX /`      / 
      / XXXXXX /_______(
     / XXXXXX /
    / XXXXXX /
    (________( ❄ 十万个为什么 @ 一问三不知 ❅ ☼ `------'
    ```

5. 让字符变得漂亮起来，彩虹色

    下载 [lolcat](https://github.com/busyloop/lolcat)  
    命令行下载 `gem install lolcat`，可能需要加 sudo  
    然后命令行打开`.bash_profile`文件, 将` lolcat ~/Desktop/welcome.txt `添加进去，就可以看到小手枪了

6. 下载安装字体库 [Powerline fonts](https://github.com/powerline/fonts)

    ```bash
    # clone
    git clone https://github.com/powerline/fonts.git --depth=1
    # install
    cd fonts
    ./install.sh
    # clean-up a bit
    cd ..
    rm -rf fonts
    ```

    打开iterm2 -> preferences -> profiles -> Text -> Font -> Change Font 挑选喜欢的字体，  
    我这里`14pt Meslo LG L DZ Regular for Powerline`

7. 安装 [Oh-My-ZSH](https://github.com/robbyrussell/oh-my-zsh)

    命令行安装`sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"`

    通过`echo $SHELL`可以查看当前系统默认shell  
    通过`cat /etc/shells `可以查看系统中安装的shell
    通过 `chsh -s /bin/zsh`可以设置默认的shell为Oh-My-ZSH 

    Mac环境下默认的shell为Bash$，这个时候你可能发现` lolcat ~/Desktop/welcome.txt` 这行命令的效果不生效了。这个是因为环境变量改变成zsh shell了，需要`open ~/.zshrc`把` lolcat ~/Desktop/welcome.txt`放在`.zshrc`里面

    
8.  换主题

    命令行打开`.zshrc`文件 `open ~/.zshrc` 找到ZSH_THEME修改为你想要的主题即可
    默认主题是 `ZSH_THEME="robbyrussell"` 我修改成 `ZSH_THEME="agnoster"`

9.  设置背景图片
    
    iTerm2 -> Preferences -> Profiles -> Window -> BackGround Image 勾选图片即可。
