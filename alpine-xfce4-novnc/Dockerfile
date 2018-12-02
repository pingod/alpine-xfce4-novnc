FROM registry.cn-hangzhou.aliyuncs.com/sourcegarden/docker-ocserv-ofss:v1.0

COPY xfce/config /etc/skel/.config

RUN set -xe \
	&& sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories \
  && apk --update --no-cache add xvfb xfce4 xfce4-terminal python bash sudo htop procps curl x11vnc x2goserver openssh wget ca-certificates\
  && update-ca-certificates \
  && mkdir -p /usr/share/wallpapers \
  && curl https://img2.goodfon.com/original/2048x1820/3/b6/android-5-0-lollipop-material-5355.jpg -o /usr/share/wallpapers/android-5-0-lollipop-material-5355.jpg \
  && addgroup heaven \
  && adduser -G heaven -s /bin/bash -D heaven \
  && echo "heaven:echoinheaven" | /usr/sbin/chpasswd \
  && echo "root:echoinheaven" | /usr/sbin/chpasswd \
  && echo "heaven ALL=NOPASSWD: ALL" >> /etc/sudoers \
  && sed -i s/#PermitRootLogin.*/PermitRootLogin\ yes/ /etc/ssh/sshd_config \
  && echo "X11Forwarding yes" >> /etc/ssh/sshd_config \
  && echo "X11UseLocalhost no" >> /etc/ssh/sshd_config \
  && ssh-keygen -A

ENV USER=alpine \
    DISPLAY=:1 \
    LANG=en_US.UTF-8 \
    LANGUAGE=en_US.UTF-8 \
    HOME=/home/heaven \
    TERM=xterm \
    SHELL=/bin/bash \
    VNC_PASSWD=echoinheaven \
    VNC_PORT=5900 \
    VNC_RESOLUTION=1024x768 \
    VNC_COL_DEPTH=24  \
    NOVNC_PORT=6080 \
    NOVNC_HOME=/home/heaven/noVNC 

USER heaven

RUN set -xe \
  && sudo bash -c "echo '@testing http://dl-cdn.alpinelinux.org/alpine/edge/testing'  >> /etc/apk/repositories" \
  && cat /etc/apk/repositories \
  && sudo apk update \
  && sudo apk add paper-icon-theme arc-theme@testing chromium \
  && sudo bash -c "echo 'CHROMIUM_FLAGS=\"--no-sandbox --no-first-run --disable-gpu\"' >> /etc/chromium/chromium.conf" \
  && mkdir -p $NOVNC_HOME/utils/websockify \
  && wget -qO- https://github.com/novnc/noVNC/archive/v1.0.0.tar.gz | tar xz --strip 1 -C $NOVNC_HOME \
  && wget -qO- https://github.com/novnc/websockify/archive/v0.8.0.tar.gz | tar xzf - --strip 1 -C $NOVNC_HOME/utils/websockify \
  && chmod +x -v $NOVNC_HOME/utils/*.sh \
  && ln -s $NOVNC_HOME/vnc.html $NOVNC_HOME/index.html

WORKDIR $HOME
EXPOSE $VNC_PORT $NOVNC_PORT

COPY run_novnc /usr/bin/
RUN sudo chmod +x /usr/bin/run_novnc
CMD ["bash","-c","/usr/bin/run_novnc"]
