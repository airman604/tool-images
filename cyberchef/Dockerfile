# Adapted from official https://github.com/gchq/CyberChef/blob/master/Dockerfile
FROM node:18-alpine AS build

ENV VERSION=v10.19.4

WORKDIR /src
RUN git clone --branch ${VERSION} --depth 1 https://github.com/gchq/CyberChef.git .
RUN npm ci
RUN npm run build

FROM nginx:1.27-alpine3.21

COPY --from=build ./build/prod /usr/share/nginx/html/
