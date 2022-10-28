# nibiru-testnet-1 Instructions

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
git fetch --tags
git checkout v0.15.0
```

In this repository, run

```bash
make install
```

Verify the binary version (should be `v0.15.0`):

```bash
nibid version
```

**Then choose option 5 or 6**

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

### 6. Setup Cosmovisor (optional)

1. Install Cosmovisor

   ```bash
   git clone https://github.com/cosmos/cosmos-sdk
   cd cosmos-sdk
   git checkout cosmovisor/v1.2.0
   make cosmovisor
   cp cosmovisor/cosmovisor $GOPATH/bin/cosmovisor
   cd $HOME
   ```

2. Set up enviromental variables

   ```bash
   export DAEMON_NAME=nibid
   export DAEMON_HOME=$HOME/.nibid
   source ~/.profile
   ```

3. Create required directories

   ```bash
   mkdir -p $DAEMON_HOME/cosmovisor/genesis/bin
   mkdir -p $DAEMON_HOME/cosmovisor/upgrades
   ```

4. Add the genesis version of the binary (currently it is `0.15.0` version). You can verify your binary location with `which nibid` command. For the default location you can use the example below:

   ```bash
   cp ~/go/bin/nibid $DAEMON_HOME/cosmovisor/genesis/bin
   ```

5. Create the service for the Cosmovisor

   ```bash
   sudo tee /etc/systemd/system/cosmovisor-nibiru.service<<EOF
   [Unit]
   Description=Cosmovisor for Nibiru Node
   Requires=network-online.target
   After=network-online.target

   [Service]
   Type=exec
   User=<your_user>
   Group=<your_user_group>
   ExecStart=/home/<your_user>/go/bin/cosmovisor run start --home /home/<your_user>/.nibid
   Restart=on-failure
   RestartSec=3
   Environment="DAEMON_NAME=nibid"
   Environment="DAEMON_HOME=/home/<your_user>/.nibid"
   Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=false"
   Environment="DAEMON_RESTART_AFTER_UPGRADE=true"
   Environment="DAEMON_LOG_BUFFER_SIZE=512"
   LimitNOFILE=65535

   [Install]
   WantedBy=multi-user.target
   EOF
   ```

   Enable the service:

   ```bash
   sudo systemctl daemon-reload
   sudo systemctl enable cosmovisor-nibiru
   ```

## Create a Testnet Validator

1. Init the chain

   ```bash
   nibid init <moniker-name> --chain-id=nibiru-testnet-1 --home $HOME/.nibid
   ```

2. Create a local key pair

   ```bash
   nibid keys add <key-name>
   nibid keys show <key-name> -a
   ```

3. Download genesis file

   ```bash
   cd $HOME
   git clone https://github.com/NibiruChain/Networks
   cp $HOME/Networks/Testnet/nibiru-testnet-1/genesis.json $HOME/.nibid/config/genesis.json
   ```

   **Genesis.json sha256**

   ```bash
    shasum -a 256 $HOME/.nibid/config/genesis.json
    afd6243fe9661f05c6eca688dd290683e8059f8ceac14f389390843cf3707650  /home/<user>/.nibid/config/genesis.json
   ```

4. Update persistent peers list in the configuration file $HOME/.nibid/config/config.toml with the ones from the persistent_peers.txt

   ```bash
   cd $HOME/Networks/Testnet/nibiru-testnet-1
   export PEERS=$(cat persistent_peers.txt| tr '\n' '_' | sed 's/_/,/g;s/,$//;s/^/"/;s/$/"/') && sed -i "s/persistent_peers = \"\"/persistent_peers = ${PEERS}/g" $HOME/.nibid/config/config.toml
   ```

5. Set minimum gas prices

   ```bash
   sed -i 's/minimum-gas-prices =.*/minimum-gas-prices = "0.025unibi"/g' $HOME/.nibid/config/app.toml
   ```

6. Update block time parameters

   ```bash
    sed -i 's/timeout_propose =.*/timeout_propose = "100ms"/g' $HOME/.nibid/config/config.toml
    sed -i 's/timeout_propose_delta =.*/timeout_propose_delta = "500ms"/g' $HOME/.nibid/config/config.toml
    sed -i 's/timeout_prevote =.*/timeout_prevote = "100ms"/g' $HOME/.nibid/config/config.toml
    sed -i 's/timeout_prevote_delta =.*/timeout_prevote_delta = "500ms"/g' $HOME/.nibid/config/config.toml
    sed -i 's/timeout_precommit =.*/timeout_precommit = "100ms"/g' $HOME/.nibid/config/config.toml
    sed -i 's/timeout_precommit_delta =.*/timeout_precommit_delta = "500ms"/g' $HOME/.nibid/config/config.toml
    sed -i 's/timeout_commit =.*/timeout_commit = "1s"/g' $HOME/.nibid/config/config.toml
    sed -i 's/skip_timeout_commit =.*/skip_timeout_commit = false/g' $HOME/.nibid/config/config.toml
   ```

7. Start your node with  `nibid start` or `sudo systemctl start nibiru` if you've created a service for it or `sudo systemctl start cosmovisor-nibiru` if you've installed cosmovisor for it.

8. Request tokens from the [Web Faucet for nibiru-testnet-1](https://faucet.testnet-1.nibiru.fi/) if required.

   ```bash
   curl -X POST -d '{"address": "your address here", "coins": ["10000000unibi"]}' https://faucet.testnet-1.nibiru.fi/
   ```

   Please note, that current Testnet Web Faucet limit is `10000000unibi`.

   You can also use Testnet Discord Faucet in the Nibiru Chain server (#💦︲faucet channel).

9. Create validator

   Make sure you have the chain synced!

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
   --chain-id nibiru-testnet-1 \
   --gas-prices 0.025unibi \
   --from <key-name>
   ```

10. Verify your validator status via [nibiru-testnet-1 block explorer](https://testnet-1.nibiru.fi/)
