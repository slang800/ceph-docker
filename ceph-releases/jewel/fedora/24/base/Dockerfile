# CEPH BASE IMAGE
# CEPH VERSION: Jewel
# CEPH VERSION DETAIL: 10.2.x

FROM fedora:24
MAINTAINER Sébastien Han "seb@redhat.com"

ENV ETCDCTL_VERSION v2.2.4
ENV ETCDCTL_ARCH linux-amd64
ENV KVIATOR_VERSION 0.0.7
ENV CONFD_VERSION 0.10.0
ENV KUBECTL_VERSION v1.2.4

# Download kviator
ADD https://github.com/AcalephStorage/kviator/releases/download/v${KVIATOR_VERSION}/kviator-${KVIATOR_VERSION}-linux-amd64.zip /kviator.zip

# Download confd
ADD https://github.com/kelseyhightower/confd/releases/download/v${CONFD_VERSION}/confd-${CONFD_VERSION}-linux-amd64 /usr/local/bin/confd

# install prerequisites
RUN dnf install -y ceph ceph-radosgw rbd-mirror nfs-ganesha-rgw nfs-ganesha-vfs nfs-ganesha-ceph sharutils systemd-udev wget unzip && \
    dnf clean all && \
\
# Install etcdctl
    wget -q -O- "https://github.com/coreos/etcd/releases/download/${ETCDCTL_VERSION}/etcd-${ETCDCTL_VERSION}-${ETCDCTL_ARCH}.tar.gz" |tar xfz - -C/tmp/ etcd-${ETCDCTL_VERSION}-${ETCDCTL_ARCH}/etcdctl && \
    mv /tmp/etcd-${ETCDCTL_VERSION}-${ETCDCTL_ARCH}/etcdctl /usr/local/bin/etcdctl && \
\
# Install kviator
    cd /usr/local/bin && unzip /kviator.zip && chmod +x /usr/local/bin/kviator && rm /kviator.zip && \
\
# Install confd
    chmod +x /usr/local/bin/confd && mkdir -p /etc/confd/conf.d && mkdir -p /etc/confd/templates

# Install kubectl
ADD https://storage.googleapis.com/kubernetes-release/release/${KUBECTL_VERSION}/bin/linux/amd64/kubectl /usr/local/bin/kubectl
RUN chmod +x /usr/local/bin/kubectl

# Install runit
RUN dnf install -y gcc glibc-static
RUN cd / && mkdir -p /package && wget -q -O- "http://smarden.org/runit/runit-2.1.2.tar.gz" |tar xfz - -C/package/ && cd /package/admin/runit-2.1.2/ && ./package/install
RUN mkdir -p /etc/service && chmod +x /etc/service/
RUN dnf remove -y gcc glibc-static

ADD https://github.com/stedolan/jq/releases/download/jq-1.5/jq-linux64 /usr/local/bin/jq
RUN chmod +x /usr/local/bin/jq

ADD entrypoint.sh /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
