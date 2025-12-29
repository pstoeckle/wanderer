FROM curlimages/curl:8.17.0 AS download-env

ENV CURL_VERSION=8.17.0

RUN set -eux ; \
    ARCHITECTURE="$(uname -m)" ; \
    case $ARCHITECTURE in \
    x86_64) ARCHITECTURE="x86_64" ;; \
    aarch64 | armv8* | arm64) ARCHITECTURE="aarch64" ;; \
    *) \
    echo "(!) Architecture $ARCHITECTURE unsupported" ; \
    exit 1 \
    ;; \
    esac ; \
    curl \
    --connect-timeout 10 \
    --fail \
    --location \
    --max-time 300 \
    --output /tmp/curl.tar.xz \
    --proto '=https' \
    --show-error \
    --silent \
    --tlsv1.2 \
    "https://github.com/stunnel/static-curl/releases/download/${CURL_VERSION}/curl-linux-${ARCHITECTURE}-glibc-${CURL_VERSION}.tar.xz" \
    ; \
    tar -xJf /tmp/curl.tar.xz -C /tmp ; \
    chmod +x /tmp/curl ;

FROM node:22-bookworm@sha256:c8abd8da9cfddd2dfd2d5aa9ea2e54f9f70d3968ecf81bf5c2422594fa13fa83 AS build-env

WORKDIR /app

COPY ["package.json", "package-lock.json*", "./"]

RUN npm ci

COPY . .

RUN npm run build

FROM gcr.io/distroless/nodejs22-debian12:nonroot@sha256:9187fd451b5e2d258d691cb7da51020bfc8f557cc4a58e80b38f3f30bbbc37f9

WORKDIR /app/uploads
WORKDIR /app

COPY --from=download-env /tmp/curl /curl
COPY --from=build-env /app /app

CMD ["build/index.js"]

EXPOSE 3000

ENV \
    MEILI_URL=http://127.0.0.1:7700 \
    PUBLIC_DISABLE_SIGNUP=false \
    PUBLIC_OVERPASS_API_URL= \
    PUBLIC_POCKETBASE_URL=http://127.0.0.1:8090 \
    UPLOAD_PASSWORD= \
    UPLOAD_USER= \
