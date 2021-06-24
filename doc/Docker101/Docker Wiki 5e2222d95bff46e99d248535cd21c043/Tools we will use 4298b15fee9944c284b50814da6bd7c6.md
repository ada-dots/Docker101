# Tools we will use

Operating system that we cau use is either Windows 10 or Linux or Mac

Tools we will use:

- Windows Subsystem for Linux

    The Windows Subsystem for Linux (WSL) is a feature of Windows 10 that enables you to run native Linux command-line tools directly on Windows, alongside your traditional Windows desktop and apps.

    - On Windows 10, enable WSL, by following the steps described here: [Enable WSL](https://docs.microsoft.com/en-us/windows/wsl/install-win10#manual-installation-steps)
    - Add the Ubuntu distro from the [Microsoft Store](https://www.microsoft.com/en-us/p/ubuntu/9nblggh4msv6)
- Visual Studio 2019 / VS Code
    - First, you will need to have a development IDE installed. The most preferable IDE is [Visual Studio 2019](https://www.visualstudio.com/vs/)  if you are running the Windows operating system.
    - You can also use [Visual Studio Code](https://code.visualstudio.com/) .
        - A couple of VS Code extensions are required to assist you during the labs. You can install them before the workshop or just in time.
        - On Windows, install the [Remote WSL](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl) extension : lets you use VS Code on Windows to build Linux applications that run on the Windows Subsystem for Linux (WSL).
        - Install the [Docker](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker) extensions
            - and configure VS Code for debugging C# by installing the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
- Docker Desktop (Windows & Mac)
    - you are going to need the Docker Community Edition tooling on your development machine.
    - Download and install Docker Community Edition (version 3.1.0 or higher):
        - [Docker Desktop for Windows](https://docs.docker.com/docker-for-windows/install/)
        - [Docker Desktop for Mac](https://docs.docker.com/docker-for-mac/install/)
    - When on Windows, make sure to enable WSL integration, if possible.

    ![Tools%20we%20will%20use%204298b15fee9944c284b50814da6bd7c6/Untitled.png](Tools%20we%20will%20use%204298b15fee9944c284b50814da6bd7c6/Untitled.png)

    - **.NET Platform**
        - Download and install [.NET 5](https://download.visualstudio.microsoft.com/download/pr/75483251-b77a-41a9-9ea2-05fb1668e148/2c27ea12ec2c93434c447f4009f2c2d2/dotnet-sdk-5.0.102-win-x64.exe)  if needed.
    - Windows Terminal
        - The [Windows Terminal](https://docs.microsoft.com/en-us/windows/terminal/get-started) is a modern, fast, efficient, powerful, and productive terminal application for users of command-line tools and shells like Command Prompt, PowerShell, and WSL
        - Settings to use the WSL inside Windows Terminal is:

        ```json
        {
                        "guid": "{2c4de342-38b7-51cf-b940-2309a097f518}",
                        "hidden": false,
                        "name": "Ubuntu",
                        "source": "Windows.Terminal.Wsl",
                        "colorScheme": "UbuntuLegit",
                        "fontFace": "Cascadia Code",
                        "fontSize": 10,
                        "acrylicOpacity": 0.8
        }
        ```

    - The color scheme used is:

        ```json
        {
                    // Color Scheme: UbuntuLegit
                    "background": "#2C001E",
                    "black": "#4E9A06",
                    "blue": "#3465A4",
                    "brightBlack": "#555753",
                    "brightBlue": "#729FCF",
                    "brightCyan": "#34E2E2",
                    "brightGreen": "#8AE234",
                    "brightPurple": "#AD7FA8",
                    "brightRed": "#EF2929",
                    "brightWhite": "#EEEEEE",
                    "brightYellow": "#FCE94F",
                    "cyan": "#06989A",
                    "foreground": "#EEEEEE",
                    "green": "#300A24",
                    "name": "UbuntuLegit",
                    "purple": "#75507B",
                    "red": "#CC0000",
                    "white": "#D3D7CF",
                    "yellow": "#C4A000"
                },
        ```

        [Docker Wiki](../Docker%20Wiki%205e2222d95bff46e99d248535cd21c043.md)