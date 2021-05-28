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
### docker-compose.yml Example (untested)  
```
GO-OPERA:
  image: opera
  volumes:
    - ~/opera:/data
  command: --genesis /data/mainnet.g --datadir /data --nat extip:111.111.111.111 --nousb --http --http.vhosts="*" --http.corsdomain="*" --ws --ws.origins="*" --http.api="ftm,eth,debug,admin,web3,personal,net,txpool,sfc"
  ports:
    - 5050:5050
    - 18546:18546
    - 18547:18547
```
