# Adapted from official https://github.com/probot/smee.io/blob/master/Dockerfile

# A temporary image that installs dependencies and
# builds the production-ready front-end bundles.

FROM node:22-alpine AS bundles
ARG VERSION=v2.0.19

RUN apk add --no-cache git
RUN git clone --branch ${VERSION} --depth 1 https://github.com/probot/smee.io.git /src

WORKDIR /usr/src/smee.io
RUN cp /src/package*.json ./
RUN cp /src/webpack.config.js ./
RUN cp -a /src/src ./src
RUN ls
# Install the project's dependencies and build the bundles
RUN npm ci
RUN env NODE_ENV=production npm run build
RUN env NODE_ENV=production npm prune

# --------------------------------------------------------------------------------

FROM node:22-alpine
LABEL description="Smee.io"

# Let's make our home
WORKDIR /usr/src/smee.io

RUN chown node:node /usr/src/smee.io -R

# This should be our normal running user
USER node

# Copy our dependencies
COPY --chown=node:node --from=bundles /usr/src/smee.io/node_modules /usr/src/smee.io/node_modules

# Copy our front-end code
COPY --chown=node:node --from=bundles /usr/src/smee.io/public /usr/src/smee.io/public

# We should always be running in production mode
ENV NODE_ENV=production

# Copy various scripts and files
COPY --chown=node:node --from=bundles /src/public ./public
COPY --chown=node:node --from=bundles /src/lib ./lib
COPY --chown=node:node --from=bundles /src/index.js ./index.js
COPY --chown=node:node --from=bundles /src/package*.json ./

EXPOSE 3000
CMD ["npm", "start"]
