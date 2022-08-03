# Nibiru-1 Testnet Instructions for validators to rebuild validator in case of chain reset

  Any upcoming resets are going to be announced in the Nibiru Chain Discord server (`#testnet` channel).  
  To rejoin the testnet, please follow the steps below:

## 1. Remove old chain data and binary

  ```bash
  sudo rm -rf $HOME/.nibid
  sudo rm $HOME/go/bin/nibid
  ```
  
## 2. Switch git to the new version and install the new binary

  Version is going to be announced in the Discord `#testnet` channel

  ```bash
  cd nibiru
  git pull
  git fetch --tags
  git checkout <version>
  make install
  ```
  Verify the binary version by running 
  
  ```bash
  nibid version
  ```
 
## 3. Recreate the validator

Follow the same steps [from the README.md manual](https://github.com/NibiruChain/Networks/blob/main/Testnet/Nibiru-testnet-1/README.md#create-nibiru-1-testnet-validator) again
