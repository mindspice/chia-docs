---
sidebar_label: CAT Admin
title: CAT Admin CLI
slug: /cat-admin-cli
---

```mdx-code-block
import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';
```

## Intro

This document is a reference guide for Chia's CAT Admin Tool, located in the [CAT-admin-tool](https://github.com/Chia-Network/CAT-admin-tool) repository.

To set up your environment (including installing this tool), follow our [CAT Creation Tutorial](/guides/cat-creation-tutorial).

If you want to use this tool to reissue CAT1, then follow our [CAT2 Token Reissuance guide](/guides/cat2-issuance).

## Reference

### `cats`

Functionality: Create and administer Chia Asset Tokens (CATs)

Usage: cats [OPTIONS]

Options:

| Short Command | Long Command  | Type    | Required | Description                                                                        |
| :------------ | :------------ | :------ | :------- | :--------------------------------------------------------------------------------- |
| -l            | --tail        | TEXT    | True     | The TAIL program to launch this CAT with                                           |
| -c            | --curry       | TEXT    | False    | An argument to curry into the TAIL                                                 |
| -s            | --solution    | TEXT    | True     | The solution to the TAIL program [default: ()]                                     |
| -t            | --send-to     | TEXT    | True     | The address these CATs will appear at once they are issued                         |
| -a            | --amount      | INTEGER | True     | The amount to issue in mojos (regular XCH will be used to fund this)               |
| -m            | --fee         | INTEGER | True     | The fees for the transaction, in mojos [default: 0]                                |
| -f            | --fingerprint | INTEGER | False    | The wallet fingerprint to use as funds                                             |
| -sig          | --signature   | TEXT    | False    | A signature to aggregate with the transaction                                      |
| -as           | --spend       | TEXT    | False    | An additional spend to aggregate with the transaction                              |
| -b            | --as-bytes    | None    | False    | Output the spend bundle as a sequence of bytes instead of JSON                     |
| -sc           | --select-coin | None    | False    | Stop the process once a coin from the wallet has been selected and return the coin |
| -q            | --quiet       | None    | False    | Quiet mode will not ask to push transaction to the network                         |
| -p            | --push        | None    | False    | Automatically push transaction to the network in quiet mode                        |
|               | --help        | None    | False    | Show a help message and exit                                                       |

<details>
<summary>Example 1 - select a coin from the wallet with a value of at least 1 XCH (1 trillion mojos)</summary>

Request:

```bash
cats --tail ./reference_tails/genesis_by_coin_id.clsp.hex --send-to txch1jk4r06xsj0fnwqk57322yjqzkdyx7kh8h8kvxus3l68tjnkf05aqd9uevs --amount 1000000000000 --as-bytes --select-coin
```

Response:

```
{
    "amount": 1999731499999,
    "parent_coin_info": "0x3179dd9b38f7c4e4de532e346cfefb33affda1f2860ed68aeb0e70c38a5c9f6e",
    "puzzle_hash": "0x74fcdd0e27ead17559cf9eaf791c62a6517c0c4fcf5ac3a6f014857571fc7608"
}
Name: 345dd430bcd7a413f8feed25c382d83855edd6ccceb41d1dbc293ca8e49e6b2d
```

The "parent_coin_info", "puzzle_hash", and "amount" values are hashed together to create the coin's "Name".

</details>

<details>
<summary>Example 2 - Push a transaction to the network, currying an inner puzzle hash into the TAIL</summary>

Request:

```bash
cats --tail ./reference_tails/genesis_by_coin_id.clsp.hex --send-to txch19k6cl5syzvxgkgulr7m49v2r57yh0aanm23hrffgd89j4nj3ywhqxadyqr --amount 1000000000000 --as-bytes --curry 0x8f4dbff8df3f6aa9303eb47625cf8f09d885f1ad6a2d440582cb6bd45f53d2e8
```

Response:

```bash
The transaction has been created, would you like to push it to the network? (Y/N)y
Successfully pushed the transaction to the network
Asset ID: 9c39398afb1d7ffa03a589f60e5e39f2ae4572ff7048e689fe3128c339581b2d
Eve Coin ID: 9fe3e95308949cb9c49333f829922dc7118cd3e2fdf365cde669b47852ce3a7b
```

After pushing the transaction, the new ID and Eve Coin (singleton parent coin) will be shown.

</details>

---

### `secure_the_bag`

Functionality: Create a tree of coins from a .csv file containing puzzlehash:amount pairs. Useful for setting up CAT airdrops.

Usage: secure_the_bag [OPTIONS]

Options:

| Short Command | Long Command                  | Type    | Required | Description                                                                                                                                                                                                            |     |
| :------------ | :---------------------------- | :------ | :------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | --- |
| -c            | --curry                       | TEXT    | False    | An argument to curry into the TAIL                                                                                                                                                                                     |
| -a            | --amount                      | INTEGER | True     | The amount to issue in mojos (regular XCH will be used to fund this)                                                                                                                                                   |
| -stbtp        | --secure-the-bag-targets-path | TEXT    | True     | Path to CSV file containing targets of secure the bag (inner puzzle hash + amount). The total value of the coins in this file must match the value of the `amount` flag. If they don't match, an error will be thrown. |
| -lw           | --leaf-width                  | INTEGER | True     | Secure the bag leaf width [default: 100]                                                                                                                                                                               |
| -pr           | --prefix                      | TEXT    | True     | Address prefix [default: xch]                                                                                                                                                                                          |
|               | --help                        | NONE    | False    | Show a help message and exit                                                                                                                                                                                           |

<details>
<summary>Create a coin tree from a CSV file, currying a coin ID that was obtained from the cats command</summary>

```bash
secure_the_bag --tail .\reference_tails\genesis_by_coin_id.clsp.hex --amount 1000000000000 --secure-the-bag-targets-path C:\Users\User\Downloads\spacebucks.csv --prefix txch --curry 0x8f4dbff8df3f6aa9303eb47625cf8f09d885f1ad6a2d440582cb6bd45f53d2e8
```

The response will be a list of coins created in the tree. The command's progress will also be displayed. The last two lines of the output will be the root puzzle hash and address of the tree:

```
...
Secure the bag root puzzle hash: 17060adf6856d2904c4fe90c9690b710cf758aee5968718e2fbfd12f7b9d817f
Secure the bag root address: txch19k6cl5syzvxgkgulr7m49v2r57yh0aanm23hrffgd89j4nj3ywhqxadyqr
```

</details>

---

### `unwind_the_bag`

Functionality: Given a coin tree, airdrop CATs to a set of pre-committed puzzle hashes obtained from a .csv file. Requires a coin tree obtained as a result of running the `secure_the_bag` command.

Usage: secure_the_bag [OPTIONS]

Options:

| Short Command | Long Command                  | Type    | Required | Description                                                                                           |
| :------------ | :---------------------------- | :------ | :------- | :---------------------------------------------------------------------------------------------------- |
| -ecid         | --eve-coin-id                 | TEXT    | True     | ID of coin that was spent to create secured bag                                                       |
| -th           | --tail-hash                   | TEXT    | True     | TAIL hash / Asset ID of CAT to unwind from secured bag of CATs                                        |
| -stbtp        | --secure-the-bag-targets-path | TEXT    | True     | Path to CSV file containing targets of secure the bag (inner puzzle hash + amount)                    |
| -utph,        | --unwind-target-puzzle-hash   | TEXT    | False    | Puzzle hash of target to unwind from secured bag. This is a useful option for testing a single unwind |
| -wi           | --wallet-id                   | INTEGER | False    | The wallet id to use (typically `1`)                                                                  |
| -f            | --fingerprint                 | INTEGER | False    | The wallet fingerprint to use as funds                                                                |
| -uf           | --unwind-fee                  | INTEGER | True     | Fee paid for each unwind spend. Enough mojos must be available to cover all spends [default: 500000]  |
| -lw           | --leaf-width                  | INTEGER | True     | Secure the bag leaf width (number of tokens to unwind in one block) [default: 100]                    |
|               | --help                        | NONE    | False    | Show a help message and exit                                                                          |

<details>
<summary>Unwind a bag that has been secured with the above example, using a puzzle hash</summary>

```bash
unwind_the_bag --eve-coin-id 9fe3e95308949cb9c49333f829922dc7118cd3e2fdf365cde669b47852ce3a7b --tail-hash 9c39398afb1d7ffa03a589f60e5e39f2ae4572ff7048e689fe3128c339581b2d --secure-the-bag-targets-path C:\Users\User\Downloads\spacebucks.csv --unwind-fee 500000 --wallet-id 1 --unwind-target-puzzle-hash af85d83ff01ec4b6f37d85d038e68736adc6cc9bb2c48c9d0973605448f73f3f
```

This example will airdrop the appropriate number of coins to the given puzzle hash. You will need to confirm each coin as it is dropped.

</details>
