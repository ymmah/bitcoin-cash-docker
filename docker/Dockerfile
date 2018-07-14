# Copyright (c) 2018 nChain Ltd
# Distributed under the GNU GPL v3.0, see accompanying file LICENSE for details
# based on work by Adrian Macneil from https://github.com/amacneil/docker-bitcoin
FROM debian:stretch-slim

ENV BCH_VERSION 0.11.0-J
ENV BCH_URL https://github.com/bitcoinxt/bitcoinxt/releases/download/v0.11J/bitcoin-xt-0.11.0-J-linux64.tar.gz
ENV BCH_SHA256 7c3ce442a50a5d1d04d4a6ddb011f278ca90cd22ac50c56726ca6ad0db24b6de

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

