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
