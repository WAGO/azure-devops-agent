FROM mcr.microsoft.com/devcontainers/base:bullseye as base

# To make it easier for build and release pipelines to run apt-get,
# configure apt to not require confirmation (assume the -y argument by default)
ENV DEBIAN_FRONTEND=noninteractive
RUN echo "APT::Get::Assume-Yes \"true\";" > /etc/apt/apt.conf.d/90assumeyes

COPY resources .

# RUN apt-get update && apt-get install -y --no-install-recommends \
#     ca-certificates \
#     curl \
#     jq \
#     git \
#     iputils-ping \
#     libcurl4 \
#     libicu60 \
#     libunwind8 \
#     netcat \
#     libssl1.0 \
#     software-properties-common

RUN install-certificates

RUN --mount=type=bind,target=/build \
    /build/install-dind.sh \
    /build/install-git.sh

# RUN update-ca-certificates --fresh

RUN rm -rf /var/lib/apt/lists/*



FROM scratch as final
ARG USERNAME=vscode
COPY --from=base / /
USER $USERNAME
WORKDIR /azp
ENV TARGETARCH=linux-x64
ENTRYPOINT ["/docker-entrypoint.sh"]

