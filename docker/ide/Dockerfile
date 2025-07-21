FROM ubuntu:latest
ARG DEBIAN_FRONTEND=noninteractive  

# Update and Upgrade VM
RUN apt-get update && apt-get upgrade -y
RUN apt-get install -y ca-certificates
RUN install -m 0755 -d /etc/apt/keyrings

# Setup Timezone
RUN ln -fs /usr/share/zoneinfo/America/Edmonton /etc/localtime
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends tzdata

# Install CURL
RUN apt-get install -y curl netcat-openbsd

# Install SSH
RUN apt-get install -y openssh-server && mkdir /var/run/sshd
COPY --from=common ./etc/ssh/sshd.conf /etc/ssh/sshd_config
RUN echo "HostKey /opt/ssh/ide/server" >> /etc/ssh/sshd_config
RUN mkdir -p /root/.ssh/
COPY ./etc/ssh/ssh.conf /root/.ssh/config

# Install VS Code Server
RUN mkdir -p /opt/vscode/ /opt/vscode/data/config/
RUN CODE_RELEASE=$(curl -sX GET https://api.github.com/repos/coder/code-server/releases/latest \
    | awk '/tag_name/{print $4;exit}' FS='[""]' | sed 's|^v||'); \
    curl -o \
    /tmp/code-server.tar.gz -L \
    "https://github.com/coder/code-server/releases/download/v${CODE_RELEASE}/code-server-${CODE_RELEASE}-linux-amd64.tar.gz" && \
    tar xf /tmp/code-server.tar.gz -C /opt/vscode/ --strip-components=1 && \
    rm -f /tmp/code-server.tar.gz
COPY ./etc/vscode/ /opt/vscode/data/config/

# Install PHP
RUN apt-get install -y software-properties-common
RUN add-apt-repository ppa:ondrej/php && apt update
RUN apt-get install -y php8.3-cli php8.3-dev php-pear 
COPY --from=common ./etc/php/ /etc/php/8.3/cli/

# Install PHP: XDebug
RUN apt-get install -y php8.3-xml
RUN pecl install xdebug

# Install PHP: Composer
RUN apt-get install -y git
RUN apt-get install -y zip
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/bin/ --filename=composer

# Install Cron
RUN apt-get -y install cron
COPY ./etc/cron/crontab /etc/cron.d/default

# Cleanup
RUN apt-get -y autoremove && apt-get -y clean

# Setup the Project Directory
RUN mkdir -p /opt/project
WORKDIR /opt/project

# Copy over Binaries and Scripts
RUN mkdir -p /opt/bin/
COPY ./bin/backup /opt/bin/
RUN chmod 755 /opt/bin/*

# Bootloader Installation & Setup
COPY ./bin/boot /boot/init
RUN chmod 755 /boot/init
CMD ["/bin/bash", "/boot/init"]