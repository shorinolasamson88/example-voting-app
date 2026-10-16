FROM node:24-slim AS base

# Install utilities needed by the container
RUN apt-get update && \
    apt-get install -y --no-install-recommends curl tini && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /usr/local/app

# Install dependencies using the lockfile
COPY package*.json ./

RUN npm ci && \
    npm cache clean --force && \
    mv node_modules /node_modules

# Development image: includes nodemon
FROM base AS dev

RUN npm install -g nodemon

COPY . .

ENV PORT=80

EXPOSE 80

ENTRYPOINT ["/usr/bin/tini", "--"]

CMD ["node", "server.js"]

# Production image
FROM base AS final

COPY --chown=node:node . .

ENV PORT=80

EXPOSE 80

# Run as the unprivileged user provided by the Node image
USER node

ENTRYPOINT ["/usr/bin/tini", "--"]

CMD ["node", "server.js"]
