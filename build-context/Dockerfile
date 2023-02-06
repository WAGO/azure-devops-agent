FROM ubuntu:22.04 as base
RUN DEBIAN_FRONTEND=noninteractive apt-get update
RUN DEBIAN_FRONTEND=noninteractive apt-get upgrade -y
# To make it easier for build and release pipelines to run apt-get,
# configure apt to not require confirmation (assume the -y argument by default)
ENV DEBIAN_FRONTEND=noninteractive
RUN echo "APT::Get::Assume-Yes \"true\";" > /etc/apt/apt.conf.d/90assumeyes

COPY resources .

RUN apt-get update && apt-get install -y --no-install-recommends \
    ca-certificates \
    curl \
    jq \
    git \
    iputils-ping \
    libcurl4 \
    libicu70 \
    libunwind-14 \
    netcat \
    libssl3 \
    docker-compose

RUN install-certificates

# RUN curl -LsS https://aka.ms/InstallAzureCLIDeb | bash 

RUN curl -ksSL https://download.docker.com/linux/ubuntu/dists/jammy/pool/stable/amd64/docker-ce-cli_23.0.0-1~ubuntu.22.04~jammy_amd64.deb  -o docker-ce-cli.deb
RUN dpkg -i docker-ce-cli.deb
RUN rm docker-ce-cli.deb

RUN update-ca-certificates --fresh
RUN rm -rf /var/lib/apt/lists/*

FROM scratch as final
COPY --from=base / /
ENV TARGETARCH=linux-x64
WORKDIR /azp
ENTRYPOINT ["/docker-entrypoint.sh"]
