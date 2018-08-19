# Copyright (c) 2018 nChain Ltd
# Distributed under the GNU GPL v3.0, see accompanying file LICENSE for details
# based on work by Adrian Macneil from https://github.com/amacneil/docker-bitcoin
FROM debian:stretch-slim

ENV BCH_VERSION 1.4.0.0
ENV BCH_URL https://www.bitcoinunlimited.info/downloads/BUcash-${BCH_VERSION}-linux64.tar.gz
ENV BCH_SHA256 1d9ea2e84e197f190a8a62bff615d8a7ead3972783b037920628397106121fa1

ADD $BCH_URL /tmp/bitcoin.tar.gz
RUN cd /tmp \
	&& echo "$BCH_SHA256  bitcoin.tar.gz" | sha256sum -c - \
	&& tar -xzvf bitcoin.tar.gz -C /usr/local --strip-components=1 --exclude=*-qt \
	&& rm bitcoin.tar.gz

RUN addgroup bitcoin && adduser --gecos "" --home /home/bitcoin --disabled-password --ingroup bitcoin bitcoin
ENV BCH_DATA /data
RUN mkdir "$BCH_DATA" \
	&& chown -R bitcoin:bitcoin "$BCH_DATA" \
	&& ln -sfn "$BCH_DATA" /home/bitcoin/.bitcoin \
	&& chown -h bitcoin:bitcoin /home/bitcoin/.bitcoin
VOLUME /data

COPY entrypoint.sh /entrypoint.sh
USER bitcoin
ENTRYPOINT ["/entrypoint.sh"]

EXPOSE 8332 8333 18332 18333
CMD ["bitcoind"]

