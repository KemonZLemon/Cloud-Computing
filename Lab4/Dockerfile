# Base image: Kali Linux rolling release
FROM kalilinux/kali-rolling

# Pin a reliable, Cloudflare-backed mirror instead of relying on the
# http.kali.org geo-redirector, which can hand out dead/unreachable
# third-party mirrors depending on your network.
# Newer Kali base images ALSO define sources in /etc/apt/sources.list.d/
# (deb822 .sources format) -- remove those too, or apt will use both.
# Using plain HTTP here (not HTTPS) to avoid TLS interception issues on
# some networks; package integrity is still guaranteed by GPG-signed
# repo metadata regardless of transport encryption.
RUN rm -f /etc/apt/sources.list.d/*.sources /etc/apt/sources.list.d/*.list && \
    echo "deb http://kali.download/kali kali-rolling main non-free non-free-firmware contrib" \
        > /etc/apt/sources.list

# Make apt more resilient to any remaining transient network blips.
RUN echo 'Acquire::Retries "5";' > /etc/apt/apt.conf.d/80-retries && \
    echo 'Acquire::http::Timeout "60";' >> /etc/apt/apt.conf.d/80-retries && \
    echo 'Acquire::https::Timeout "60";' >> /etc/apt/apt.conf.d/80-retries

# Install base packages first (fast, so this layer caches quickly)
RUN apt-get update && \
    apt-get install -y \
        git \
        python3-pip \
        pciutils \
        vim \
        gnupg \
        curl \
        openssh-client \
        net-tools

# Install the full pentesting toolset separately.
# This is the slow, large install -- keeping it in its own layer means
# a failure here doesn't force redoing the base packages above.
RUN apt-get update && \
    apt-get install -y kali-linux-headless

# Set up /root/.ssh and known_hosts to avoid host-verification prompts
RUN mkdir -p /root/.ssh && \
    touch /root/.ssh/known_hosts && \
    ssh-keyscan bitbucket.org >> /root/.ssh/known_hosts && \
    ssh-keyscan github.com >> /root/.ssh/known_hosts

RUN apt-get clean

CMD ["/bin/bash"]
