FROM php:8.3-apache
ARG DEBIAN_FRONTEND=noninteractive

# Update and Upgrade VM
RUN apt-get update && apt-get upgrade -y
RUN apt-get install -y ca-certificates
RUN install -m 0755 -d /etc/apt/keyrings

# Setup Timezone
RUN ln -fs /usr/share/zoneinfo/America/Edmonton /etc/localtime
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends tzdata

# Install PHP: XDebug
RUN apt-get install -y libxml2-dev
RUN docker-php-ext-install xml
RUN pecl install xdebug

# Copy PHP Configuration
COPY --from=common ./etc/php/ /usr/local/etc/php/

# Bootloader Installation & Setup
COPY ./bin/boot /boot/init
RUN chmod 755 /boot/init
ENTRYPOINT []
CMD ["/bin/bash", "/boot/init"]