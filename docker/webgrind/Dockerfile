FROM php:7.4-apache AS builder

RUN apt-get update
RUN apt-get install -y wget unzip
RUN mkdir -p /build
RUN cd /tmp && \
    wget https://github.com/jokkedk/webgrind/archive/refs/tags/v1.9.3.zip -O webgrind.zip && \
    unzip webgrind.zip && \
    cp -av webgrind-1.9.3/* /build
    
RUN apt-get install -y build-essential zlib1g-dev
RUN cd /build && make

FROM php:7.4-apache
WORKDIR /var/www/html

RUN apt-get update \
    && apt-get install -y graphviz python3 \
    && rm -rf /var/lib/apt/lists/*

COPY --from=builder /build /var/www/html
COPY --from=builder /build/bin/preprocessor /var/www/html/bin/preprocessor
COPY ./src/ /var/www/html/

RUN mkdir -p /opt/webgrind
RUN chmod 777 /opt/webgrind