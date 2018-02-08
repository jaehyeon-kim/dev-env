# docker-vscode

I use Docker for development on Windows and it is not convenient to update code in a Docker image directly. It can be much easier to run Visual Studio Code from Docker rather than teaching myself an editor like _vim_. The image includes R ([rocker from Ubuntu 16.04](https://github.com/rocker-org/rocker/tree/master/r-apt/xenial)) by default and a X Windows Server named _VcXsrv_ is necessary as indicated below.

Original setup can be found in [ctaggart/golang-vscode](https://github.com/ctaggart/golang-vscode) and explained in [Visual Studio Code served from Docker](http://blog.ctaggart.com/2016/05/visual-studio-code-served-from-docker.html)

Steps

### 1. [Install Docker for Windows](https://docs.docker.com/docker-for-windows/install/) 
### 2. Build the Docker image

```
docker build -t <repo>:<tag> .
```

### 3. Install X Windows Server on Windows - [VcXsrv](https://sourceforge.net/projects/vcxsrv/)
### 4. Start _VcXsrv_ in PowerShell - multiwindow with no access control

```
$env:path="$env:ProgramFiles\VcXsrv;$env:path"
vcxsrv -multiwindow -ac
```

### 5. Start Visual Studio Code

Identify IPv4 Address using `ipconfig` under _Ethernet adapter Ethernet_ or _Wireless LAN adapter Local Area Connection_

![alt text](https://raw.githubusercontent.com/jaehyeon-kim/docker-vscode/master/image/ipconfig.png)

```
$ip='10.0.75.1'
$cmd="export DISPLAY=${ip}:0; code -w ."
docker run -d <repo>:<tag> su - docker -c $cmd
```

### 6. Exit/Restart Visual Studio Code

`-d` flag starts a daemon (non-interactive container) and VS code pops up in an window.

![alt text](https://raw.githubusercontent.com/jaehyeon-kim/docker-vscode/master/image/window.png)

If the window is closed by clicking _X_ in the upper right hand side, the daemon container exists. It can be restarted by

```
docker start [container-id or container name]
```
