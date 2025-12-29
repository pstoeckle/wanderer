FROM curlimages/curl:8.17.0 AS download-env

ENV CURL_VERSION=8.17.0

RUN set -eux ; \
    ARCHITECTURE="$(uname -m)" ; \
    case $ARCHITECTURE in \
    x86_64) ARCHITECTURE="x86_64" ;; \
    aarch64 | armv8* | arm64) ARCHITECTURE="aarch64-glibc" ;; \
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
    "https://github.com/stunnel/static-curl/releases/download/${CURL_VERSION}/curl-linux-${ARCHITECTURE}-${CURL_VERSION}.tar.xz" \
    ; \
    tar -xJf /tmp/curl.tar.xz -C /tmp ; \
    chmod +x /tmp/curl ;

FROM golang AS build

WORKDIR /app

COPY . .

RUN go mod download
RUN CGO_ENABLED=0 go build -o /app/pocketbase

FROM scratch

WORKDIR /

COPY --from=build /app/pocketbase /pocketbase
COPY --from=download-env /tmp/curl /curl
COPY migrations ./migrations
COPY templates ./templates

ENV MEILI_URL=http://localhost:7700 \
    MEILI_MASTER_KEY= \
    POCKETBASE_ENCRYPTION_KEY=

EXPOSE 8090

ENTRYPOINT ["/pocketbase", "serve", "--http=0.0.0.0:8090", "--dir=/pb_data"]
