Sure, here's the revised README file with the new name VaultFi:

---

# VaultFi

&nbsp;

Non-Custodial | Smart Contract Staking | Peer-to-Peer Options Trading Platform.

&nbsp;

| Contracts                                                                      | Description                                                        |
| :----------------------------------------------------------------------------- | :----------------------------------------------------------------- |
| [VaultMod](contracts/vaultmod)                                                 | Proxy for minting vaults                                           |
| [Vault](contracts/vault)                                                       | Staking with peer-to-peer options trading                          |

&nbsp;

## Preparing for Merge

Before you merge the code, make sure it builds and passes all tests using the command below.

`$ cargo test`

&nbsp;

## Release Builds

You can build release artifacts manually like this, which creates a reproducible optimized build for each contract and saves them to the `./artifacts` directory:

```zsh
$ docker run --rm -v "$(pwd)":/code \
  --mount type=volume,source="$(basename "$(pwd)")_cache",target=/code/target \
  --mount type=volume,source=registry_cache,target=/usr/local/cargo/registry \
  cosmwasm/rust-optimizer:0.12.6
```

&nbsp;

## Working with Smart Contracts (archwayd)

(See [instructions](https://docs.archway.io/developers) on how to install `archwayd`)

&nbsp;

### View archwayd Config Variables

```zsh
$ open ~/.archway/config/config.toml
```

&nbsp;

### Testnet Block Explorer

[Constantine-3 test-net on mintscan](https://testnet.mintscan.io/archway-testnet) 

&nbsp;

### Add Test Accounts

```zsh
$ archwayd keys add <wallet_name> --recover
```

&nbsp;

### List Test Accounts

```zsh
$ archwayd keys list
```

&nbsp;

### Export Variables for Use in Terminal - Constantine-3

```zsh
# Export path to your Go installation
$ source ~/.profile

$ export CHAIN_ID="constantine-3"

$ export RPC="https://rpc.constantine.archway.tech:443"

$ export NODE=(--node $RPC)

$ export TXFLAG=($NODE --chain-id $CHAIN_ID --gas-prices 20000000000000aconst --gas auto --gas-adjustment 1.3)
```

&nbsp;

### Export Variables for Use in Terminal - Triomphe

```zsh
# Export path to your Go installation
$ source ~/.profile

$ export CHAIN_ID="archway-1"

$ export RPC="https://rpc.mainnet.archway.io:443"

$ export NODE=(--node $RPC)

$ export TXFLAG=($NODE --chain-id $CHAIN_ID --gas-prices 900000000000aarch --gas auto --gas-adjustment 1.3)
```

&nbsp;

### Query Balances

```zsh
$ archwayd query bank total $NODE

$ archwayd query bank balances $(archwayd keys show -a palingram) $NODE
```

&nbsp;

### Send Funds to Other Account

```zsh
$ archwayd tx bank send <sender_account_name> <receiver_address> <amount><denom> $TXFLAG
```

&nbsp;

### See the List of Code Uploaded to the Testnet

```zsh
$ archwayd query wasm list-code $NODE
```

&nbsp;

### Store the Contract on the Blockchain and Get the <CODE_ID>

```zsh
$ export RES=$(archwayd tx wasm store artifacts/<contract_name.wasm> --from <account_name> $TXFLAG -y --output json -b block)

$ export CODE_ID=$(echo $RES | jq -r '.logs[0].events[-1].attributes[1].value')

$ echo $CODE_ID
```

&nbsp;

### Get a List of Contracts Instantiated for <CODE_ID>

```zsh
$ archwayd query wasm list-contract-by-code $CODE_ID $NODE --output json
```

&nbsp;

### Prepare the JSON Message Payload

```zsh
$ export INIT='{}'
```

&nbsp;

### Instantiate the Contract

```zsh
$ archwayd tx wasm instantiate $CODE_ID "$INIT" --from <account_name> --label "CONTRACT LABEL" $TXFLAG -y --no-admin
```

&nbsp;

### Get the Latest Contract Instantiated for Contract with $CODE_ID

```zsh
$ export CONTRACT=$(archwayd query wasm list-contract-by-code $CODE_ID $NODE --output json | jq -r '.contracts[-1]')

$ echo $CONTRACT
```

&nbsp;

### Check the Contract Details

```zsh
$ archwayd query wasm contract $CONTRACT $NODE
```

&nbsp;

### Check the Contract Balance

```zsh
$ archwayd query bank balances $CONTRACT $NODE
```

&nbsp;

### Query the Entire Contract State

```zsh
$ archwayd query wasm contract-state all $CONTRACT $NODE
```

&nbsp;

### Query the Data for a Storage Key in the Contract State Directly

```zsh
$ archwayd query wasm contract-state raw $CONTRACT 636F6E666967 $NODE  --output "json" | jq -r '.data' | base64 -d
```

&nbsp;

### Calling Execute Methods

```zsh
$ export E_PAYLOAD='{"<prop>":{}}'

$ archwayd tx wasm execute $CONTRACT "$E_PAYLOAD" --from palingram $NODE $TXFLAG -y
```

&nbsp;

### Calling Query Methods

```zsh
$ export Q_PAYLOAD='{"query_list":{}}'

$ archwayd query wasm contract-state smart $CONTRACT "$Q_PAYLOAD" $NODE --output json
```

&nbsp;

## Testnet Docs

See [TESTNET.md](./TESTNET.md) for the complete VaultFi smart contracts testing docs.


