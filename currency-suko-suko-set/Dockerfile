FROM ubuntu AS base

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && \
  apt-get upgrade -y && \
  apt-get install -y build-essential \
    libtool autotools-dev autoconf \
    libssl-dev \
    libboost-all-dev \
    libevent-dev \
    pkg-config \
    software-properties-common \
    git \
    wget \
    curl && \
  add-apt-repository -y ppa:bitcoin/bitcoin && \
  apt-get update && \
  apt-get install -y libdb4.8-dev libdb4.8++-dev


FROM base AS bitzeny
ARG ZNY_REF=z2.0.0a
RUN git clone https://github.com/BitzenyCoreDevelopers/bitzeny.git /bitzeny && \
  cd /bitzeny && \
  git checkout "$ZNY_REF" && \
  ./autogen.sh && \
  ./configure --prefix=/usr --without-miniupnpc --without-gui --disable-tests && \
  make -j4


FROM base AS koto
ARG KOTO_REF=v1.0.14
RUN git clone https://github.com/koto-dev/koto.git koto && \
  cd koto && \
  git checkout "$KOTO_REF" && \
  ./zcutil/fetch-params.sh && \
  ./zcutil/build.sh --disable-rust -j4 && \
  make

FROM base AS monacoin
ARG MONA_REF=v0.14.2rc1
RUN git clone https://github.com/monacoinproject/monacoin.git monacoin && \
  cd monacoin && \
  git checkout "$MONA_REF" && \
  ./autogen.sh && \
  ./configure --prefix=/usr --without-miniupnpc --without-gui --disable-tests && \
  make -j4


FROM ubuntu

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
  apt-get clean

COPY --from=bitzeny /bitzeny/src/bitzenyd /usr/bin/bitzenyd
COPY --from=koto /koto/src/kotod /usr/bin/kotod
COPY --from=monacoin /monacoin/src/monacoind /usr/bin/monacoind

VOLUME /root/.bitzeny
VOLUME /root/.koto
VOLUME /root/.monacoin
EXPOSE 9252 9253

# TOOD: make entrypoint
ENTRYPOINT [ "false" ]
