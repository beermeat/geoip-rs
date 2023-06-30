FROM debian:trixie-20230612-slim

RUN apt-get update && apt-get install -y wget gcc

ENV RUST_ARCHIVE=rust-1.70.0-x86_64-unknown-linux-gnu.tar.gz
ENV RUST_DOWNLOAD_URL=https://static.rust-lang.org/dist/$RUST_ARCHIVE
WORKDIR /opt/rust

RUN cd /opt/rust \
    && mkdir build \
    && cd build \
    && wget -q $RUST_DOWNLOAD_URL \
    && wget -q -O - $RUST_DOWNLOAD_URL.sha256 | sha256sum -c - \
    && tar -xzf $RUST_ARCHIVE --strip-components=1 \
    && ./install.sh --without=rust-docs \
    && cd /opt/rust \
    && rm -rf build

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
