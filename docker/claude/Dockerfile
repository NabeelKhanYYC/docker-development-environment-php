FROM ubuntu:latest
ARG DEBIAN_FRONTEND=noninteractive

# Update and Upgrade Container
RUN apt-get update && apt-get upgrade -y
RUN apt-get install -y ca-certificates
RUN install -m 0755 -d /etc/apt/keyrings

# Setup Timezone
RUN ln -fs /usr/share/zoneinfo/America/Edmonton /etc/localtime
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends tzdata

# Install Vim and CURL
RUN apt-get install -y vim curl

# Install SSH
RUN apt-get install -y openssh-server && mkdir /var/run/sshd
COPY --from=common ./etc/ssh/sshd.conf /etc/ssh/sshd_config
RUN echo "HostKey /opt/ssh/claude/server" >> /etc/ssh/sshd_config
RUN mkdir -p /root/.ssh/
COPY ./etc/ssh/ssh.conf /root/.ssh/config

# Install Claude
RUN curl -sL https://deb.nodesource.com/setup_24.x | bash -
RUN apt-get install -y nodejs
RUN npm install -g npm
RUN npm install -g @anthropic-ai/claude-code
RUN npm install -g ccusage
COPY ./etc/claude/settings.json /root/.claude/settings.json

RUN echo "cd /opt/project/" >> /root/.profile
WORKDIR /opt/project/

# Bootloader Installation & Setup
COPY ./bin/boot /boot/init
RUN chmod 755 /boot/*

CMD ["/bin/sh", "/boot/init"]