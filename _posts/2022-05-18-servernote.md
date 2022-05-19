---
layout: post
title:  在服务器上运行深度学习代码的一些tips
date: 2022-5-05
categories: blog
tags: [tips,标签二]
description: 基于学习的MVS方法的经典pipeline
---
### 查看显卡实时情况
#### 第一种方案
`watch -n 2 -d nvidia-smi`<br>
每两秒刷新一次,每次在终端的固定位置刷新<br>
+ wtach命令每个时间周期开启一次进程(PID)查看后再关闭进程,会影响cuda操作,比如cudaMalloc
#### 第二种方案
`nvidia-smi --loop=2`<br>
每两秒刷新一次,每次会接着上一个往下刷新,将持续扩张终端,不便于查看

### 远程训练模型
为了防止SSH连接中断后(关闭连接SSH的终端或者网络中断),训练过程随之中断,推荐使用screen命令<br>
通过screen命令,可以让终端前端显示,后台存活,从而解决了上面提到的问题(当然如nohup等命令亦可)<br>
#### 新建终端
`screen`命令即可新建终端,但是这样建立的终端命名混乱<br>
`screen -R MyScreen`命令可以指定建立一个名字为MyScreen的终端,同时会检查是否已存在名为MyScreen的终端,如果有,则进入之前的名为MyScreen的终端<br>
`screen -S MyScreen`命令可以指定建立一个名字为MyScreen的终端,但不像上一条命令,它并不会检查是否有重名终端.也就是说,此命令可能会创建重名终端<br>
#### 返回终端
退出之前创建的终端后,需要之后重新进入终端查看进度和清除终端.因此需要返回之前的终端<br>
`screen -R MyScreen`和`screen -r MyScreen`命令均可返回之前创建的名为MyScreen的终端.当然,如果有同名的终端(我觉得还是不创建同名终端为好),也可以使用终端ID返回终端<br>
`screen -R/-r terminal_ID`
#### 查看所有终端
如果遗忘了自己创建的终端名字或想查询终端ID,则需要使用<br>
`screen -ls`<br>
#### 清除终端
进入之前的终端后,使用`exit`指令即可清除终端<br>
#### 其它指令
使用Ctrl+A+D即可返回主终端而不清除虚拟终端<br>
