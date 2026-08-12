# Zap PA proot app package
ARG REPO
FROM ghcr.io/${REPO}:debian-trixie AS rootfs
ARG REPO

# Install the published Linux build from upstream.
RUN \
  echo "**** install packages ****" && \
  apt-get update && \
  apt-get install -y --no-install-recommends \
    ca-certificates \
    curl \
    tar \
    libgl1-mesa-dri \
    libgbm1 \
    libgl1 \
    libegl1 \
    xterm && \
  ZAP_URL=$(curl -fsSL https://api.github.com/repos/zerx-lab/zap/releases/latest \
    | awk -F'"' '/browser_download_url.*zap-linux-x86_64\.tar\.gz/{print $4;exit}') && \
  mkdir -p /opt/zap && \
  curl -fsSL "$ZAP_URL" | tar -xz -C /opt/zap && \
  ln -s /opt/zap/zap-oss /usr/local/bin/zap-oss && \
  apt-get autoclean && \
  rm -rf \
    /root/.cache \
    /var/lib/apt/lists/* \
    /var/tmp/* \
    /tmp/*

COPY ./icon.svg /usr/share/icons/hicolor/scalable/apps/zap-pa.svg
COPY ./zap-pa.desktop.template /usr/share/applications/zap-pa.desktop

RUN \
  sed -i \
    -e "s#__REPO__#${REPO}#g" \
    -e '/^TryExec=/d' \
    /usr/share/applications/zap-pa.desktop && \
  printf 'zap-pa\n' > /bin-name

COPY ./entrypoint /entrypoint
COPY ./install /install
COPY ./remove /remove
RUN chmod +x /entrypoint /install /remove

FROM scratch
COPY --from=rootfs / /
