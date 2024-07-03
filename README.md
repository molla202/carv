

<h1 align="center"> Carv </h1>

 * [Topluluk kanalımız](https://t.me/corenodechat)<br>
 * [Topluluk Twitter](https://twitter.com/corenodeHQ)<br>

## 💻 Sistem Gereksinimleri
| Bileşenler | Minimum Gereksinimler | 
| ------------ | ------------ |
| CPU |	4 or 8 |
| RAM	| 8+ or 16+ GB |
| Storage	| 400 GB SSD |

NOT: ben kurmadım PARA ile alınan node işi için bu PARA ile satılan nodu almadıysanız kuramassınız.

### 🚧Gerekli kurulumlar
```
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
```

### 🚧 Go kurulumu
```
cd $HOME
VER="1.21.3"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin
```

### Dosyaları çekelim
```
git clone https://github.com/carv-protocol/verifier.git && \
cd verifier && \
git checkout verifier-alphanet && \
make build
```
### Servis
```
sudo tee /etc/systemd/system/carvd.service > /dev/null <<EOF
[Unit]
Description=carv node
After=network-online.target

[Service]
User=$USER
WorkingDirectory=$HOME/verifier
ExecStart=/root/verifier/bin/verifier -conf /root/verifier/configs/config.yaml

Restart=always
RestartSec=10
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```
```
sudo systemctl daemon-reload
sudo systemctl enable carvd
```
### Config dosyası güncelleme
NOT: private key yazıyorsunuz - reward adresini yazıyorsunuz - komisyonu yazıyorsunuz. ( anladığım kadarı ile bidane private keye ihtiyacınız var mm olusutup alın)
NOT: dosyay nano ile girip içindekileri silip altakini kopyalayıp duzenlıyecekınız
```
env: ENV_PROD
server:
  http:
    addr: 0.0.0.0:8000
    timeout: 1s
  grpc:
    addr: 0.0.0.0:9000
    timeout: 1s
chain:
  chain_id: 5611
  chain_name: "opBNB"
  rpc_url: "https://opbnb-testnet.nodereal.io/v1/9e210feafbec4ed9bd48f855c2bd979a"
  start_block: 0
  offset_block: 3600 # opBNB block time: 1 sec. An offset of 3600 starts fetching blocks from 1 hours ago.
contract:
  addr: "0xbd9a9ac0172f9eddebea4172fb7d9a3ca95cee52"
  tee_addr : "0x7f57004E08ef1702b2b88b87ae01a561ae10F10e"
  topic: "0x99a038e9d345d0b12130b3b1fb003bf8f2d3a5c27ce2a800bbb1608efff6c591"
wallet:
  mode: 0
  private_key: ""
  reward_claimer_addr: ""
  commission_rate: ""
dacp:
  identity_path: "../configs/identity.json"
  tcb_path: "../configs/tcb.json"
  trusted_path: "../configs/trusted.json"
```

### Başlatalım
```
sudo systemctl restart carvd
```
### loglara bakalım
```
sudo journalctl -u carvd -fo cat
```





















