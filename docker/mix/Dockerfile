FROM node:18-alpine

RUN apk add --no-cache curl

RUN USER=node && \
    GROUP=node && \
    curl -SsL https://github.com/boxboat/fixuid/releases/download/v0.5.1/fixuid-0.5.1-linux-amd64.tar.gz | tar -C /usr/local/bin -xzf - && \
    chown root:root /usr/local/bin/fixuid && \
    chmod 4755 /usr/local/bin/fixuid && \
    mkdir -p /etc/fixuid && \
    printf "user: $USER\ngroup: $GROUP\n" > /etc/fixuid/config.yml

ENTRYPOINT ["fixuid"]

RUN mkdir /www
WORKDIR /www/

COPY start.sh /scripts/start.sh
RUN chmod +x /scripts/start.sh

CMD ["/scripts/start.sh"]
