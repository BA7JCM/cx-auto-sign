# ⚠注意
由于超星学习通更新，目前无法签到二维码签到 [#23](https://github.com/cyanray/cx-auto-sign/issues/23) 。
其他类型的签到正常。

# 项目简介

![](https://github.com/cyanray/cx-auto-sign/workflows/.NET%20Core/badge.svg)

cx-auto-sign 是基于 .NET5 的超星学习通自动签到工具。

本项目最低程度实现超星学习通的即时通讯协议。通过超星学习通的即时通讯协议监测最新的课程活动。当指定的课程有新的消息，就检查该课程是否有新的签到任务，如果有则进行签到。该方法与轮询相比，灵敏度更高，占用资源更低。

# 项目进度

- [x] 支持 **手机号登录** 和 **学号登录** 两种登录方式
- [x] 支持 `init` 指令，用以生成配置文件
- [x] 支持 `update` 指令，更新课程信息
- [x] 实现自动签到工作流程
- [x] 支持 **WebApi** 控制自动签到启停
- [x] 支持通过 **Server 酱** 发送通知
- [x] 支持通过 **PushPlus 推送加** 发送通知
- [x] 支持签到成功后发送 **邮件通知**
- [x] 支持多账号配置


# 使用方法

## 0x00 运行环境

首先需要在 [.Net Runtime 下载页](https://dotnet.microsoft.com/download/dotnet/current/runtime) 下载并安装 **.NET5 Runtime**（提示：Run server apps下边的下载）。

然后在 [Release页面](https://github.com/cyanray/cx-auto-sign/releases) 下载 cx-auto-sign.zip，并解压到某个目录。

（你也可以在 [Actions](https://github.com/cyanray/cx-auto-sign/actions) 中找到自动编译的测试版）

## 0x01 登录并初始化配置文件

在 cx-auto-sign.dll 所在的目录执行以下命令行（Windows 和 Linux都适用）：

```powershell
# 通过手机号码登录，不需要学校编码
dotnet ./cx-auto-sign.dll init -u "双引号里面填手机号" -p "双引号里面填密码" 
```

**或：**

```powershell
# 通过学号登录，需要学校编码
dotnet ./cx-auto-sign.dll init -u "双引号里面填学号" -p "双引号里面填密码" -f "学校编码"
```

[comment]: <> (以上指令会创建 **AppConfig.json** 文件、 **EmailConfig.json** 文件、 **Courses** 目录 和 **Images** 目录。)

[comment]: <> (**AppConfig.json** 文件用于配置签到的一些参数。)

[comment]: <> (**EmailConfig.json** 文件用于配置通知邮件的参数。)

[comment]: <> (**Courses** 目录下有一系列 **.json** 文件，每个文件对应一门课程。对于不需要自动签到的课程，请删除对应的文件。)

`Images` 目录中的图片会用于拍照签到。

## 0x02 开始自动签到

在 `cx-auto-sign.dll` 所在的目录执行以下命令行:

```powershell
dotnet ./cx-auto-sign.dll work
```

即可开始自动签到。

[comment]: <> (# 配置文件说明&#40;AppConfig.json&#41;)

[comment]: <> (执行 **init** 指令时会创建该文件，其内容以及解释如下：)

[comment]: <> (```jsonc)

[comment]: <> ({)

[comment]: <> (    "Username": "",             // 学号或手机号)

[comment]: <> (    "Password": "",             // 密码)

[comment]: <> (    "Fid": "",                  // 学校代号，fid为null时使用手机号登录)

[comment]: <> (    "Address": "中国",           // 定位签到的中文名地址)

[comment]: <> (    "Latitude": "-1",           // 定位签到的纬度)

[comment]: <> (    "Longitude": "-1",          // 定位签到的经度)

[comment]: <> (    "ClientIp": "1.1.1.1",      // 签到时提交的客户端ip地址)

[comment]: <> (    "DelaySeconds": 10,         // 检测到新签到活动后延迟签到的秒数（过小容易出现秒签到现象）)

[comment]: <> (    "EnableWebApi": true,       // 是否启动 Web Api，默认关闭)

[comment]: <> (    "ServerChanKey": "SCKEY"    // Server酱的SCKEY，默认为 null)

[comment]: <> (})

[comment]: <> (```)

# WebApi 说明

<details>
<summary>详细</summary>

WebApi 默认监听规则是 `http://localhost:5743`，可在配置文件中修改。

若要监听全部网卡的 5743 端口，可写为：`http://*:5743`。

## 查看状态

请求：`GET` `/status`

响应：

```jsonc
{
    "username":"0000000000",    // 学号或手机号
    "cxAutoSignEnabled":true    // 是否启动自动签到，默认为 true
}
```

## 启动自动签到

请求：`GET` `/status/enable`

响应：

```jsonc
{
    "code": 0,
    "msg":"success"
}
```

## 停止自动签到

请求：`GET` `/status/disable`

响应：

```jsonc
{
    "code": 0,
    "msg":"success"
}
```

</details>

# FQA

## 1. 如何关闭自动更新检测? 
在 `cx-auto-sign.dll` 所在目录下创建一个名为 `.noupdate` 的文件。

# 声明

一切开发旨在学习，请勿用于非法用途。

## 拍照签到参数说明

* `""` 等无效路径：一张黑图
* `true`：随机使用 `Images` 文件夹下的一张图片
* `[]` 数组：随机使用数组中的一张图片 
*  可指定文件夹，将随机使用文件夹下的一张图片，会遍历子文件夹
* 可使用绝对路径和相对路径，相对路径是相对于 Images 文件夹

例如：
* `"机房/"`：随机使用 `Images/机房` 文件夹下的一张图片
* `["", "机房/", "电脑/", "老师.jpg"]`：将从黑图片、`Images/机房` 文件夹、`Images/电脑` 文件夹和 `Images/老师.jpg` 图片中随机使用一张图片
