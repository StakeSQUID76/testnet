<p align="center">
  <img height="100" height="auto" src="https://user-images.githubusercontent.com/50621007/172488614-7d93b016-5fe4-4a51-99e2-67da5875ab7a.png">
</p>

# Manual node setup
If you want to setup fullnode manually follow the steps below

## Setting up vars
Here you have to put name of your moniker (validator) that will be visible in explorer
```
NODENAME=<YOUR_MONIKER_NAME_GOES_HERE>
```

Save and import variables into system
```
PALOMA_PORT=10
echo "export NODENAME=$NODENAME" >> $HOME/.bash_profile
if [ ! $WALLET ]; then
	echo "export WALLET=wallet" >> $HOME/.bash_profile
fi
echo "export PALOMA_CHAIN_ID=paloma-testnet-6" >> $HOME/.bash_profile
echo "export PALOMA_PORT=${PALOMA_PORT}" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

## Update packages
```
sudo apt update && sudo apt upgrade -y
```

## Install dependencies
```
sudo apt install curl build-essential git wget jq make gcc tmux -y
```

## Install go
```
ver="1.18.2"
cd $HOME
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile
source ~/.bash_profile
go version
```

## Download and build binaries
```
wget -O - https://github.com/palomachain/paloma/releases/download/v0.2.5-prealpha/paloma_0.2.5-prealpha_Linux_x86_64.tar.gz | \
sudo tar -C /usr/local/bin -xvzf - palomad
sudo chmod +x /usr/local/bin/palomad
sudo wget -P /usr/lib https://github.com/CosmWasm/wasmvm/raw/main/api/libwasmvm.x86_64.so
```

## Config app
```
palomad config chain-id $PALOMA_CHAIN_ID
palomad config keyring-backend test
palomad config node tcp://localhost:${PALOMA_PORT}657
```

## Init app
```
palomad init $NODENAME --chain-id $PALOMA_CHAIN_ID
```

## Download genesis and addrbook
```
wget -O ~/.paloma/config/genesis.json https://raw.githubusercontent.com/palomachain/testnet/master/paloma-testnet-6/genesis.json
wget -O ~/.paloma/config/addrbook.json https://raw.githubusercontent.com/palomachain/testnet/master/paloma-testnet-6/addrbook.json
```

## Set seeds and peers
```
SEEDS=""
PEERS="d6ee6c3ccc5d307600fc9d01619b4eae2ad331da@217.79.187.35:26656,98bbed8647e2bd80d169ed7ae04e41e82f24594f@65.21.181.135:36656,b159364b4e6a3036c36ef6c7c690c5fbc81fa9c4@65.108.71.92:54056,3f053da998299894a4574720629780fcafa5875c@138.201.139.175:20456,e4b7cdd48c39c355e9a3480f4f4d5afab8fb0e08@46.0.203.78:26637,983c19423a0c9a4a444d6ccd76a73ecac523b868@78.47.128.136:26656,26f67d710b591bb2b7efd4f339daac56ca5e7e41@194.163.172.204:26656,a25f8bc10d9b3834e2834ac8231152fa862e7160@38.242.242.189:36416,68e4fb7ed3c792a3cf6f8c43d984d23c513b66f4@146.19.24.34:16656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.paloma/config/config.toml
```

## Set custom ports
```
sed -i.bak -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:${PALOMA_PORT}658\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:${PALOMA_PORT}657\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:${PALOMA_PORT}060\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:${PALOMA_PORT}656\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":${PALOMA_PORT}660\"%" $HOME/.paloma/config/config.toml
sed -i.bak -e "s%^address = \"tcp://0.0.0.0:1317\"%address = \"tcp://0.0.0.0:${PALOMA_PORT}317\"%; s%^address = \":8080\"%address = \":${PALOMA_PORT}080\"%; s%^address = \"0.0.0.0:9090\"%address = \"0.0.0.0:${PALOMA_PORT}090\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:${PALOMA_PORT}091\"%" $HOME/.paloma/config/app.toml
```

## Disable indexing
```
indexer="null"
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.paloma/config/config.toml
```

## Config pruning
```
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="50"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.paloma/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.paloma/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.paloma/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.paloma/config/app.toml
```

## Set minimum gas price
```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0ugrain\"/" $HOME/.paloma/config/app.toml
```

## Enable prometheus
```
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.paloma/config/config.toml
```

## Reset chain data
```
palomad tendermint unsafe-reset-all --home $HOME/.paloma
```

## Create service
```
sudo tee /etc/systemd/system/palomad.service > /dev/null <<EOF
[Unit]
Description=paloma
After=network-online.target

[Service]
User=$USER
ExecStart=$(which palomad) start --home $HOME/.paloma
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

## Register and start service
```
sudo systemctl daemon-reload
sudo systemctl enable palomad
sudo systemctl restart palomad && sudo journalctl -u palomad -f -o cat
```