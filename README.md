**From https://github.com/yangxuan8282/docker-image/tree/master/alpine-xfce4-novnc**

![](https://github.com/yangxuan8282/docker-image/raw/master/alpine-xfce4-novnc/chrome_2018-09-23_07-55-15.png)

### TAG

registry.cn-hangzhou.aliyuncs.com/sourcegarden/alpine-xfce4-novnc

v1.1   桌面完整版.带有完整的桌面(主题和图标)  chrome 游览器  sshd   x2go   vnc  novnc   （不带内网穿透)

### 默认账号
novnc  `echoinheaven`
ssh    `heaven`/`echoinheaven`  `root`/`echoinheaven`

### RUN

通过novnc来访问容内的桌面:

```
docker run -d -p 6080:6080 registry.cn-hangzhou.aliyuncs.com/sourcegarden/alpine-xfce4-novnc
```

通过novnc  x2go 并且需要在容器中拥有管控宿主机docker的能力:
```
docker run --name demo  -d -p 6081:6080 -p 2223:22 -v /var/run/docker.sock:/var/run/docker.sock -v /tmp/.X11-unix:/tmp/.X11-unix registry.cn-hangzhou.aliyuncs.com/sourcegarden/alpine-xfce4-novnc
```

to run in a local nested X window:

```
Xephyr -screen 1024x768 :1 &
docker run -v /tmp/.X11-unix:/tmp/.X11-unix registry.cn-hangzhou.aliyuncs.com/sourcegarden/alpine-xfce4-novnc startxfce4
```

to run multiple container:

```
Xephyr -screen 1024x768 :2 &
docker run -v /tmp:/tmp registry.cn-hangzhou.aliyuncs.com/sourcegarden/alpine-xfce4-novnc startxfce4
```

it is also possible to nested in VNC:

```
sudo Xephyr -screen 800x600 :2 &
sudo docker run -e DISPLAY=:2 -v /tmp:/tmp registry.cn-hangzhou.aliyuncs.com/sourcegarden/alpine-xfce4-novnc startxfce4
```

> need to mount `/tmp/.X11-unix` when start container:

```
docker run -d -p 6080:6080 -v /var/run/docker.sock:/var/run/docker.sock -v /tmp/.X11-unix:/tmp/.X11-unix registry.cn-hangzhou.aliyuncs.com/sourcegarden/alpine-xfce4-novnc
```

remote X session with ssh tunnel:

run container on server:

```
docker run -d -p 6080:6080 -p 2222:22 -v /var/run/docker.sock:/var/run/docker.sock -v /tmp/.X11-unix:/tmp/.X11-unix registry.cn-hangzhou.aliyuncs.com/sourcegarden/alpine-xfce4-novnc
```

run command in VNC:

```
sudo apk --update --no-cache add openssh &&
sudo bash -c 'echo "X11Forwarding yes" >> /etc/ssh/sshd_config' &&
sudo bash -c 'echo "X11UseLocalhost no" >> /etc/ssh/sshd_config' &&
sudo ssh-keygen -A &&
sudo /usr/sbin/sshd -D
```

then on client:

```
Xephyr -screen 800x600 :1 &
DISPLAY=:1.0 ssh -Xf alpine@SERVER_IP -p 2222 xfce4-session
```

to use mmal on raspberry pi:

```
--device /dev/vchiq
```