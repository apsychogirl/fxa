FROM node:12-alpine

RUN apk add --no-cache make git gcc g++ python

RUN addgroup -g 10001 app && \
    adduser -D -G app -h /app -u 10001 app
WORKDIR /app

USER app

COPY --chown=app:app package-lock.json package-lock.json
COPY --chown=app:app package.json package.json

RUN npm ci --production && rm -rf ~app/.npm

COPY --chown=app:app . /app
