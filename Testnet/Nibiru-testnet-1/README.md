# Nibiru-1 Testnet Instructions

## Minimum hardware requirements

- 2CPU
- 4GB RAM
- 100GB of disk space (SSD)

## Install prerequisites and Nibiru binary


### 1. Update the system

```bash
sudo apt update
sudo apt upgrade --yes
```

### 3. Install prerequisites

```bash
sudo apt install git build-essential ufw curl jq snapd make gcc --yes
```

### 3. Install Golang

```bash
wget -q -O - https://git.io/vQhTU | bash -s -- --version 1.18
```

After the installation open a new terminal to properly load go or run `source $HOME/.bashrc`

### 4. Install Nibiru from the repository

```bash
cd $HOME
git clone https://github.com/NibiruChain/nibiru && cd nibiru
git checkout v0.4.21
```
or extract the archive received from the Nibiru team.

In this repository, run 
```bash
make install
```

Verify the binary version (should be v0.4.14):

```bash
nibid version
```


### 5. Create a nibid service (optional)

1. Create a service file

```bash
sudo tee /etc/systemd/system/nibiru.service<<EOF
[Unit]
Description=Nibiru Node
Requires=network-online.target
After=network-online.target

[Service]
Type=exec
User=<your_user>
Group=<your_user_group>
ExecStart=/home/<your_user>/go/bin/nibid start --home /home/<your_user>/.nibid
Restart=on-failure
ExecReload=/bin/kill -HUP $MAINPID
KillSignal=SIGTERM
PermissionsStartOnly=true
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
``` 

2. Enable the service

```bash
sudo systemctl daemon-reload
sudo systemctl enable nibiru
```

## Create Nibiru-1 Testnet validator

1. Init Chain and start your node

   ```bash
   nibid init <moniker-name> --chain-id=nibiru-testnet-2 --home $HOME/.nibid
   ```

2. Create a local key pair

   ```bash
   nibid keys add <key-name>
   nibid keys show <key-name> -a
   ```

3. Download genesis file
   
   ```bash
   curl https://<your_github_access_token>@raw.githubusercontent.com/NibiruChain/Networks/main/Testnet/Nibiru-testnet-1/genesis.json > $HOME/.nibid/config/genesis.json
   ```

   **Genesis.json sha256**

   ```bash
    shasum -a 256 ~/.nibid/config/genesis.json
    183f45b7038ee8844d372ea725ca8f9de966d084f74b2ced2e5d1fa26df172c3  /home/<user>/.nibid/config/genesis.json
   ```
   
   Or copy the genesis file included in the archive received from the Nibiru Team to the `$HOME/.nibid/config` folder
   
4. Update persistent peers list in the configuration file $HOME/.nibid/config/config.toml with the ones from the persistent_peers.txt
   ```bash
   cd $HOME
   git clone https://github.com/NibiruChain/Networks
   cd Networks/Testnet/Nibiru-testnet-1
   export PEERS=$(cat persistent_peers.txt| tr '\n' '_' | sed 's/_/,/g;s/,$//;s/^/"/;s/$/"/') && sed -i "s/persistent_peers = \"\"/persistent_peers = ${PEERS}/g" $HOME/.nibid/config/config.toml
   ```
   or navigate to the directory with the `persistent_peers.txt`file you've received from the Nibiru team manually and run
   ```bash
   export PEERS=$(cat persistent_peers.txt| tr '\n' '_' | sed 's/_/,/g;s/,$//;s/^/"/;s/$/"/') && sed -i "s/persistent_peers = \"\"/persistent_peers = ${PEERS}/g" $HOME/.nibid/config/config.toml
   ```
   

5. Set gas prices

```
sudo nano $HOME/.nibid/config/app.toml
# recommended to set to "0.025unibi"
```

6. Start your node with  `nibid start` or `sudo systemctl start nibiru` if you've created a service for it. Make sure it synced up to the tip.

7. Request tokens from the [Web Faucet for Nibiru-1 Testnet](http://ec2-35-172-193-127.compute-1.amazonaws.com:8003/) if required.

Example:
```bash
curl -X POST -d '{"address": "your address here", "coins": ["10000000unibi"]}' http://ec2-35-172-193-127.compute-1.amazonaws.com:8003
```
Please note, that current Testnet Web Faucet limit is `10000000unibi`.

You can also use Testnet Discord Faucet in the Nibiru Chain server (#faucet channel).

8. Create validator

   ```bash
   nibid tx staking create-validator \
   --amount 10000000unibi \
   --commission-max-change-rate "0.1" \
   --commission-max-rate "0.20" \
   --commission-rate "0.1" \
   --min-self-delegation "1" \
   --details "put your validator description there" \
   --pubkey=$(nibid tendermint show-validator) \
   --moniker <your_moniker> \
   --chain-id nibiru-testnet-2 \
   --gas-prices 0.025unibi \
   --from <key-name>
   ```

8. Verify your validator status via [Nibiru-1 Testnet Block Explorer](http://ec2-54-221-169-63.compute-1.amazonaws.com:3003/validators)
