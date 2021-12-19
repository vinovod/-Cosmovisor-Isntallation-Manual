### Cosmovisor-Isntallation-Manual

#### Устанавливаем Cosmovisor и копируем его бинарник в директорию бинарников Go
```cd ~
git clone https://github.com/cosmos/cosmos-sdk
cd cosmos-sdk
git checkout cosmovisor/v1.0.0
cd cosmovisor
make
cp cosmovisor ~/go/bin/cosmovisor
```
#### Создаем структуру директорий для Cosmovisor
```cd ~
mkdir -p ~/.umee/cosmovisor
mkdir -p ~/.umee/cosmovisor/genesis
mkdir -p ~/.umee/cosmovisor/genesis/bin
mkdir -p ~/.umee/cosmovisor/upgrades
```
#### Создаем переменные для Cosmovisor и запишем их в наш шелл
```echo "# Setup Cosmovisor" >> ~/.profile
echo "export DAEMON_NAME=umeed" >> ~/.profile
echo "export DAEMON_HOME=$HOME/.umee" >> ~/.profile
echo "export DAEMON_RESTART_AFTER_UPGRADE=true" >> ~/.profile
source ~/.profile
```
#### Копируем бинарник для запуска ноды в Cosmovisor
```cp /usr/local/bin/umeed ~/.umee/cosmovisor/genesis/bin/```

#### Настраиваем сервисный файл
```sudo tee /etc/systemd/system/umeed.service > /dev/null <<EOF
[Unit]
Description=Umeed Daemon
After=network-online.target
[Service]
User=$USER
ExecStart=$(which cosmovisor) start
Restart=always
RestartSec=3
LimitNOFILE=65535
Environment="DAEMON_HOME=$HOME/.umee"
Environment="DAEMON_NAME=umeed"
Environment="DAEMON_ALLOW_DOWNLOAD_BINARIES=false"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"
[Install]
WantedBy=multi-user.target
EOF
```
#### Запускаем сервисный файл
```sudo systemctl daemon-reload
sudo systemctl enable umeed
sudo systemctl start umeed
```
#### Проверка
```journalctl -u umeed -f```
