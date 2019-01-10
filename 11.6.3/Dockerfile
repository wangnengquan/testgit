FROM ubuntu:18.04

# Install required packages
RUN apt update -q \
    && apt upgrade -yq \
    && apt install -yq curl openssh-server \
        ca-certificates

# Download & Install GitLab
RUN curl -sS https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | bash
RUN EXTERNAL_URL="http://gitlab.suanzaozi.com" apt-get install gitlab-ce=11.6.3-ce.0

# Prepare default configuration
RUN ( \
    echo "" && \
    echo "# Docker options" && \
    echo "# Prevent Postgres from trying to allocate 25% of total memory" && \
    echo "postgresql['shared_buffers'] = '1MB'" ) >> /etc/gitlab/gitlab.rb && \
    mkdir -p /assets/ && \
    cp /etc/gitlab/gitlab.rb /assets/gitlab.rb

# Install locales
RUN set -xe \
    && export DEBIAN_FRONTEND=noninteractive \
    && apt update \
    && apt install -yq locales tzdata \
    && locale-gen en_US.UTF-8 \
    && rm -rf /var/lib/apt/lists/*

# Expose web & ssh
EXPOSE 443 80 22

ENV LANG=en_US.UTF-8 LANGUAGE=en_US:en LC_ALL=en_US.UTF-8
ENV TZ=Asia/Shanghai

# Define data volumes
VOLUME ["/etc/gitlab", "/var/opt/gitlab", "/var/log/gitlab"]

# Copy assets
COPY assets/wrapper /usr/local/bin/

# Wrapper to handle signal, trigger runit and reconfigure GitLab
CMD ["/usr/local/bin/wrapper"]