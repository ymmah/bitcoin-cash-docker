# Copyright (c) 2018 nChain Ltd
# Distributed under the GNU GPL v3.0, see accompanying file LICENSE for details
# based on work by Adrian Macneil from https://github.com/amacneil/docker-bitcoin
FROM debian:stretch-slim

ENV BCH_VERSION 0.11.0-I
ENV BCH_URL https://github.com/bitcoinxt/bitcoinxt/releases/download/v0.11I/bitcoin-xt-0.11.0-I-linux64.tar.gz
ENV BCH_SHA256 a99b66f757fa9b476fdf91c7249f8bc83eb4130cf9a946d5c5db0a3c78570614

RUN apt-get update \
	&& apt-get install -y wget \
	&& wget "$BCH_URL" -O /tmp/bitcoin.tar.gz
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

