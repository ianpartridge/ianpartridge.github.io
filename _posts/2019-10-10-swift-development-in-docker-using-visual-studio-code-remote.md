---
layout: post
title: Swift development in Docker using Visual Studio Code Remote
---

Xcode provides a great experience when developing Swift applications on macOS, but as Swift matures and becomes more popular on Linux there is a strong need for a first-class experience when developing on Linux too.

In this guide, I’ll show how to use the [Visual Studio Code Remote](https://code.visualstudio.com/docs/remote/containers) extension to develop your Swift application on macOS or Windows, while running in a Linux Docker container. You’ll have full IntelliSense thanks to [SourceKit-LSP](https://github.com/apple/sourcekit-lsp) and debugging thanks to [CodeLLDB](https://github.com/vadimcn/vscode-lldb).

![Debugging a remote Docker container using VS Code on macOS](https://ianpartridge.github.io/img/vscode-remote-1.png)

VS Code Remote is an extension you can install into VS Code. Once installed, if your project contains a `.devcontainer` directory VS Code will give you the option of running your project in “remote mode”.

In remote mode, your local VS Code talks to a remote Docker container which runs a “VS Code Server” that launches your application. Extensions like SourceKit-LSP and LLDBCode run in the remote VS Code Server, not your local VS Code.

![VS Code Remote architecture](https://ianpartridge.github.io/img/vscode-remote-2.png)

So, what should you put in your `.devcontainer` directory? Two files are required: `devcontainer.json` and `Dockerfile`.

### devcontainer.json

Here’s an example `devcontainer.json`:

```json
{
    "name": "Swift",
    "dockerFile": "Dockerfile",
    "runArgs": [
        "--cap-add=SYS_PTRACE",
        "--security-opt", "seccomp=unconfined"],
    // Uncomment the next line if you want to publish any ports.
    // "appPort": [8080, 1234],
    "extensions": [
        "pvasek.sourcekit-lsp--dev-unofficial",
        "vadimcn.vscode-lldb"],
    "settings": {
        "lldb.adapterType": "bundled",
        "lldb.executable": "/usr/bin/lldb",
        "terminal.integrated.shell.linux": "/bin/bash"
    }
}
```

Let’s break this down.

`"name":` — a human-readable name for this devcontainer. It’s shown in the VS Code interface when you launch your container.

`"dockerFile":` — the name of your `Dockerfile`. Just use `"Dockerfile"`, it’s the convention.

`"appPort":` — here you can specify if you want any ports to be opened in the container. You’d do this if your application is a server that you want to be able to connect to from outside the container.

`"runArgs":` — the arguments that VS Code should pass to `docker run` when it runs your container. In the case of Swift we need to give the container some elevated privileges so that LLDB can use the `ptrace()` system call to attach to your running application.

`"extensions":` — extensions from the VS Code Marketplace that should be installed in the VS Code Server in the container. In this case, we install SourceKit-LSP (which provides IntelliSense) and CodeLLDB (which provides debugging).

`"settings":` — configuration options for the VS Code extensions you are installing. In this case, CodeLLDB needs to be told where to find `lldb` inside the container, and we also teach VS Code how to run a shell inside the container.

That’s it. Now we just need to write our `Dockerfile` alongside `devcontainer.json` and we are good to go!

### Dockerfile

You can write any `Dockerfile` you want, but the simplest way is just to use the official [Swift Docker image](https://github.com/apple/swift-docker/):

```
FROM swift:5.1
```

That’s it!

## Launching

Once you’ve installed the VS Code [Remote Containers](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers) extension, when you open up a project that contains a `.devcontainer` directory, VS Code prompts you to reopen your project using the container.

![VS Code spots the .devcontainer directory in your project](https://ianpartridge.github.io/img/vscode-remote-3.png)

The first time you do this, VS Code will build the Docker image. After that, the image is cached for reuse. Then, VS Code will run the container and connect to it.

You now have a full remote Swift development experience including IntelliSense and debugging!

## Example Code

I’ve put together an example project showing how this works:

[https://github.com/ianpartridge/vscode-remote-try-swift](https://github.com/ianpartridge/vscode-remote-try-swift)

You should be able to install the extension into your VS Code, then clone this project and open it in VS Code and try it out!

## Feedback

If you’ve got feedback on this article, please do tweet me: [https://twitter.com/alfa](https://twitter.com/alfa)
