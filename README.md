# super-light-REPLs-IDE-in-pure-text-UI
# 超轻量级纯文本界面 REPL 类语言 IDE

##  目录

- [介绍](#介绍)
- [特性](#特性)
- [实现原理](#实现原理)
- [安装插件](#安装插件)
- [配置方法](#配置方法)
- [使用说明](#使用说明)
- [其他信息](#其他信息)


##  介绍

这是基于 `Vim + Tmux` 快速定制的一个针对 `REPL` 类编程语言的超轻量级开发环境, 不需要任何 `GUI`, 运行于纯粹的文本界面, 所以响应速度很快, 而且对于系统的要求很低, 在一些内存有限的旧电脑上也可以流畅地运行. 

原本的目标是搭建一个可以流畅运行于 `Raspberry Pi 2` 的超轻量级 `Common Lisp` 调试开发环境, 用户可以通过 `SSH` 登录到树莓派上进行不受限制的开发, 最大限度地利用树莓派的硬件计算能力, 而不需要启动任何类型的 `视窗`. 

后来发现本集成环境可以支持任何一种拥有 `REPL` 机制的编程语言, 如 `Clojure`, `Racket`, `Schema` 以及 `Lua` 等多种语言.

环境需求: 需要安装好 `vim` 和 `tmux`.

技能要求: 需要使用者熟悉基本的 `vi` 操作, 熟悉一般的 `tmux` 操作.

##  特性

优点:
- 方便
- 超轻量级
- 高度可定制
- 响应速度最快
- 使用超级流畅
- 支持结对编程
- 运行资源需求最少
- 可运行于内存有限的旧电脑
- 全平台支持:Linux|Win32|OSX
- 支持任意拥有命令行 REPL 机制的语言
- 支持快捷键从代码编辑区发送代码到REPL区进行调试

缺点:
- 跟 `Emacs` 下的 `Slime` 相比快捷键支持的调试命令比较少

##  实现原理

利用了 `tmux` 可以在在不同窗口|面板间传输|接收数据的能力

##  安装插件

需要为 `vim` 按顺序依次安装如下插件:

必需安装:
[pathogen](https://github.com/tpope/vim-pathogen)
[rainbow-parentheses](https://github.com/kien/rainbow_parentheses.vim)
[vim-slime](https://github.com/jpalardy/vim-slime)

可选安装:
[vim-clojure-static](https://github.com/guns/vim-clojure-static)

安装 `pathogen`:

首先为 `vim` 安装 `pathogen` 插件, 有了它可以超级简单地安装设置其他插件.

分别建立 ~/.vim/bundle,  ~/.vim/autoload 目录

  cd ~/.vim
  mkdir bundle
  mkdir autoload

把 pathogen.vim 文件下载到 autoload 目录下

  cd ~/.vim/autoload
  wget https://tpo.pe/pathogen.vim

安装 `rainbow-parentheses` 和 `vim-slime` :

进入 `~/.vim/bundle/` 目录, 克隆 `rainbow-parentheses` 和 `vim-slime` 即可, 剩余的 `pathogen` 会自动为你安装:

  cd ~/.vim/bundle/
  git clone https://github.com/kien/rainbow_parentheses.vim
  git clone https://github.com/jpalardy/vim-slime
  
安装 `vim-clojure-static`, 跟上面的步骤一样:

  cd ~/.vim/bundle/
  git clone https://github.com/guns/vim-clojure-static   

接下来要对上述插件进行配置, 下面会详细介绍如何配置.

##  配置方法

需要分别对 `vim` 和 `tmux` 进行配置, 主要是修改位于 `~/` 目录下的 `.vimrc` 和 `.tmux.conf` 这两个文件.

下面是配置好的文件, 把你原来的这两个文件做一个备份, 然后把这两个文件直接克隆到你的 `~/` 目录下.

- .vimrc

```
execute pathogen#infect()
syntax on
syntax enable
set number
set autoindent
filetype plugin indent on

“ vim-slime  options
“ Clojure options.
let g:slime_target = "tmux"
let g:slime_paste_file = "$HOME/.slime_paste"

“ rainbow_parentheses options
autocmd Syntax clojure RainbowParenthesesLoadRound
autocmd BufEnter *.clj RainbowParenthesesToggle
autocmd BufLeave *.clj RainbowParenthesesToggle

let g:rbpt_colorpairs = [
    \ ['magenta',     'purple1'],
    \ ['cyan',        'magenta1'],
    \ ['green',       'slateblue1'],
    \ ['yellow',      'cyan1'],
    \ ['red',         'springgreen1'],
    \ ['magenta',     'green1'],
    \ ['cyan',        'greenyellow'],
    \ ['green',       'yellow1'],
    \ ['yellow',      'orange1'],
    \ ]
let g:rbpt_max =9

“ vim-clojure-static options
let g:clojure_syntax_keywords = {
    \ 'clojureMacro': ["defproject", "defcustom"],
    \ 'clojureFunc': ["string/join", "string/replace"]
    \ }
```

- .tmux.conf

```
# ##############################################################
# Global settings.
#

# Switch the prefix to Ctrl-a since Ctrl-b interferes with Vim.
# set -g prefix C-a
# unbind C-b

# Change the default input delay in order to improve Vim
# performance.
set -sg escape-time 1

# Number windows and panes starting at 1 so that we can jump to
# them easier.
# 设置 tmux 窗口序号从 1 开始, 设置 tmux 面板序号从 1 开始
set -g base-index 1
set -g pane-base-index 1

# vi mode.
set -g mode-keys vi
set -g status-keys vi

# 256-color mode.
set -g default-terminal "screen-256color"


# ##############################################################
# Key bindings.
#

# Reload .tmux.conf with "r".
# 修改 tmux 配置后加载新配置文件的快捷键为: Ctrl-b r
bind r source-file ~/.tmux.conf \; display "Reloaded!"

# Split windows with a more visual set of characters.
bind | split-window -h
bind - split-window -v

# Select panes with vi-style movement commands.
# 使用 h j k l 选择面板, 也可以用上下左右箭头来选择
# 具体快捷键为: Ctrl-b h -> 左边面板
bind h select-pane -L
bind j select-pane -D
bind k select-pane -U
bind l select-pane -R

# Resize panes with vi-style movement commands.
# 调整面板大小
# 具体快捷键为: 
bind -r H resize-pane -L 5
bind -r J resize-pane -D 5
bind -r K resize-pane -U 5
bind -r L resize-pane -R 5
```

##  使用说明

### 编程实例:

假设我们准备试验 `Common Lisp` 开发环境, 具体的实现是 `CLISP`.

启动 `tmux`, 在当前窗口创建两个面板(`pane`), 快捷键为: `C-b c`, 也就是: `Ctrl-b c`;
在 1 号面板里用 `vi` 编辑一个名为 `test.lisp` 的文件;
在 2 号面板内启动一个 `REPL`, 例如: `clisp`. 

补充: `C-b q` 可以查看当前窗口所有面板的编号

现在就可以进行 `Common Lisp` 代码调试了, 光标放在代码编辑区(切换面板的快捷键为 `C-b o`), 输入一段代码,如下:

  (+ 123 245)
  
光标保持在括号内, 进入 `vi` 命令模式, 输入快捷键 `C-c C-c`, 首次执行此命令, 会要求设定目标, 提示输入内容如下:

  tmux socket name: default `回车`
  tmux target pane: `:2.2`    
  
`:2` 表示 2 号窗口, `.2` 表示2号窗口的第2个面板

这样就会把表达式 `(+ 123 245)` 发送到 2 号面板的 `REPL`, 马上就会被求值

默认的快捷键绑定:

  C-c C-c 把编辑区光标所在处的代码段落发送到 REPL 区
  C-c v 重新设定发送目标, 需要按照提示输入:
  tmux socket name: default `回车`
  tmux target pane: `:2.2`

":" 单独输入一个冒号表示当前窗口,当前面板, 这是合理的默认值;
":i" 冒号跟一个数字 i 表示第 i 个窗口, 当前面板;
":i.j" 冒号 数字i 小数点 数字 j 表示第 i 个窗口,第 j 个面板;
"h:i.j" 如果前面再加一个 h, h 表示 `tmux` 的 `h` 号会话, `h` 可能是会话名,也可能是数字, 后面的含义不变.

### 一些有用的快捷键

`vim` 的快捷键(命令状态下输入):

`C-f 和 C-b` 向后或向前翻页
`C-p 和 C-n` `vi`自带的简单自动补全

`tmux`的快捷键:

`C-b r`   修改 `.tmux.conf` 文件后快速加载
`C-b 数字`  跳转到数字对应窗口
`C-b 空格键` 调整窗口的面板布局
`C-b |` 或 `C-b "` 新建面板跟当前面板左右分布(水平分布)
`C-b -` 或 `C-b %` 新建面板跟当前面板上下分布(垂直分布)

##  其他信息

本项目参考了如下文档:

- [Vim + tmux + Clojure](http://michaelalynmiller.com/)
- [Programming Scheme(Racket) with VIM - How to get started](http://stackoverflow.com/questions/5352997/programming-schemeracket-with-vim-how-to-get-started)
- [tmux and vim for Scheme development](http://blog.suriar.net/2013/12/tmux-and-vim-for-scheme-development.html)
- [GNU screen 替代品 Tmux 的常用命令详解](http://blog.sina.com.cn/s/blog_a0db295e0100vznq.html)
- [Vim 配置详解](http://blog.csdn.net/ruglcc/article/details/7804297)

