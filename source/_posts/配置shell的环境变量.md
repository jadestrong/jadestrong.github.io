---
title: 配置shell的环境变量
nano: 2016-09-24 16:24:33
tags: emacs shell env
---

在配置npm的时候，为了解决使用npm安装包的权限问题，我们将所安装包都统一安装到了`~/.npm-global`目录下，然后通过配置文件使得添加环境变量使得终端可以读取到npm安全的全局命令：
``` shell
#创建存放文件夹
mkdir ~/.npm-gloabl
#配置npm使用该文件夹
npm set prefix '~/.npm-global'
#打开或着创建一个~/.profile文件，添加如下命令
export PATH=~/.npm-global/bin:$PATH
#回到命令行，更新系统变量
source ~/.profile
```
解读一下上面的命令：
`$PATH`就是系统的命令地址等，而export句命令是将 `~/.npm-global/bin`拼接到已有的路径上，再重新赋值给该变量。 `:`是变量之间的分割符。

上面都是配置到 `~/.profile`文件下的，系统会到启动时读取该文件，所以系统的终端程序能够读取里面的配置。但是在emacs中嵌入的shell却读取不到在该文件中配置的语句，所以我在 `~/.bashrc`文件下，把上面那句加上了，这样每个shell终端启动应该都可以读取这个配置。

另外一个问题是，emacs内置的eshell程序却只能读取到系统级别的命令，如 `/usr/bin`等目录下的命令，所以通过npm指定的命令还是不起作用，目前可以找到的方法是为emacs添加环境变量来解决，但怎么加，还得再研究下。
由于上面这个问题导致的，通过npm安装的全局命令如`js-beautify`等命令不能使用，例如在`web-beautify`这个elisp插件会调用该命令去处理文件的格式化，由于访问不到该命令所以会失败，提示让通过 `npm -g install js-beautify`来安装，但是我们已经安装了，所以还是需要继续解决这个问题。

注：在检查环境变量时，在命令行中直接的输入 `env`即可查看。

这两个问题都需要设计emacs的环境变量来解决：改环境变量有两种方法，一种是使用 `exec-path-from-shell`，然而本人并没有改成功，应该是没弄明白文档中是怎么用的，另一种是使用`Exec Path`,也就是直接在配置文件中设置环境变量，如 `.emacs`或 `init.el`,又或者是supacemacs的`.init.el`的user-config中设置如下：
``` emacs-lisp
(setqenv "PATH" (concat (getenv "PATH") ":/home/jadestong/.npm-global/bin"))
(setq exec-path (append exec-path '("/home/jadestrong/.npm-global/bin")))
```
上面设置了两个变量，一个是PATH，这个是给shell程序使用的，也就是eshell以及emacs中的shell都可以调用npm安装的全局命令了，有了这个应该就不用再修改 `.bashrc`文件了；另一个exec-path就是给elisp程序使用的了，对于一些如locate等系统命令都是走的这条路，所以在使用 `js-beautify`等命令时都需要设置这个变量。
