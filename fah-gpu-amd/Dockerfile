# Folding@home AMD ROCm Container

FROM ubuntu:24.04
LABEL description="Folding@home AMD ROCm Container"

ARG ROCM_RELEASE=6.3.2
ENV DEBIAN_FRONTEND=noninteractive

RUN useradd -Ms /bin/bash fah-client \
    && apt-get update \
    && apt-get install -y --no-install-recommends \
      kmod \
      python3 \
      curl \
      gnupg \
      ca-certificates \
      libnuma-dev \
      polkitd-pkla \
      policykit-1 \
    # get ROCm OpenCL runtime packages
    && curl -sL https://repo.radeon.com/rocm/rocm.gpg.key | apt-key add - \
    && printf "deb [arch=amd64] https://repo.radeon.com/rocm/apt/${ROCM_RELEASE}/ ubuntu main" \
        | tee /etc/apt/sources.list.d/rocm.list \
    && apt-get update && apt-get install -y --no-install-recommends \
      rocm-opencl-runtime \
      rocm-smi-lib \
      rocminfo \
    # download and verify checksum
    && curl -fsSL \
      https://download.foldingathome.org/releases/public/fah-client/debian-10-64bit/release/fah-client_8.4.9_amd64.deb \
      -o fah.deb \
    && echo "098f4abf7c172e543de4391e86eacd10655e67d53fd4b0ae05633e2b57f9e07c fah.deb" \
      | sha256sum -c --strict - \
    # install and cleanup
    && dpkg --install --force-depends fah.deb \
    && rm -rf fah.deb \
    && apt-get purge --autoremove -y curl \
    && rm -rf /var/lib/apt/lists/* /var/cache/apt/archives/*

# explicitly point FAHClient to the ROCm libOpenCL
#ENV LD_LIBRARY_PATH=/opt/rocm-${ROCM_VER}/lib
#ENV PATH=$PATH:/opt/rocm-${ROCM_VER}/bin:/opt/rocm-${ROCM_VER}/opencl/bin
ENV LD_LIBRARY_PATH=/opt/rocm/lib
ENV PATH=$PATH:/opt/rocm/bin:/opt/rocm/opencl/bin

WORKDIR "/fah"
VOLUME ["/fah"]
EXPOSE 7396 36330

ENTRYPOINT ["/usr/bin/fah-client"]
