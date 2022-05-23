# Nibiru Testnets

Everything you need to join the latest Nibiru Testnet.

| Network | Chain ID         | Version | Description      |
|---------|------------------|---------|------------------|
| Testnet | Nibiru-testnet-1 | v0.0.1  | Nibiru testnet 1 |

First, make sure that you have the correct Nibiru version installed according to
the instructions [here](https://github.com/NibiruChain/nibiru).

## Connect to the Testnet

### Initialize the node

```
nibid init <your-node-name> --chain-id <chain-id>
```

### Download genesis file

```
curl https://raw.githubusercontent.com/NibiruDao/Networks/main/Testnet/genesis.json > $HOME/.Nibiru/config/genesis.json
```

### Double check the genesis file checksum

```

```

### Update your gas prices

```
sudo nano $HOME/.nibid/config/app.toml
# recommended to set to "0.025unibi"
```

### Configure your config file

```
sudo nano $HOME/.nibid/config/config.toml
```

- Set external address
- Add seeds and/or persistent peers
- Set pex and private_peer_ids according to your sentry node architecture

### Start your node

```
nibid start
```

Within a few minutes you should start connecting to peers and catching up blocks.

## Create a validator

### Add keys

```
nibid keys add <key-name>
```

Fund your wallet from the faucet and make sure that your node has caught up to the latest block.

### Send create-validator transaction

TODO
