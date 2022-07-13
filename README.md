# AniYa-免杀框架

## 免责声明

该工具仅用于安全研究，禁止使用工具发起非法攻击等违法行为，造成的后果使用者负责

## 介绍

`Golang`免杀马生成工具，在重复造轮子的基础上尽可能多一点自己的东西，最重要的loader部分参考其他作者。

相较其他免杀工具具备以下优势：

1. 使用`fyne`的GUI界面，不算难看，简单易懂，还有个炫酷的进度条！wakuwaku(*^▽^*)
2. 可自定义多种反沙箱，其中检查微信的适合钓鱼
3. 可自定义多种编译选项，支持garble编译环境
4. 分离免杀
5. 支持打包PE文件（如`mimikatz`）

## 使用

![image-20220711114540147](https://github.com/piiperxyz/AniYa/blob/main/img/image-20220711114540147.png)

1. 后缀支持bin/exe/dll，可输入绝对路径或相对路径或点击按钮选择。默认beacon.bin。（必选）
2. 生成木马的名称。默认result.exe。（必选）
3. 分离免杀，可输入绝对路径或相对路径，但生成的文件是固定在当前目录生成，木马会去读取目标路径下的分离shellcode
4. sgn混淆shellcode，默认5-10次。因为该功能所以必需配备keystone.dll，不放心可自行替换。
5. 选择shellcode加密算法（必选）
6. 选择loader（必选）
7. 反沙箱
8. 编译选项

| 反沙箱参数     | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| timestart      | 添加启动参数，参数为运行木马的机器的系统时间格式为HHMMDD,如9日11:08，则启动参数为1189，不填充0 |
| ramcheck       | 检查内存，小于4G则退出                                       |
| cpunumbercheck | 检查CPU核数，小于4则退出                                     |
| wechatcheck    | 检查微信进程，不存在则退出                                   |
| disksizecheck  | 检查C盘硬盘大小，小于60G则退出                               |

loader的说明搬一下4rain的介绍。

| 模块名                   | 简介                                                     |
| ------------------------ | -------------------------------------------------------- |
| CreateFiber              | 利用Windows CreateFiber函数                              |
| CreateProcess            | 利用Windows CreateProcess函数在挂起状态下创建进程        |
| CreateRemoteThread       | 远程进程注入ShellCode（注入explorer.exe）                |
| CreateRemoteThreadNative | 和上一条区别在于使用更底层的方式（注入explorer.exe）     |
| CreateThread             | 利用Windows CreateThread函数                             |
| CreateThreadNative       | 和上一条区别在于使用更底层的方式                         |
| EarlyBird                | 注入的代码在进程主线程的入口点之前运行                   |
| EtwpCreateEtwThread      | 利用Windows EtwpCreateEtwThread函数在进程中执行ShellCode |
| HeapAlloc                | 创建一个可供调用进程使用的堆并分配内存写入ShellCode      |
| NtQueueApcThreadEx       | 在当前进程的当前线程中创建一个特殊用户APC来执行ShellCode |
| RtlCreateUserThread      | 利用Windows RtlCreateUserThread函数（注入explorer.exe）  |
| UuidFromString           | 利用Windows UuidFromStringA函数                          |

编译参数说明，不包含`ldflag -s -w` ，默认自带

|                             参数                             |                          参数说明                          |
| :----------------------------------------------------------: | :--------------------------------------------------------: |
|                             race                             |      使用竞态检测器-race进行编译（可能提高免杀效果）       |
|                             Hide                             |     隐藏窗口ldflags -H windowsgui（可能降低免杀效果）      |
|        [garble](https://github.com/burrowers/garble)         | 使用编译混淆器garble来编译，需事先安装好，编译速度会慢一些 |
| [literalobf](https://github.com/burrowers/garble#literal-obfuscation) |             garble特有的参数，混淆所有字符串等             |
| [randomseed](https://github.com/burrowers/garble#determinism-and-seeds) |      garble特有的参数，使编译变的更随机，更加难以逆向      |

## 环境准备

在生成免杀马之前请注意以下四件事

1. 确保安装`Golang`且环境变量中包含`go`否则无法编译
2. 请在当前目录先执行`go env -w GO111MODULE=on`然后`go mod download`命令下载依赖
3. 生成木马时需将杀软关闭，go产生的中间文件会被查杀
4. 如果下载依赖过慢配置镜像`go env -w GOPROXY=https://mirrors.aliyun.com/goproxy`。国内用户建议配置。

一切就绪后就可以开始生成了

## 免杀效果

很多大佬都根据同个优秀的loader写了一些框架，目前啥选项都不配置有越来越多的杀软可以查杀，基本不能使用，建议使用分离shellcode，技术简单但效果好。写了一个能过DF的增强功能暂不放出。

## 参考

感谢[不羡](https://github.com/V1rtu0l)师傅提供的GUI建议

- https://github.com/safe6Sec/GolangBypassAV
- https://github.com/Ne0nd0g/go-shellcode

## TODO

- 签名伪造

- 自定义icon和versioninfo
- 文件捆绑功能

- ~~更多增强功能，如脱钩技术（halo's gate hell's gate unhook），考虑到免杀实效性，暂不考虑公开~~

