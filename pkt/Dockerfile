FROM alpine:3.13 as packetcrypt_rs-builder


WORKDIR /tmp
RUN apk update \
  # Install dependencies
  && apk add --no-cache gcc git rust cargo make \
  && git clone https://github.com/cjdelisle/packetcrypt_rs \
  && cd packetcrypt_rs \
  && cargo build --release

FROM alpine:3.13 as pktd_go-builder

WORKDIR /tmp
RUN apk update \
  # Install dependencies
  && apk add --no-cache gcc git go \
  && git clone https://github.com/pkt-cash/pktd \
  && cd pktd && ./do

FROM alpine:3.13 as pkt-miner

# Create a group and user
RUN apk update \
  && apk add --no-cache gcc \
  && addgroup -S pkt-group && adduser -S pkt-miner -G pkt-group -h /pkt

# Tell docker that all future commands should run as the pkt-miner user
USER pkt-miner
WORKDIR /pkt

COPY --from=packetcrypt_rs-builder /tmp/packetcrypt_rs/target/release/packetcrypt /usr/local/bin/packetcrypt
COPY --from=pktd_go-builder /tmp/pktd/bin/* /usr/local/bin/
