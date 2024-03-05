FROM lukemathwalker/cargo-chef:latest-rust-1-alpine3.18 AS chef
WORKDIR /app

FROM chef AS planner
COPY src src
COPY Cargo.* .
RUN cargo chef prepare --recipe-path recipe.json

FROM chef AS builder

COPY --from=planner /app/recipe.json recipe.json
# Build dependencies - this is the caching Docker layer!
RUN cargo chef cook --release --recipe-path recipe.json
# Build application
COPY src src
COPY Cargo.* .
RUN cargo build --release --bin geoip-rs
RUN strip /app/target/release/geoip-rs

# Download the latest GeoLite2-City.mmdb
ARG GEOLIGHT_RELEASE=latest
RUN wget -O GeoLite2-City.mmdb https://git.io/GeoLite2-City.mmdb?release=${GEOLIGHT_RELEASE}

FROM debian:buster-slim AS runtime
COPY --from=builder /app/target/release/geoip-rs /usr/local/bin
COPY --from=builder /app/GeoLite2-City.mmdb /opt/GeoLite2-City.mmdb

ARG GEOLIGHT_RELEASE=latest
RUN echo ${GEOLIGHT_RELEASE} > /opt/geo_version

ENV GEOIP_RS_DB_PATH=/opt/GeoLite2-City.mmdb
ENV GEOIP_RS_HOST=0.0.0.0
ENV GEOIP_RS_PORT=8080

EXPOSE 8080
ENTRYPOINT ["/usr/local/bin/geoip-rs"]
