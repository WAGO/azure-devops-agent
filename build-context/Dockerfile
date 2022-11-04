FROM ubuntu:18.04 as base

# To make it easier for build and release pipelines to run apt-get,
# configure apt to not require confirmation (assume the -y argument by default)
ENV DEBIAN_FRONTEND=noninteractive
RUN echo "APT::Get::Assume-Yes \"true\";" > /etc/apt/apt.conf.d/90assumeyes

RUN apt-get update && apt-get install -y --no-install-recommends \
    ca-certificates \
    curl \
    jq \
    git \
    iputils-ping \
    libcurl4 \
    libicu60 \
    libunwind8 \
    netcat \
    libssl1.0 \
    software-properties-common \
    && add-apt-repository ppa:git-core/ppa -y \
    && apt-get update && apt-get install -y --no-install-recommends git \
    && rm -rf /var/lib/apt/lists/*

RUN curl -ksSL $(curl -ksSL $(curl -ksSL https://artifactory.wago.local/api/storage/certs-generic-prod-local/wago-intercept-certificates?lastModified | jq -r .'uri') | jq -r .'downloadUri') -o /usr/local/share/ca-certificates/wago.crt; exit 0
RUN curl -ksSL https://artifactory.wago.local/certs-generic-prod/wago-root-certificates/IssuingCA-SVCA01002-SHA256-base64.crt -o /usr/local/share/ca-certificates/IssuingCA-SVCA01002-SHA256-base64.crt; exit 0
RUN curl -ksSL https://artifactory.wago.local/certs-generic-prod/wago-root-certificates/RootCA-SVCA01001-SHA256-base64.crt -o /usr/local/share/ca-certificates/RootCA-SVCA01001-SHA256-base64.crt; exit 0
RUN update-ca-certificates

RUN curl -LsS https://aka.ms/InstallAzureCLIDeb | bash \
    && rm -rf /var/lib/apt/lists/*

RUN curl -ksSL https://download.docker.com/linux/ubuntu/dists/bionic/pool/stable/amd64/docker-ce-cli_20.10.9~3-0~ubuntu-bionic_amd64.deb  -o docker-ce-cli.deb
RUN dpkg -i docker-ce-cli.deb
RUN rm docker-ce-cli.deb

COPY ./docker-entrypoint.sh /
COPY ./commands /usr/bin/

FROM scratch as final
COPY --from=base / /
ENV TARGETARCH=linux-x64
WORKDIR /azp
ENTRYPOINT ["/docker-entrypoint.sh"]
