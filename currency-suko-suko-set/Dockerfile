FROM ubuntu AS base

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get upgrade -y

RUN apt-get install -y \
  build-essential \
  libtool autotools-dev autoconf \
  libssl-dev \
  libboost-all-dev \
  libevent-dev \
  pkg-config

RUN apt-get install -y \
  software-properties-common \
  git \
  curl \
  bsdmainutils

RUN add-apt-repository -y ppa:bitcoin/bitcoin && \
  apt-get update && \
  apt-get install -y libdb4.8-dev libdb4.8++-dev

RUN apt-get install wget


FROM base AS bitzeny
ARG ZNY_REF=z2.0.0a
RUN git clone https://github.com/BitzenyCoreDevelopers/bitzeny.git bitzeny -b "$ZNY_REF" --depth=1 && \
  cd bitzeny && \
  ./autogen.sh && \
  ./configure --prefix=/usr --without-miniupnpc --without-gui --disable-tests && \
  make -j4


FROM base AS koto
ARG KOTO_REF=master
RUN git clone https://github.com/koto-dev/koto.git koto -b "$KOTO_REF" --depth=1 && \
  cd koto && \
  ./zcutil/fetch-params.sh && \
  ./zcutil/build.sh --disable-rust -j4 && \
  make -j4 && make install

FROM base AS monacoin
ARG MONA_REF=v0.14.2rc1
RUN git clone https://github.com/monacoinproject/monacoin.git monacoin -b "$MONA_REF" --depth=1 && \
  cd monacoin && \
  ./autogen.sh && \
  ./configure --prefix=/usr --without-miniupnpc --without-gui --disable-tests && \
  make -j4


FROM ubuntu

COPY --from=bitzeny /bitzeny/src/bitzenyd /usr/bin/bitzenyd
COPY --from=koto /koto/src/kotod /usr/bin/kotod
COPY --from=monacoin /monacoin/src/monacoind /usr/bin/monacoind

RUN apt-get update && \
  apt-get upgrade -y && \
  apt-get install -y \
    libssl-dev \
    libboost-all-dev \
    libevent-dev \
    software-properties-common && \
  add-apt-repository -y ppa:bitcoin/bitcoin && \
  apt-get update && \
  apt-get install -y libdb4.8-dev libdb4.8++-dev && \
  apt-get autoremove -y software-properties-common && \
  apt-get clean && \
  ldd $(which bitzenyd kotod monacoind)

VOLUME /root/.bitzeny
VOLUME /root/.koto
VOLUME /root/.monacoin

# TOOD: make entrypoint
ENTRYPOINT [ "false" ]
