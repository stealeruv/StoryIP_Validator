# Story Protocol Validator Node Setup Guide

Story raised $140M from Tier1 investors. Story is a blockchain making IP protection and licensing programmable and efficient. It automates IP management, allowing creators to easily license, remix, and monetize their work. With Story, traditional legal complexities are replaced by on-chain smart contracts and off-chain legal agreements, simplifying the entire process.

## System Requirements

| **Hardware** | **Minimum Requirement** |
|--------------|-------------------------|
| **CPU**      | 4 Cores                 |
| **RAM**      | 8 GB                    |
| **Disk**     | 200 GB                  |
| **Bandwidth**| 10 MBit/s               |


Follow our TG : https://t.me/cryptoconsol

Show your support on Twitter : https://www.x.com/cryptoconsol


## Install dependencies

```
sudo apt update
sudo apt-get update
sudo apt install curl git make jq build-essential gcc unzip wget lz4 aria2 pv -y
```

## Install Go

```
cd $HOME && \
ver="1.22.0" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile && \
source ~/.bash_profile && \
go version
```

## Download Story-Geth binary

```
wget https://story-geth-binaries.s3.us-west-1.amazonaws.com/geth-public/geth-linux-amd64-0.9.2-ea9f0d2.tar.gz
tar -xzvf geth-linux-amd64-0.9.2-ea9f0d2.tar.gz
[ ! -d "$HOME/go/bin" ] && mkdir -p $HOME/go/bin
if ! grep -q "$HOME/go/bin" $HOME/.bash_profile; then
  echo 'export PATH=$PATH:$HOME/go/bin' >> $HOME/.bash_profile
fi
sudo cp geth-linux-amd64-0.9.2-ea9f0d2/geth $HOME/go/bin/story-geth
source $HOME/.bash_profile
story-geth version
```

## Download Story binary

```
wget https://story-geth-binaries.s3.us-west-1.amazonaws.com/story-public/story-linux-amd64-0.9.11-2a25df1.tar.gz
tar -xzvf story-linux-amd64-0.9.11-2a25df1.tar.gz
[ ! -d "$HOME/go/bin" ] && mkdir -p $HOME/go/bin
if ! grep -q "$HOME/go/bin" $HOME/.bash_profile; then
  echo 'export PATH=$PATH:$HOME/go/bin' >> $HOME/.bash_profile
fi
sudo cp story-linux-amd64-0.9.11-2a25df1/story $HOME/go/bin/story
source $HOME/.bash_profile
story version
```

## Initiate Iliad node

Replace "Your_moniker_name" with any name you want 
(Ex: story init --network iliad --moniker cryptoconsole)

```
story init --network iliad --moniker "Your_moniker_name"
```
### Peers setup
```
PEERS="a320f8a15892bddd7b5502527e0d11c5b5b9d0e3@69.67.150.107:29931,2f372238bf86835e8ad68c0db12351833c40e8ad@story-testnet-rpc.itrocket.net:26656,d4c5dcfbec11d80399bcf18d83a157259ca3efc7@138.201.200.100:26656,15c7e2b630c04ee11b2c3cfbfb1ede0379df9407@52.74.117.64:26656,359e4420e63db005d8e39c490ad1c1c329a68df3@3.222.216.118:26656,eeb7d2096a887f8ff8fdde2695c394fcf5a19273@194.238.30.192:36656,0b512c9a4421c0259813aaa05c865f82365fa7c0@3.1.137.11:26656,f4d96bf0dc67a05a48287ca2c821bc8e1d2b2023@63.35.134.129:26656,5e4f9ce2d20f2d3ef7f5c92796b1b954384cbfe1@34.234.176.168:26656,371ee318d105b0239b3997c287068ccbbcd46a91@3.248.113.42:26656,df40eee673df8eb3eddd10b359539f0e86ecaee3@207.244.230.111:36656,c82d2b5fe79e3159768a77f25eee4f22e3841f56@3.209.222.59:26656,960278d079a111b44c207dca7c2ffac640b477d1@44.223.234.211:26656,1708afbf73e2fbbb5a943aa2d97c976bf8e0d25c@52.9.183.131:26656,1cceccb08bae25a0f91fe85b0ca562fa791f47aa@184.169.154.204:26656,8876a2351818d73c73d97dcf53333e6b7a58c114@3.225.157.207:26656,0a2bc2ce69b7292deaf0b6a33af76bf9b2e25ec6@88.198.46.55:41656,6bb4ed28b08a186fc1373cfc2e96b83165c1e882@162.55.245.254:33656,a2fe3dfd6396212e8b4210708e878de99307843c@54.209.160.71:26656,aac5871efa351872789eef15c2da7a55a68abdad@88.218.226.79:26656"
sed -i -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*persistent_peers *=.*/persistent_peers = \"$PEERS\"/}" $HOME/.story/story/config/config.toml
```

## Create story-geth service file
```
sudo tee /etc/systemd/system/story-geth.service > /dev/null <<EOF
[Unit]
Description=Story Geth Client
After=network.target

[Service]
User=root
ExecStart=/root/go/bin/story-geth --iliad --syncmode full
Restart=on-failure
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
EOF
```
## Create story service file

```
sudo tee /etc/systemd/system/story.service > /dev/null <<EOF
[Unit]
Description=Story Consensus Client
After=network.target

[Service]
User=root
ExecStart=/root/go/bin/story run
Restart=on-failure
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
EOF
```

## Reload and start story-geth
```
sudo systemctl daemon-reload && \
sudo systemctl enable story-geth && \
sudo systemctl enable story && \
sudo systemctl start story-geth && \
sudo systemctl start story && \
sudo systemctl status story-geth
```

# Check logs

### Geth logs
```
sudo journalctl -u story-geth -f -o cat
```
### Story logs
```
sudo journalctl -u story -f -o cat
```
### Check sync status

```
curl localhost:26657/status | jq
```

# SYNC using snapshot File

C2 Mandragora and Joseph Tran

Choose Either one of the snapshot file

```
sudo apt-get install wget lz4 -y

wget -O geth_snapshot.lz4 https://snapshots.mandragora.io/geth_snapshot.lz4
wget -O story_snapshot.lz4 https://snapshots.mandragora.io/story_snapshot.lz4

sudo systemctl stop story-geth
sudo systemctl stop story

sudo cp $HOME/.story/story/data/priv_validator_state.json $HOME/.story/priv_validator_state.json.backup

sudo rm -rf $HOME/.story/geth/iliad/geth/chaindata
sudo rm -rf $HOME/.story/story/data

lz4 -c -d geth_snapshot.lz4 | tar -x -C $HOME/.story/geth/iliad/geth
lz4 -c -d story_snapshot.lz4 | tar -x -C $HOME/.story/story

sudo rm -v geth_snapshot.lz4
sudo rm -v story_snapshot.lz4

sudo cp $HOME/.story/priv_validator_state.json.backup $HOME/.story/story/data/priv_validator_state.json

sudo systemctl start story-geth
sudo systemctl start story
```
or
```
sudo apt-get install wget lz4 aria2 pv -y

sudo systemctl stop story
sudo systemctl stop story-geth

cd $HOME
rm -f Geth_snapshot.lz4
if curl -s --head https://vps7.josephtran.xyz/Story/Geth_snapshot.lz4 | head -n 1 | grep "200" > /dev/null; then
    echo "Snapshot found, downloading..."
    aria2c -x 16 -s 16 https://vps7.josephtran.xyz/Story/Geth_snapshot.lz4 -o Geth_snapshot.lz4
else
    echo "No snapshot found."
fi

cd $HOME
rm -f Story_snapshot.lz4
if curl -s --head https://vps7.josephtran.xyz/Story/Story_snapshot.lz4 | head -n 1 | grep "200" > /dev/null; then
    echo "Snapshot found, downloading..."
    aria2c -x 16 -s 16 https://vps7.josephtran.xyz/Story/Story_snapshot.lz4 -o Story_snapshot.lz4
else
    echo "No snapshot found."
fi

mv $HOME/.story/story/data/priv_validator_state.json $HOME/.story/priv_validator_state.json.backup

rm -rf ~/.story/story/data
rm -rf ~/.story/geth/iliad/geth/chaindata

sudo mkdir -p /root/.story/story/data
lz4 -d Story_snapshot.lz4 | pv | sudo tar xv -C /root/.story/story/

sudo mkdir -p /root/.story/geth/iliad/geth/chaindata
lz4 -d Geth_snapshot.lz4 | pv | sudo tar xv -C /root/.story/geth/iliad/geth/

mv $HOME/.story/priv_validator_state.json.backup $HOME/.story/story/data/priv_validator_state.json
Restart node 

sudo systemctl start story
sudo systemctl start story-geth

```

# Register your Validator

### 1. Export wallet:
```
story validator export --export-evm-key
```
### 2. Private key preview
```
sudo nano ~/.story/story/config/private_key.txt
```
### 3. Import Key to Metamask 

Get the wallet address for faucet

### 4. You need at least have 1 IP on wallet

Get it from faucet : https://faucet.story.foundation/

Check the sync, the catching up must be 'false'
```
curl -s localhost:26657/status | jq
```
Stake only after "catching_up": false

### 5. Validator registering

Replace "your_private_key" with your key from the step2

```
story validator create --stake 1000000000000000000 --private-key "your_private_key"
```

### 6. Check your validator INFO
```
curl -s localhost:26657/status | jq -r '.result.validator_info' 
```

### 7. check your validator

Explorer: https://testnet.story.explorers.guru/

## BACK UP FILE

### 1. Wallet private key:
```
sudo nano ~/.story/story/config/private_key.txt
```
### 2. Validator key:

```
sudo nano ~/.story/story/config/priv_validator_key.json
```

Join our TG : https://t.me/cryptoconsol
