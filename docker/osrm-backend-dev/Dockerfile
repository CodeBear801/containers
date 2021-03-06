
FROM wangyoucao577/osmium-tool as osmium_builder

FROM telenavmap/osrm-backend-dev:latest


# install extra tools
RUN apt-get update && apt-get install -y \
  openssh-server \
  gdb \
  tmux \
  locales-all \
  iperf3 \
  net-tools \
  iputils-ping \
  dnsutils \
  htop \
  atop \
  sysstat \
  strace \
  zip

# osmium
COPY --from=osmium_builder /osmium/bin /workspace/osmium/bin

# prepare for sshd
RUN echo 'root:root' | chpasswd
RUN echo "PermitRootLogin yes" >> /etc/ssh/sshd_config
COPY jay_easy_rsa.pub /root/.ssh/
RUN cat /root/.ssh/jay_easy_rsa.pub >> /root/.ssh/authorized_keys && \
  chmod 600 /root/.ssh/authorized_keys && \
  chmod 700 /root/.ssh/ && \
  chmod 755 /root/
RUN service ssh start

# easy to use
COPY .tmux.conf /root/
COPY .vimrc /root/
COPY .bashrc /root/
RUN git config --global core.editor vim && \
  git config --global credential.helper 'cache --timeout 3600' && \
  git config --global log.decorate auto

# vscode configs and latest extensions
COPY vscode/ /workspace/vscode/ 
RUN cd /workspace/vscode && \
  wget --progress=dot:mega https://github.com/microsoft/vscode-cpptools/releases/download/0.27.0/cpptools-linux.vsix && \
  wget --progress=dot:mega https://github.com/microsoft/vscode-go/releases/download/0.13.1/Go-0.13.1.vsix
RUN mkdir -p /root/.vscode-server/data/Machine && \
  cp /workspace/vscode/settings.json /root/.vscode-server/data/Machine/

# prepare osrm-backend dev env
RUN cd /workspace && \
  git clone https://github.com/Telenav/osrm-backend.git && \
  cd osrm-backend && \
  mkdir -p build/debug build/release && \
  cp -r /workspace/vscode/cpp .vscode && \
  cp -r /workspace/vscode/go integration/.vscode

# sample osm pbf
RUN cd /workspace && \
  mkdir -p osrm-mapdata-nevada && \
  cd osrm-mapdata-nevada && \
  wget --progress=dot:mega https://download.geofabrik.de/north-america/us/nevada-latest.osm.pbf 


# for shown on runtime
ARG IMAGE_TAG
ENV IMAGE_TAG ${IMAGE_TAG}
RUN echo IMAGE_TAG=${IMAGE_TAG} >> /etc/environment



WORKDIR /workspace
ENTRYPOINT ["/usr/sbin/sshd", "-D"]

# labels
LABEL maintainer="wangyoucao577@gmail.com"

