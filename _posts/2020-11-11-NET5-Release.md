---
title: .NET 5.0 正式发布
date: 2020/11/11 17:30:53
categories: 
- 博客
tags: 
- 开发环境
---
.NET 5.0 现已正式发布，这是一个主要版本。该版本引入了 C# 9 和 F# 5 编程语言、提供 Windows ARM64 支持、并进行了一系列的新功能添加和改进。

​       微软希望 .NET Framework 开发者能够迁移他们的代码和应用到 .NET 5.0 上，为明年发布的 .NET 6.0 将 Xamarin 开发者过渡到统一平台奠定基础。其目标是将所有的 .NET 组件整合到一个产品下，然后用户可以根据需求使用 .NET 的某个部分，而不是单独下载或安装所有内容。
​       对于[Visual Studio](https://visualstudio.microsoft.com/zh-hans/)用户，需要Visual Studio 16.8或更高的版本才能在Windows上使用.NET 5.0，在MacOS上使用最新版本的Visual Studio for Mac。[Visual Studio Code](https://code.visualstudio.com/)的C#扩展也已经支持.NET5.0和C#9。![dotnet5_platform.png](https://i.loli.net/2020/11/13/tz1Pclh9TB2no8g.png)

[Announcing .NET 5.0](https://devblogs.microsoft.com/dotnet/announcing-net-5-0/)

[What's new in .NET 5](https://docs.microsoft.com/en-us/dotnet/core/dotnet-five)

<!--more-->

.NET 5 的亮点内容：

- .NET 5.0 已经部署到 dot.net 和 Bing.com 托管数月，得到了实战检验。


- 很多组件的性能都得到了极大的提高。.NET 5.0 中 ARM64 的性能和 GRPC 等都有所改善。


- C# 9 和 F# 5  提供了新的语言改进，例如 C# 9 的顶层程序和记录，而 F# 5 则提供了交互式编程和.NET上功能编程的性能提升。


- .NET 库增强了 Json 序列化，正则表达式和 HTTP (HTTP 1.1, HTTP/2) 的性能有所提升。它们现在也被完全注释为可空性。


- 由于 GC、分层编译等方面的改进，P95 延迟有所下降。


- 应用部署选项更好了，有了 ClickOnce 客户端应用发布、单文件应用、容器镜像尺寸减小，并增加了 Server Core 容器镜像。


- 平台范围扩大，增加了 WindowsArm64 和 WebAssembly。  

[Download .NET 5.0](https://dotnet.microsoft.com/download/dotnet/5.0)