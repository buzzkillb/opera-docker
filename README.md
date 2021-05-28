# opera-docker
Fantom go-opera docker  
### Get genesis file and throw into a folder, using ~/opera for the datadir  
```
cd ~
mkdir opera
cd opera
wget https://opera.fantom.network/mainnet.g
cd ~
```
### Build Docker Image Locally  
```
git clone https://github.com/buzzkillb/opera-docker
cd opera-docker
docker build -t opera .
```
### Basic Docker Run Command
```
docker run --name=go-opera --rm -t -v ~/opera:/data opera --genesis /data/mainnet.g --datadir /data --nousb
```
