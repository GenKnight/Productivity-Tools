# Emacs-Diary

记录我学习 Emacs 的过程

## 在 ubuntu 16.04 上安装 Emacs25

安装命令

```shell
sudo add-apt-repository ppa:kelleyk/emacs
sudo apt update
sudo apt install emacs25
```

删除命令

```shell
sudo apt remove emacs25 && sudo apt autoremove
```

来源 <http://ubuntuhandbook.org/index.php/2017/04/install-emacs-25-ppa-ubuntu-16-04-14-04/>

## 阅读 《Emacs 快速指南.》

快捷键记录

```text
C-x C-c 退出 Emacs
C-g     退出一个正在运行中的命令

C-v     向前移动一屏
M-v     向后移动一屏
C-l     重绘屏幕，并将光标所在行置于屏幕的中央 （注意是 CONTROL-L，不是 CONTROL-1）

C-f     向右移动一个字符
C-b     向左移动一个字符

M-f     向右移动一个词【对中文是移动到下一个标点符号】
M-b     向左移动一个词【对中文是移动到上一个标点符号】

C-n     移动到下一行
C-p     移动到上一行

C-a     移动到行首
C-e     移动到行尾

M-a     移动到句首
M-e     移动到句尾

<DEL>   删除光标前的一个字符
C-d     删除光标上的一个字符

M-<DEL> 移除光标前的一个词
M-d     移除光标上的一个词

C-k     移除从光标到“行尾”间的字符
M-k     移除从光标到“句尾”间的字符
```