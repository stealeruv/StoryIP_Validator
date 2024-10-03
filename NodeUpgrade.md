## Updatev1 @ Block #626575

### Stop node
```
sudo systemctl stop story
```

### Download binary

```
cd $HOME
wget https://story-geth-binaries.s3.us-west-1.amazonaws.com/story-public/story-linux-amd64-0.10.1-57567e5.tar.gz
tar -xzvf story-linux-amd64-0.10.1-57567e5.tar.gz
```

### Copy binary to $HOME/go/bin

```
cp $HOME/story-linux-amd64-0.10.1-57567e5/story $HOME/go/bin
source $HOME/.bash_profile
story version
```

### Restart node
```
sudo systemctl daemon-reload
sudo systemctl start story
sudo systemctl status story
```

# Snapshot

## Credits to Joseph Tran
### Install tool
```
sudo apt-get install wget lz4 aria2 pv -y
```
### Stop node
```
sudo systemctl stop story
sudo systemctl stop story-geth
```
### Download Story-data
```
cd $HOME
rm -f Story_snapshot.lz4
wget --show-progress https://josephtran.co/story/Story_snapshot.lz4
```
### Download Geth-data
```
cd $HOME
rm -f Geth_snapshot.lz4
wget --show-progress https://josephtran.co/story/Geth_snapshot.lz4
```
### Backup priv_validator_state.json:
```
mv ~/.story/story/data/priv_validator_state.json ~/.story/priv_validator_state.json.backup
```
### Remove old data
```
rm -rf ~/.story/story/data
rm -rf ~/.story/geth/iliad/geth/chaindata
```
### Extract Story-data
```
sudo mkdir -p /root/.story/story/data
lz4 -d Story_snapshot.lz4 | pv | sudo tar xv -C /root/.story/story/
```
### Extract Geth-data
```
sudo mkdir -p /root/.story/geth/iliad/geth/chaindata
lz4 -d Geth_snapshot.lz4 | pv | sudo tar xv -C /root/.story/geth/iliad/geth/
```
### Move priv_validator_state.json back
```
mv ~/.story/priv_validator_state.json.backup ~/.story/story/data/priv_validator_state.json
```
### Restart node
```
sudo systemctl start story
sudo systemctl start story-geth
```
