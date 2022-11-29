# SPDX-FileCopyrightText: 2021 The HedgeDoc developers (see AUTHORS file)
#
# SPDX-License-Identifier: CC-BY-SA-4.0

# BUILD
FROM node:18-alpine@sha256:9eff44230b2fdcca57a73b8f908c8029e72d24dd05cac5339c79d3dedf6b208b AS builder
ENV NODE_ENV=production
ENV NEXT_TELEMETRY_DISABLED=1
ARG BUILD_VERSION=CLIENT_VERSION_MISSING

USER node
WORKDIR /usr/src/app

COPY --chown=node .yarn .yarn
COPY --chown=node .yarnrc.yml .yarnrc.yml
COPY --chown=node package.json package.json
COPY --chown=node frontend frontend

WORKDIR /usr/src/app/frontend

RUN rm -rf public/public && \
    rm -rf src/pages/api && \
    yarn install --immutable && \
    sed -i "s/CLIENT_VERSION_MISSING/${BUILD_VERSION}/" src/version.json && \
    yarn build

# RUNNER
FROM node:18-alpine@sha256:9eff44230b2fdcca57a73b8f908c8029e72d24dd05cac5339c79d3dedf6b208b
ENV NODE_ENV=production
ENV NEXT_TELEMETRY_DISABLED=1

WORKDIR /usr/src/app

COPY --from=builder /usr/src/app/frontend/next.config.js ./
COPY --from=builder /usr/src/app/frontend/public ./public
COPY --from=builder --chown=node:node /usr/src/app/frontend/.next/static ./.next/static
COPY --from=builder --chown=node:node /usr/src/app/frontend/.next/standalone ./

USER node

ENV PORT 3001
EXPOSE 3001/tcp
CMD ["node", "server.js"]
