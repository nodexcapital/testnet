<h3><p style="font-size:14px" align="right">Founder :
<a href="https://discord.gg/nodexcapital" target="_blank">NodeX Capital Discord Community</a></p></h3>
<h3><p style="font-size:14px" align="right">Visit Our Website :
<a href="https://discord.gg/nodexcapital" target="_blank">NodeX Capital Official</a></p></h3>
<h3><p style="font-size:14px" align="right">Hetzner :
<a href="https://hetzner.cloud/?ref=bMTVi7dcwSgA" target="_blank">Deploy Hetzner VPS Get 20€ Bonus!</a></h3>
<hr>

<p align="center">
  <img height="100" height="auto" src="https://user-images.githubusercontent.com/50621007/176226900-aae9149d-a186-4fd5-a9aa-fc3ce8b082b3.png">
</p>

## Setting up vars
>Replace `YOUR_NODENAME` below with the name of your node
```
NODENAME=<YOUR_NODENAME>
```

Save and import variables into system
```
echo "export NODENAME=$NODENAME" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

# Install peaq node
To setup peaq node follow the steps below

## Update packages
```
sudo apt update && sudo apt upgrade -y
```

## Install dependencies
```
sudo apt install curl jq ocl-icd-opencl-dev libopencl-clang-dev libgomp1 -y
```

## Update executables
```
cd $HOME
sudo rm -rf peaq-node
APP_VERSION=$(curl -s https://api.github.com/repos/peaqnetwork/peaq-network-node/releases/latest | jq -r ".tag_name")
wget -O peaq-node.tar.gz https://github.com/peaqnetwork/peaq-network-node/releases/download/${APP_VERSION}/peaq-node-${APP_VERSION}.tar.gz
sudo tar zxvf peaq-node.tar.gz
sudo chmod +x peaq-node
sudo mv peaq-node /usr/local/bin/
```

## Create chain data directory
```
sudo mkdir /chain-data
sudo chmod 0777 /chain-data
```

## Create peaq-node service
```
sudo tee <<EOF >/dev/null /etc/systemd/system/peaqd.service
[Unit]
Description=Peaq Node
After=network.target
[Service]
Type=simple
User=$USER
ExecStart=$(which peaq-node) \\
--base-path /chain-data \\
--chain agung \\
--port 1033 \\
--ws-port 9944 \\
--rpc-port 9933 \\
--rpc-cors all \\
--pruning archive \\
--name $NODENAME
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```

## Run peaq services
```
sudo systemctl restart systemd-journald
sudo systemctl daemon-reload
sudo systemctl enable peaqd
sudo systemctl restart peaqd
```

## Check node logs
```
journalctl -fu peaqd -o cat
```
