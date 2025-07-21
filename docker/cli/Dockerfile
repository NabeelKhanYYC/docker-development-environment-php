FROM php:8.3-cli
ARG DEBIAN_FRONTEND=noninteractive

# Update and Upgrade VM
RUN apt-get update && apt-get upgrade -y
RUN apt-get install -y ca-certificates
RUN install -m 0755 -d /etc/apt/keyrings

# Setup Timezone
RUN ln -fs /usr/share/zoneinfo/America/Edmonton /etc/localtime
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends tzdata

# Install SSH
RUN apt-get install -y openssh-server && mkdir /var/run/sshd
COPY --from=common ./etc/ssh/sshd.conf /etc/ssh/sshd_config
RUN echo "HostKey /opt/ssh/cli/server" >> /etc/ssh/sshd_config
RUN mkdir -p /root/.ssh/
COPY ./etc/ssh/ssh.conf /root/.ssh/config

# Install PHP: XDebug
RUN apt-get install -y libxml2-dev
RUN docker-php-ext-install xml
RUN pecl install xdebug

# Install PHP: Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/bin/ --filename=composer

# Copy PHP Configuration
COPY --from=common ./etc/php/ /usr/local/etc/php/

# Bootloader Installation & Setup
COPY ./bin/boot /boot/init
RUN chmod 755 /boot/init
CMD ["/bin/bash", "/boot/init"]