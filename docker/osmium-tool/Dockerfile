
FROM debian:stretch-slim as builder

# Install necessary packages
RUN DEBIAN_FRONTEND=noninteractive apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y \
  build-essential \
  git \
  pkg-config \
  cmake \
  rapidjson-dev \
  libboost-program-options-dev \
  libbz2-dev \
  zlib1g-dev \
  libexpat1-dev

# Alternatively `-- -j` to enable parallel compiling.
# It must be disabled on GitHub Actions due to runner's resource limitation.
ARG CMAKE_BUILD_EXTRA_ARGS="-- -j"

# osmium
RUN mkdir -p /osmium /osmium/bin && cd osmium && \
  git clone https://github.com/mapbox/protozero.git && \
  git clone https://github.com/osmcode/osmium-tool.git && \
  git clone https://github.com/wangyoucao577/libosmium.git && \
  cd osmium-tool && \
  mkdir -p build && \
  cd build && \
  cmake .. && \
  cmake --build . ${CMAKE_BUILD_EXTRA_ARGS} && \
  cp ./src/osmium /osmium/bin && \
  chmod +x /osmium/bin/* && \
  /osmium/bin/osmium --version


FROM debian:stretch-slim

# Install necessary packages
RUN DEBIAN_FRONTEND=noninteractive apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y \
  libboost-program-options-dev \
  libbz2-dev \
  zlib1g-dev \
  libexpat1-dev

COPY --from=builder /osmium/bin /osmium/bin
ENV PATH=/osmium/bin/:$PATH
RUN osmium --version

ENTRYPOINT [ "/osmium/bin/osmium" ]


# for shown on runtime
ARG IMAGE_TAG
ENV IMAGE_TAG ${IMAGE_TAG}
RUN echo IMAGE_TAG=${IMAGE_TAG} >> /etc/environment

# labels
LABEL maintainer="wangyoucao577@gmail.com"

