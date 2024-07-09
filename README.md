Initia: Running Your First Node
===============================

This document is a guide and review of my experience setting up and running a node on Initia’s Public Testnet.
--------------------------------------------------------------------------------------------------------------


![captionless image](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*2U821lx9d9Sksh3AFpefBg.png)

Six weeks ago, Initia announced its Incentivized Testnet. I couldn’t miss the opportunity to earn my place in Initia’s Genesis Validator Set come mainnet launch. However, there were a few challenges, such as the community being quite large and many people wanting to run validator nodes. Fortunately, the Initiation Validator Program consists of 5 phases over the course of 8 weeks. Each phase requires validators to contribute in different ways and they are scored based on the completion of tasks and the quality of their performance.

Approximately 13,000 validator nodes had been set up. It was a huge participation for public testing, but problems arose! Many participants did not adhere to hardware requirements and likely rented low-cost VPS from Hetzner or Contabo. Unfortunately, this caused issues with syncing and processing blocks due to bad peers. The good news is that the team started an elimination process and selected around 400 operators. This was my first experience during phase 1. I maintained my node, upgraded it when new versions were released, voted on governance proposals, ensured high uptime, and provided oracle prices for the other phases, hoping to be selected as a genesis validator.

> Please be aware: Initia’s Incentivized Testnet participation has ended for validators.

> **_End of 8 weeks, incentivized testnet for validators has been completed. I maintained uptime, submitted oracle prices, and participated in governance voting. Around 200 validator operators have been selected to active set in groups of 50–60. The team collected these on-chain data to examine their performance. Although I have enough high specification server, I almost 5 blocks missed for 100 blocks. I guess that it was caused by bad peers on network. Some validators misconfigured indexer and it caused low uptime._**

Let’s Dive into Setting Up and Running a Node Step-by-Step
==========================================================

Hardware/OS Requirements:
-------------------------

*   **CPU:** 16 cores
*   **Memory:** 32GB RAM
*   **Disk:** 2 TB NVMe/SSD Storage with Write Throughput > 1000 MiBps
*   **Bandwidth:** 100 Mbps

> I bought a dedicated server from Hetzner auction. The server has high specifications: AMD Ryzen 9 5950X 16-core 32-thread CPU, 128GB DDR4 RAM, and 3.84 TB SSD. There was no initial setup cost, and I pay just $76/monthly. I installed Ubuntu 24.04 LTS.

Update Packages:
----------------

```
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
```

Install Go:
-----------

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

Checkout and Build Initia:
--------------------------

```
git clone https://github.com/initia-labs/initia
cd initia
git checkout v0.2.21
make build
```

System Link:
------------

```
mkdir -p $HOME/.initia/cosmovisor/genesis/bin
mv /root/initia/build/initiad $HOME/.initia/cosmovisor/genesis/bin/
sudo ln -s $HOME/.initia/cosmovisor/genesis $HOME/.initia/cosmovisor/current -f
sudo ln -s $HOME/.initia/cosmovisor/current/bin/initiad /usr/local/bin/initiad -f
```

Install Cosmovisor:
-------------------

```
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.5.0
```

Create Service File:
--------------------

```
sudo tee /etc/systemd/system/initiad.service > /dev/null << EOF
[Unit]
Description=Initia node service
After=network-online.target
``````
```
[Service]
User=$USER
ExecStart=$(which cosmovisor) run start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
Environment="DAEMON_HOME=$HOME/.initia"
Environment="DAEMON_NAME=initiad"
Environment="UNSAFE_SKIP_BACKUP=true"
Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:$HOME/.initia/cosmovisor/current/bin"[Install]
WantedBy=multi-user.target
EOF
```

Enable Service:
---------------

```
sudo systemctl daemon-reload
sudo systemctl enable initiad.service
```

Initialize Node:
----------------

A moniker is a human-readable name for your node. It can contain only ASCII characters and cannot exceed 70 characters. Your private key is generated during initialization and is saved in `~/.initia/config/priv_validator_key.json`. Remember to backup your private key if you are running a validator node. If a private key is lost, the node may never be recoverable from a hardware failure. Plan your backup when running a Testnet node, and secure your private key safely for the mainnet.

```
initiad config set client chain-id initiation-1
initiad config set client node tcp://localhost:15657
initiad config set client keyring-backend test
initiad init [moniker] --chain-id initiation-1
```

Set Up External Address:
------------------------

To allow access from external nodes to your node through the P2P network, `config.toml` has to be modified. By entering the public IP/Port into the `p2p.external_address` field, the network settings will allow external nodes to access your node.

```
sed -i -e 's/external_address = \"\"/external_address = \"'$(curl httpbin.org/ip | jq -r .origin)':26656\"/g' ~/.initia/config/config.toml
```

Set Minimum Gas Prices:
-----------------------

The minimum gas price accepted by the validator to process a transaction and prevent spamming.

```
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.15uinit,0.01uusdc\"|" $HOME/.initia/config/app.toml
```

Genesis File:
-------------

The genesis file is named `genesis.json` and must be relocated to the `~/.initia/config` directory. Should the HOME directory for Initia be configured to a different location, ensure to move the genesis file to the appropriate config directory within that set location.

```
curl -Ls https://initia.s3.ap-southeast-1.amazonaws.com/initiation-1/genesis.json > $HOME/.initia/config/genesis.json
```

Seed & Peers:
-------------

```
SEEDS="2eaa272622d1ba6796100ab39f58c75d458b9dbc@34.142.181.82:26656,c28827cb96c14c905b127b92065a3fb4cd77d7f6@testnet-seeds.whispernode.com:25756"
PEERS="45d4bd57afe12aa862a2252b32617aa06013f857@49.165.90.94:25756,59a6e2a34fcebf1605a26059d40bff20bf620b36@37.27.107.22:26656,a4b6849e58ff2b96ca35487a675db90e86134c51@88.99.137.151:26656,95071a39cab00a428c532c682c6cd05618f5a9f4@46.4.80.48:25756,1e07b92a4c3bc1d66c2257a3b2378f80a7bdc1a2@135.181.239.149:26656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.initia/config/config.toml
```

Snapshot:
---------

Snapshots allow a new node to join the network by recovering the application state from a backup file. A snapshot contains a compressed copy of the chain data directory. To keep backup files as small as possible, the snapshot server is periodically state-synced.

```
initiad tendermint unsafe-reset-all --home $HOME/.initia
curl -L https://snapshots.kjnodes.com/initia-testnet/snapshot_latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.initia
```

Addrbook:
---------

It is highly recommended to copy `addrbook.json` to `$INITIA_HOME/config/addrbook.json` for fast peer connection.

```
wget https://initia.s3.ap-southeast-1.amazonaws.com/initiation-1/addrbook.json
mv addrbook.json ~/.initia/config/addrbook.json
```

Start Service & Check Logs:
---------------------------

```
sudo systemctl daemon-reload
sudo systemctl restart initiad
sudo journalctl -u initiad.service -f --no-hostname -o cat
```

Create or Restore Wallet:
-------------------------

In order to create a validator, you have to create or recover a wallet and fund it with GAS tokens via [the faucet](https://faucet.testnet.initia.xyz/) (Gitcoin score must be greater than 20) for the testnet. The INIT token will be used as a gas token on the mainnet.

```
# Create a new wallet
initiad keys add [wallet-name]
``````
```
# or restore a new wallet
initiad keys add wallet --recover
```

Becoming Validator:
-------------------

```
initiad tx mstaking create-validator \
  --amount=5000000uinit \
  --pubkey=$(initiad tendermint show-validator) \
  --moniker=[your moniker] \
  --chain-id=initiation-1 \
  --commission-rate=0.05 \
  --commission-max-rate=0.10 \
  --commission-max-change-rate=0.01 \
  --from=[wallet-name] \
  --identity=[keybase-id] \
  --website="" \
  --details="" \
  --node=http://localhost:657 \
  --gas-adjustment 1.4 \
  --gas=2000000 \
  --fees=563000move/944f8dd8dc49f96c25fea9849f16436dcfa6d564eec802f3ef7f8b3ea85368ff \
  -y
```

Check Status of Your Node:
--------------------------

Syncing and processing blocks take some time depending on your server specifications and peers. You can check your sync status with the following command. `catching_up` must be false and all blocks synced with the latest block. If your node does not catch up for some time, you should change your peers and download the latest snapshot.

```
initiad status | jq '.sync_info'
``````
##### Response should be similar as below.
```
{
  "latest_block_hash": "2AF656E3A6CE94DC1C54ECA71A04FAF79F7BDE924377270D0A795565AC06E039",
  "latest_app_hash": "F9D4D51A25EF0011B1B1CB196A33A3186ABC5A9EB776A1A64DF0E70BDCEA24A0",
  "latest_block_height": "1867818",
  "latest_block_time": "2024-06-29T17:47:37.70374895Z",
  "earliest_block_hash": "48B64E41963BF7B8732D067D4DD6003D27C166E5BE95C43265B6BC54D3E4CBF2",
  "earliest_app_hash": "DEDC82AEB9377B4542528A05709D588F1A3B0539028301660ADCFA930FEDC470",
  "earliest_block_height": "408402",
  "earliest_block_time": "2024-05-29T01:20:04.566288257Z",
  "catching_up": false
}
```

**Congratulations** on successfully setting up your first Initia Validator Node!

For troubleshooting, consult the official documentation first. If needed, seek assistance on the official Discord server or feel free to reach out to me directly on our Discord community.
