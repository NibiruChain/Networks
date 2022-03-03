# Matrix Testnets

Everything you need to join the latest Matrix Testnet.

Network | Chain ID | Version | Description
------- | -------- | ------- | -----------
Testnet | matrix-testnet-1 | v0.0.1 | Matrix testnet 1

First, make sure that you have the correct Matrix version installed according to the instructions [here](https://github.com/MatrixDao/matrix).

## Connect to the Testnet

Initialize the node

```
matrixd init <your-node-name> --chain-id <chain-id>
```

Download genesis file

```
curl https://raw.githubusercontent.com/MatrixDao/Networks/main/Testnet/genesis.json > $HOME/.matrix/config/genesis.json
```

Double check the genesis file checksum

```

```

Update your gas prices

```
sudo nano $HOME/.matrix/config/app.toml
# recommended to set to "0.025umatrx"
```

Configure your config file

```
sudo nano $HOME/.matrix/config/config.toml
```

- Set external address
- Add seeds and/or persistent peers
- Set pex and private_peer_ids according to your sentry node architecture

Start your node

```
matrixd start
```

Within a few minutes you should start connecting to peers and catching up blocks.

## Create a validator

Add keys

```
matrixd keys add <key-name>
```

Fund your wallet from the faucet and make sure that your node has caught up to the latest block.

Send create-validator transaction

```
matrixd
```
