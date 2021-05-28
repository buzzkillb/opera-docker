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
### docker-compose.yml Example  
```
version: "3.3"

services:

  GO-OPERA:
    image: opera
    container_name: "go-opera"
    volumes:
      - ~/opera:/data
    command: --genesis /data/mainnet.g --datadir /data --nat extip:1.1.1.1 --nousb --http --http.vhosts="*" --http.corsdomain="*" --ws --ws.origins="*" --http.api="ftm,eth,debug,admin,web3,personal,net,txpool,sfc"
    ports:
      - 5050:5050
      - 18545:18545
      - 18546:18546
```
### docker-compose.yml (traefik v2 forward rpc port, untested)  
```
version: "3.3"

services:

  traefik:
    image: "traefik:v2.4"
    container_name: "traefik"
    command:
      #- "--log.level=DEBUG"
      - "--api.insecure=true"
      - "--providers.docker=true"
      - "--providers.docker.exposedbydefault=false"
      - "--entrypoints.web.address=:80"
      - "--entrypoints.websecure.address=:443"
      - "--certificatesresolvers.myresolver.acme.dnschallenge=true"
      - "--certificatesresolvers.myresolver.acme.dnschallenge.provider=cloudflare"
      #- "--certificatesresolvers.myresolver.acme.caserver=https://acme-staging-v02.api.letsencrypt.org/directory"
      - "--certificatesresolvers.myresolver.acme.email=travanx@gmail.com"
      - "--certificatesresolvers.myresolver.acme.storage=/letsencrypt/acme.json"
    ports:
      - "80:80"
      - "443:443"
      - "8080:8080"
    environment:
      - "CF_API_EMAIL=email@example.com"
      - "CF_API_KEY=SECRETAPIKEY"
    volumes:
      - "./letsencrypt:/letsencrypt"
      - "/var/run/docker.sock:/var/run/docker.sock:ro"
  go-opera:
    image: opera
    container_name: "go-opera"
    volumes:
      - ~/opera:/data
    command: --genesis /data/mainnet.g --datadir /data --nat extip:1.1.1.1 --nousb --http --http.vhosts="*" --http.corsdomain="*" --ws --ws.origins="*" --http.api="ftm,eth,debug,admin,web3,personal,net,txpool,sfc"
    ports:
      - 5050:5050
      - 18545:18545
      - 18546:18546
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.go-opera.rule=Host(`subdomain.example.com`)"
      - "traefik.http.routers.go-opera.entrypoints=websecure"
      - "traefik.http.routers.go-opera.tls.certresolver=myresolver"
      - "traefik.http.services.go-opera.loadbalancer.server.port=18545"
 ```
