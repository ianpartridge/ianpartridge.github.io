---
layout: post
title: Swift development in Docker using Visual Studio Code Remote
---

Xcode provides a great experience when developing Swift applications on macOS, but as Swift matures and becomes more popular on Linux there is a strong need for a first-class experience when developing on Linux too.

In this guide, I’ll show how to use the [Visual Studio Code Remote](https://code.visualstudio.com/docs/remote/containers) extension to develop your Swift application on macOS or Windows, while running in a Linux Docker container. You’ll have full IntelliSense thanks to [SourceKit-LSP](https://github.com/apple/sourcekit-lsp) and debugging thanks to [CodeLLDB](https://github.com/vadimcn/vscode-lldb).

