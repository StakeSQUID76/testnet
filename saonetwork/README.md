<h3><p style="font-size:14px" align="right">Founder :
<a href="https://discord.gg/nodexcapital" target="_blank">NodeX Capital Discord Community</a></p></h3>
<h3><p style="font-size:14px" align="right">Visit Our Website :
<a href="https://discord.gg/nodexcapital" target="_blank">NodeX Capital Official</a></p></h3>
<h3><p style="font-size:14px" align="right">Hetzner :
<a href="https://hetzner.cloud/?ref=bMTVi7dcwSgA" target="_blank">Deploy Hetzner VPS Get 20€ Bonus!</a></h3>
<hr>

<p align="center">
  <img height="100" height="auto" src="https://i.imgur.com/bZzE0Ie.png">
</p>

# Sao Network Testnet | Chain ID : sao-testnet0 | Port : 245

### Official Documentation:
>- [Validator setup instructions](https://docs.sao.network/participate-in-sao-network/run-sao-validator)

### Explorer:
>-  https://explorer.nodexcapital.com/sao

### Automatic Installer
You can setup your Sao Network fullnode in few minutes by using automated script below.
```
wget -O sao.sh https://raw.githubusercontent.com/nodexcapital/testnet/main/saonetwork/sao.sh && chmod +x sao.sh && ./sao.sh
```
### Public Endpoint

>- API : https://api.sao-t.nodexcapital.com
>- RPC : https://rpc.sao-t.nodexcapital.com
>- gRPC : https://grpc.sao-t.nodexcapital.com

### Snapshot (Update every 5 hours)
```
sudo systemctl stop saod
cp $HOME/.sao/data/priv_validator_state.json $HOME/.sao/data/priv_validator_state.json.backup
rm -rf $HOME/.sao/data

curl -L https://snap.nodexcapital.com/nolus/nolus-latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.sao

mv $HOME/.sao/data/priv_validator_state.json.backup $HOME/.sao/data/priv_validator_state.json

sudo systemctl start saod && sudo journalctl -fu saod -o cat
```

### State Sync
```
saod tendermint unsafe-reset-all --home $HOME/.sao --keep-addr-book

SNAP_RPC="https://rpc.sao-t.nodexcapital.com:443"

LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 2000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"|" $HOME/.sao/config/config.toml

sudo systemctl start saod && sudo journalctl -fu saod -o cat
```

### Seed Peers
```
sed -i -e "s|^seeds *=.*|seeds = \"2aad459c0dd3a81b1d5eb297986c8d8309ad20e3@peers-sao.sxlzptprjkt.xyz:27656\"|" $HOME/.sao/config/config.toml
```
### Addrbook (Update every hour)
```
curl -Ls https://snap.nodexcapital.com/saonetwork/addrbook.json > $HOME/.sao/config/addrbook.json
```
### Genesis
```
curl -Ls https://snap.nodexcapital.com/saonetwork/genesis.json > $HOME/.sao/config/genesis.json
```