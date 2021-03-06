FROM node:12-alpine AS builder

RUN addgroup -g 10001 app && \
    adduser -D -G app -h /app -u 10001 app

# S3 bucket in Cloud Services prod IAM
ADD https://s3.amazonaws.com/dumb-init-dist/v1.2.0/dumb-init_1.2.0_amd64 /usr/local/bin/dumb-init
RUN chmod +x /usr/local/bin/dumb-init
ENTRYPOINT ["/usr/local/bin/dumb-init", "--"]

RUN apk add --repository http://dl-cdn.alpinelinux.org/alpine/v3.11/community/ --no-cache graphicsmagick=1.3.35-r0 git && \
    apk add --repository http://dl-cdn.alpinelinux.org/alpine/v3.9/community/ --no-cache --virtual .build-deps git python make g++

COPY --chown=app:app fxa-profile-server /app
COPY --chown=app:app ["fxa-shared", "../fxa-shared/"]

USER app
WORKDIR /app

RUN npm install --production && rm -rf ~app/.npm

WORKDIR /fxa-shared
RUN npm ci

USER root
RUN chown -R app:app /fxa-shared
USER app

# Build final image by copying from builder
FROM node:12-alpine

RUN addgroup -g 10001 app && \
    adduser -D -G app -h /app -u 10001 app
WORKDIR /app

# S3 bucket in Cloud Services prod IAM
ADD https://s3.amazonaws.com/dumb-init-dist/v1.2.0/dumb-init_1.2.0_amd64 /usr/local/bin/dumb-init
RUN chmod +x /usr/local/bin/dumb-init
ENTRYPOINT ["/usr/local/bin/dumb-init", "--"]

RUN apk add --repository http://dl-cdn.alpinelinux.org/alpine/v3.11/community/ --no-cache graphicsmagick=1.3.35-r0 git

USER app

COPY --from=builder --chown=app /app/ /app/
COPY --from=builder --chown=app /fxa-shared/ /fxa-shared/
