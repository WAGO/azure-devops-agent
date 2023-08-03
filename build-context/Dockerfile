ARG USERNAME=vscode

FROM ubuntu:18.04 as base

# To make it easier for build and release pipelines to run apt-get,
# configure apt to not require confirmation (assume the -y argument by default)
ENV DEBIAN_FRONTEND=noninteractive
RUN echo "APT::Get::Assume-Yes \"true\";" > /etc/apt/apt.conf.d/90assumeyes

ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID

# Create the user
RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME \
    #
    # [Optional] Add sudo support. Omit if you don't need to install software after connecting.
    && apt-get update \
    && apt-get install -y sudo \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME

COPY resources .

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
    software-properties-common

RUN install-certificates

# RUN curl -LsS https://aka.ms/InstallAzureCLIDeb | bash 

RUN curl -ksSL https://download.docker.com/linux/ubuntu/dists/bionic/pool/stable/amd64/docker-ce-cli_20.10.9~3-0~ubuntu-bionic_amd64.deb  -o docker-ce-cli.deb
RUN dpkg -i docker-ce-cli.deb
RUN rm docker-ce-cli.deb


#Update git
RUN add-apt-repository ppa:git-core/ppa -y \
    && apt-get update && apt-get install -y --no-install-recommends git

RUN apt-get update && apt-get install -y --no-install-recommends docker-compose

RUN update-ca-certificates --fresh
RUN rm -rf /var/lib/apt/lists/*



FROM scratch as final
ARG USERNAME=vscode
COPY --from=base / /
ENV TARGETARCH=linux-x64
USER $USERNAME
WORKDIR /azp
ENTRYPOINT ["/docker-entrypoint.sh"]
