Initia: Deploy your Future of Interwoven Rollup L2 (A.K.A: Minitia) in 10 minutes
=================================================================================

![captionless image](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*C0WjFeAw2RCZAqCmPT2tpw.png)

What is Initia?
===============

Initia is a blockchain platform (Layer 1) designed with advanced technology (Layer 2) to support a network of connected, modular sub-networks. This setup boosts scalability, efficiency, and flexibility within the Initia ecosystem. The unique Initia technology stack ensures smooth integration between Layer 1 and Layer 2, enabling developers to create mechanisms that align the interests of both users and developers. Essentially, Initia incorporates specific Layer 2 solutions (minitias) directly into its foundational Layer 1, making it a network of interconnected rollups.

Initia’s Architecture
=====================

![captionless image](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*uPShNXMJo1v5suJb2iKVow.png)

Initia’s architecture, also known as Omnitia, integrates Minitias into a robust two-layer platform. Here’s a breakdown of its components:

**Initia Orchestration Layer (Layer 1)**: Known as the “Initia Base Chain,” this foundational blockchain layer oversees network security, consensus, governance, interoperability, liquidity, and inter-chain messaging.

**Initia Rollups (Layer 2s)**: These are referred to as “Minitia” or mini Initias. They are Layer 2 solutions built on the Initia Base Chain to enhance scalability and transaction throughput. Minitias can operate in EVM, MoveVM, or WasmVM environments, leveraging the CosmosSDK.

**Initia Optimistic Rollup Framework (OPinit Stack)**: The OPinit Stack is Initia’s Optimistic Rollup (OR) framework, developed using the CosmosSDK. It ensures the security of Initia Rollups through fraud proofs and rollbacks.

**Interoperability/Bridging Middleware**: Combining the Inter-Blockchain Communication (IBC) protocol with selected bridge providers, this middleware facilitates seamless cross-chain asset and data transfers, as well as interactions between Layer 1 and Layer 2s within Omnitia.

This architecture enables a cohesive ecosystem where Initia’s Layer 1 and Layer 2 solutions work in tandem to support scalable and interoperable blockchain applications.

Let’s dive into deploy your own minitia in 10 minutes.

Setting up & Starting the Minitia
=================================

In this step, we’re going to install dependencies, choose your virtual machine (move, wasm, evm) and start minitia via cli.

[Install go (v1.22+)](https://go.dev/doc/install)

Download and specify minitia vm. ( I preferred to use MoveVM)

```
export VM=move # one of "move", "wasm", or "evm"
export MINITIA_VERSION="0.2.16"
git clone https://github.com/initia-labs/mini$VM.git
cd mini$VM
git checkout v$MINITIA_VERSION
make install
#verify your installation
minitiad version
```

The minitaid CLI provides a utility `launch` command to simplify the initial setup sequences of minitia. It is designed to automate the process of setting up and configuring your minitia environment.

> Before launching, we must create and fund 5 wallets for validator, bridge executer, output submitter, batch submitter and challenger.

The launch command allows customization of the minitia and related configurations and parameters through a `config.json` file.

Here is the sample `config.json` file I have created and used for this guide. I have created 5 wallet for testing purpose.

```
{
  "l1_config": {
    "chain_id": "initiation-1",
    "rpc_url": "https://initia-testnet-rpc.polkachu.com:443",
    "gas_prices": "0.15move/944f8dd8dc49f96c25fea9849f16436dcfa6d564eec802f3ef7f8b3ea85368ff"
  },
  "l2_config": {
    "chain_id": "oldbutgold-1",
    "denom": "umin",
    "moniker": "operator"
  },
  "op_bridge": {
    "output_submission_start_time": "2024-07-02T13:00:00.113678+03:00",
    "output_submission_interval": 3600000000000,
    "output_finalization_period": 3600000000000,
    "batch_submission_target": "l1"
  },
  "system_keys": {
    "validator": {
      "address": "init1gfhfujxen8jqgllud3f08fdt88n36t49vzv28a",
      "mnemonic": "bomb dinner absent siren talent unlock grant enable congress legend gallery change tongue digital invest noise journey now useless please leave quote equip once"
    },
    "bridge_executor": {
      "address": "init1tga248dnp4gl5lrw5hyxu8z6v3jkga24awgkhg",
      "mnemonic": "trash pair report cancel letter bus huge pepper liar swallow tonight muffin carpet hammer response include wood matrix pool country turtle service city shy"
    },
    "output_submitter": {
      "address": "init15a3nzt7xjx3ghzj3hc0f66c6cxrfsatfsw7qjl",
      "mnemonic": "duck seat aisle oblige until sound vacant lake fever blue post olive stick measure matrix speak nation pill broken waste gown senior scare lawn"
    },
    "batch_submitter": {
      "address": "init154xvgcpdcaf5uat9tkwz26szsjlwr9ct36ukfs",
      "mnemonic": "decrease camera butter gorilla reunion club asthma rigid alpha net level elite universe tribe universe spoon lobster cousin spend earn dose auction memory hedgehog"
    },
    "challenger": {
      "address": "init193r4nky4thmza3s94udzsc93glrt3450wnt4hp",
      "mnemonic": "absurd business salad monkey wine load certain large hub captain eternal problem check mother stamp coach valid diagram educate voyage crawl bulk access rifle"
    }
  },
  "genesis_accounts": [    {
      "address": "init1gfhfujxen8jqgllud3f08fdt88n36t49vzv28a",
      "coins": "100000000umin"
    },
    {
      "address": "init1tga248dnp4gl5lrw5hyxu8z6v3jkga24awgkhg"
    },
    {
      "address": "init15a3nzt7xjx3ghzj3hc0f66c6cxrfsatfsw7qjl"
    },
    {
      "address": "init154xvgcpdcaf5uat9tkwz26szsjlwr9ct36ukfs"
    },
    {
      "address": "init193r4nky4thmza3s94udzsc93glrt3450wnt4hp"
    }
  ]
}
```

You must change the following config items in configs.json

**l2_config -> chain_id** the chain ID of the minitia being deployed.

**l2_config -> denom** the base token denom to use for the minitia

**Launch minitiad**

```
export L1_CHAIN_ID=initiation-1 #replace with the desired Initia L1 chain ID
minitiad launch $L1_CHAIN_ID - with-config config.json
```

Once the launch steps successfully completes, two artifact files will be generated in the directory `$HOME/.minitia/artifact`and you should get an output similar to the one below:

```
############################################
Artifact written to
* /Users/initia/.minitia/artifacts/config.json
* /Users/initia/.minitia/artifacts/artifact.json
{
 "BRIDGE_ID": "105",
 "EXECUTOR_L1_MONITOR_HEIGHT": "2050600",
 "EXECUTOR_L2_MONITOR_HEIGHT": "55"
}
```

**Starting the Minitia**

We can now start running our minitia. This process will begin running the minitia and producing blocks.

```
minitiad start
```

OPInit Programs
===============

The OPinit Stack is bolstered by four dedicated bot programs:

*   Bridge Executor
*   Output Submitter
*   Batch Submitter
*   Challenger

**Install dependencies**

[NodeJS](https://nodejs.org/) (v20+)

[PostgreSQL](https://www.postgresql.org/)

**Clone and build OPInit bots**

```
git clone https://github.com/initia-labs/OPinit-bots.git
cd OPinit-bots
npm install
npm run build
```

Bridge Executor
===============

The bridge executor is the core component in minitia rollup, which is charge of following operations via [L2 Bridge Module](https://github.com/initia-labs/OPinit/blob/main/specs/l2_bridge.md):

*   Finalize L1 deposit transaction to L2.
*   Construct withdraw tx storage Merkle Tree.
*   Compute L2 output root.
*   Provide the withdrawal proofs (Merkle Proofs) to users.

**1.Set Bridge Executor Config on** `**.env.executor**`

```

touch .env.executor
```

Here is the sample `**.env.executor**` file I have created and used for this guide.

```
TYPEORM_CONNECTION=postgres               # database connection (currently only support `postgres`)
TYPEORM_HOST=localhost                    # database host
TYPEORM_USERNAME=minitia                 # database username
TYPEORM_PASSWORD=minitia                 # database password
TYPEORM_DATABASE=minitia                  # database name
TYPEORM_PORT=5432                         # database port
TYPEORM_SYNCHRONIZE=true                  # synchronize database schema
TYPEORM_LOGGING=false                     # enable logging
TYPEORM_ENTITIES=dist/orm/*Entity.js      # entity path
L1_GAS_PRICES=0.15move/944f8dd8dc49f96c25fea9849f16436dcfa6d564eec802f3ef7f8b3ea85368ff # default 0.15uinit
L1_CHAIN_ID=initiation-1
L2_CHAIN_ID=oldbutgold-1 # need to be same as in config.json
L1_LCD_URI=https://lcd.initiation-1.initia.xyz
L1_RPC_URI=https://rpc.initiation-1.initia.xyz
L2_LCD_URI=http://127.0.0.1:1317
L2_RPC_URI=http://127.0.0.1:26657
BRIDGE_ID=105 # need to be same as in $HOME/.minitia/artifacts/artifacts.json
EXECUTOR_PORT=5001
ENABLE_ORACLE=true
EXECUTOR_MNEMONIC='trash pair report cancel letter bus huge pepper liar swallow tonight muffin carpet hammer response include wood matrix pool country turtle service city shy'         # need to be same as in $HOME/.minitia/artifacts/artifacts.json
# optional config
 EXECUTOR_L1_MONITOR_HEIGHT=2031212 # I set nearest block height to do not monitor old blocks
# EXECUTOR_L2_MONITOR_HEIGHT=0
# SLACK_WEB_HOOK=''
```

**2.Run the Executer**

```
npm run prod:executor
```

You should see the similar output after successfully run.

```
024-07-04T11:14:57.537Z [info - Executor]: executor_l1_monitor syncedHeight 2053390
::ffff:127.0.0.1 - - [04/Jul/2024:11:15:00 +0000] "POST /api/v0/swarm/peers?timeout=2500ms HTTP/1.1" 404 9
2024-07-04T11:15:00.835Z [info - Executor]: executor_l1_monitor syncedHeight 2053400
2024-07-04T11:15:02.401Z [info - Executor]: [resurrect - resurrector] No unconfirmed txs found
::ffff:127.0.0.1 - - [04/Jul/2024:11:15:03 +0000] "POST /api/v0/swarm/peers?timeout=2500ms HTTP/1.1" 404 9
2024-07-04T11:15:03.488Z [info - Executor]:
          [handleNewBlock - executor_l1_monitor] Succeeded to update oracle tx in height
          currentHeight: 2053403
          latestHeight: 2076183
          txhash: 2D4DF36889CE035BB5B49BE6CC060EFD1CC4108A4229D1D05DE5EF75B43A3CF3
2024-07-04T11:15:03.504Z [info - Executor]: [checkSubmissionInterval - executor_l2_monitor] Output index not matched
2024-07-04T11:15:03.979Z [info - Executor]: [checkSubmissionInterval - executor_l2_monitor] Output index not matched
::ffff:127.0.0.1 - - [04/Jul/2024:11:15:06 +0000] "POST /api/v0/swarm/peers?timeout=2500ms HTTP/1.1" 404 9
2024-07-04T11:15:07.291Z [info - Executor]: executor_l1_monitor syncedHeight 2053410
```

Output Submitter
================

The output submitter takes the output information recorded by the bridge executor and submits them to the Initia L1 to ensure that the minitia’s block are finalized. This is done at a regular, configurable interval.

**1.Set Output Submitter Config on** `**.env.output**`

```
touch .env.output
```

Here is the sample `**.env.output**` file I have created and used for this guide.

```
TYPEORM_CONNECTION=postgres               # database connection (currently only support `postgres`)
TYPEORM_HOST=localhost                    # database host
TYPEORM_USERNAME=minitia                 # database username
TYPEORM_PASSWORD=minitia                 # database password
TYPEORM_DATABASE=minitia                   # database name
TYPEORM_PORT=5432                         # database port
TYPEORM_SYNCHRONIZE=true                  # synchronize database schema
TYPEORM_LOGGING=false                     # enable logging
TYPEORM_ENTITIES=dist/orm/*Entity.js      # entity path
L1_GAS_PRICES=0.15move/944f8dd8dc49f96c25fea9849f16436dcfa6d564eec802f3ef7f8b3ea85368ff
L1_CHAIN_ID=initiation-1
L2_CHAIN_ID=oldbutgold-1 # need to be same as in config.json
L1_LCD_URI=https://lcd.initiation-1.initia.xyz
L1_RPC_URI=https://rpc.initiation-1.initia.xyz
L2_LCD_URI=http://127.0.0.1:1317
L2_RPC_URI=http://127.0.0.1:26657
BRIDGE_ID=105 # need to be same as in $HOME/.minitia/artifacts/artifacts.json
OUTPUT_SUBMITTER_MNEMONIC='duck seat aisle oblige until sound vacant lake fever blue post olive stick measure matrix speak nation pill broken waste gown senior scare lawn'
```

**2.Run the Output Submitter**

```
npm run prod:output
```

You should see the similar output after successfully run.

```
activate  .env.output
Starting worker in mode: output
Listening on port 8081 for metrics
2024-07-04T11:18:31.044Z [info - Output]: successfully submitted! output index: 6, output root: Hj4gt0geO0S9ILtO9rgY0/TVyDMnLlh0OjQqqMV7fbs= (2168, 2452)
2024-07-04T11:18:41.889Z [info - Output]: waiting for output index from DB: 7, processed block number: 2452
2024-07-04T11:18:52.118Z [info - Output]: waiting for output index from DB: 7, processed block number: 2452
2024-07-04T11:19:02.526Z [info - Output]: waiting for output index from DB: 7, processed block number: 2452
2024-07-04T11:19:12.831Z [info - Output]: waiting for output index from DB: 7, processed block number: 2452
```

Batch Submitter
===============

The batch submitter handles aggregating transaction data stored by other bots into batches and submitting them to either the Initia L1 or Celestia to ensure [data availability](https://celestia.org/what-is-da/). I used Initia L1 for this guide.

**1.Set Batch Submitter Config on** `**.env.batch**`

```
touch .env.output
```

Here is the sample `**.env.batch**` file I have created and used for this guide.

```
TYPEORM_CONNECTION=postgres               # database connection (currently only support `postgres`)
TYPEORM_HOST=localhost                    # database host
TYPEORM_USERNAME=minitia                 # database username
TYPEORM_PASSWORD=minitia                 # database password
TYPEORM_DATABASE=minitia                   # database name
TYPEORM_PORT=5432                         # database port
TYPEORM_SYNCHRONIZE=true                  # synchronize database schema
TYPEORM_LOGGING=false                     # enable logging
TYPEORM_ENTITIES=dist/orm/*Entity.js      # entity path
L1_GAS_PRICES=0.15move/944f8dd8dc49f96c25fea9849f16436dcfa6d564eec802f3ef7f8b3ea85368ff
L1_CHAIN_ID=initiation-1
L2_CHAIN_ID=oldbutgold-1 # need to be same as in config.json
L1_LCD_URI=https://lcd.initiation-1.initia.xyz,
L1_RPC_URI=https://rpc.initiation-1.initia.xyz,
L2_LCD_URI=http://127.0.0.1:1317
L2_RPC_URI=http://127.0.0.1:26657
BRIDGE_ID=105 # need to be same as in $HOME/.minitia/artifacts/artifacts.json
PUBLISH_BATCH_TARGET=l1
# important: submitting to InitiaL1 needs gas fee and inititation-1 uses below gas prices
BATCH_GAS_PRICES=0.15move/944f8dd8dc49f96c25fea9849f16436dcfa6d564eec802f3ef7f8b3ea85368ff
BATCH_PORT=5002
BATCH_SUBMITTER_MNEMONIC='decrease camera butter gorilla reunion club asthma rigid alpha net level elite universe tribe universe spoon lobster cousin spend earn dose auction memory hedgehog'
# if you use 'celestia' as DA, set following configs
# PUBLISH_BATCH_TARGET=celestia
# BATCH_CHAIN_ID=arabica-11
# BATCH_LCD_URI=https://api.celestia-arabica-11.com
# BATCH_CHAIN_RPC_URI=https://rpc.celestia-arabica-11.com
# BATCH_GAS_PRICES='0.2utia'
# BATCH_DENOM=utia
# CELESTIA_TOKEN_AUTH=eyJ...
# CELESTIA_NAMESPACE_ID=00000000000000000000000000000000000000000000000123456789
```

**2.Run the Batch Submitter**

```
npm run batch
```

You should see the similar output after successfully run.

```
2024-07-03T12:57:15.388Z [info - Batch]: creating connection default to minitia@localhost:5432
Listening on port 5002
Listening on port 8082 for metrics
2024-07-03T12:57:21.067Z [info - Batch]: 1th batch (1, 1) is successfully saved
2024-07-03T12:59:01.097Z [info - Batch]: waiting for output index from DB: 2
2024-07-03T13:00:41.131Z [info - Batch]: waiting for output index from DB: 2
2024-07-03T13:02:21.164Z [info - Batch]: waiting for output index from DB: 2
2024-07-03T13:04:01.197Z [info - Batch]: waiting for output index from DB: 2
```

Challenger
==========

The challenger bot is responsible for monitoring and detecting invalid output proposals that are made by the output submitter and preventing them from being recorded on the Initia L1. If an invalid output is detected, it creates a record of it and sends a `MsgDeleteOutput` transaction on the L1 to dispute the proposal.

**1.Set Challenger Config on** `**.env.challenger**`

```
touch .env.challenger
```

Here is the sample `**.env.challenger**` file I have created and used for this guide.

```
TYPEORM_CONNECTION=postgres               # database connection (currently only support `postgres`)
TYPEORM_HOST=localhost                    # database host
TYPEORM_USERNAME=minitia                 # database username
TYPEORM_PASSWORD=minitia                 # database password
TYPEORM_DATABASE=minitia                   # database name
TYPEORM_PORT=5432                         # database port
TYPEORM_SYNCHRONIZE=true                  # synchronize database schema
TYPEORM_LOGGING=false                     # enable logging
TYPEORM_ENTITIES=dist/orm/*Entity.js      # entity path
L1_GAS_PRICES=0.15move/944f8dd8dc49f96c25fea9849f16436dcfa6d564eec802f3ef7f8b3ea85368ff
L1_CHAIN_ID=initiation-1
L2_CHAIN_ID=oldbutgold-1 # need to be same as in config.json
L1_LCD_URI=https://lcd.initiation-1.initia.xyz
L1_RPC_URI=https://rpc.initiation-1.initia.xyz
L2_LCD_URI=http://127.0.0.1:1317
L2_RPC_URI=http://127.0.0.1:26657
BRIDGE_ID=105 # need to be same as in $HOME/.minitia/artifacts/artifacts.json
CHALLENGER_MNEMONIC='absurd business salad monkey wine load certain large hub captain eternal problem check mother stamp coach valid diagram educate voyage crawl bulk access rifle'
```

**2.Run the Challenger**

```
npm run challenger
```

You should see the similar output after successfully run.

```
npm run challenger
> @initia/rollup@0.1.17 challenger
> WORKER_NAME=challenger node dist/worker/challenger/index.js
activate  .env.challenger
2024-07-04T11:18:34.763Z [info - Challenger]: challenger_l2_monitor syncedHeight 2010
2024-07-04T11:18:34.777Z [info - Challenger]: challenger_l2_monitor syncedHeight 2020
2024-07-04T11:18:34.889Z [info - Challenger]: challenger_l2_monitor syncedHeight 2030
2024-07-04T11:18:34.898Z [info - Challenger]: challenger_l2_monitor syncedHeight 2040
```

CONGRATULATIONS! YOU HAVE SUCCESSFULLY CONFIGURED AND RUNNING YOUR FIRST MINITA!

To safeguard against spam and maintain network stability, we must set min-gas-prices. In previous config file, we set denom as umin. Let’s set min gas price 0.15umin to our minitia.

> In our genesis config, we set 100000000umin coins to our validator wallet.

Create and execute message to opchild.

```
echo "{
  \"messages\": [    {
      \"@type\":\"/opinit.opchild.v1.MsgUpdateParams\",
      \"authority\": \"init1gz9n8jnu9fgqw7vem9ud67gqjk5q4m2w0aejne\",
      \"params\": {
        \"max_validators\": 100,
        \"historical_entries\": 10000,
        \"min_gas_prices\": [{\"amount\": \"0.15\", \"denom\": \"umin\"}],
        \"bridge_executor\": \"$(minitiad keys show bridge_executer -a)\",
        \"admin\": \"$(minitiad keys show validator -a)\",
        \"fee_whitelist\": [\"$(minitiad keys show bridge_executer -a)\", \"$(minitiad keys show output_submitter -a)\", \"$(minitiad keys show batch_submitter -a)\"]
      }
    }
  ]
}" > messages.json
```

Execute

```
minitiad tx opchild execute-messages \
  ./messages.json \
  --from validator \
  --chain-id oldbutgold-1 \
  --gas-prices 0.15umin \ 
  --gas auto --gas-adjustment 1.4
```

**Last things you might be know:**

Ok, great! We launched our minitia but how we use local explorer: [https://docs.initia.xyz/build-on-initia/general-tutorials/using-the-initia-local-explorer](https://docs.initia.xyz/build-on-initia/general-tutorials/using-the-initia-local-explorer)

Do you want to create your NFT collection in your chain?

Here is the sample command that I used for you. I just created collection with 1000 amount can be minted.

```
minitiad tx move execute 0x1 simple_nft create_collection \
  --args "string:celebration option<u64>:1000 string:oldbutgold string: bool:true bool:true bool:true bool:true bool:true bool:true decimal128:0.05" \
  --from validator \
  --gas-prices 0.15umin \
  --node http://127.0.0.1:26657 --chain-id oldbutgold-1
```

Thanks for reading!

Serkan
