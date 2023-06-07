---
title: 如何使用VS Code
tags: "开发环境配置"
key: env-VSCode
---

本文介绍了代码编辑器VS Code的使用方法，以及如何通过安装插件的方式，使其具备与集成开发环境相同的功能。<!--more-->

# 前言

集成开发环境是一种集成了代码编辑、编译、调试、运行等功能的软件，可以极大的提高开发效率。但集成开发环境可能过于庞大冗杂。也许你只想要一个代码编辑器，例如VS Code，它同样可以进行代码的编辑，但不具备编译、调试、运行等功能，但是它的插件生态非常丰富，可以通过安装插件的方式，使其具备与集成开发环境相同的功能。

# 安装VS Code

VS Code的安装非常简单，直接在[官网](https://link.zhihu.com/?target=https%3A//code.visualstudio.com/download)选择对应操作系统的稳定版下载即可。

安装完成之后，你需要下载一系列的插件来丰富VSC的功能，这里推荐几个常用的插件：

通用：
- Chinese(Simplified)：中文汉化包；
- Project Manager：项目管理器，可以方便在多个项目之间切换；
- Todo Tree：用于在代码中标记TODO、FIXME等注释；
- Docker：用于管理Docker容器；
- Remote Development：用于远程开发；
- Dev Container：用于开发容器；
- Error Lens：用于在代码中显示错误信息；
- IntelliCode：用于提供代码补全、代码建议等功能；
- IntelliCode API Usage Examoples：用于提供代码补全、代码建议等功能；
- GitHub Copilot：微软出品的AI代码助手，学生可申请免费使用；
- Github Copilot Labs：微软出品的AI代码助手，学生可申请免费使用；
- ChatGPT - Genie AI：内嵌的ChatGPT，非官方。

C/C++：
- C/C++：C/C++语言支持；
- C/C++ Extension Pack：C/C++语言支持；
- CMake：CMake语言支持；

Python：
- Python：Python语言支持；
- Pylance：Python语法检测工具；
- Python Extension Pack：Python语言支持；
- Python Environment Mangaer：环境管理工具；

# 使用VS Code编写Python

在安装完成上述python的插件后，需要根据提示选择python解释器，如果使用conda等管理python环境，vsc可以直接识别到，下面就可以进行Python语言的开发了，F5可以直接以调试模式运行代码。

# 使用VS Code编写C/C++

C/C++的使用相对复杂，首先需要使用VS Code打开项目文件夹，在该文件夹中创建一个名为`.vscode`的文件夹，该文件夹用于配置项目的专属配置。在文件夹中创建三个文件，分别为`c_cpp_properties.json`、`launch.json`、`tasks.json`，这里我贴出我自己常用的配置，需要根据自己电脑的实际情况进行修改。第一个文件用于代码编写时的提示功能等，第二个文件用于调试功能，第三个文件用于编译功能。
这三个文件的内容如下：

`c_cpp_properties.json`：
```
// 该文件定义了项目的编译器、编译选项等信息
{
    "configurations": [
        {
            "name": "Mac",
            // 头文件路径
            "includePath": [
                "${workspaceFolder}/**",
            ],
            "defines": [],
            // 标准库的路径
            "macFrameworkPath": [
                "/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/System/Library/Frameworks"
            ],
            // 编译器路径
            "compilerPath": "/usr/bin/clang++",
            // C/C++语言标准，只是帮助插件进行语法检查，不影响编译
            "cStandard": "c11",
            "cppStandard": "c++11",
            "intelliSenseMode": "macos-clang-arm64"
        }
    ],
    "version": 4
}
```

`tasks.json`：
```
// 该文件定义主要用于程序的编译，可以定义多个编译任务，如果用于调试，需要在编译选项中添加"-g"
{
    "tasks": [
        {
            "type": "cppbuild",
            "label": "C/C++: clang++ 生成活动文件",
            // 编译器路径
            "command": "/usr/bin/clang++",
            // 编译选项
            "args": [
                "-fcolor-diagnostics",
                "-fansi-escape-codes",
                "-std=c++11",
                "-g",
                "${file}",
                "-o",
                "main"
            ],
            "options": {
                "cwd": "${fileDirname}"
            },
            "problemMatcher": [
                "$gcc"
            ],
            "group": {
                "kind": "build",
                "isDefault": true
            },
            "detail": "调试器生成的任务。"
        }
    ],
    "version": "2.0.0"
}
```

`launch.json`：
```
{
    "version": "0.2.0",
    "configurations": [
        {
            "type": "lldb",
            "request": "launch",
            "name": "C++ debug",   
            // 目标程序
            "program": "${fileDirname}/main",
            "args": [],
            "cwd": "${workspaceFolder}",
            // 该选项用于在调试时调用tasks.json中创建的任务进行编译
            "preLaunchTask": "C/C++: clang++ 生成活动文件",
            "terminal": "console"
        }
    ]
}
```