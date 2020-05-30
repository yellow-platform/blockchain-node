# Docker containers setup

## Traefik

Guide: https://www.digitalocean.com/community/tutorials/how-to-use-traefik-as-a-reverse-proxy-for-docker-containers-on-ubuntu-18-04

```
docker run -d \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v $PWD/traefik.toml:/traefik.toml \
  -v $PWD/acme.json:/acme.json \
  -p 80:80 \
  -p 443:443 \
  -l traefik.frontend.rule=Host:monitor.blockchain.yellow.com \
  -l traefik.port=8080 \
  --network web \
  --name traefik \
  traefik:1.7.2-alpine
```

### Add to each container

```
  --label "traefik.XXX.backend=NAME" \
  --label "traefik.XXX.frontend.rule=Host:DOMAIN_NAME" \
  --label "traefik.XXX.port=PORT" \
  --network web \
```

Replace `XXX` by NAME and you can duplicate those labels.

## Bitcoin mainnet

```
docker run --detach \
  --name bitcoind-lncm-mainnet \
  --mount type=volume,source=bitcoind-lncm-mainnet,destination=/root/.bitcoin \
  --publish 8332:8332 \
  --publish 8333:8333 \
  --publish 28332:28332 \
  --publish 28333:28333 \
  --restart=always \
  --label "traefik.rpc.backend=bitcoind-lncm-mainnet" \
  --label "traefik.rpc.frontend.rule=Host:bitcoin.blockchain.yellow.com" \
  --label "traefik.rpc.port=8332" \
  --label "traefik.docker.network=web" \
  --network web \
  lncm/bitcoind:v0.19.0.1
```

## Ethereum mainnet

```
docker run --detach \
  --name geth-mainnet \
  --mount type=volume,source=geth-mainnet,destination=/root/.ethereum \
  --publish 8545:8545 \
  --publish 8546:8546 \
  --publish 8547:8547 \
  --publish 30303:30303 \
  --restart=always \
  --label "traefik.rpc.backend=geth-mainnet" \
  --label "traefik.rpc.frontend.rule=Host:ethereum.blockchain.yellow.com" \
  --label "traefik.rpc.port=8545" \
  --label "traefik.ws.backend=geth-mainnet-ws" \
  --label "traefik.ws.frontend.rule=Host:ws.ethereum.blockchain.yellow.com" \
  --label "traefik.ws.port=8546" \
  --label "traefik.graphql.backend=geth-mainnet-graphql" \
  --label "traefik.graphql.frontend.rule=Host:graphql.ethereum.blockchain.yellow.com" \
  --label "traefik.graphql.port=8547" \
  --label "traefik.docker.network=web" \
  --network web \
  ethereum/client-go:v1.9.14 \
  --nat "extip:49.229.34.132" \
  --light.serve 100 \
  --rpc \
  --rpcaddr "0.0.0.0" \
  --rpcvhosts "*" \
  --rpccorsdomain "*" \
  --ws \
  --wsaddr "0.0.0.0" \
  --wsorigins "*" \
  --rpcapi "eth,net,rpc,web3,debug" \
  --wsapi "eth,net,rpc,web3,debug" \
  --graphql \
  --graphql.addr "0.0.0.0" \
  --graphql.corsdomain "*" \
  --graphql.vhosts "*"
```

## Ethereum ropsten

```
docker run --detach \
  --name geth-ropsten \
  --mount type=volume,source=geth-ropsten,destination=/root/.ethereum \
  --publish 8555:8555 \
  --publish 8556:8556 \
  --publish 8557:8557 \
  --publish 30313:30313 \
  --restart=always \
  --label "traefik.rpc.backend=geth-ropsten" \
  --label "traefik.rpc.frontend.rule=Host:ropsten.ethereum.blockchain.yellow.com" \
  --label "traefik.rpc.port=8555" \
  --label "traefik.ws.backend=geth-ropsten-ws" \
  --label "traefik.ws.frontend.rule=Host:ws.ropsten.ethereum.blockchain.yellow.com" \
  --label "traefik.ws.port=8556" \
  --label "traefik.graphql.backend=geth-ropsten-graphql" \
  --label "traefik.graphql.frontend.rule=Host:graphql.ropsten.ethereum.blockchain.yellow.com" \
  --label "traefik.graphql.port=8557" \
  --label "traefik.docker.network=web" \
  --network web \
  ethereum/client-go:v1.9.14 \
  --port 30313 \
  --nat "extip:49.229.34.132" \
  --light.serve 100 \
  --rpc \
  --rpcaddr "0.0.0.0" \
  --rpcport 8555 \
  --rpcvhosts "*" \
  --rpccorsdomain "*" \
  --ws \
  --wsaddr "0.0.0.0" \
  --wsport 8556 \
  --wsorigins "*" \
  --rpcapi "eth,net,rpc,web3,debug" \
  --wsapi "eth,net,rpc,web3,debug" \
  --graphql \
  --graphql.addr "0.0.0.0" \
  --graphql.port 8557 \
  --graphql.corsdomain "*" \
  --graphql.vhosts "*" \
  --testnet
```

## Ethereum rinkeby

```
docker run --detach \
  --name geth-rinkeby \
  --mount type=volume,source=geth-rinkeby,destination=/root/.ethereum \
  --publish 8565:8565 \
  --publish 8566:8566 \
  --publish 8567:8567 \
  --publish 30323:30323 \
  --restart=always \
  --label "traefik.rpc.backend=geth-rinkeby" \
  --label "traefik.rpc.frontend.rule=Host:rinkeby.ethereum.blockchain.yellow.com" \
  --label "traefik.rpc.port=8565" \
  --label "traefik.ws.backend=geth-rinkeby-ws" \
  --label "traefik.ws.frontend.rule=Host:ws.rinkeby.ethereum.blockchain.yellow.com" \
  --label "traefik.ws.port=8566" \
  --label "traefik.graphql.backend=geth-rinkeby-graphql" \
  --label "traefik.graphql.frontend.rule=Host:graphql.rinkeby.ethereum.blockchain.yellow.com" \
  --label "traefik.graphql.port=8567" \
  --label "traefik.docker.network=web" \
  --network web \
  ethereum/client-go:v1.9.14 \
  --nat "extip:49.229.34.132" \
  --port 30323 \
  --light.serve 100 \
  --rpc \
  --rpcaddr "0.0.0.0" \
  --rpcport 8565 \
  --rpcvhosts "*" \
  --rpccorsdomain "*" \
  --ws \
  --wsaddr "0.0.0.0" \
  --wsport 8566 \
  --wsorigins "*" \
  --rpcapi "eth,net,rpc,web3,debug" \
  --wsapi "eth,net,rpc,web3,debug" \
  --graphql \
  --graphql.addr "0.0.0.0" \
  --graphql.port 8567 \
  --graphql.corsdomain "*" \
  --graphql.vhosts "*" \
  --rinkeby
```

## Ethereum goerli

```
docker run --detach \
  --name geth-goerli \
  --mount type=volume,source=geth-goerli,destination=/root/.ethereum \
  --publish 8575:8575 \
  --publish 8576:8576 \
  --publish 8577:8577 \
  --publish 30333:30333 \
  --restart=always \
  --label "traefik.rpc.backend=geth-goerli" \
  --label "traefik.rpc.frontend.rule=Host:goerli.ethereum.blockchain.yellow.com" \
  --label "traefik.rpc.port=8575" \
  --label "traefik.ws.backend=geth-goerli-ws" \
  --label "traefik.ws.frontend.rule=Host:ws.goerli.ethereum.blockchain.yellow.com" \
  --label "traefik.ws.port=8576" \
  --label "traefik.graphql.backend=geth-goerli-graphql" \
  --label "traefik.graphql.frontend.rule=Host:graphql.goerli.ethereum.blockchain.yellow.com" \
  --label "traefik.graphql.port=8577" \
  --label "traefik.docker.network=web" \
  --network web \
  ethereum/client-go:v1.9.14 \
  --port 30333 \
  --nat "extip:49.229.34.132" \
  --light.serve 100 \
  --rpc \
  --rpcaddr "0.0.0.0" \
  --rpcport 8575 \
  --rpcvhosts "*" \
  --rpccorsdomain "*" \
  --ws \
  --wsaddr "0.0.0.0" \
  --wsport 8576 \
  --wsorigins "*" \
  --rpcapi "eth,net,rpc,web3,debug" \
  --wsapi "eth,net,rpc,web3,debug" \
  --graphql \
  --graphql.addr "0.0.0.0" \
  --graphql.port 8577 \
  --graphql.corsdomain "*" \
  --graphql.vhosts "*" \
  --goerli
```

## Ethereum classic

```
docker run --detach \
  --name multigeth-classic \
  --mount type=volume,source=multigeth-classic,destination=/root/.ethereum \
  --publish 8585:8585 \
  --publish 8586:8586 \
  --publish 8587:8587 \
  --publish 30343:30343 \
  --restart=always \
  --label "traefik.rpc.backend=multigeth-classic" \
  --label "traefik.rpc.frontend.rule=Host:classic.ethereum.blockchain.yellow.com" \
  --label "traefik.rpc.port=8585" \
  --label "traefik.ws.backend=multigeth-classic-ws" \
  --label "traefik.ws.frontend.rule=Host:ws.classic.ethereum.blockchain.yellow.com" \
  --label "traefik.ws.port=8586" \
  --label "traefik.graphql.backend=multigeth-classic-graphql" \
  --label "traefik.graphql.frontend.rule=Host:graphql.classic.ethereum.blockchain.yellow.com" \
  --label "traefik.graphql.port=8587" \
  --label "traefik.docker.network=web" \
  --network web \
  multigeth/multi-geth:version-1.9.16 \
  --port 30343 \
  --nat "extip:49.229.34.132" \
  --light.serve 100 \
  --rpc \
  --rpcaddr "0.0.0.0" \
  --rpcport 8585 \
  --rpcvhosts "*" \
  --rpccorsdomain "*" \
  --ws \
  --wsaddr "0.0.0.0" \
  --wsport 8586 \
  --wsorigins "*" \
  --rpcapi "eth,net,rpc,web3,debug" \
  --wsapi "eth,net,rpc,web3,debug" \
  --graphql \
  --graphql.addr "0.0.0.0" \
  --graphql.port 8587 \
  --graphql.corsdomain "*" \
  --graphql.vhosts "*" \
  --classic
```
