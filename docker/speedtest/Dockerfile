FROM debian:10-slim AS get-speedtest

RUN apt-get update && apt-get install -y curl gnupg1 apt-transport-https dirmngr lsb-release -y
RUN curl -s https://packagecloud.io/install/repositories/ookla/speedtest-cli/script.deb.sh | bash
RUN apt-get install -y speedtest-cli

FROM node:18-alpine as install-dependencies
RUN apk add --no-cache npm
WORKDIR /build
COPY . .
RUN npm ci

FROM alpine as prod-stage
RUN apk add --no-cache nodejs
LABEL maintainer="Jonas Friedmann <j@frd.mn>"

WORKDIR /usr/src/app

CMD [ "node", "index.js" ]

COPY --from=get-speedtest /usr/bin/speedtest /usr/bin/speedtest
COPY --from=install-dependencies /build .
