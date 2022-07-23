# Nibiru testnet network upgrade instructions

The upgrade for the Nibiru Testnet is planned for block 98640. New binary version is going to be `v0.10.0`. You can find [the changelog there](https://github.com/NibiruChain/nibiru/releases/tag/v0.10.0)
You can also check the [upgrade proposal itself](http://ec2-54-221-169-63.compute-1.amazonaws.com:3003/proposals/1)
How to vote "yes" for the upgrade:
```
nibid tx gov vote 1 yes --from %your-account-name% --chain-id nibiru-testnet-3 --yes 
```

## Manual upgrade

Your node is going to stop syncing the blocks at height 98640. You will see the error message in the logs like `ERR UPGRADE "v0.10.0" NEEDED at height: 98640:`
Steps to be taken:
1. Stop your `nibid` binary or service, if you've configured one
2. Update your binary:

Open the folder with the Nibiru git ($HOME/nibiru by default) and update the binary
```
git pull
git fetch --tags
git checkout v0.10.0
make install
```

Launch your binary or service again and confirm it is syncing the blocks with `nibid status 2>&1 | jq .`

## Upgrade using Cosmovisor

If you have Cosmovisor setup for the nibid binary, you should create the corresponding upgrade directory and prepare upgraded binary before the upgrade (automatic download is not yet supported)

```
mkdir -p ~/.nibid/cosmovisor/upgrades/v0.10.0/bin
cd ~/nibiru
git pull
git fetch --tags
git checkout v0.10.0
make build
cp ~/nibiru/build/nibid ~/.nibid/cosmovisor/upgrades/v0.10.0/bin/nibid
```

After that, Cosmovisor should upgrade your binary at block 98640 automatically.
