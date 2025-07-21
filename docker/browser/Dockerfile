FROM nginx:latest

COPY ./etc/nginx/ /etc/nginx/

# Bootloader Installation & Setup
COPY ./bin/boot /boot/init
RUN chmod 755 /boot/*

ENTRYPOINT []
CMD ["/bin/sh", "/boot/init"]