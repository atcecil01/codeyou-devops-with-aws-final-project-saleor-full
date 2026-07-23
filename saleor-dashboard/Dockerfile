# --- STAGE 1: Builder ---
FROM node:18-alpine as builder

RUN apk update && apk add bash --no-cache

WORKDIR /app

COPY package.json package-lock.json /app/

ENV CI=1
RUN npm ci --legacy-peer-deps

COPY . .

ARG API_URL
ENV API_URL=${API_URL:-'http://localhost:8000/graphql/'}

ARG APP_MOUNT_URI
ENV APP_MOUNT_URI=${APP_MOUNT_URI:-'/dashboard/'}

ARG APPS_MARKETPLACE_API_URL
ENV APPS_MARKETPLACE_API_URL=${APPS_MARKETPLACE_API_URL:-'https://apps.saleor.io/api/v2/saleor-apps'}

ARG APPS_TUNNEL_URL_KEYWORDS
ENV APPS_TUNNEL_URL_KEYWORDS=${APPS_TUNNEL_URL_KEYWORDS}

ARG STATIC_URL
ENV STATIC_URL=${STATIC_URL:-'/dashboard/'}

ARG SKIP_SOURCEMAPS
ENV SKIP_SOURCEMAPS=${SKIP_SOURCEMAPS:-'true'}

ARG LOCALE_CODE
ENV LOCALE_CODE=${LOCALE_CODE:-'EN'}

RUN npm run build


# --- STAGE 2: Runner ---
FROM nginx:alpine

WORKDIR /app

COPY --from=builder /app/build/ /app/
COPY ./nginx/default.conf /etc/nginx/conf.d/default.conf
COPY ./nginx/replace-env-vars.sh /docker-entrypoint.d/50-replace-env-vars.sh
