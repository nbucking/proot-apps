ARG REPO
FROM ghcr.io/${REPO}:debian-trixie AS rootfs
ARG REPO

# Install packages
RUN \
  echo "**** install packages ****" && \
  apt-get update && \
  apt-get install -y --no-install-recommends \
    ca-certificates \
    curl \
    gnupg \
    libpolkit-gobject-1-0 && \
  mkdir -p /etc/apt/keyrings && \
  curl -fsSL https://keys.anydesk.com/repos/DEB-GPG-KEY | gpg --dearmor -o /etc/apt/keyrings/keys.anydesk.com.gpg && \
  echo "deb [signed-by=/etc/apt/keyrings/keys.anydesk.com.gpg] https://deb.anydesk.com all main" > /etc/apt/sources.list.d/anydesk-stable.list && \
  apt-get update && \
  apt-get install -y --no-install-recommends anydesk && \
  echo "**** customize desktop file ****" && \
  sed -i \
    -e "/^Exec=/c Exec=/bin/sh -c \"\$HOME\/.local\/bin\/proot-apps run ghcr.io/${REPO}:anydesk %U\"" \
    -e "s/Name=AnyDesk/Name=AnyDesk PA/g" \
    -e '/^TryExec=/d' \
    /usr/share/applications/anydesk.desktop && \
  mv \
    /usr/share/applications/anydesk.desktop \
    /usr/share/applications/anydesk-pa.desktop && \
  echo "**** set bin name ****" && \
  echo "anydesk-pa" > /bin-name && \
  echo "**** cleanup ****" && \
  apt-get autoclean && \
  rm -rf \
    /root/.cache \
    /var/lib/apt/lists/* \
    /var/tmp/* \
    /tmp/*

COPY ./entrypoint /entrypoint
COPY ./install /install
COPY ./remove /remove

FROM scratch
COPY --from=rootfs / /
