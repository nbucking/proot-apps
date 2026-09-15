ARG REPO
FROM ghcr.io/${REPO}:debian-trixie AS rootfs
ARG REPO

# Install packages
RUN \
  echo "**** install packages ****" && \
  apt-get update && \
  apt-get install -y --no-install-recommends \
    git \
    libgtk-3-bin \
    libatk1.0 \
    libatk-bridge2.0 \
    libnss3 \
    python3-xdg && \
  URL=$(curl -sX GET "https://api.github.com/repos/obsidianmd/obsidian-releases/releases" | jq -r --arg arch "$(if [ "$(uname -m)" = "x86_64" ]; then echo "x86_64"; else echo "arm64"; fi)" '
    [ .[] | .assets[] | select(.name | endswith(".AppImage")) ] |
    if $arch == "x86_64" then
      [ .[] | select(.name | test("arm64") | not) ]
    else
      [ .[] | select(.name | test("arm64")) ]
    end | .[0].browser_download_url
  ') && \
  curl -o \
    /tmp/obsidian.app -L \
    "${URL}" && \
  chmod +x /tmp/obsidian.app && \
  cd /tmp && \
  ./obsidian.app --appimage-extract && `#This does not work in QEMU for arm64`\
  mv squashfs-root /opt/obsidian && \
  echo "**** customize desktop file ****" && \
  DESKTOP_FILE=$(ls /opt/obsidian/*obsidian*.desktop /opt/obsidian/*Obsidian*.desktop 2>/dev/null | head -n1) && \
  sed -i \
    -e "/^Exec=/c Exec=/bin/sh -c \"\$HOME\/.local\/bin\/proot-apps run ghcr.io/${REPO}:obsidian %U\"" \
    -e "s/Name=Obsidian/Name=Obsidian PA/g" \
    -e '/^TryExec=/d' \
    "${DESKTOP_FILE}" && \
  mv \
    "${DESKTOP_FILE}" \
    /usr/share/applications/obsidian-pa.desktop && \
  echo "**** set bin name ****" && \
  echo "obsidian-pa" > /bin-name && \
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
