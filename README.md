# Opensea NFT API - Getting Started

Today, we'll explore Opensea NFT APIs and show you how to retrieve Opensea trading data using Bitquery Streaming APIs. Opensea has transitioned to the Seaport protocol, and we'll delve into its characteristics later in the article. For now, we'll use straightforward DEXTrades APIs to access NFT trades from Opensea.

Note: In the queries, you'll see `seaport_v1.4`, which represents not just version 1.4 of Seaport but encompasses all Seaport versions.

[![Telegram Badge](https://badgen.net/static/Join/Bloxy_info?icon=telegram)](https://t.me/Bloxy_info) [![Diiscord Bage](https://badgen.net/discord/members/qHMBkJ8gDk)](https://discord.gg/qHMBkJ8gDk)

> This tutorial was originally published on Bitquery Blog. Please check out original post: [Opensea NFT API - A Complete Developer Guide
](https://bitquery.io/blog/opensea-nft-api)

## Table of Contents
- [Getting Started](#getting-started) 
- [Newly Created NFTs on Ethereum](#newly-created-nfts-onethereum)
- [OpenSea Shared Storefront NFTs](#opensea-shared-storefront-nfts)
- [Latest NFT Trades on OpenSea](#latest-nft-trades-onopensea)
- [Most traded NFTs on Opensea](#most-traded-nfts-onopensea)
- [Total buy sell of an NFT token on Opensea](#total-buy-sell-of-an-nft-token-onopensea)
- [Top buyers of NFTs on Opensea](#top-buyers-of-nfts-onopensea)
- [Specific buyer stats for an NFT on Opensea](#specific-buyer-stats-for-an-nft-on-opensea)
- [Latest NFT Trades on Seaport](#latest-nft-trades-onseaport)
- [Get the Latest Trades of an Address](#get-the-latest-trades-of-anaddress)
- [Get Top Traded Tokens](#get-top-tradedtokens)
- [Getting Basic properties and stats of an NFT token](#getting-basic-properties-and-stats-of-an-nfttoken)
- [Popular Orangez ](#popular-orangez)
- [Top Holders of NFT token](#top-holders-of-nfttoken)
- [Conclusion](#conclusion)
- [About Bitquery](#about-bitquery)

## Getting Started

To get started for free, please create an account with your email: [GraphQL IDE ](https://ide.bitquery.io/).Once you create the account, check out our docs to learn [how to create your first query](https://docs.bitquery.io/docs/ide/query/).

To learn more about how to use the Bitquery API, please see the following resources:
- Historical / Near-Realtime Chain Data: [Blockchain API Documentation (V1 Graphql Docs) | Blockchain Graphql API (V1  API Docs)](https://docs.bitquery.io/v1/) 
- Realtime Data , Websocket and Cloud Product: [Blockchain Streaming API (V2 Graphql Docs) | Streaming API (V2  API Docs)](https://docs.bitquery.io/) 

## Newly Created NFTs on Ethereum

Before starting with Opensea, let’s first see the API to get [newly created/minted NFTs on Ethereum](https://ide.bitquery.io/New-NFTs-created-on-Ethereum_1). In the following API, we are getting nonfungible transfers from NULL Address (0x0000000000000000000000000000000000000000). Also, we are adding a date filter; it’s important as NULL Address contains so much data that it will be challenging to process them without a date filter.

```
{
  EVM(dataset: combined) {
    Transfers(
      orderBy: {descending: Block_Time}
      limit: {count: 100}
      where: {
        Transfer: {
          Sender: {
            is: "0x0000000000000000000000000000000000000000"
            }
          },
        Block: {
          Date: {
            after: "2023-06-01"
          }
        }
      }
    ) {
      Block {
        Date
        Number
      }
      Transfer {
        Amount
        Receiver
        Currency {
          Name
          SmartContract
        }
        Id
        URI
      }
      Transaction {
        Hash
      }
    }
  }
}
```

## OpenSea Shared Storefront NFTs

When you create NFT directly on OpenSea, it mints as OpenSea Shared Storefront (OPENSTORE) token. Therefore, let’s see the query to the [latest NFTs transfers OPENSTORE Token.](https://ide.bitquery.io/Latest-Opensea-Openstore-Minted-NFTs)

```graphql
{
	EVM(dataset: combined) {
		Transfers(
			orderBy: { descending: Block_Time }
			limit: { count: 100 }
			where: {
				Transfer: {
					Currency: {
						SmartContract: {
							is: "0x495f947276749ce646f68ac8c248420045cb7b5e"
						}
					}
				}
				Block: { Date: { after: "2023-06-01" } }
			}
		) {
			Block {
				Date
				Number
			}
			Transfer {
				Amount
				Receiver
				Currency {
					Name
					SmartContract
				}
				Id
				URI
			}
			Transaction {
				Hash
			}
		}
	}
}
```

Additionally, also, check API to check the [most transferred NFTs on Opensea.](https://ide.bitquery.io/Top-Transferred-NFTs-for-OPENSTORE-OpenSea-token_1) And if you want to know more about transfer and Metadata related NFT APIs, [read this blog](https://community.bitquery.io/t/nft-api-complete-guide/1509) that delves into these details.

```
{
  EVM(dataset: combined) {
    Transfers(
      orderBy: {descendingByField: "count"}
      limit: {count: 10}
      where: {Transfer: {Currency: {SmartContract: {is: "0x495f947276749ce646f68ac8c248420045cb7b5e"}}}}
    ) {
      count
      Transfer {
        Id
        Currency {
          Name
          SmartContract
        }
      }
    }
  }
}
```

## Latest NFT Trades on OpenSea

In the following query, we are getting the latest Opensea trades by tracking the [Seaport protocol](https://opensea.io/blog/articles/introducing-seaport-protocol) (Here seaport_v1.4 means all versions of Seaport) and all transactions sent to Opensea’s seaport contract `[0x00000000000000adc04c56bf30ac9d3c0aaf14dc](https://explorer.bitquery.io/ethereum/smart_contract/0x00000000000000adc04c56bf30ac9d3c0aaf14dc)`.

Actually, you can check any Marketplace NFT trades just by changing. `Transaction -> To` to the marketplace contract. Additionally, you can use [**this query**](https://ide.bitquery.io/Different-Seaport-related-Contracts) to see who is forwarding the most trades to Opensea.

```
{
  EVM(dataset: combined, network: eth) {
    DEXTrades(
      where: {Trade: {Dex: {ProtocolName: {in: "seaport_v1.4"}}}}
      limit: {count: 50}
      limitBy: {by: Transaction_To, count: 1}
      orderBy: {descendingByField: "count"}
    ) {
      Transaction {
        To
      }
      count
    }
  }
}
```

## Most traded NFTs on Opensea

We can aggregate trading vol, trade count, buyer, seller, and nfts and sort them based on trade count in the following query to get the [**most traded NFT on OpenSea**](https://ide.bitquery.io/Top-Traded-NFTs-on-Opensea).

```
{
  EVM(dataset: combined, network: eth) {
    DEXTrades(
      where: {Trade: {
        Dex: {
          ProtocolName: {
            in: "seaport_v1.4"
            }
          }
        },
        Transaction: {
          To: {
            is: "0x00000000000000adc04c56bf30ac9d3c0aaf14dc"
            }
          }
        }
      orderBy: {descendingByField: "count"}
      limit: {count: 10}
    ) {
      tradeVol: sum(of: Trade_Buy_Amount)
      count
      buyers: count(distinct: Trade_Buy_Buyer)
      seller: count(distinct: Trade_Buy_Seller)
      nfts: count(distinct: Trade_Buy_Ids)
      Trade {
        Buy {
          Currency {
            Name
            ProtocolName
            Symbol
            Fungible
            SmartContract
          }
        }
      }
    }
  }
}
```

In terms of trade count Introducing World App is the most traded NFT with 94634 trades, then Gemesis with 87655 trades, and XTREME PIXELS with 56396 trades

## Total buy sell of an NFT token on Opensea

To get the stats for specific NFT, we need to add the [Nakamigos NFT contract address](https://explorer.bitquery.io/ethereum/token/0xd774557b647330c91bf44cfeab205095f7e6c367/nft_smart_contract) in the above query.

## Top buyers of NFTs on Opensea

To know who are top buyers of NFTs, we can use the[following query](https://ide.bitquery.io/Top-buyers-of-NFTs-on-Opensea).

```
{
  EVM(dataset: combined, network: eth) {
    DEXTrades(
      where: {
        Trade: {
          Dex: {
            ProtocolName: {
              in: "seaport_v1.4"
            }
          },
          Buy: {
            Currency: {
              Fungible: false
            }
          }
        },
        Transaction: {
          To: {
            is: "0x00000000000000adc04c56bf30ac9d3c0aaf14dc"
          }
        }
      }
      orderBy: {descendingByField: "count"}
      limit: {count: 10}
    ) {
      count
      uniq_tx: count(distinct: Transaction_Hash)
      Block {
        first_date: Time(minimum: Block_Date)
        last_date: Time(maximum: Block_Date)
      }
      nfts: count(distinct: Trade_Buy_Ids)
      difffernt_nfts: count(distinct: Trade_Buy_Currency_SmartContract)
      total_money_paid: sum(of: Trade_Sell_Amount)
      Trade {
        Buy {
          Buyer
        }
      }
    }
  }
}
```

However, if you want to know the top buyers of a specific NFT, then you need to add the Currency’s smart contract address. For example,[in this query](https://ide.bitquery.io/Top-buyers-of-The-Orangez-NFT-on-Opensea), we are getting top buyers of[The Orangez NFT token](https://explorer.bitquery.io/ethereum/token/0xcd76d0cf64bf4a58d898905c5adad5e1e838e0d3/nft_smart_contract).

```
{
  EVM(dataset: combined, network: eth) {
    DEXTrades(
      where: {
        Trade: {
          Dex: {
            ProtocolName: {
              in: "seaport_v1.4"
            }
          },
          Buy: {
            Currency: {
              Fungible: false,
              SmartContract: {
                is: "0xcd76d0cf64bf4a58d898905c5adad5e1e838e0d3"
              }
            }
          }
        },
        Transaction: {
          To: {
            is: "0x00000000000000adc04c56bf30ac9d3c0aaf14dc"
          }
        }
      }
      orderBy: {descendingByField: "count"}
      limit: {count: 10}
    ) {
      count
      uniq_tx: count(distinct: Transaction_Hash)
      Block {
        first_date: Time(minimum: Block_Date)
        last_date: Time(maximum: Block_Date)
      }
      nfts: count(distinct: Trade_Buy_Ids)
      difffernt_nfts: count(distinct: Trade_Buy_Currency_SmartContract)
      total_money_paid: sum(of: Trade_Sell_Amount)
      Trade {
        Buy {
          Buyer
        }
      }
    }
  }
}
```

## Specific buyer stats for an NFT on Opensea

In this query, we will get specific [buyer stats for a specific NFT on Opensea](https://ide.bitquery.io/Specific-buyer-stats-for-an-NFT-onopensea).

```
{
  EVM(dataset: combined, network: eth) {
    DEXTrades(
      where: {
        Trade: {
          Dex: {
            ProtocolName: {
              in: "seaport_v1.4"
            }
          },
          Buy: {
            Currency: {
              SmartContract: {
                is: "0xbc4ca0eda7647a8ab7c2061c2e118a18a936f13d"
              }
            },
            Buyer: {
              is: "0x2f9ecaa66e12b6168996a6b80cda9bb142f80dd0"
            }
          }
        },
        Transaction: {
          To: {
            is: "0x00000000000000adc04c56bf30ac9d3c0aaf14dc"
          }
        }
      }
      orderBy: {descendingByField: "count"}
      limit: {count: 10}
    ) {
      count
      uniq_tx: count(distinct: Transaction_Hash)
      Block {
        first_date: Time(minimum: Block_Date)
        last_date: Time(maximum: Block_Date)
      }
      nfts: count(distinct: Trade_Buy_Ids)
      Trade {
        Buy {
          Buyer
          Currency {
            Name
            ProtocolName
            Symbol
            Fungible
            SmartContract
          }
        }
      }
    }
  }
}
```

For example, in the following query, we are getting the first and last transfer, total trade count, and NFTs bought for [0x2f9ecaa66e12b6168996a6b80cda9bb142f80dd0](https://explorer.bitquery.io/ethereum/address/0x2f9ecaa66e12b6168996a6b80cda9bb142f80dd0) address and [BoredApeYachtClub NFT token](https://explorer.bitquery.io/ethereum/token/0xbc4ca0eda7647a8ab7c2061c2e118a18a936f13d/nft_smart_contract).

## Latest NFT Trades on Seaport

In the following query, we are getting all[NFT trades for](https://ide.bitquery.io/latest-NFT-trades-on-Ethereum-network)`[Seaport](https://ide.bitquery.io/latest-NFT-trades-on-Ethereum-network)`[protocol](https://ide.bitquery.io/latest-NFT-trades-on-Ethereum-network) (Here, seaport_v1.4 includes all seaport versions). Many marketplaces utilize the Seaport protocol; we can add a Smart contract in Trade → Dex → SmartContract to get a specific marketplace for this protocol.

```
{
  EVM {
    DEXTrades(
      limit: {offset: 0, count: 10}
      orderBy: {descendingByField: "Block_Time"}
      where: {
        Trade: {
          Dex: {
            ProtocolName: {
              is: "seaport_v1.4"
            }
          }
        }
      }
    ) {
      Trade {
        Dex {
          ProtocolName
        }
        Buy {
          Price
          Seller
          Buyer
          Currency {
            HasURI
            Name
            Fungible
            SmartContract
          }
        }
        Sell {
          Price
          Amount
          Currency {
            Name
          }
          Buyer
          Seller
        }
      }
      Transaction {
        Hash
      }
      Block {
        Time
      }
    }
  }
}
```

## Get the Latest Trades of an Address

In this query, we are getting the [latest NFT trades of this address](https://ide.bitquery.io/Get-the-latest-trades-of-an-address). You can change the address according to your requirement. Additionally, if you want to see this address trade for a specific NFT, then add the smart contract of that NFT token in the filter; for example, check this query.

```
{
  EVM {
    DEXTrades(
      limit: {offset: 0, count: 10}
      orderBy: {descendingByField: "Block_Time"}
      where: {
        Trade: {
          Dex: {
            ProtocolName: {
              is: "seaport_v1.4"
            }
          },
          Sender: {
            is: "0x47f98fa3a3f8d8a62d99d112ce74e68b8de79f89"
          }
        }
      }
    ) {
      Trade {
        Dex {
          ProtocolName
        }
        Buy {
          Price
          Seller
          Buyer
          Currency {
            HasURI
            Name
            Fungible
            SmartContract
          }
        }
        Sell {
          Price
          Amount
          Currency {
            Name
          }
          Buyer
          Seller
        }
      }
      Transaction {
        Hash
      }
      Block {
        Time
      }
    }
  }
}
```

## Get Top Traded Tokens

Let’s get the [**most traded NFTs on Ethereum**](https://ide.bitquery.io/Top-traded-NFT-tokens-in-a-month) of the month using the following query. If you see we are putting the date from 1st May to 28th May, you can change them accordingly.

```
{
  EVM(dataset: combined, network: eth) {
    DEXTrades(
      orderBy: {descendingByField: "count"}
      limit: {offset: 0, count: 10}
      where: {
        Block: {
          Date: {
            since: "2023-05-01",
            till: "2023-05-28"
          }
        },
        Trade: {
          Buy: {
            Currency: {
              Fungible: false
            }
          },
          Sell: {
            Currency: {
              Fungible: true
            }
          }
        }
      }
    ) {
      Trade {
        Buy {
          Currency {
            Symbol
            SmartContract
          }
          min_price: Price(minimum: Trade_Buy_Price)
          max_rice: Price(maximum: Trade_Buy_Price)
        }
        Sell {
          Currency {
            Symbol
            SmartContract
          }
        }
      }
      buy_amount: sum(of: Trade_Buy_Amount)
      sell_amount: sum(of: Trade_Sell_Amount)
      count

    }
  }
}
```

## Getting Basic properties and stats of an NFT token

If you want to get basic properties and stats such as token NFTs, holders, total transfers, and first and last transfers, you can use[API below](https://ide.bitquery.io/Properties-of-an-NFT-Token_1). In this Graphql API, we are getting the LO-Fi PEPE token’s stats.

```
{
  EVM(dataset: combined, network: eth) {
    BalanceUpdates(
      orderBy: {descendingByField: "transfers"}
      limit: {count: 1}
      where: {
        Currency: {
          SmartContract: {
            is: "0x0fcbd68251819928c8f6d182fc04be733fa94170"
          }
        }
      }
    ) {
      ChainId
      Currency {
        Symbol
        Name
        SmartContract
      }
      transfers: count(if: {BalanceUpdate: {Type: {is: transfer}}})
      ids: uniq(of: BalanceUpdate_Id, method: approximate)
      holders: uniq(of: BalanceUpdate_Address, method: approximate)
      Block {
        last: Time(maximum: Block_Time)
        first: Time(minimum: Block_Time)
      }
    }
  }
}
```

## Popular Orangez 

In the [following query](https://ide.bitquery.io/Popular-Token-IDs-for-NFT-Based-on-Transfer-Count_1_1), we are checking the most transferred Orangez; we called them [popular Orangez](https://explorer.bitquery.io/ethereum/token/0xcd76d0cf64bf4a58d898905c5adad5e1e838e0d3/nft_smart_contract).

```
{
  EVM(dataset: combined, network: eth) {
    Transfers(
      orderBy: {descendingByField: "count"}
      limit: {offset: 0, count: 10}
      where: {
        Transfer: {
          Currency: {
            SmartContract: {
              is: "0xcd76d0cf64bf4a58d898905c5adad5e1e838e0d3"
            }
          }
        }
      }
    ) {
      ChainId
      Transfer {
        Currency {
          Symbol
          SmartContract
        }
        Id
        URI
        last_receiver: Receiver(maximum: Block_Number)
      }
      count
      receivers: uniq(of: Transfer_Receiver, method: approximate)
    }
  }
}
```

## Top Holders of NFT token

In the [following API](https://ide.bitquery.io/Top-Holders-of-WokePixels-NFT), we are getting the token holders of the [WokePixels NFT token](https://explorer.bitquery.io/ethereum/token/0xd9156c2ae917cb358cb77707e73a2e05cb3dc553/nft_smart_contract). You can get holders of any NFT just by changing the smart contract address in the query.

```
{
  EVM(dataset: combined, network: eth) {
    BalanceUpdates(
      orderBy: {descendingByField: "balance"}
      limit: {offset: 0, count: 10}
      where: {
        Currency: {
          SmartContract: {
            is: "0x0fcbd68251819928c8f6d182fc04be733fa94170"
          }
        }
      }
    ) {
      ChainId
      BalanceUpdate {
        Address
      }
      balance: sum(of: BalanceUpdate_Amount)
      ids: uniq(of: BalanceUpdate_Id, method: approximate)
    }
  }
}
```

## Conclusion

To learn, read this article on [NFT APIs](https://community.bitquery.io/t/nft-api-complete-guide/1509/1). Additionally, Sign up on our [GraphQL IDE](https://streaming.bitquery.io/) to get the API key for free. We have recently launched [Streaming API](https://community.bitquery.io/t/bitquery-streaming-apis-live-blockchain-data/1408) to provide live blockchain data.

**Also Read**

-   [The Ultimate Guide to NFT Analytics](https://bitquery.io/blog/the-ultimate-guide-to-nft-analytics)
-   [How to Track NFT Wash Trading](https://bitquery.io/blog/how-to-track-nft-wash-trading)
-   [Streaming APIs Help Gather Top Holders on Ethereum](https://bitquery.io/blog/streaming-apis-help-gather-top-holders-on-ethereum)

-   [[Video] How to track NFT Whales - A tutorial](https://www.youtube.com/watch?v=ZGEmOkLnUBg)

## About Bitquery

[**Bitquery**](https://bitquery.io/?source=blog&utm_medium=about_coinpath) is your comprehensive toolkit designed with developers in mind, simplifying blockchain data access. Our products offer practical advantages and flexibility.

-   **APIs** - [Explore API](https://ide.bitquery.io/streaming): Easily retrieve precise real-time and historical data for over 40 blockchains using GraphQL. Seamlessly integrate blockchain data into your applications, making data-driven decisions effortless.

-   **Coinpath®** - [Try Coinpath](https://bitquery.io/products/coinpath?utm_source=blog&utm_medium=about): Streamline compliance and crypto investigations by tracing money movements across 40+ blockchains. Gain insights for efficient decision-making.

-   **Data in Cloud** - [Try Demo Bucket](https://bitquery.io/products/data-in-cloud?utm_source=blog&utm_medium=about): Access indexed blockchain data cost-effectively and at scale for your data pipeline. We currently support Ethereum, BSC, Solana, with more blockchains on the horizon, simplifying your data access.

-   **Explorer** - [Try Explorer](http://explorer.bitquery.io): Discover an intuitive platform for exploring data from 40+ blockchains. Visualize data, generate queries, and integrate effortlessly into your applications.

Bitquery empowers developers with straightforward blockchain data tools. If you have questions or need assistance, connect with us on our [Telegram channel](https://t.me/Bloxy_info) or via email at <hello@bitquery.io>. Stay updated on the latest in cryptocurrency by subscribing to our newsletter below.

If you have any questions about our products, ask them on our [Telegram channel](https://t.me/Bloxy_info) or email us at [hello@bitquery.io](mailto:hello@bitquery.io). Also, subscribe to our newsletter below; we will keep you updated with the latest in the cryptocurrency world.
