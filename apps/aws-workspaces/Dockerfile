FROM ubuntu:22.04
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update \
 && apt-get install -y wget gnupg2 ca-certificates libxss1 libasound2 libgtk-3-0 libnss3 libx11-6 libxext6 libxrender1 libxtst6 libgbm1 \
 && wget -q -O - https://workspaces-client-linux-public-key.s3-us-west-2.amazonaws.com/ADB332E7.asc | apt-key add - \
 && echo  deb [arch=amd64] https://d3nt0h4h6pmmc4.cloudfront.net/ubuntu jammy main > /etc/apt/sources.list.d/amazon-workspaces-clients.list \
 && apt-get update \
 && apt-get install -y workspacesclient \
 && rm -rf /var/lib/apt/lists/*

# Copy helper scripts for proot-app packaging
COPY entrypoint /entrypoint
COPY install /install
COPY remove /remove
RUN chmod +x /entrypoint /install /remove

ENTRYPOINT [/entrypoint]
