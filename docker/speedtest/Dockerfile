FROM debian:10-slim AS get-speedtest

RUN apt-get update && apt-get install -y curl gnupg1 apt-transport-https dirmngr lsb-release -y
RUN curl -s https://packagecloud.io/install/repositories/ookla/speedtest-cli/script.deb.sh | bash
RUN apt-get install -y speedtest-cli

FROM node:14-alpine as install-dependencies
WORKDIR /build
COPY package*.json ./
RUN npm ci

FROM node:14-alpine as prod-stage
LABEL maintainer="Jonas Friedmann <j@frd.mn>"

WORKDIR /usr/src/app

CMD [ "node", "index.js" ]

COPY --from=get-speedtest /usr/bin/speedtest /usr/bin/speedtest
COPY --from=install-dependencies /build .

RUN apk add --no-cache --virtual .build-deps alpine-sdk python

RUN npm install --production

RUN apk del .build-deps

COPY index.js .

ENV INFLUXDB_HOST=influxdb
ENV INFLUXDB_DB=speedtest
ENV INFLUXDB_USERNAME=root
ENV INFLUXDB_PASSWORD=root
ENV SPEEDTEST_HOST=local
ENV SPEEDTEST_INTERVAL=3600

CMD ["node", "index.js"]
