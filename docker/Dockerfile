FROM alpine:3.2

ENV GOROOT /usr/lib/go
ENV GOPATH /gopath
ENV GOBIN /gopath/bin
ENV PATH $PATH:$GOROOT/bin:$GOPATH/bin

RUN \
  apk add --update \
  bzr \
  g++ \
  git \
  go \
  gmp-dev \
  mercurial

RUN \
  go get -v github.com/tools/godep

RUN \
  godep get -v github.com/ethereum/go-ethereum/cmd/geth

RUN \
  cd /gopath/src/github.com/ethereum/go-ethereum/cmd/geth && \
  godep go build -a --ldflags '-extldflags "-static"' . && \
  mv geth $GOBIN/

RUN \
  apk del --purge -r \
    bzr \
    g++ \
    git \
    go \
    gmp-dev \
    mercurial && \
  rm -rf /gopath/pkg && \
  rm -rf /gopath/src && \
  rm -rf /var/cache/apk/*

# As of 2015-08-04 Docker on OSX + Boot2Docker mounts drives as VBOXFS which is
# unusable by LevelDB. Use this script to mount instead as NFS.
# https://gist.github.com/neilbartley/73f2eb334f04bf95a906

VOLUME ["/data", "/ipc"]

WORKDIR /data

# RPC port
EXPOSE 8545

# Network listening port
EXPOSE 30303

ENTRYPOINT ["/gopath/bin/geth"]

CMD [ \
  "--datadir", "/data", \
  "--genesis", "/data/genesis_block.json", \
  "--ipcpath", "/ipc/geth.ipc", \
  "--logtostderr", \
  "--rpc", \
  "--rpcaddr", "0.0.0.0", \
  "--rpcapi", "admin,db,eth,debug,miner,net,shh,txpool,personal,web3", \
  "--rpcport", "8545", \
  "--rpccorsdomain", "*" \
]
