# Adapted from official https://github.com/excalidraw/excalidraw/blob/master/Dockerfile
FROM node:18 AS build
# builds fail when using v*.*.* tags ¯\_(ツ)_/¯
ARG VERSION=master

RUN apt-get update && apt-get install -y git

WORKDIR /opt/node_app
RUN git clone --branch ${VERSION} --depth 1 https://github.com/excalidraw/excalidraw.git .

RUN yarn --network-timeout 600000

ARG NODE_ENV=production
RUN yarn build:app:docker

FROM nginx:1.27-alpine
COPY --from=build /opt/node_app/excalidraw-app/build /usr/share/nginx/html
HEALTHCHECK CMD wget -q -O /dev/null http://localhost || exit 1
