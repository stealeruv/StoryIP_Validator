## Updatev1 @ Block #626575


### Download binary

```
cd $HOME
wget https://story-geth-binaries.s3.us-west-1.amazonaws.com/story-public/story-linux-amd64-0.10.0-9603826.tar.gz
tar -xzvf story-linux-amd64-0.10.0-9603826.tar.gz
```

###Stop node
```
sudo systemctl stop story
```

### Copy binary to $HOME/go/bin

```
cp $HOME/story-linux-amd64-0.10.0-9603826/story $HOME/go/bin
source $HOME/.bash_profile
story version
```

### Restart node
```
sudo systemctl start story && sudo systemctl status story
```

# Snapshot

Credits to Mandragora's infrastructure services
```
sudo apt-get install wget lz4 -y

sudo systemctl stop story-geth
sudo systemctl stop story
```
```
sudo cp $HOME/.story/story/data/priv_validator_state.json $HOME/.story/priv_validator_state.json.backup
```
```
sudo rm -rf $HOME/.story/geth/iliad/geth/chaindata
sudo rm -rf $HOME/.story/story/data
```
```
wget -O geth_snapshot.lz4 https://snapshots.mandragora.io/geth_snapshot.lz4
wget -O story_snapshot.lz4 https://snapshots.mandragora.io/story_snapshot.lz4
```
```
lz4 -c -d geth_snapshot.lz4 | tar -x -C $HOME/.story/geth/iliad/geth
lz4 -c -d story_snapshot.lz4 | tar -x -C $HOME/.story/story
```
```
sudo rm -v geth_snapshot.lz4
sudo rm -v story_snapshot.lz4
```
```
sudo cp $HOME/.story/priv_validator_state.json.backup $HOME/.story/story/data/priv_validator_state.json
```
```
sudo systemctl start story-geth
sudo systemctl start story
```
