---
title: Network Standard
excerpt: >-
  This document provides a guide on how to fill in the network field in the
  request body when sending API requests such as the User Verification API, User
  Account Verification API, or Risk Assessment with the Chainalysis KYT API.
  Additionally, you can check the list of networks that can be entered in the
  network field.
deprecated: false
hidden: false
metadata:
  robots: index
---
## Necessarily of Network Field

The network field is required when it is not clearly confirmed whether your VASP supports the network that the originating VASP wants to use for executing virtual asset transactions.

For example, it is impossible to specify the network on which a virtual asset, such as USDT, is deployed across multiple networks using only the symbol.

In this case, you can fill in the network field in your request body to specify the network where the virtual asset transaction would execute and prevent incorrect transactions.

<br />

## Recommendations of VerifyVASP

The network field is not required in APIs supported by VerifyVASP, but if it is not filled in, the MISMATCH-NETWORK error could occur during user or account verification. Therefore, VerifyVASP strongly recommends filling in the network field for every request.

<br />

## Network list that supported by VerifyVASP

The table below lists the values that can be entered in the Network field. The values are sorted alphabetically and are case-insensitive. **Since this table is updated irregularly, periodic checks by the responsible personnel are necessary.**

| Network Name             |
| ------------------------ |
| ABBC                     |
| Acala                    |
| Aelf                     |
| Aeternity                |
| Agoric                   |
| Akash                    |
| Algorand                 |
| Aptos                    |
| Arbitrum                 |
| Ardor                    |
| Ark                      |
| Arweave                  |
| Astar                    |
| Avalanche                |
| Axelar                   |
| Beam                     |
| Binance\_Smart\_Chain    |
| Bitcoin                  |
| Bitcoin\_Cash            |
| Bitcoin\_Diamond         |
| Bitcoin\_Gold            |
| Bitcoin\_Satoshi\_Vision |
| Bitshares                |
| Binance\_Chain           |
| Blackcoin                |
| Bytom                    |
| Cardano                  |
| Casper                   |
| Celestia                 |
| Celo                     |
| Centrifuge               |
| Chia                     |
| Chiliz                   |
| Chiliz2                  |
| Concordium               |
| Conflux                  |
| Consensus                |
| Constellation            |
| Core                     |
| Coreum                   |
| Cortex                   |
| Cosmos                   |
| Counterparty             |
| Cronos                   |
| CyberMiles               |
| Dash                     |
| Decred                   |
| DeFiChain                |
| Deso                     |
| Diamond                  |
| Diem                     |
| Digibyte                 |
| DigitalNote              |
| Divi                     |
| Dogecoin                 |
| Double\_A\_Chain         |
| Dreamcoin                |
| Ecash                    |
| Edgeware                 |
| Einsteinium              |
| Electra\_Protocol        |
| Elrond                   |
| Eminer                   |
| ENULS                    |
| Enumium                  |
| EOS                      |
| Ergo                     |
| Ethereum                 |
| EthereumFair             |
| Ethereum\_Classic        |
| Ethereum\_PoW            |
| EUNO                     |
| ExclusiveCoin            |
| Expanse                  |
| Factom                   |
| Fantom                   |
| Feathercoin              |
| Filecoin                 |
| Firo                     |
| Flare                    |
| Flo                      |
| Flow                     |
| FNCY                     |
| Folmcoin                 |
| Force                    |
| Fuse                     |
| Fusion                   |
| Gleec                    |
| Gnosis                   |
| Gochain                  |
| Gulden                   |
| Gxchain                  |
| Harmony                  |
| Hathor                   |
| Haven                    |
| Hcash                    |
| Hdac                     |
| HECO                     |
| Hedera                   |
| Helium                   |
| Hive                     |
| Horizen                  |
| HPB                      |
| Icon                     |
| Iconic                   |
| ImmutableX               |
| Injective                |
| INTChain                 |
| Internet\_Computer       |
| Iost                     |
| Iota                     |
| Iotex                    |
| Kadena                   |
| Kardiachain              |
| Kava                     |
| KCC                      |
| Khala                    |
| Klaytn                   |
| Komodo                   |
| Kon                      |
| Kurara                   |
| Kusama                   |
| Lightning                |
| Link                     |
| LinkEye                  |
| Liquid                   |
| Lisk                     |
| Litecoin                 |
| Loopring                 |
| LTO\_Network             |
| Metadium                 |
| Metaverse\_DNA           |
| Metis                    |
| Milk                     |
| Mina                     |
| MobileCoin               |
| Monacoin                 |
| Monero                   |
| Moonbeam                 |
| Moonriver                |
| Myriadcoin               |
| Nano                     |
| Navcoin                  |
| Ndau                     |
| Near                     |
| Neblio                   |
| Nebulas                  |
| Nem                      |
| Neo                      |
| Nervos                   |
| Nimiq                    |
| Numbers                  |
| Nexus                    |
| Oasis                    |
| Oasys                    |
| Okexchain                |
| Omni                     |
| Ontology                 |
| ONUS                     |
| Optimism                 |
| Palette                  |
| Palm                     |
| Payprotocol              |
| Peercoin                 |
| Persistence              |
| Pinkcoin                 |
| Pivx                     |
| PlatON                   |
| Pocket\_Network          |
| Polygon                  |
| Polygon\_zkevm           |
| Polkadot                 |
| Proton                   |
| Provenance               |
| PulseChain               |
| QRL                      |
| Qtum                     |
| Radix                    |
| Ravencoin                |
| Reddcoin                 |
| Ronin                    |
| RSK                      |
| Salus                    |
| Secret                   |
| Shiden                   |
| Shimmer                  |
| Sia                      |
| Signum                   |
| Simple\_Ledger\_Protocol |
| smartBCH                 |
| Social\_Send             |
| Solana                   |
| Sologenic                |
| Stacks                   |
| Starknet                 |
| Stellar                  |
| Step                     |
| Stratis                  |
| Sui                      |
| Supercoin                |
| Symbol                   |
| Syscoin                  |
| Tachyon                  |
| Terra                    |
| Terra2                   |
| Tezos                    |
| Theta                    |
| Thorchain                |
| TNC                      |
| TON                      |
| Tron                     |
| TrueChain                |
| Ubiq                     |
| UMEE                     |
| V\_Systems               |
| VeChain                  |
| Verge                    |
| Vertcoin                 |
| Voucher\_Coin            |
| Wanchain                 |
| Waves                    |
| Wax                      |
| Waykichain               |
| Wemix                    |
| Woo\_Network             |
| Xana                     |
| XinFin                   |
| XRP                      |
| Yoyow                    |
| Zcash                    |
| Zilliqa                  |
| zkSync                   |
| zkSync2                  |