# 在Mac上彻底卸载Docker


<!--more-->

## 情况一：可以打开 Docker Desktop

直接在 Mac 上打开 Docker Desktop，然后点击troubleshoot ![](https://pic.yqqy.top/blog/202203081550197.png?imageMogr2/format/webp/interlace/1) 进入后再点击 uninstall。

![img](https://pic.yqqy.top/blog/202203081544328.png?imageMogr2/format/webp/interlace/1 "img1")

## 情况二：打不开 Docker Desktop

打开命令行执行以下命令：

```shell
/Applications/Docker.app/Contents/MacOS/Docker --uninstall
```

## 情况三：打不开 Docker Desktop 且情况二无法解决

打开命令行执行以下命令：

```shell
sudo rm -Rf /Applications/Docker.app
sudo rm -f /usr/local/bin/docker
sudo rm -f /usr/local/bin/docker-machine
sudo rm -f /usr/local/bin/com.docker.cli
sudo rm -f /usr/local/bin/docker-compose
sudo rm -f /usr/local/bin/docker-compose-v1
sudo rm -f /usr/local/bin/docker-credential-desktop
sudo rm -f /usr/local/bin/docker-credential-ecr-login
sudo rm -f /usr/local/bin/docker-credential-osxkeychain
sudo rm -f /usr/local/bin/hub-tool
sudo rm -f /usr/local/bin/hyperkit
sudo rm -f /usr/local/bin/kubectl.docker
sudo rm -f /usr/local/bin/vpnkit
sudo rm -Rf ~/.docker
sudo rm -Rf ~/Library/Containers/com.docker.docker
sudo rm -Rf ~/Library/Application\ Support/Docker\ Desktop
sudo rm -Rf ~/Library/Group\ Containers/group.com.docker
sudo rm -f ~/Library/HTTPStorages/com.docker.docker.binarycookies
sudo rm -f /Library/PrivilegedHelperTools/com.docker.vmnetd
sudo rm -f /Library/LaunchDaemons/com.docker.vmnetd.plist
sudo rm -Rf ~/Library/Logs/Docker\ Desktop
sudo rm -Rf /usr/local/lib/docker
sudo rm -f ~/Library/Preferences/com.docker.docker.plist
sudo rm -Rf ~/Library/Saved\ Application\ State/com.electron.docker-frontend.savedState
sudo rm -f ~/Library/Preferences/com.electron.docker-frontend.plist
```

**参考资料：**

* https://docs.docker.com/desktop/mac/troubleshoot/
