FROM node:15.11.0-alpine

WORKDIR /app

RUN apk update && \
    apk add curl && \
    npm install && \
    npm install -g agentkeepalive --save && \
    npm install -g npm && \
    npm install -g vue-cli