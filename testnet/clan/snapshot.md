# Clan Testnet Snapshot Instructions
**Chain ID:** playstation-2

**Node Version:** 1.0.4-alpha

## Pre-requisite Setup
Configure the pruning settings as follows:
**app.toml**
```
pruning = "custom"

pruning-keep-recent = "100"
pruning-keep-every = "0"
pruning-interval = "10"
```

**config.toml**
```
indexer = "null"
```

Install lz4 if not already installed. This guide assumes that a  Debian based OS is used:
```
sudo apt update &&\

sudo apt install -y snapd &&\

sudo snap install lz4
```

Stop the node. If not using cosmovisor to manage the chain daemon, use whatever service you use to manage the chain daemon
```
sudo systemctl cosmovisor stop
```

Reset the node. Depending on the chain, this can be done in one of two ways.

**WARNING:** While this should not wipe the `priv_validator_key.json` file, you should back up this key on validators prior to running the following commands.
```
# On some tendermint chains, use this command to reset the node database
cland tendermint unsafe-reset-all --home $HOME/.clan

# On other tendermint chains, use this command if the above command did not work
cland unsafe-reset-all
```


## Basic Setup
Download the latest snapshot:
```
wget $(wget -q -O - https://eu2.contabostorage.com/3a857da4bd64407caf5c7a4f9af739b7:relyte-snapshots/clan_testnet%2Flatest)
```

Extract the snapshot to the database location:
```
lz4 -c -d clan_*.tar.lz4  | tar -x -C $HOME/.clan
```

Restart the node assuming no errors were thrown while extracting the snapshot:
```
sudo systemctl cosmovisor start
```

Remove the snapshot archive to free up disk space
```
rm -v clan_*.tar.lz4
```

Make sure that the node is running without issues
```
sudo systemctl status cosmovisor
sudo journalctl -fu cosmovisor -o cat
```

## Advanced Setup (Recommended)
This method requires less disk space than the previous method and is less commands overall.

Download and unarchive the snapshot to the database directory
```
wget -O - $(wget -q -O - https://eu2.contabostorage.com/3a857da4bd64407caf5c7a4f9af739b7:relyte-snapshots/clan_testnet%2Flatest) | lz4 -c -d - | tar -x -C $HOME/.clan
```

Make sure that the node is running without issues
```
sudo systemctl status cosmovisor
sudo journalctl -fu cosmovisor -o cat
```