FROM rust:alpine3.18

RUN apk add musl-dev

WORKDIR /opt/rust

# Download recent geoip data
RUN wget https://git.io/GeoLite2-City.mmdb

RUN mkdir build
COPY src build/src
COPY Cargo.* build/
RUN cd build && cargo build --release && mv target/release/geoip-rs /opt/rust/geoip-rs && cd /opt/rust/ && rm -rf build

ENV GEOIP_RS_DB_PATH=/opt/rust/GeoLite2-City.mmdb
ENV GEOIP_RS_HOST=0.0.0.0
ENV GEOIP_RS_PORT=8080

EXPOSE 8080
ENTRYPOINT ["/opt/rust/geoip-rs"]
