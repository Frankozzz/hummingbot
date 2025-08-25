# Crypto Arbitrage Bot Project Plan

## Introduction

Cryptocurrency arbitrage is the practice of buying and selling the same
crypto asset on different markets or platforms to profit from price
differences[\[1\]](https://www.binance.com/en-NG/blog/p2p/421499824684903551#:~:text=Crypto%20arbitrage%20is%20the%20practice,and%20act%20on%20price%20differences).
In essence, a trader buys a coin at a lower price on Exchange A and
quickly sells it at a higher price on Exchange B, capturing the price
spread as profit. This strategy has long existed in traditional markets
and is now popular in crypto due to frequent price discrepancies across
exchanges[\[2\]](https://www.binance.com/en-NG/blog/p2p/421499824684903551#:~:text=This%20practice%20isn%E2%80%99t%20new%20%E2%80%93,price%20differences%20between%20various%20platforms).
The goal of this project is to build a **fully automated crypto
arbitrage bot** (in Python) that can operate in Chile's crypto market
context. We will cover both **spot arbitrage** (buying and selling
actual coins on exchanges) and **futures arbitrage** (exploiting
differences between spot prices and futures or perpetual swap prices).
The plan also addresses integration with **Chilean exchanges** (for CLP
markets), handling of fees, and generating reports for **profits
(capital gains)** tracking.

**Scope and Objectives:**\
- **Exchanges:** Focus on Chilean crypto exchanges (for CLP trading)
like Buda.com, CryptoMarket (CryptoMKT), and Orionx, while also
connecting to major global exchanges (e.g. Binance, Bybit) for
additional liquidity and futures markets. We will detail how to connect
to each (APIs, libraries, accounts).\
- **Arbitrage Strategies:** Implement spot-spot arbitrage between
exchanges (including potential cross-border price
differences[\[3\]](https://www.coinsclone.com/best-crypto-arbitrage-bots/#:~:text=,accounts%20in%20multiple%20exchange%20platforms))
and spot-futures arbitrage (e.g. cash-and-carry or funding rate plays).\
- **Automation:** The bot should run continuously, monitoring markets
and executing trades 24/7 without manual intervention ("full
auto-trading").\
- **Technology:** Use Python as the primary language (as preferred),
leveraging existing libraries/frameworks where possible (for example,
CCXT for exchange APIs, or an open-source bot framework as a base).\
- **Reporting:** Include calculation of net profits after fees, and
maintain logs or reports of trades to assess performance and for capital
gains tax reporting (since crypto profits in Chile are subject to
capital gains
tax[\[4\]](https://coinpedia.org/cryptocurrency-regulation/crypto-regulations-in-chile-2024/#:~:text=Chile)).

By the end, we aim to have a detailed plan (and a separate
`proyectoplan.txt` outline) that can guide the development process,
which will likely be carried out with the help of AI coding assistants
(e.g. Claude for coding, as noted).

## Understanding Crypto Arbitrage in Chile

Arbitrage opportunities arise from **market inefficiencies** -- one
exchange's price being higher or lower than another's for the same asset
at the same time. In Chile, this can happen due to varying liquidity,
local demand, and fiat (CLP) conversion rates. Some relevant types of
crypto arbitrage include:

- **Spatial (Cross-Exchange) Arbitrage:** This is the standard approach
  of buying on one exchange and simultaneously selling on another when a
  price spread
  exists[\[3\]](https://www.coinsclone.com/best-crypto-arbitrage-bots/#:~:text=,accounts%20in%20multiple%20exchange%20platforms).
  For example, if Bitcoin is trading at a lower price on Buda (a Chilean
  exchange) than on Binance, the bot can buy BTC on Buda and sell the
  same amount of BTC on Binance to lock in profit. The trade must be
  executed quickly to avoid the gap closing.
- **Cross-Border (Fiat) Arbitrage:** Involves exploiting regional price
  differences, often caused by fiat conversion
  rates[\[3\]](https://www.coinsclone.com/best-crypto-arbitrage-bots/#:~:text=,accounts%20in%20multiple%20exchange%20platforms).
  For instance, Bitcoin's price in CLP on a local exchange might
  occasionally diverge from its USD price on international exchanges
  (after accounting for the CLP/USD exchange rate). A Chilean
  arbitrageur could take advantage by converting CLP to crypto and vice
  versa across borders. However, this often involves additional steps
  (fiat on/off-ramping) and potentially slower transfers (bank wires or
  using stablecoins as proxy for fiat). Our bot will primarily focus on
  exchange-to-exchange arbitrage, but we note this dynamic since Chilean
  markets sometimes have "prêmio" or discounts relative to global
  markets.
- **Triangular Arbitrage:** This occurs on a single exchange by trading
  through three currency pairs to end up with more of the starting
  currency. For example, on an exchange that has CLP, BTC, and USDT
  markets, a triangular arbitrage might involve CLP→BTC, BTC→USDT, then
  USDT→CLP. If the combined rate yields more CLP than started, that's a
  profit. This can be complex and is usually done within one exchange's
  order books. While our focus is primarily cross-exchange, the bot
  could be extended for triangular arbitrage on exchanges like CryptoMKT
  which list many trading pairs.
- **Spot--Futures Arbitrage:** This strategy leverages the difference
  between spot prices and futures prices. A common example is
  **cash-and-carry arbitrage** -- if a futures contract (or perpetual
  swap) is trading above the spot price (contango), one can **short**
  the futures and **buy** the equivalent amount on the spot market,
  locking in the spread as profit when the prices converge at expiry or
  through funding payments. Conversely, if futures trade below spot
  (backwardation), you'd do the opposite (long the futures, short/sell
  spot). The bot will look for such discrepancies between (for example)
  a Chilean spot price and a USD-based perpetual futures price. Notably,
  **Hummingbot's built-in strategies include "Spot Perpetual Arbitrage"
  to exploit differences between spot and perpetual
  markets[\[5\]](https://dev.to/einarcesar/complete-hummingbot-arbitrage-usage-guide-1g32#:~:text=1,Arbitrage%20with%20Automated%20Market%20Makers)**,
  which aligns with our approach.

**Challenges and Considerations:** Arbitrage is conceptually "risk-free"
profit, but in practice there are several challenges our plan must
address:

- **Fees and Costs:** Exchanges charge trading fees on each buy/sell
  (e.g., Buda's taker fee is
  0.8%[\[6\]](https://www.cryptowisser.com/exchange/buda/#:~:text=matching%20makers%E2%80%99%20orders%20with%20their,own),
  CryptoMKT's taker fee
  \~0.68%[\[7\]](https://www.cryptowisser.com/exchange/cryptomarket/#:~:text=matching%20makers%E2%80%99%20orders%20with%20their,own),
  Binance \~0.1%, etc.). Additionally, withdrawing crypto between
  exchanges incurs blockchain fees, and converting between CLP and USD
  (or stablecoins) might involve fees or spreads. These costs **must be
  factored in** -- an arbitrage opportunity is only profitable if the
  price difference exceeds all
  fees[\[8\]](https://www.binance.com/en-NG/blog/p2p/421499824684903551#:~:text=Exchanges%20with%20low%20fees%20and,higher%20price%20on%20Exchange%20B).
  Our bot will incorporate fee calculations to filter out unprofitable
  trades.
- **Latency and Execution Risk:** Price gaps can close within seconds.
  The bot needs to execute both legs of the trade almost simultaneously.
  If one leg executes and the other fails or lags, you could end up
  holding an unhedged position subject to market risk. We'll mitigate
  this by using fast API endpoints or possibly WebSocket order
  execution, and setting conservative thresholds (only trade if the
  spread is comfortably above fees to allow some slippage).
- **Exchange Liquidity & Order Book Depth:** On smaller exchanges
  (especially local ones like Buda or Orionx), the order book might be
  thin. The bot should consider the available volume at the target
  price. We might need to limit trade sizes to avoid moving the market.
  Alternatively, posting limit orders (especially on the *sell-high*
  side) could earn maker fees (lower cost) but risks not filling. A
  hybrid "market making" approach (like **cross-exchange market
  making**) can be employed -- this is actually a strategy Hummingbot
  supports, where you act as a maker on one exchange and hedge on
  another[\[5\]](https://dev.to/einarcesar/complete-hummingbot-arbitrage-usage-guide-1g32#:~:text=1,Arbitrage%20with%20Automated%20Market%20Makers).
  For simplicity, initial implementation may use taker orders for
  instant execution, then possibly refine to use maker orders if
  feasible.
- **Capital Distribution:** To arbitrage between two exchanges, the bot
  needs capital on *both* sides. For example, to arbitrage BTC price
  between Exchange X and Y, you should maintain some BTC and some cash
  (CLP or USDT) on each exchange. That way, you can buy on the cheaper
  venue while simultaneously selling on the pricier venue without
  waiting for transfers. After a round of trades, you may need to
  **rebalance** funds (e.g., move some BTC or fiat between exchanges) to
  continue trading. The plan will include managing funds and perhaps
  automating some transfers (at least crypto transfers via exchange
  APIs) when profitable and safe to do so.
- **Regulatory and Tax Compliance:** The bot itself will just trade, but
  the user must be mindful of regulations. In Chile, crypto is legal but
  considered an asset, and profits from trading are subject to capital
  gains
  tax[\[4\]](https://coinpedia.org/cryptocurrency-regulation/crypto-regulations-in-chile-2024/#:~:text=Chile).
  Therefore, it's crucial to keep a record of all trades. Our bot will
  generate logs and periodic reports detailing each trade's date, asset,
  buy/sell prices, fees, and profit/loss in terms of base currency. This
  will help in calculating capital gains for tax purposes and evaluating
  performance.

## Exchanges and Connectivity (Chile Focus)

A key aspect of this project is integrating with Chilean exchanges and
also major global exchanges. Below we list the exchanges of interest and
how to connect to them:

- **Buda.com (Chile):** Buda (previously known as SurBTC) is one of the
  first Chilean crypto exchanges, active since
  2015[\[9\]](https://www.cryptowisser.com/exchange/buda/#:~:text=What%20is%20Buda%3F).
  It offers trading in a few major cryptocurrencies (BTC, ETH, BCH, LTC)
  and even a stablecoin (USD Coin) with CLP
  markets[\[10\]](https://www.cryptowisser.com/exchange/buda/#:~:text=Cryptos%20).
  Buda's trading fees are relatively high (around 0.8% taker, 0.4%
  maker[\[6\]](https://www.cryptowisser.com/exchange/buda/#:~:text=matching%20makers%E2%80%99%20orders%20with%20their,own)),
  which is common in South America. Buda provides a REST API and
  WebSocket API for
  trading[\[11\]](https://api.buda.com/en/#:~:text=The%20Buda,you%20have%20the%20necessary%20authentication)[\[12\]](https://api.buda.com/en/#:~:text=Now%20that%20you%20have%20a,the%20documentation%20for%20both%20protocols).
  We can connect to Buda in Python either by using their API directly or
  via the **CCXT library**. Notably, Buda's own documentation recommends
  CCXT, confirming that CCXT supports Buda exchange
  integration[\[13\]](https://api.buda.com/en/#:~:text=). Using CCXT
  will greatly simplify authentication and API calls for Buda (and many
  other exchanges) through a unified interface. We will need to register
  on Buda, complete KYC, and generate API keys (with trading
  permissions) from Buda's website before the bot can trade on our
  behalf[\[14\]](https://api.buda.com/en/#:~:text=%60https%3A%2F%2Fwww.buda.com%2Fapi%2Fv2%2Fmarkets%2Feth).

- **CryptoMarket (CryptoMKT, Chile):** CryptoMKT is another Chilean
  exchange operating since
  2016[\[15\]](https://www.cryptowisser.com/exchange/cryptomarket/#:~:text=What%20is%20CryptoMarket%20).
  It supports a larger variety of coins (40+ listed) and also allows CLP
  deposits and
  trading[\[16\]](https://www.cryptowisser.com/exchange/cryptomarket/#:~:text=Cryptos%20).
  Fees are similar to Buda (approx 0.68%
  taker[\[7\]](https://www.cryptowisser.com/exchange/cryptomarket/#:~:text=matching%20makers%E2%80%99%20orders%20with%20their,own)).
  CryptoMKT has an API as well, though it's less documented publicly
  than Buda's. If CCXT supports CryptoMKT, we will use it; otherwise, we
  may use any available Python wrapper or implement HTTP requests to
  their endpoints. (We might check if CryptoMKT offers a documented API
  on their site or through open-source clients.) Integration steps are
  analogous: create account, get API key/secret, and use those
  credentials in our bot's config.

- **Orionx (Chile):** Orionx is a newer Chilean exchange that has gained
  traction and even attracted investment from Tether's venture arm in
  2023--2025[\[17\]](https://www.coindesk.com/business/2025/06/03/tether-invests-in-chilean-crypto-exchange-orionx-to-drive-latin-american-adoption#:~:text=,treasury%20management%20for%20underserved%20communities).
  Orionx offers an easy platform for Chilean users to trade BTC, ETH,
  SOL, XRP, **USDT**, and more against
  CLP[\[18\]](https://www.orionx.com/#:~:text=Si%20quieres%20comenzar%20en%20el,USDT%2C%20Ripple%20y%20muchas%20m%C3%A1s).
  The presence of USDT (Tether) on Orionx is notable, as it provides a
  direct bridge between CLP and a major stablecoin. This means our bot
  could, for example, convert CLP to USDT on Orionx and move USDT to an
  international exchange quickly if needed for arbitrage, or vice versa.
  Orionx likely has an API (we'd check their documentation or ask their
  support). If CCXT doesn't have Orionx by default, we might need a
  custom integration. Given Orionx's expansion, we will consider
  including it for arbitrage opportunities especially involving USDT/CLP
  pricing differences. Steps: create account, obtain API credentials,
  and connect via REST API calls (likely similar in pattern to other
  exchanges).

- **Global Exchanges (for USD or BTC markets and Futures):** To execute
  cross-market arbitrage and futures strategies, we will integrate at
  least one major international exchange:

- **Binance**: Binance is a top global exchange accessible from Chile
  (though only via their international site; Binance has no CLP fiat
  market, but users often use Binance P2P to get CLP
  in/out[\[19\]](https://www.binance.com/en-NG/blog/p2p/421499824684903551#:~:text=,to%20profit%20from%20price%20differences)).
  Binance offers a vast selection of spot markets and also **Binance
  Futures** (USDT-margined perpetual swaps, COIN-margined futures, etc).
  It has very low fees (0.1% or less) and high liquidity -- ideal for
  the sell side of arbitrage (selling on Binance if price is higher
  there, or buying on Binance if price is lower there). We will use
  Binance's API via CCXT or Python-Binance library. Binance requires API
  keys and possibly enabling futures in the API settings for futures
  trading. We must take care to use testnet keys or small size initially
  to ensure our bot works correctly.

- **Bybit**: Bybit is another popular exchange that offers USD perpetual
  futures and has been highlighted as one of the best for Chilean
  users[\[20\]](https://www.onesafe.io/blog/crypto-exchange-in-chile#:~:text=The%20Top%205%20Best%20Crypto,MEXC).
  If we need an alternative or additional futures platform (for example
  to arbitrage between two futures exchanges), Bybit could be integrated
  (CCXT supports Bybit as well).

- **Kraken or Bitstamp**: These are regulated exchanges that might
  support CLP trading pairs indirectly (Kraken supports some Latin
  American currencies via third parties). However, they may not be
  necessary if Binance/Bybit cover our needs. We will focus on Binance
  as the primary global exchange for both spot and futures data.

**Connecting via CCXT:** We will leverage **CCXT (CryptoCurrency
eXchange Trading library)** for unified access to multiple exchanges.
CCXT supports over 100 exchanges including
Buda[\[13\]](https://api.buda.com/en/#:~:text=), Binance, Bybit, Kraken,
etc., through a consistent API. This means we can initialize exchange
objects in Python like:

    import ccxt
    buda = ccxt.buda({'apiKey': 'YOUR_KEY', 'secret': 'YOUR_SECRET'})
    binance = ccxt.binance({'apiKey': 'YOUR_KEY', 'secret': 'YOUR_SECRET'})

and use methods like `fetch_ticker`, `fetch_order_book`, `create_order`,
etc., in a uniform way. This significantly speeds up development since
we won't have to write custom HTTP calls for each exchange's API quirks.
We'll double-check CCXT's support for CryptoMKT and Orionx; if not
supported, we may either find another library or implement minimal REST
calls for those. The bot will be designed such that adding or removing
an exchange is straightforward (just update configuration and keys).

**WebSockets vs REST Polling:** For real-time price monitoring,
especially for fast arbitrage, using WebSocket streams (where available)
is ideal to get live order book updates. However, implementing WebSocket
for every exchange can be complex. As a starting point, our bot might
poll the **ticker or order book** from each exchange's REST API at a
reasonable frequency (e.g., every few seconds) and compute price
differences. CCXT offers REST endpoints; some exchanges (like Binance)
also have official WebSocket Python clients. A hybrid approach can be
used: for local exchanges with slower-moving prices, REST polling might
suffice; for rapid futures markets, a WebSocket feed might be better.
We'll include this consideration in development, possibly starting with
REST for simplicity, and upgrading to WebSockets if performance demands.

## Arbitrage Strategy Design (Spot and Futures)

**1. Spot-to-Spot Arbitrage (Cross-Exchange):**\
In this strategy, the bot looks for a cryptocurrency (e.g., BTC) trading
at two different prices on two exchanges. Let's illustrate with Chilean
context: - Suppose on **Buda**, BTC/CLP is trading at 25,000,000 CLP per
BTC, while simultaneously on **Binance** the BTC/USDT market price
equates to 26,000,000 CLP per BTC (after converting USD price to CLP).
This implies BTC is cheaper on Buda. The bot would immediately place a
buy order on Buda (for, say, 0.1 BTC at 25M CLP each) and concurrently
place a sell order on Binance for 0.1 BTC at the equivalent price (which
might be around 30,000 USDT if that equals 26M CLP). If both orders
fill, the arbitrage yield before fees is roughly 4% (a 1,000,000 CLP
difference on 25M cost). **This is cross-exchange arbitrage**, and if
executed properly the profit is locked in.

Our bot will implement this by continuously: - Fetching **latest
prices** (or order book top) for key trading pairs across exchanges. We
will monitor a list of target assets, likely the major ones (BTC, ETH,
perhaps USDT or DAI, and any high-volume altcoins common to both local
and global exchanges).\
- Converting prices to a common quote currency for comparison. For
example, Buda gives price in CLP, Binance in USDT; we'll convert Binance
price to CLP using a real-time USD/CLP rate (which we can get via an API
or perhaps from Binance P2P market). Alternatively, convert Buda's price
to USD for comparison -- either way, incorporate FX rate. (We might use
an FX API or simply use stablecoin markets if available, e.g., if Buda
has USDC/CLP market, that gives an implied FX rate.)\
- Checking **spread = Price_BuyExchange \* (1 + fee_buy) vs
Price_SellExchange \* (1 - fee_sell)**. We include fees in the effective
prices. Also, if a crypto withdrawal will be needed to rebalance, factor
an estimated withdrawal fee. Only proceed if the net spread is positive
above a threshold.\
- If a profitable spread is found, execute **simultaneous orders**: one
`buy` on the lower-priced exchange, one `sell` on the higher-priced
exchange. In practice, true simultaneity is impossible over separate
APIs, but we will send the requests back-to-back and hope to fill both.
We'll likely use market orders to ensure immediate execution (especially
on the taker side). For the leg on a thinner exchange, we might use a
limit order set at the observed price (to avoid heavy slippage). The bot
should have a safeguard that if one order executes and the other fails
or only partially fills, it will quickly try to mitigate (e.g., if buy
succeeded but sell didn't, maybe place a sell on another exchange or at
worst, set a stop-loss if the market moves adverse). This kind of
failure is an **execution risk** inherent in arbitrage bots; we will
handle it in the code (possibly by canceling the successful leg if
possible, or executing a market order anyway and accepting a smaller
profit).\
- **Volume and Order Book considerations:** The bot will determine how
much volume to trade based on available liquidity. For example, if the
best price on Buda (to buy) is only for 0.05 BTC at that price, and the
rest is higher, the bot might only do 0.05 BTC for that round. We will
likely iterate small trades rather than one huge trade to minimize
slippage.

After executing, the result is: one exchange account will have **less of
the coin and more cash**, the other will have **more of the coin and
less cash**. This is a market-neutral profit, but requires
rebalancing: - The bot will consider periodically transferring crypto
from the exchange where it accumulated to the other where it decreased.
For instance, in the example, after buying BTC on Buda and selling on
Binance, we end up with +0.1 BTC on Buda (but CLP spent) and -0.1 BTC on
Binance (but USDT gained). We should move 0.1 BTC from Buda to Binance
to restore balance (and possibly convert the USDT on Binance back to CLP
eventually). However, moving BTC on-chain has a fee and delay. An
alternative is to use a faster/cheaper network or a stablecoin route:
e.g., sell the BTC on Buda for CLP (if not already in CLP), withdraw CLP
to bank and then fund Binance via P2P -- not automatable easily; or
convert USDT to a network like Tron or Polygon to withdraw cheaply and
deposit somewhere useful. These details will be addressed -- to start,
the bot might run a **round-robin strategy**: alternate directions of
arbitrage if possible (so that one time it buys on A sells on B, next
time if market flips it buys on B sells on A) which naturally
rebalances. If not, we plan a periodic manual or semi-automatic
rebalance step (the bot could notify when balances need rebalancing).

- **Risk Management:** Although arbitrage trades are short-lived, we
  will incorporate some risk controls. For instance, set a *minimum
  profitability threshold* (e.g. 0.5% after fees) to account for
  uncertainties. Also implement a stop or kill-switch: if the bot
  accumulates a certain amount of loss (e.g., due to failed trades or
  price swings), it should pause
  trading[\[21\]](https://dev.to/einarcesar/complete-hummingbot-arbitrage-usage-guide-1g32#:~:text=config%20kill_switch_mode%20).
  Hummingbot, for example, has a kill switch parameter for losing
  trades[\[21\]](https://dev.to/einarcesar/complete-hummingbot-arbitrage-usage-guide-1g32#:~:text=config%20kill_switch_mode%20)
  -- we can emulate similar behavior.

**2. Spot--Futures Arbitrage (Cash-and-Carry or Funding Arbitrage):**\
This strategy takes advantage of differences between an asset's spot
price on an exchange and its price on a futures market. Two main
scenarios:

- *Static Basis Arbitrage:* If a quarterly futures contract on, say,
  **Bybit**, is priced higher than spot (common in bull markets), the
  bot can **sell/short the futures** and **buy the equivalent amount in
  spot** (either on the same platform's spot market or another
  exchange's spot). These positions are opened simultaneously. The
  result is that at the futures expiry, the prices converge -- the bot's
  long spot asset can be sold at whatever the spot price is, and the
  short futures settles at that price, locking in the initial premium as
  profit. If using perpetual swaps instead of a dated future, there's no
  expiry, but there is a periodic **funding rate** paid between long and
  short traders. If the perpetual is above index price, shorts get paid
  funding from longs. Our bot could thus earn the funding payments while
  holding the arbitrage. For example, if on Binance the BTC perpetual
  swap is trading 1% higher than BTC spot on Buda, we buy 1 BTC on Buda
  and transfer it (or equivalently buy and hold it there), and open a
  short of 1 BTC on Binance Futures. The price gap might persist, but
  eventually through funding or convergence it should normalize;
  meanwhile the bot pays or receives funding every 8 hours. Ideally, in
  this case, since perp was above, the bot's short position receives
  funding from the longs, adding to profit. **Hummingbot's
  "Spot-Perpetual Arbitrage" strategy is designed for this
  scenario[\[5\]](https://dev.to/einarcesar/complete-hummingbot-arbitrage-usage-guide-1g32#:~:text=1,Arbitrage%20with%20Automated%20Market%20Makers)**,
  indicating it will continuously balance a long spot and short
  perpetual position to exploit price gaps. Our implementation can
  mirror that logic.
- *Reverse Scenario:* If the futures is below spot (occasionally in bear
  markets or if a particular exchange's perpetual has negative funding),
  the bot would **long the futures** and **short/sell spot** to gain
  from the discrepancy and possibly collect inverse funding. Shorting
  spot might require margin or using another futures. Alternatively, one
  could simulate a spot short by borrowing the asset on an exchange like
  Kraken margin and selling it -- but this complicates things. For
  simplicity, we might limit to the classic case of shorting the
  derivative and buying spot, since shorting spot is not straightforward
  on most spot-only exchanges. (We could use a second futures to create
  a long/short pair, but that becomes pure futures arbitrage.)

**Execution:** For spot-futures arbitrage, the bot's operation will
differ slightly from pure spot arbitrage: - It will monitor the **spread
between spot and futures** for a given asset. For example, track
BTC/USDT price on Binance (spot) vs BTC perpetual price on Binance or
Bybit. Futures exchanges usually provide an "index price" and "mark
price" which indicate the underlying spot equivalent. The bot can use
those to gauge premium/discount.\
- When a significant premium is detected (futures \> spot by X%), the
bot triggers an arbitrage trade: open short on futures, open long on
spot (or simply buy spot). Because futures API (like Binance's) may be
separate, we ensure both APIs (spot and futures) are accessible. We also
need sufficient margin balance on the futures account and cash on the
spot side.\
- The positions are then **held** for some duration. This is not an
instant round-trip trade like spot arbitrage; it's more of a hedged
position that earns over time. The bot must monitor this open arbitrage
position: e.g., if using a perpetual swap, watch the funding payments.
It might automatically close the positions when the futures premium
narrows to zero or turns into a discount, thereby realizing the profit.
Or if using a fixed-expiry future, just hold until expiry. For our bot,
a simpler approach is to treat each funding interval or a set target as
the exit: e.g., "if basis drops below 0.2% or after X days, close the
trade".\
- Risk: if not perfectly hedged (e.g., using two different exchanges for
spot and futures, there is counterparty risk or if the prices don't
track exactly), but generally this is low-risk. The main risk is if the
exchange doesn't allow withdrawing the profit or if something like
funding rate flips (in which case we might decide to exit early to avoid
paying funding). The bot should also have stop-loss in case the spread
widens unexpectedly (which could indicate something's off, e.g., maybe
our price feed mismatch, etc.).

**Fees in futures arbitrage:** When opening and closing futures, trading
fees apply (often around 0.04% per side for takers on futures). We also
might pay funding if on the wrong side during some intervals. All these
will be accounted for in calculating the expected profit. For example,
if 8-hour funding is +0.01% to shorts, that's an incentive; if it's
-0.01%, that's a cost. The bot can retrieve current funding rates via
API and include that in decisions (maybe only do the arb if funding will
be earned or if the initial spread is large enough to cover paying
funding).

**Integration with Spot Arbitrage:** We will design the system to handle
both types in parallel or separately configurable. For instance, the bot
could have two modes or threads: one performing fast cross-exchange spot
arbitrage on smaller spreads, and another maintaining a longer
spot-futures position. Depending on complexity, we might implement one
after the other (likely start with spot-spot arbitrage, then add the
spot-futures module).

## Technical Implementation Plan

*(A detailed step-by-step breakdown is also provided in*
`proyectoplan.txt` *at the end for quick reference.)*

**Step 1: Project Setup** -- We will set up a Python development
environment for the bot. This includes installing required libraries:
`ccxt` for exchange APIs, possibly `pandas` for data handling, and any
others (websocket clients, etc.). We'll use a version control system
(Git) to manage the code. If using an existing open-source bot as a base
(like **Hummingbot** or parts of it), we will clone that
repository[\[22\]](https://dev.to/einarcesar/complete-hummingbot-arbitrage-usage-guide-1g32#:~:text=,git%20cd%20hummingbot).
Hummingbot is quite comprehensive, so alternatively we might start a
fresh lightweight script but refer to Hummingbot's strategy logic for
guidance. (Hummingbot's code is open-source under Apache
2.0[\[23\]](https://github.com/hummingbot/hummingbot#:~:text=Hummingbot%20is%20an%20open,across%20140%2B%20unique%20trading%20venues)
-- we can draw inspiration or even modules from it as needed). The "best
public GitHub base" to start with is likely **Hummingbot's repository**
given its proven arbitrage
strategies[\[24\]](https://dev.to/einarcesar/complete-hummingbot-arbitrage-usage-guide-1g32#:~:text=Available%20Arbitrage%20Strategies)
and support for multiple exchanges. We will leverage its ideas, and even
consider using Hummingbot directly in the early phase (running a
configured instance) to test strategies. Another useful base is
**Freqtrade** (open-source Python trading
bot)[\[25\]](https://medium.com/@a.m.saghiri2008/the-best-open-source-crypto-trading-bots-on-github-f21918d28feb#:~:text=1)[\[26\]](https://medium.com/@a.m.saghiri2008/the-best-open-source-crypto-trading-bots-on-github-f21918d28feb#:~:text=Freqtrade%20is%20a%20free%20and,Telegram%20or%20a%20web%20interface),
although Freqtrade is more for single-exchange algorithmic trading.
Since arbitrage is multi-exchange, Hummingbot is more suitable, as it
provides built-in strategies like cross-exchange market making and
spot-perp
arbitrage[\[5\]](https://dev.to/einarcesar/complete-hummingbot-arbitrage-usage-guide-1g32#:~:text=1,Arbitrage%20with%20Automated%20Market%20Makers).
In summary, we'll treat Hummingbot as a reference implementation and
possibly integrate with it or use its connector components to avoid
reinventing wheels.

**Step 2: Exchange API Integration** -- Configure connections to each
target exchange: - Gather API keys and secrets from Buda, CryptoMKT,
Orionx, Binance, etc. Store them securely (the bot will likely have a
config file or use environment variables for keys). Each exchange in
CCXT has slightly different param needs; we will test connectivity by
fetching something simple (like account balance) to ensure keys work.
For example, `buda.fetch_balance()` should return our balances on
Buda[\[14\]](https://api.buda.com/en/#:~:text=%60https%3A%2F%2Fwww.buda.com%2Fapi%2Fv2%2Fmarkets%2Feth).\
- For **private trading endpoints**, ensure proper authentication (CCXT
handles HMAC signing for keys). Also note rate limits -- we must abide
by each exchange's limits (Buda's API docs have a section on rate
limits[\[27\]](https://api.buda.com/en/#:~:text=,Errors)). We might
implement a small delay or use CCXT's built-in rate limit handling (it
usually has a throttle). - If any exchange is not in CCXT, use
`requests` or an official SDK. E.g., if CryptoMKT isn't supported, we
find their API endpoint documentation and replicate what's needed
(likely similar to Buda's since both allow basic REST calls for orders
and ticker). Possibly use the **MatiasDW/Buda-Api** GitHub
snippet[\[28\]](https://github.com/MatiasDW/Buda-Api#:~:text=MatiasDW%2FBuda,8)
if it offers insight or a wrapper for Buda (though CCXT likely
suffices). Similarly, check Orionx---if no library, we'll work directly
with REST calls.

**Step 3: Market Data Retrieval** -- Implement functions to fetch
current prices: - Use CCXT's `fetch_ticker(symbol)` or
`fetch_order_book(symbol)` for each exchange/market pair of interest.
For instance, on Buda the symbol might be `"BTC/CLP"`, on Binance
`"BTC/USDT"`. CCXT will unify these if names are standard. We will
likely maintain a mapping of symbols per exchange if needed (not all
exchanges use the same currency code, e.g., CLP might be just "CLP" on
one and "CLP" on another, which is fine).\
- Convert prices to a common reference. For CLP vs USD, get an exchange
rate. We have a few options: \* Use an external FX API (like an open
exchange rate API) to get CLP per USD periodically. \* Use the CryptoMKT
or Orionx order book for USDT/CLP (if Orionx has USDT/CLP market, its
price gives a direct CLP-USD rate considering USDT ≈ USD). \* Use
Binance P2P CLP market
prices[\[19\]](https://www.binance.com/en-NG/blog/p2p/421499824684903551#:~:text=,to%20profit%20from%20price%20differences)
as an approximate rate (Binance P2P often lists how many CLP for 1
USDT). \* For initial simplicity, we might input a fixed rate or a daily
updated rate, though a real-time approach is better if CLP moves. The
CLP/USD doesn't usually swing wildly intraday, so daily or hourly
updates might be okay. - Structure: The bot will likely have an
in-memory table of latest prices from each exchange for each asset. It
will continuously update this (maybe in a loop or using asynchronous
callbacks if websockets). Then a separate process will analyze the
differences.

**Step 4: Arbitrage Opportunity Detection** -- Write logic to scan
combinations of exchanges for price differences: - For each asset in our
watchlist (BTC, ETH, etc.), and for each pair of exchanges (Exchange A
vs Exchange B), compute the potential profit percentage if bought on A
and sold on B, and vice versa. This yields two values (A→B and B→A). -
Subtract estimated fees: e.g., Profit_net ≈ (Price_B -
Price_A)/Price_A - (fee_A + fee_B). If Price_B is the sell price and
Price_A the buy price. Also subtract withdrawal fees if we plan to
transfer; however, for just a single arbitrage round we might not
withdraw immediately, so withdrawal fees aren't per trade but per
rebalance. We will, however, consider them in overall profitability
(maybe amortize those costs over trades). - If Profit_net \> 0 and above
a threshold, flag it as an opportunity. - We should also ensure both
exchanges have sufficient balance for the trade (e.g., Exchange A needs
enough quote currency to buy, Exchange B needs enough asset to sell if
we're selling from holdings; if not, either skip or if margin trading is
available use it -- but prefer using our own inventory to avoid margin
interest). - Prioritize the best opportunity at any time, or potentially
execute multiple if they don't conflict (like one on BTC, one on ETH
simultaneously). - The detection will run continuously. If using
synchronous loop, it will sleep a short interval after each cycle. If
using asyncio or multi-thread, it can be more real-time. A cautious
approach: update prices every X seconds, compute spreads, if any valid,
attempt trade, then loop.

**Step 5: Trade Execution** -- Implement the function to execute a
paired trade: - Given an identified arbitrage, the bot will calculate
the optimal quantity to trade. We might choose the minimum of (balance
available on buy side / buy price, balance available of asset on sell
side) to ensure we don't exceed either. We might also limit to the order
book depth: e.g., if our trade size is big, we may split it or only take
as much as is available at the target price without significant
slippage. - Place orders: Using CCXT, `create_market_order` for a market
order or `create_limit_order` for a specific price. Possibly do one as
limit and one as market. For example, we could submit a limit buy at
Exchange A at the current best ask (to avoid buying if price suddenly
spikes) and a market sell at Exchange B (to guarantee execution). Or
vice versa depending on which side is more liquid. These choices may be
refined with experience. Initially, doing both as market might be
simplest (ensuring immediate execution but accepting price impact). -
Use asynchronous calls if possible so we don't wait too long between
orders. In Python, we can spawn threads or async tasks for each
exchange's order. Or simply place one then immediately the other -- the
delay of a few hundred milliseconds might be acceptable in
low-volatility moments, but in crypto even that can matter. HFT-level
precision is probably not needed for the scales we handle, but we remain
mindful. - After sending orders, confirm they were filled. CCXT's
`create_order` returns an order id which we can use to query status, or
we can use `exchange.fetch_order` or `fetch_open_orders`. Ideally, both
fill completely. If one is partially filled, we have a dilemma: we can
either cancel the other proportionally or proceed to fill the remainder.
Perhaps set both orders with the same quantity determined by the lesser
side's available depth, to avoid partial fills. - Error handling: If an
order fails (e.g., API error or network issue), the bot should attempt
to cancel the other order if possible to avoid an unpaired trade. If
cancellation fails and one side got filled, we then have an unhedged
position -- the bot should alert and perhaps immediately place an
opposite order on the same exchange to undo it, even if that means
taking a small loss, to avoid exposure. These scenarios will be logged
for analysis. - Logging: Every trade attempt (successful or not) will be
logged with timestamp, details, and result. This is critical for
debugging and for the reporting feature. For example, log entry:
"2025-08-24 21:30:00: Bought 0.05 BTC @ 25,000,000 CLP on Buda, Sold
0.05 BTC @ 26,000,000 CLP (in USDT) on Binance; Profit \~50,000 CLP
(\~\$60) before fees, net profit \~30,000 CLP after fees."

**Step 6: Futures Arbitrage Execution:** This is somewhat separate from
the immediate spot arb steps: - When the bot finds a spot vs perp price
gap and decides to act, execution involves opening positions rather than
a direct swap of assets. For example, to short a BTC perpetual, we call
`binanceusdm.create_order(symbol="BTC/USDT", type="MARKET", side="sell", amount=0.1)`
and simultaneously on spot side (maybe Buda or Binance spot) do a buy
for 0.1 BTC. After that, the bot records that it has an open arbitrage
position of 0.1 BTC (long spot, short futures). - The bot should then
periodically monitor this position: check current basis, accumulated
funding, etc. It might also top-up collateral if needed (if price moves
and threatens liquidation on the short, though if we fully hedge 1:1 and
use cross-margin, it's relatively safe because if BTC pumps, our spot
BTC increases in value offsetting futures loss -- but on the exchange,
the futures account might need more margin if separate; we might simply
use the same exchange for both sides if possible, like both on Binance,
to allow using the spot BTC as collateral if using an innovative
approach like Binance's "Multi-asset mode" -- advanced topic). -
Eventually, to close: the bot will buy back the short (or sell the long
if the long was on a different exchange) when conditions are met.
Closing too early might miss some profit, waiting too long might expose
to unexpected swings -- so strategy is needed (for a fixed futures,
you'd close at expiry; for perpetual, maybe after earning X in funding
or after Y hours). - All these actions too are logged and included in
reporting (with mark-to-market P&L calculation when closed).

**Step 7: Handling Exchange-Specific Needs:** Each exchange has
nuances: - Buda and CryptoMKT are fiat on/off ramps, so they might have
daily withdrawal limits or slower transfers. The bot should be aware of
how much it can withdraw if needed (to not get stuck with funds it can't
move). Possibly integrate a check for limits from API or config. - Some
exchanges might occasionally go down or have maintenance. The bot should
catch exceptions (CCXT will throw errors if an API call fails) and
either skip that exchange temporarily or halt if critical. Implement
retry logic for transient errors. - Time synchronization: ensure system
clock is accurate (some exchanges require nonces or timestamp within
tolerance). CCXT generally handles nonces but if we use any custom calls
we ensure correct timestamp.

**Step 8: Fees, Profit Calculation & Reporting:**\
Tracking profit accurately is crucial. For each completed arbitrage
trade (spot-spot cycle or the open+close of a spot-futures position),
the bot will compute: - **Gross profit**: difference in sell vs buy
value in a base currency. For cross-exchange spot, a natural base is
maybe USD or CLP. We can compute both: say we made 50,000 CLP profit
which is \~\$60 -- store both CLP and USD equivalent. For futures
trades, profit might be realized in USDT (if we shorted a BTC/USDT
perpetual and it converged, we'll have gained USDT). Convert that to CLP
as well using the rate. - **Fees paid**: sum of trading fees on both
legs, plus any withdrawal fees if we had to move assets for that trade
specifically. Usually, we won't withdraw every trade, so withdrawal fees
might be accounted per rebalance rather than per trade. - **Net
profit**: gross minus fees.\
- **Cumulative totals**: the bot can maintain running totals of profit
in each currency. This helps in performance evaluation and also in
estimating taxes.

We will output **reports** in a user-friendly format periodically, for
example: - **Console output** for quick monitoring: after each trade
print a summary line (like the log example above). - **CSV log file**:
append each trade details as a row. The user can later import this to
Excel or a tax calculation tool. Fields might include: Date/Time,
Exchange A, Exchange B, Asset, Amount, Price A, Price B, Fees A, Fees B,
Net Profit (in CLP, USD, BTC terms). - **Summary report**: perhaps daily
or weekly, the bot could compile total number of arbitrages done, total
profit, and even estimate account balances now. Could also highlight any
significant events (e.g., "on 2025-09-10, an arbitrage trade resulted in
a -\$5 loss due to partial fill" etc., for transparency).

Capital gains for tax purposes in Chile are typically calculated on
realized gains in CLP or USD terms at the time of
transaction[\[29\]](https://lukka.tech/how-chile-taxes-cryptocurrencies/#:~:text=Taxpayers%20are%20required%20to%20pay,through%20buying%20and%20selling%20cryptocurrencies)[\[4\]](https://coinpedia.org/cryptocurrency-regulation/crypto-regulations-in-chile-2024/#:~:text=Chile).
Our detailed log will facilitate this: the user or their accountant can
see each realized gain. (Unrealized positions, like if we still hold
some asset, would be handled separately, but arbitrage by design tries
to not hold unhedged positions.)

Additionally, the bot might provide an API or interface to get a
**current status**: e.g., if one wants to query "what's my profit so far
today?" or "what positions do I have open?" Hummingbot CLI has commands
like `history` and `balance` for
this[\[30\]](https://dev.to/einarcesar/complete-hummingbot-arbitrage-usage-guide-1g32#:~:text=%2A%20%60stop%60%20,Exit%20Hummingbot)[\[31\]](https://dev.to/einarcesar/complete-hummingbot-arbitrage-usage-guide-1g32#:~:text=Verify%20Connections).
We can implement something similar in a simple way, even if just via
logs or a small web dashboard if ambitious (perhaps out-of-scope
initially, but possible extension).

**Step 9: Testing and Deployment:**\
Before trading with real funds, extensive testing is needed: -
**Backtesting**: We could backtest the strategy on historical data to
estimate profitability and tune parameters. For instance, feed
historical price series of BTC on Buda vs Binance to the logic to see
how often arbitrage existed and what profit could be. If using
Hummingbot, note that it offers backtesting and paper trading features,
as do frameworks like
Freqtrade[\[26\]](https://medium.com/@a.m.saghiri2008/the-best-open-source-crypto-trading-bots-on-github-f21918d28feb#:~:text=Freqtrade%20is%20a%20free%20and,Telegram%20or%20a%20web%20interface).
We might not have easy historic CLP market data readily, so backtesting
may be limited. Alternatively, run the bot in a **paper trading mode**
(simulate trades without actually executing) for a few days to see how
it behaves. This can be done by having a flag that instead of sending
orders, it just logs that it "would have" executed and updates a
simulated balance sheet. - **Small Scale Pilot**: Start with minimal
amounts (e.g., equivalent of \$50 or so) on two exchanges to do a live
test. This minimizes risk while verifying end-to-end flow (including
actual trade execution and funds moving). We expect initial hiccups
(like rounding issues, API quirks) which can be fixed in this phase. -
**Performance Optimization**: If the bot is missing arb opportunities
due to slow processing, we may need to optimize (maybe switch to
WebSockets, or adjust the loop frequency, or use multi-threading to
fetch prices in parallel). Python might handle a moderate load, but if
scaling up, we'll consider performance improvements. The use of Cython
in
Hummingbot[\[32\]](https://dev.to/einarcesar/complete-hummingbot-arbitrage-usage-guide-1g32#:~:text=%2A%20Order%20Books%20,speed%20execution%20of%20trading%20logic)
shows that high-frequency arbitrage benefits from optimized code, but we
likely won't reach that extreme initially.

**Step 10: Continuous Operation and Maintenance:**\
Once stable, the bot will run on a server or cloud instance (ensuring a
reliable internet connection and low latency to exchanges if possible).
We'll set up proper monitoring: - If the bot crashes or encounters an
exception, ensure it either handles it or at least alerts us (maybe via
email or messaging). We don't want it to be silently off while we assume
it's trading. - Security: API keys should have withdrawal disabled
(except maybe on one exchange if we plan automated rebalances -- even
then, better to not automate fiat withdrawals for security). Keep keys
safe (not hard-coded in public code, etc.). Use a secure server. -
Update exchange details as needed (if an exchange changes API or adds
new pairs, or if new opportunities arise). For example, if a new Chilean
exchange opens or if volumes shift, we may integrate that. - Regularly
review trade logs to identify if the bot made any trades that shouldn't
have happened (for example, a false positive where after fees it wasn't
actually profitable). Adjust parameters accordingly (increase threshold,
etc.). - Scale capital gradually as confidence grows. Also, remain aware
of diminishing returns -- very large trades might not execute at assumed
prices due to slippage, so incremental increase is wise.

**Regulatory Note:** Crypto regulations in Chile are evolving (with a
Fintech law in place since
2023[\[33\]](https://coinpedia.org/cryptocurrency-regulation/crypto-regulations-in-chile-2024/#:~:text=January%204%2C%202023%20Fintech%20law,3%2C538%E2%80%98Crypto%20services%E2%80%99%20require)).
While individuals can trade freely, if the operation gets large,
consider if any reporting or registration is needed for the bot (likely
not if just personal use). The banks in Chile previously have had issues
serving crypto
exchanges[\[34\]](https://news.bitcoin.com/cryptocurrency-exchanges-still-fighting-private-banks-for-right-to-open-bank-accounts-in-chile/#:~:text=,services%20to%20cryptocurrency%20exchanges),
but those legal battles are settling. Still, as we involve Chilean peso
transactions, staying compliant (e.g., declaring tax on profits) is
important. Our reports will help with that.

## Using an Open-Source Base (Hummingbot Recommendation)

As mentioned, **Hummingbot** is an excellent open-source framework to
base our arbitrage bot on. It has a proven architecture for algorithmic
trading across multiple exchanges and includes specific arbitrage
strategies
out-of-the-box[\[24\]](https://dev.to/einarcesar/complete-hummingbot-arbitrage-usage-guide-1g32#:~:text=Available%20Arbitrage%20Strategies).
Hummingbot's advantages for our purpose:

- It supports **30+ exchanges** through a connector
  system[\[35\]](https://dev.to/einarcesar/complete-hummingbot-arbitrage-usage-guide-1g32#:~:text=Hummingbot%20is%20an%20open,strategies%20across%20multiple%20cryptocurrency%20exchanges).
  We would need to check if it has connectors for Buda or CryptoMKT; it
  definitely has Binance, and possibly one can create a custom connector
  if not available. Even if Buda isn't included, using CCXT within
  Hummingbot or externally is possible.
- It offers a **cross-exchange market making strategy** (essentially
  arbitrage while posting orders on one side) and a **spot-perpetual
  arbitrage strategy** which are exactly what we want to
  implement[\[24\]](https://dev.to/einarcesar/complete-hummingbot-arbitrage-usage-guide-1g32#:~:text=Available%20Arbitrage%20Strategies).
  We can study Hummingbot's configuration for these strategies to guide
  our parameters. For example, Hummingbot will ask for "maker market"
  and "taker market" and a minimum profitability threshold for
  cross-exchange
  arbitrage[\[36\]](https://dev.to/einarcesar/complete-hummingbot-arbitrage-usage-guide-1g32#:~:text=2)
  -- those align with our plan to set thresholds and designate which
  side to trade passively if needed.
- The framework is written in Python/Cython for performance and is
  actively maintained by a community (Hummingbot users have collectively
  done billions in
  volume[\[23\]](https://github.com/hummingbot/hummingbot#:~:text=Hummingbot%20is%20an%20open,across%20140%2B%20unique%20trading%20venues),
  indicating robustness). Using it might save a lot of coding of
  low-level exchange handling and allow us to focus on strategy logic.
  We could either use it as a library (Hummingbot can be imported and a
  strategy triggered via code) or run the Hummingbot client separately.
- It provides **CLI commands** to monitor status, see balances, and even
  export trade history to
  CSV[\[37\]](https://dev.to/einarcesar/complete-hummingbot-arbitrage-usage-guide-1g32#:~:text=%2A%20%60create%60%20,Exit%20Hummingbot)[\[30\]](https://dev.to/einarcesar/complete-hummingbot-arbitrage-usage-guide-1g32#:~:text=%2A%20%60stop%60%20,Exit%20Hummingbot)
  -- features that match our needs for reporting and control.

Given all this, the plan is to initially develop a simple custom bot to
ensure we understand each component, but in parallel attempt to
configure Hummingbot for our scenario as a benchmark. The best approach
might be: - Use our custom code to do quick prototyping and perhaps
handle the fiat conversions explicitly (Hummingbot is more geared to
crypto-to-crypto arb, but we can handle CLP by treating CLP markets as
just another pair). - Meanwhile, set up Hummingbot with, say,
"cross_exchange_market_making" strategy between Buda and Binance on
BTC/USDT vs BTC/CLP. If a connector for Buda doesn't exist, we might
integrate Buda via CCXT in Hummingbot (there are ways to add a new
exchange using CCXT as an adapter). If that proves too much, our custom
route stands. - For spot-perp, Hummingbot's strategy can be directly
used if we, for example, connect Binance (for futures) and Binance or
another exchange (for spot). Actually, one idea: Orionx could be spot
CLP, and Binance perp as derivative -- but Hummingbot likely doesn't
have Orionx. We could test Binance spot vs Binance USD-M futures using
Hummingbot's `spot_perpetual_arbitrage` as a controlled environment and
later adapt to the Chile context.

The **best public GitHub** to start with, therefore, is
**hummingbot/hummingbot**[\[35\]](https://dev.to/einarcesar/complete-hummingbot-arbitrage-usage-guide-1g32#:~:text=Hummingbot%20is%20an%20open,strategies%20across%20multiple%20cryptocurrency%20exchanges)[\[24\]](https://dev.to/einarcesar/complete-hummingbot-arbitrage-usage-guide-1g32#:~:text=Available%20Arbitrage%20Strategies)
for a comprehensive solution. Another useful repository is the
**fendouai/ArbitrageBot** which lists multiple arbitrage tools and
references (including CCXT, Hummingbot, Blackbird,
etc.)[\[38\]](https://github.com/fendouai/ArbitrageBot#:~:text=Trading%20API)[\[39\]](https://github.com/fendouai/ArbitrageBot#:~:text=Hummingbot).
While that repo is more of a guide than code, it pointed us to key
resources we have leveraged, like CCXT and Blackbird. (Blackbird was a
popular Bitcoin arbitrage bot in
C++[\[40\]](https://github.com/fendouai/ArbitrageBot#:~:text=https%3A%2F%2Fgithub),
but we stick to Python solutions here).

By starting with Hummingbot's framework, we get a lot of built-in
functionality: robust exchange connectivity, high-performance order book
handling (critical for arbitrage
timing)[\[32\]](https://dev.to/einarcesar/complete-hummingbot-arbitrage-usage-guide-1g32#:~:text=%2A%20Order%20Books%20,speed%20execution%20of%20trading%20logic),
and a user interface for configuration. We will still need to customize
it for Chilean exchanges and ensure it accounts for CLP currency and
local fees, but the heavy lifting of strategy logic might already be
done by the community. This saves time and reduces bugs, letting us
focus on exchange integration and strategy tuning.

## Conclusion

Building a crypto arbitrage bot in Chile requires careful planning
across technical, financial, and regulatory dimensions. We have outlined
a comprehensive plan covering: - **Setup of a Python-based trading
bot**, recommending open-source tools (CCXT, Hummingbot) to accelerate
development. - **Integration with Chile's key exchanges** (Buda,
CryptoMKT, Orionx) and global ones, including handling of CLP
currency. - **Dual arbitrage strategies**: fast cross-exchange spot
arbitrage for immediate gains, and spot-futures arbitrage for
potentially larger but slower, hedged profits. - **Incorporation of
fees, slippage, and other real-world factors** to ensure true
profitability (no false arbitrage that actually loses after costs). -
**Automated operation** with risk management and fail-safes to handle
execution risks. - **Detailed reporting** of trades and profits, crucial
for evaluating performance and meeting tax obligations on capital
gains[\[4\]](https://coinpedia.org/cryptocurrency-regulation/crypto-regulations-in-chile-2024/#:~:text=Chile).

This plan is designed to be as comprehensive as possible -- covering the
steps from ideation to deployment. With this in hand, the next phase is
implementation. We expect to iterate on the strategy as we test in live
markets, but the foundational pieces laid out here will guide
development.

Below is the `proyectoplan.txt` which summarizes the step-by-step action
plan for implementation, suitable for feeding into coding workflows or
for quick reference during development.

    proyectoplan.txt
    1. **Setup Environment**: Install Python 3.x. Install libraries: ccxt for exchange APIs, requests (if needed for any custom API), pandas (for data analysis), and any exchange-specific SDKs if available. Optionally, clone Hummingbot repository as reference/base.
    2. **Exchange Accounts & API Keys**: Create and verify accounts on Buda, CryptoMKT, Orionx (Chilean exchanges) and on Binance (or Bybit for futures). Enable API access. Generate API KEY/SECRET for each; record them securely.
    3. **Configure API Access in Code**: Use CCXT to set up exchange objects:
       - Initialize Buda (`ccxt.buda`), CryptoMKT (if supported, else skip for now or use their API), Orionx (if supported or via custom API), Binance (`ccxt.binance`) and Binance's futures (`ccxt.binanceusdm` for USD-M futures).
       - Set API keys and secrets. Test each by fetching account balance or server time to ensure connectivity.
    4. **Fetch Market Data**: Implement functions to fetch latest prices:
       - Fetch order book or ticker for each target pair (e.g., BTC/CLP on Buda, BTC/USDT on Binance, etc.).
       - If needed, fetch multiple order books (for depth info).
       - Retrieve CLP/USD exchange rate (via a stablecoin market like USDT/CLP on Orionx or an external FX API) to convert prices for comparison.
       - Ensure this runs continuously (e.g., in a loop or scheduled every few seconds).
    5. **Identify Arbitrage Opportunities**: Write logic to compare prices:
       - For each asset (BTC, ETH, etc.), compare price on each exchange vs every other exchange.
       - Calculate potential profit % = (sell_price * (1 - sell_fee) - buy_price * (1 + buy_fee)) / (buy_price * (1 + buy_fee)).
       - Account for withdrawal fees if immediate transfer is required (generally, we pre-fund exchanges, so transfers happen later, not each trade).
       - Set a profitability threshold (e.g., >0.5% net) to trigger a trade.
       - Also check that required balances are available (enough quote currency on buy side, enough crypto on sell side).
    6. **Trade Execution (Spot-Spot)**: Implement function to execute arbitrage trade:
       - Determine trade amount = min(max allowable based on balances, available order book liquidity for that price difference).
       - Place buy order on cheaper exchange and sell order on expensive exchange (preferably simultaneously or as close as possible).
       - Use market orders for immediate execution (or limit orders at current best price if wanting to be maker on one side to save fee).
       - After execution, verify both orders filled. If one is partially filled or failed, handle accordingly:
         * If partial, adjust the other order’s amount (or cancel remaining).
         * If one failed entirely, cancel the other to avoid open exposure.
       - Log the trade details (exchanges, asset, amount, buy price, sell price, fees, net profit).
    7. **Trade Execution (Spot-Futures)**: Implement function for spot-futures arbitrage:
       - Monitor spot vs futures price (and funding rate if perp).
       - If futures price is higher than spot by threshold:
         * Buy asset on spot exchange (or use existing inventory) and short equivalent amount on futures exchange.
         * Record this open position (store entry prices, etc.).
       - If futures price falls back or at a chosen exit point (e.g., just before contract expiry or after X hours of funding):
         * Close the short position (buy back on futures) and sell the asset on spot (or use it elsewhere), effectively unwinding.
         * Calculate profit (includes initial price diff + any funding received - trading fees).
         * Log the round-trip.
       - (If futures is lower than spot by threshold, do the opposite: short spot (sell, possibly via margin or another future) and long futures. This step may be optional if shorting spot is not straightforward.)
    8. **Balance Management**:
       - Ensure each exchange has sufficient float: e.g., keep some BTC and CLP on Buda, some BTC or USDT on Binance, etc.
       - Implement a routine to rebalance if needed:
         * If one exchange runs low on crypto or fiat, consider transferring from the other. For example, after several buy-on-Buda/sell-on-Binance cycles, Buda might be low on CLP and high on BTC, while Binance is high on USDT and low on BTC. The bot could withdraw BTC from Buda and deposit to Binance to replenish what's sold, and optionally withdraw USDT from Binance (convert to CLP externally) to top up CLP on Buda.
         * Automating fiat transfer is hard (likely manual via bank/P2P), so the bot might just alert when rebalancing is needed (e.g., “CLP balance low on Buda”).
         * Automate crypto transfers where possible: use exchange APIs to withdraw and another’s API to detect deposit. This should be done when no immediate arb opportunities and when network fees won’t wipe profit. Possibly schedule rebalances during low volatility periods.
    9. **Profit & Performance Tracking**:
       - Maintain counters for profit in each currency (CLP, USD, BTC, etc.).
       - After each trade, update these totals.
       - Calculate ROI or yield if needed (requires tracking initial capital).
       - Generate periodic summary logs or reports (daily summary of total trades and net profit).
       - Implement an export to CSV of all trades for analysis and tax purposes.
       - (Optional) If using Hummingbot, utilize its `history` or `export` commands[37] to get trade records.
    10. **Risk Management & Safeguards**:
        - Set a global kill-switch: if cumulative losses exceed a certain amount or if a single trade loss exceeds a threshold, stop trading and send alert.
        - Set max exposure per trade and overall (don’t use all funds in one trade; keep some reserve).
        - Monitor for errors/exceptions: program should catch exceptions (like network issues, API errors) and either retry or safely shut down (possibly with cancelling any open orders).
        - Implement heartbeat or notification (the bot can send a simple email or message if it stops or if an important event occurs, so the user is aware).
    11. **Testing Phase**:
        - **Dry-run/Paper Trade**: Run the bot with `execute` functions in “dry mode” (log actions but don’t actually place orders). Feed it real market data to see how often it would trade and hypothetical profits. Refine thresholds and logic based on results.
        - **Small Real Trades**: Start with minimal amounts (e.g. 0.001 BTC) to do actual arbitrage on a quiet market. Observe behavior, ensure both legs execute properly, and that profits roughly match expectation.
        - Adjust any parameters (like time delays, order types) if issues are observed (e.g., if slippage is high, maybe use limit orders).
        - Gradually increase trade size once confident.
    12. **Deployment**:
        - Run the bot on a stable server (VPS or cloud instance). Ensure it can run 24/7. Use a process manager to auto-restart if needed.
        - Log output to files for record-keeping.
        - Secure the environment: no hardcoded secrets in code (use env vars), enable 2FA on exchange accounts (but note API keys bypass interactive 2FA), and restrict API key permissions (enable trading, disable withdrawals to avoid misuse, unless using withdrawal in code for rebalancing).
        - Regularly monitor bot performance, and update the strategy as market conditions change (e.g., if a certain exchange’s liquidity changes or new arbitrage opportunities appear).

------------------------------------------------------------------------

[\[1\]](https://www.binance.com/en-NG/blog/p2p/421499824684903551#:~:text=Crypto%20arbitrage%20is%20the%20practice,and%20act%20on%20price%20differences)
[\[2\]](https://www.binance.com/en-NG/blog/p2p/421499824684903551#:~:text=This%20practice%20isn%E2%80%99t%20new%20%E2%80%93,price%20differences%20between%20various%20platforms)
[\[8\]](https://www.binance.com/en-NG/blog/p2p/421499824684903551#:~:text=Exchanges%20with%20low%20fees%20and,higher%20price%20on%20Exchange%20B)
[\[19\]](https://www.binance.com/en-NG/blog/p2p/421499824684903551#:~:text=,to%20profit%20from%20price%20differences)
What is Crypto Arbitrage -- And How It Works on Binance P2P \| Binance
Blog

<https://www.binance.com/en-NG/blog/p2p/421499824684903551>

[\[3\]](https://www.coinsclone.com/best-crypto-arbitrage-bots/#:~:text=,accounts%20in%20multiple%20exchange%20platforms)
10 Best Crypto Arbitrage Bots Of 2025

<https://www.coinsclone.com/best-crypto-arbitrage-bots/>

[\[4\]](https://coinpedia.org/cryptocurrency-regulation/crypto-regulations-in-chile-2024/#:~:text=Chile)
[\[33\]](https://coinpedia.org/cryptocurrency-regulation/crypto-regulations-in-chile-2024/#:~:text=January%204%2C%202023%20Fintech%20law,3%2C538%E2%80%98Crypto%20services%E2%80%99%20require)
Crypto Regulations In Chile 2025

<https://coinpedia.org/cryptocurrency-regulation/crypto-regulations-in-chile-2024/>

[\[5\]](https://dev.to/einarcesar/complete-hummingbot-arbitrage-usage-guide-1g32#:~:text=1,Arbitrage%20with%20Automated%20Market%20Makers)
[\[21\]](https://dev.to/einarcesar/complete-hummingbot-arbitrage-usage-guide-1g32#:~:text=config%20kill_switch_mode%20)
[\[22\]](https://dev.to/einarcesar/complete-hummingbot-arbitrage-usage-guide-1g32#:~:text=,git%20cd%20hummingbot)
[\[24\]](https://dev.to/einarcesar/complete-hummingbot-arbitrage-usage-guide-1g32#:~:text=Available%20Arbitrage%20Strategies)
[\[30\]](https://dev.to/einarcesar/complete-hummingbot-arbitrage-usage-guide-1g32#:~:text=%2A%20%60stop%60%20,Exit%20Hummingbot)
[\[31\]](https://dev.to/einarcesar/complete-hummingbot-arbitrage-usage-guide-1g32#:~:text=Verify%20Connections)
[\[32\]](https://dev.to/einarcesar/complete-hummingbot-arbitrage-usage-guide-1g32#:~:text=%2A%20Order%20Books%20,speed%20execution%20of%20trading%20logic)
[\[35\]](https://dev.to/einarcesar/complete-hummingbot-arbitrage-usage-guide-1g32#:~:text=Hummingbot%20is%20an%20open,strategies%20across%20multiple%20cryptocurrency%20exchanges)
[\[36\]](https://dev.to/einarcesar/complete-hummingbot-arbitrage-usage-guide-1g32#:~:text=2)
[\[37\]](https://dev.to/einarcesar/complete-hummingbot-arbitrage-usage-guide-1g32#:~:text=%2A%20%60create%60%20,Exit%20Hummingbot)
Hummingbot Arbitrage Usage Guide - DEV Community

<https://dev.to/einarcesar/complete-hummingbot-arbitrage-usage-guide-1g32>

[\[6\]](https://www.cryptowisser.com/exchange/buda/#:~:text=matching%20makers%E2%80%99%20orders%20with%20their,own)
[\[9\]](https://www.cryptowisser.com/exchange/buda/#:~:text=What%20is%20Buda%3F)
[\[10\]](https://www.cryptowisser.com/exchange/buda/#:~:text=Cryptos%20)
Buda -- Reviews, Trading Fees & Cryptos (2025) \| Cryptowisser

<https://www.cryptowisser.com/exchange/buda/>

[\[7\]](https://www.cryptowisser.com/exchange/cryptomarket/#:~:text=matching%20makers%E2%80%99%20orders%20with%20their,own)
[\[15\]](https://www.cryptowisser.com/exchange/cryptomarket/#:~:text=What%20is%20CryptoMarket%20)
[\[16\]](https://www.cryptowisser.com/exchange/cryptomarket/#:~:text=Cryptos%20)
CryptoMarket -- Reviews, Trading Fees & Cryptos (2025) \| Cryptowisser

<https://www.cryptowisser.com/exchange/cryptomarket/>

[\[11\]](https://api.buda.com/en/#:~:text=The%20Buda,you%20have%20the%20necessary%20authentication)
[\[12\]](https://api.buda.com/en/#:~:text=Now%20that%20you%20have%20a,the%20documentation%20for%20both%20protocols)
[\[13\]](https://api.buda.com/en/#:~:text=)
[\[14\]](https://api.buda.com/en/#:~:text=%60https%3A%2F%2Fwww.buda.com%2Fapi%2Fv2%2Fmarkets%2Feth)
[\[27\]](https://api.buda.com/en/#:~:text=,Errors) Buda.com API Docs

<https://api.buda.com/en/>

[\[17\]](https://www.coindesk.com/business/2025/06/03/tether-invests-in-chilean-crypto-exchange-orionx-to-drive-latin-american-adoption#:~:text=,treasury%20management%20for%20underserved%20communities)
Tether Invests in Chilean Exchange Orionx to Drive Latin American
Adoption

<https://www.coindesk.com/business/2025/06/03/tether-invests-in-chilean-crypto-exchange-orionx-to-drive-latin-american-adoption>

[\[18\]](https://www.orionx.com/#:~:text=Si%20quieres%20comenzar%20en%20el,USDT%2C%20Ripple%20y%20muchas%20m%C3%A1s)
Orionx: Invierte en Bitcoin con la plataforma más fácil

<https://www.orionx.com/>

[\[20\]](https://www.onesafe.io/blog/crypto-exchange-in-chile#:~:text=The%20Top%205%20Best%20Crypto,MEXC)
The Top 5 Best Crypto Exchanges in Chile in 2025 - OneSafe Blog

<https://www.onesafe.io/blog/crypto-exchange-in-chile>

[\[23\]](https://github.com/hummingbot/hummingbot#:~:text=Hummingbot%20is%20an%20open,across%20140%2B%20unique%20trading%20venues)
GitHub - hummingbot/hummingbot: Open source software that helps you
create and deploy high-frequency crypto trading bots

<https://github.com/hummingbot/hummingbot>

[\[25\]](https://medium.com/@a.m.saghiri2008/the-best-open-source-crypto-trading-bots-on-github-f21918d28feb#:~:text=1)
[\[26\]](https://medium.com/@a.m.saghiri2008/the-best-open-source-crypto-trading-bots-on-github-f21918d28feb#:~:text=Freqtrade%20is%20a%20free%20and,Telegram%20or%20a%20web%20interface)
The Best Open-Source Crypto Trading Bots on GitHub \| by Ali M Saghiri
\| Medium

<https://medium.com/@a.m.saghiri2008/the-best-open-source-crypto-trading-bots-on-github-f21918d28feb>

[\[28\]](https://github.com/MatiasDW/Buda-Api#:~:text=MatiasDW%2FBuda,8)
MatiasDW/Buda-Api - GitHub

<https://github.com/MatiasDW/Buda-Api>

[\[29\]](https://lukka.tech/how-chile-taxes-cryptocurrencies/#:~:text=Taxpayers%20are%20required%20to%20pay,through%20buying%20and%20selling%20cryptocurrencies)
How Chile Taxes Cryptocurrencies - Lukka.tech

<https://lukka.tech/how-chile-taxes-cryptocurrencies/>

[\[34\]](https://news.bitcoin.com/cryptocurrency-exchanges-still-fighting-private-banks-for-right-to-open-bank-accounts-in-chile/#:~:text=,services%20to%20cryptocurrency%20exchanges)
Cryptocurrency Exchanges Still Fighting Private Banks for Right to \...

<https://news.bitcoin.com/cryptocurrency-exchanges-still-fighting-private-banks-for-right-to-open-bank-accounts-in-chile/>

[\[38\]](https://github.com/fendouai/ArbitrageBot#:~:text=Trading%20API)
[\[39\]](https://github.com/fendouai/ArbitrageBot#:~:text=Hummingbot)
[\[40\]](https://github.com/fendouai/ArbitrageBot#:~:text=https%3A%2F%2Fgithub)
GitHub - fendouai/ArbitrageBot: ArbitrageBot, Detect Arbitrage
Opportunities, Trading Clients, etc.

<https://github.com/fendouai/ArbitrageBot>
