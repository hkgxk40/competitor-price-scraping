# Competitor price scraping: what to capture, how often to run it, and whether to build, buy a scraping API, or buy a finished monitoring tool

Competitor price scraping usually lands on one of three setups: a scraper you write and host yourself, a scraping API you call from your own scheduler, or a finished monitoring tool with a dashboard and alerts built in. Which one fits depends on two numbers, the SKU count and the check frequency, plus how much maintenance you are willing to own.

Here is a price anchor to size things with. ScraperAPI's entry paid tier, Hobby, is **$49/month for 100,000 API credits**, and an Amazon, Walmart or eBay page costs **5 credits** per request. That is roughly 20,000 e-commerce page fetches a month before you add rendering or premium proxies. The same page on ScraperAPI's official pricing page lists six self-serve tiers plus Enterprise, and the plan you need is decided less by total page count than by which sites those pages sit on.

Below: the full plan table, a credit-cost table that explains why two scrapers quoting different prices are not comparable, a frequency decision rule by category, and the three routes with their real running costs.

> **Most readers should start on a scraping API and keep their own scheduler.** If you already know your SKU list and check cadence, ScraperAPI handles proxy rotation, geotargeting, JS rendering and parsing, and you keep control of storage and alerts. 👉 [Compare ScraperAPI plans and current credit allowances](https://www.scraperapi.com/pricing/?fp_ref=coupons)

## All ScraperAPI plans at a glance

| Plan | Credits / month | Concurrent threads | Monthly price | Annual billing | Get it |
| --- | --- | --- | --- | --- | --- |
| Hobby | 100,000 | 20 | $49 | About 10% cheaper | [Start on Hobby](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Startup | 1,000,000 | 50 | $149 | $134.10/mo, $1,609.20/yr | [Choose Startup](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Business | 3,000,000 | 100 | $299 | About 10% cheaper | [Choose Business](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Scaling | 5,000,000 | 200 | $475 | About 10% cheaper | [Choose Scaling](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Professional | 10,500,000 | 300 | $975 | About 10% cheaper | [Choose Professional](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Advanced | 21,500,000 | 500 | $1,975 | About 10% cheaper | [Choose Advanced](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Enterprise | 22,000,000+ | 500+ | Custom quote | Custom | [Request Enterprise pricing](https://www.scraperapi.com/pricing/?fp_ref=coupons) |

Two differences inside the table matter more than the credit counts. Geotargeting is US and EU only on Hobby and Startup, and becomes country-level from Business upward. Analytics history is the last 30 days on the two cheapest tiers and unlimited from Business on. If you need to check a Brazilian or Japanese storefront in local currency, that is a Business feature.

There is also a free tier: 1,000 API credits with a maximum of 5 concurrent connections, enough to test a handful of URLs and see what each one actually costs. Paid plans carry a **7-day no-questions-asked refund window**.

## What a request really costs

This is the part most comparisons skip, and it is where budgets break. ScraperAPI charges by credit, and credits per request vary by target:

| Request type | Credits | Note |
| --- | --- | --- |
| Standard page | 1 | Per request |
| Amazon, Walmart, eBay | 5 | Per request |
| Google, Bing (incl. subdomains) | 25 | Per request |
| LinkedIn | 30 | Per request |
| Bot-protection bypass | +10 | Cloudflare, DataDome, PerimeterX/HUMAN, Akamai |
| JS rendering (`render=true`) | +10 | Per request |
| Premium proxies (`premium=true`) | +10 | Per request |
| Premium proxy + JS rendering | 25 | Per request |
| Ultra premium (`ultra_premium=true`) | 30 | Paid plans only |
| Ultra premium + JS rendering | 75 | Paid plans only |

Geotargeting, sessions, device type, output format and auto parsing cost nothing extra. Every response returns its own cost in the `sa-credit-cost` header, and a Domain Cost Estimator in the dashboard quotes a URL before you scrape it. You can also set `max_cost` per request so a single expensive page cannot eat a chunk of your monthly allowance.

Now the arithmetic for a realistic price-monitoring job. 100 SKUs checked four times a day for 30 days is 12,000 requests. At 5 credits each that is 60,000 credits, which fits Hobby with room left over. Scale the same cadence to 300 SKUs and you are at 180,000 credits, so Startup. Push 100 SKUs to a 15-minute cadence, 96 checks a day, and you hit 288,000 requests and 1.44 million credits, which puts you on Business. Turn on JS rendering for Amazon pages and each fetch goes from 5 to 15 credits, tripling the bill without changing the SKU count.

👉 [Work out which plan covers your monthly request volume](https://www.scraperapi.com/pricing/?fp_ref=coupons)

## Capture more than the price tag

A price field on its own will mislead you. Shipping cost, stock status, promotional pricing, seller name and rating all change what a buyer actually pays, and they change on different schedules. A competitor who is $2 cheaper but charging $6 shipping is not cheaper. A listing showing "in stock" that ships in three weeks is not really competing with yours.

Sort your competitor list before you spend a credit on it. Primary competitors sell the same products you sell and are worth checking constantly. Secondary competitors have overlapping ranges but not identical SKUs, and tertiary players sit in adjacent markets. Monitoring primary competitors only keeps the data actionable and keeps the noise, and the bill, down.

## How often should you scrape?

Frequency is a budget decision as much as a pricing decision, and the honest answer depends on how fast your category moves.

| Category profile | Cadence | Why |
| --- | --- | --- |
| Electronics, fashion, marketplace sellers with repricing bots | Every 15–60 minutes | Prices move several times a day; a daily snapshot misses the moves you would react to |
| Most categories with active but not automated competitors | Every 6 hours | Catches same-day changes without paying for near-real-time data |
| Slow-moving categories, long lead times, B2B catalogs | Every 12–24 hours | Fewer meaningful changes per week; hourly checks add cost and no signal |

For reference, a common self-hosted setup ships with 6, 12 and 24-hour check intervals and an hourly cron scheduler underneath, while a Python tutorial build refreshes prices via a cron job every 6 hours. Those defaults exist because they are cheap and adequate for most catalogs, not because 6 hours is a law. If you sell into a category where a competitor's bot undercuts you within the hour, 6-hour checks are too slow and you should budget accordingly.

## The three routes, and what each one costs you

**1. Build the scraper yourself.** A Python stack of httpx for requests, parsel for XPath and CSS parsing, loguru for logs and asyncio for concurrency gets you started in an afternoon. The maintenance starts on day two. Every site needs its own parser: Walmart uses `data-testid='item-stack'` and `data-automation-id='product-price'`, Amazon uses `div[data-component-type='s-search-result']` and `span.a-price`, Best Buy uses `ol.sku-item-list` and `div.priceView`. When a retailer reskins its search page, your parser returns None and your price history gets a hole. You also own proxy rotation and browser fingerprint management.

A heavier self-hosted pattern is Playwright with Chromium rendering, SQLite storage, Express and TypeScript, node-cron scheduling, retry with exponential backoff, and a `PROXY_URL` variable for when your IP gets banned. That is a real, workable system, and it is still code you maintain.

**2. Scraping API plus your own scheduling layer.** The API fetches and parses; your cron, database and alerting sit on top. A workflow-automation route looks like this: a scheduled scrape of product-detail URLs, results written into a price matrix in a spreadsheet with columns for SKU, my price, competitor price, difference %, trigger threshold, floor, ceiling, new price and whether it updated, then a repricing step that pushes changes to the storefront. Non-USD rows get logged and skipped. This is the setup most small teams converge on because the anti-bot work is outsourced and the business logic stays yours.

**3. Buy a finished monitoring tool.** Price2Spy takes a list of competitor URLs and checks them daily or hourly with historical trend graphs and threshold alerts, and you never touch the scraping layer. Import.io offers a point-and-click extractor builder with daily change reports, aimed at projects with thousands of queries. ParseHub is a desktop visual builder that handles JS and AJAX sites and can schedule runs in the cloud. These are the right call when nobody on the team wants to own a pipeline.

## The cost figures that do not compare

Three vendors, three billing units, no shared denominator. Apify's E-commerce Scraping Tool bills per event at **$0.50 per 1,000 listing pages**, with residential proxies, browser rendering, review scraping and AI summaries charged separately, and higher subscription tiers lowering the per-actor rate. Octoparse charges for its Amazon template runs by keyword volume, up to 100,000 keywords per run, with optional proxies at **$3 per GB**. ScraperAPI bills by credit, with the per-domain multiplier described above. A monthly subscription from a monitoring tool bundles all of it into one price.

Before you compare two quotes, ask what one page of your target site costs in each system. A 5-credit Amazon page and a $0.50-per-1,000-listing page are not the same unit, and neither includes rendering, proxies or retries unless it says so.

Worth knowing why scraping exists at all here: Amazon, Walmart and similar marketplaces provide official pricing APIs only to approved affiliates or partners, with usage restrictions attached. Small retailers usually publish no API or do not maintain one. For most catalog sizes, scraping is the available path rather than a shortcut.

## Side-by-side, without the sales copy

| Tool | Fits | Billing basis | Known limitation |
| --- | --- | --- | --- |
| Apify E-commerce Scraping Tool | Multi-platform, no-code scraping with automation integrations | Per event, $0.50 per 1,000 listing pages, extras billed separately | Depends on Apify's team keeping up with retailer HTML changes; complex pricing |
| Octoparse Amazon template | Fast Amazon keyword research | Per run, up to 100,000 keywords; proxies $3/GB | Amazon-centric; proxy cost scales with volume |
| Price2Spy | Teams that want monitoring without touching scrapers | Subscription | Little control over extraction; you get the vendor's feature set |
| Import.io | Large enterprise data collection with visual training | Subscription, enterprise-oriented | Built for big projects; overkill for a 200-SKU catalog |
| ParseHub | Dynamic JS/AJAX sites, desktop-configured | Subscription with cloud scheduling | Desktop-centric setup; general-purpose rather than price-specific |

## Questions that come up before the first run

**Can I get Amazon prices without a partnership?** Through scraping, yes, with the caveat that Amazon is the most defended target on the list and costs 5 credits per page on ScraperAPI. Use canonical `/dp/` product URLs rather than search result pages, and expect bot-protection bypass charges on some listings.

**What happens when a target starts blocking?** On a managed API, the proxy rotation and bypass are the vendor's problem, and the extra charge shows in the credit count. On a self-built scraper, this is the failure mode that eats your weekends: rotating proxies, isolating browser contexts, retrying with exponential backoff, and finding out weeks later that half your history is empty.

**How long before I have usable data?** The free 1,000 credits cover a real test against your own URLs, and one scrape gives you a baseline. Trend data needs two to three weeks at your chosen cadence before threshold alerts stop firing on normal fluctuation.

**Do unused credits carry over?** No. The balance resets each billing cycle, so size for your busiest month and trim cadence on long-tail SKUs rather than buying a tier you use half of.

**What happens when I hit the cap?** On Hobby, Startup, Business, Scaling and Professional you can switch on auto-upgrade to move to the next tier automatically. Pay-as-you-go, which continues at a fixed per-credit rate up to a monthly cap you set, is available on Scaling, Professional, Advanced and Enterprise.

👉 [Check the current plans and open a free account with 1,000 credits](https://www.scraperapi.com/pricing/?fp_ref=coupons)

## Picking your setup

Under about 150 SKUs at a four-times-daily cadence, Hobby covers you with credits to spare and the US/EU geotargeting is probably enough. Somewhere between 300 and 1,000 SKUs at six-hour checks, Startup or Business is the honest answer, and Business is where country-level geotargeting and unlimited analytics history start. If you genuinely need 15-minute checks on a few hundred SKUs, plan for Business or Scaling, and budget for the rendering credits on top. If nobody on your team wants to own a scheduler, a database and an alerting path, buy a finished monitoring tool instead and accept less control over extraction.

Two things to confirm on the pricing page before you pay. First, run your actual target URLs through the Domain Cost Estimator so you know your true per-request cost rather than the headline 1-credit figure. Second, decide what should happen at your limit, because auto-upgrade and pay-as-you-go are available on different tiers, and credits do not roll over.

👉 [Compare ScraperAPI's plans against your SKU count](https://www.scraperapi.com/pricing/?fp_ref=coupons)
