# SPDX-License-Identifier: MIT
# SPDX-FileCopyrightText: (c) Copyright 2023 Advanced Micro Devices, Inc.
ARG DTK_AUTO

FROM ${DTK_AUTO} as builder
ARG ONLOAD_BUILD_PARAMS
ARG ONLOAD_LOCATION
ARG KERNEL_VERSION

WORKDIR /build/
ADD ${ONLOAD_LOCATION} onload.tar.gz
RUN mkdir -p /build/onload
RUN tar xzf onload.tar.gz -C /build/onload --strip-components=1
WORKDIR /build/onload/

RUN scripts/onload_build --kernel --kernelver ${KERNEL_VERSION} ${ONLOAD_BUILD_PARAMS}
RUN scripts/onload_install --nobuild --kernelfiles --kernelver ${KERNEL_VERSION}

FROM ubi8:8.8
ARG KERNEL_VERSION

RUN yum -y install kmod && yum clean all
RUN mkdir -p /opt/lib/modules/${KERNEL_VERSION}

COPY --from=builder /lib/modules/${KERNEL_VERSION}/extra/onload.* /opt/lib/modules/${KERNEL_VERSION}
COPY --from=builder /lib/modules/${KERNEL_VERSION}/extra/sfc_char.* /opt/lib/modules/${KERNEL_VERSION}
COPY --from=builder /lib/modules/${KERNEL_VERSION}/extra/sfc_resource.* /opt/lib/modules/${KERNEL_VERSION}

RUN /usr/sbin/depmod -b /opt
