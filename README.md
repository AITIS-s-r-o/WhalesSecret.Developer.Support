# Whale's Secret Developer Support

[Whale's Secret ScriptApiLib Library](https://www.nuget.org/packages/WhalesSecret.ScriptApiLib) is a library designed to provide unified API for .NET[^1] for [Binance](https://www.binance.com) and [KuCoin](https://www.kucoin.com) exchanges[^2].

## Reporting Issues

This repository is dedicated to [reporting issues](https://github.com/AITIS-s-r-o/WhalesSecret.Developer.Support/issues), and [discussing enhancements](https://github.com/AITIS-s-r-o/WhalesSecret.Developer.Support/discussions).

**Please note:** Issues will be prioritized and resolved based on whether you are a paying customer or not. We appreciate your understanding as our time and resources are limited.

When reporting an issue, please provide the following information:

* A clear description of the issue.
* Steps to reproduce the issue. If possible, please attach a small repro project to help us understand and fix your issue sooner.
* Any error messages you received.
* Your environment details (i.e. your operating system).

If you want to discuss an issue, you might as well join the Telegram support channel: https://t.me/+j0D_BFB1-FEyYzFk

## Installation

You can install the library using:

```xml
<PackageReference Include="WhalesSecret.ScriptApiLib" Version="1.0.1.2" />
```

See nuget.org for [the latest version](https://www.nuget.org/packages/WhalesSecret.ScriptApiLib).

## License 

[Whale's Secret ScriptApiLib Library](https://www.nuget.org/packages/WhalesSecret.ScriptApiLib) is a commercial product, but you are allowed to use the product for _free_ with the following restriction: _Unless you purchase a valid license, you can only create small orders_.

## Basic Usage

The following sample presents a simple program to download the latest top of the book from Binance for BTC/USDT. For more code snippets and examples, see our [samples repository](https://github.com/AITIS-s-r-o/WhalesSecret.ScriptApiLib.Samples?tab=readme-ov-file#how-to-start).

```cs
using WhalesSecret.ScriptApiLib;
using WhalesSecret.TradeScriptLib.API.TradingV1;
using WhalesSecret.TradeScriptLib.API.TradingV1.MarketData;
using WhalesSecret.TradeScriptLib.Entities;
using WhalesSecret.TradeScriptLib.Entities.MarketData;
using WhalesSecret.TradeScriptLib.Exceptions;
using WhalesSecret.TradeScriptLib.Exchanges;

ExchangeMarket exchangeMarket = ExchangeMarket.BinanceSpot;
SymbolPair symbolPair = SymbolPair.BTC_USDT;

// In order to unlock large orders, a valid license has to be used.
CreateOptions createOptions = new(license: null);
await using ScriptApi scriptApi = await ScriptApi.CreateAsync(createOptions).ConfigureAwait(false);

// Initialization of the market is required before connection can be created.
await Console.Out.WriteLineAsync($"Initialize exchange market {exchangeMarket}.").ConfigureAwait(false);
ExchangeInfo exchangeInfo = await scriptApi.InitializeMarketAsync(exchangeMarket).ConfigureAwait(false);

await Console.Out.WriteLineAsync($"Connect to {exchangeMarket} exchange with full-trading access.").ConfigureAwait(false);
ConnectionOptions connectionOptions = new(ConnectionType.MarketData);
ITradeApiClient tradeClient = await scriptApi.ConnectAsync(exchangeMarket, connectionOptions).ConfigureAwait(false);

await using IOrderBookSubscription subscription = await tradeClient.CreateOrderBookSubscriptionAsync(symbolPair).ConfigureAwait(false);
await Console.Out.WriteLineAsync($"Order book subscription for '{symbolPair}' on {exchangeMarket} has been created successfully as '{subscription}'. Wait for the next order order book update.").ConfigureAwait(false);

OrderBook orderBook = await subscription.GetOrderBookAsync(getMode: OrderBookGetMode.WaitUntilNew).ConfigureAwait(false);

if ((orderBook.Bids.Count == 0) || (orderBook.Asks.Count == 0))
{
    string msg = $"Empty order book has been received for symbol pair '{symbolPair}' on {exchangeMarket}.";
    await Console.Error.WriteLineAsync($"ERROR: {msg}").ConfigureAwait(false);
    throw new SanityCheckException(msg);
}

decimal bestBid = orderBook.Bids[0].Price;
decimal bestAsk = orderBook.Asks[0].Price;

await Console.Out.WriteLineAsync($"Best bid price is {bestBid}, best ask price is {bestAsk}.").ConfigureAwait(false);
```


[^1]: .NET 9+ is currently supported.
[^2]: More to come.