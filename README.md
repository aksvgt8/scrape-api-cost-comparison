# Scrape.do alternatives: which API survives protected sites, what 1,000 requests really cost, and when an AI pipeline justifies the switch (plan tables and migration math included)

Three answers before the detail. If your targets sit inside a dedicated endpoint catalog and price is the whole game, ScrapingDog is the cheap pick. If most of your volume lands on Cloudflare, DataDome or LinkedIn, Bright Data is the name that keeps topping the published benchmarks, at a flat rate per 1,000. If you are feeding an LLM pipeline, Firecrawl removes the parsing layer entirely. Whichever route you take, the headline rate on a pricing page is rarely the rate you end up paying, and that gap is where most switching decisions go wrong.

> **The sensible default for mixed, general web scraping:** ScraperAPI's Hobby plan, **$49/month for 100,000 credits** (or $44.10/month billed annually), with 20 concurrent threads and a published credit cost for every request type. It suits teams pulling ordinary pages plus the occasional Amazon or Google SERP, who want to price a URL before firing 50,000 requests at it.
> 👉 [See ScraperAPI's plan prices and credit table](https://www.scraperapi.com/pricing?fp_ref=coupons)

## ScraperAPI's full plan lineup, and the number the pricing page buries

ScraperAPI sells credits, and the price per credit collapses as you move up the tiers. Hobby works out at $0.49 per 1,000 credits. Business works out at roughly $0.10. That five-fold spread decides more than the sticker price does, because a JavaScript-rendered page consumes 10 credits: the same page costs $4.90 per 1,000 on Hobby and about $1.00 per 1,000 on Business.

| Plan | Credits / concurrent threads | Monthly | Annual (per month) | $ per 1,000 credits | Get it |
| --- | --- | --- | --- | --- | --- |
| Free | 1,000 / 5 | $0 | — | — | [Open a free account](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Hobby | 100,000 / 20 | $49 | $44.10 | $0.49 | [Start with Hobby](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Startup | 1,000,000 / 50 | $149 | $134.10 | $0.15 | [Start with Startup](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Business | 3,000,000 / 100 | $299 | $269.10 | $0.10 | [Start with Business](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Scaling (most popular) | 5,000,000 / 200 | $475 | $427.50 | $0.10 | [Start with Scaling](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Professional | 10,500,000 / 300 | $975 | $877.50 | $0.09 | [Start with Professional](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Advanced | 21,500,000 / 500 | $1,975 | $1,777.50 | $0.09 | [Start with Advanced](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Enterprise | 22,000,000+ / 500+ | Custom | Custom | Custom | [Request Enterprise pricing](https://www.scraperapi.com/pricing?fp_ref=coupons) |

The details that change a purchase decision sit outside the price columns:

- **Signup includes a 7-day, 5,000-credit trial with no credit card.** When it ends you drop to the free plan: 1,000 credits a month, 5 threads.
- **Annual billing is exactly 10% off** and you receive the full year's credits upfront, spendable on any schedule.
- **Geotargeting is tiered.** Hobby and Startup give you US and EU only. Country-level targeting starts at Business.
- **Analytics history** runs 30 days on Hobby and Startup, unlimited from Business upward.
- **Credits reset at renewal and never roll over.** On Hobby, Startup and Business, exhausting them returns a 429 rather than a surprise charge. Pay-as-you-go is a Scaling-and-above feature, with a monthly cap you set yourself.
- **Refunds:** 7 days, no questions asked.

## What 1,000 requests actually cost, request by request

This is the table to read before you compare anyone's headline rate. ScraperAPI publishes a fixed credit cost per request type, and every response returns its own cost in a header. At the Hobby rate of $0.49 per 1,000 credits:

| Request type | Credits each | $ per 1,000 at Hobby | $ per 1,000 at Business |
| --- | --- | --- | --- |
| Standard page | 1 | $0.49 | $0.10 |
| Amazon, Walmart, eBay | 5 | $2.45 | $0.50 |
| Google and Bing (incl. subdomains) | 25 | $12.25 | $2.49 |
| LinkedIn | 30 | $14.70 | $2.99 |
| Bot-protection bypass (Cloudflare, DataDome, PerimeterX, Akamai) | +10 added to base | $4.90+ | $1.00+ |
| JavaScript rendering (`render=true`) | 10 | $4.90 | $1.00 |
| Premium proxies (`premium=true`) | 10 | $4.90 | $1.00 |
| Premium proxies + JS rendering | 25 | $12.25 | $2.49 |
| Ultra premium (`ultra_premium=true`, paid plans only) | 30 | $14.70 | $2.99 |
| Ultra premium + JS rendering | 75 | $36.75 | $7.48 |

Two things follow. First, `render=true` on every request multiplies your bill by ten, and stacking bot-protection bypass on top of rendering can push a single page past $7 per 1,000 at the Business rate. Second, the Domain Cost Estimator in the dashboard quotes the exact cost for a URL before you scrape it, and `max_cost` caps any single request. That combination is what makes a budget forecastable: you find out a domain is ultra-premium before the invoice, not after.

👉 [Price your own target domains against the current credit table](https://www.scraperapi.com/pricing?fp_ref=coupons)

For contrast, Scrape.do charges $29/month for 250,000 credits, which is $0.116 per 1,000 credits at base. On static HTML that is roughly a quarter of ScraperAPI's Hobby rate, and pretending otherwise would be dishonest. ScraperAPI's case does not rest on being cheapest. It rests on a published per-domain rate card, pre-built parsed endpoints for SERPs, e-commerce and real estate, and four ways to call it (API, async API, proxy port, ready-parsed endpoints) so you can drop it under an existing pipeline rather than rebuilding around it.

## Why teams leave Scrape.do

Scrape.do's $29/250,000-credit plan is genuinely cheap, and its opt-in parameter model means you only pay multipliers when you ask for them. The multipliers are what erode the headline number: JavaScript rendering counts 5x and residential proxies count 10x. Those 250,000 credits become 50,000 rendered requests, or 25,000 residential ones. Effective cost moves from $0.116 to $0.58 and $1.16 per 1,000 respectively. If most of your traffic needs either feature, the plan you thought you bought is a fifth or a tenth of its advertised size.

The functional gaps matter more for some teams than the price does. Scrape.do returns raw HTML by default; `output=md` gets you markdown, but there is no built-in LLM extraction and no schema-driven JSON. There is no crawl endpoint, so multi-page site traversal is your own URL queue to build and maintain. There are no official SDK packages, only HTTP code samples, and no AI framework integrations. It is closed source and cannot be self-hosted. What it does have is a 1,000-request monthly free tier with no credit card and no expiry, which remains one of the least painful ways to test a scraping API.

If your workload is mostly static HTML at low volume, leaving is hard to justify. If you are assembling an AI pipeline, or you are tired of maintaining a crawler around a proxy API, the switch pays for itself in deleted glue code.

## The alternatives, grouped by the job you need done

### Heavily protected targets

**Bright Data** tops Scrape.do's own benchmark, which is worth something precisely because the publisher is a competitor. Pricing is the awkward part: the same benchmark reports a flat **$1.50 per 1,000** in one place and **$2.50 per 1,000** for premium sites like Walmart or LinkedIn in another. Those look like two tiers rather than a contradiction, but treat the range as $1.50 to $2.50 depending on the domain. There is no free tier, only a trial, and simple sites pay the same rate as hard ones. FAUN.dev's index scores Bright Data Web Unlocker 75/100.

**Oxylabs** averaged a 92.52% success rate across the tested domains, with broad proxy coverage. Bandwidth-based billing makes cost hard to predict, and entry pricing starts at $75/month, which is steep for small teams.

**ZenRows** starts at $69/month and held up more consistently than ScraperAPI on protected sites in the same benchmark, without forced parameter combinations. Some domains still force render and premium parameters, which raises the per-request credit cost. FAUN.dev scores it 69/100.

### Budget scraping with dedicated endpoints

**ScrapingDog** is the cheapest reliable option if, and only if, your targets fall inside its endpoint catalog: Amazon, Google, Indeed, Zillow, X, LinkedIn, YouTube, Walmart, eBay and similar. Calling `/amazon/product` returns parsed JSON, so selector maintenance disappears. Average response time sits under five seconds, and its benchmark average came in at $0.47 per 1,000. Outside the catalog, the pricing logic stops being predictable. FAUN.dev: 68/100.

**Crawlbase** bills only successful requests, starting at **$3 per 1,000** and dropping from there. FAUN.dev scores it 72/100.

### AI and LLM pipelines

**Firecrawl** is the shortest path from URL to something an LLM can read. Hobby costs **$19/month** billed monthly, or **$16/month** billed annually, for 5,000 credits at 1 credit per page. Output is LLM-ready markdown or JSON by default, `/crawl` walks a whole site in one call with sitemap discovery and depth controls, and there is an agent endpoint for natural-language task descriptions. Official SDKs cover Python, Node.js and Java, with LangChain, LlamaIndex and an MCP server alongside. The core is AGPL-3.0 and self-hostable, with SOC 2 Type II, GDPR and a DPA available. FAUN.dev puts it at 77/100, second overall; G2 reviewers rate it around 4.7. Its published benchmark (run 13 January 2026 across 1,000 URLs) claims 96% coverage with a P95 latency of 3,387 ms, but the harness is not public, so treat those figures as vendor-reported.

**ScrapFly** starts at **$30/month**, 57% below ZenRows, and ships native LangChain and LlamaIndex support plus Zapier, Make and N8N connectors and a dedicated screenshot API. The catch is onboarding friction: SMS verification is mandatory before you can test, and multiple user reports describe accounts banned without explanation. Its performance could not be validated in the benchmark because access was blocked. FAUN.dev: 73/100.

**ScrapeGraphAI** (72/100) is an open-source Python library plus hosted API that extracts fields you describe in plain English. **Jina Reader** (65/100) needs no API key at all: prefix a URL with `r.jina.ai` and get markdown back.

### If you would rather not write code

**Octoparse AI** is a different species: a visual RPA builder with drag-and-drop workflows, an AI Copilot, built-in CAPTCHA solving and OCR, and an App Store of templates for Amazon, Google Maps and TikTok. Basic workflows are free and unlimited, with $5 in AI credits for new users. It runs **only on Windows**, executes locally rather than in the cloud, and does not appear in API benchmarks, so high-concurrency work is not its territory.

Names worth knowing if none of the above fit: **Yozh Scraper** (82/100, open-source Playwright-based scraping and crawling), **Zyte API** (75/100, priced per site by scraping difficulty), **Spider** (71/100, MIT-licensed Rust crawler), **Olostep** (69/100), **ScrapeOps** (68/100) and **Diffbot** (61/100). On G2, **Apify** sits around 4.7 from 702+ reviews.

## Side by side

| Tool | Entry point | How billing works | Where it wins | Where it costs you |
| --- | --- | --- | --- | --- |
| ScraperAPI | $49/mo, 100k credits | Published credits per request type, 10x–75x for premium tiers | Rate card you can price in advance; parsed SERP and e-commerce endpoints | Hobby per-credit rate is 5x the Business rate |
| Scrape.do | $29/mo, 250k credits | Opt-in multipliers: JS 5x, residential 10x | Cheap base rate, 1,000 free requests monthly, no forced parameters | No crawl endpoint, no official SDK, closed source, raw HTML default |
| Firecrawl | $19/mo, 5,000 credits | 1 credit per page | LLM-ready output, `/crawl`, official SDKs, self-hostable | Credit count is small; coverage claims are vendor-run |
| Bright Data | Pay-as-you-go | Flat per 1,000, quoted at $1.50 and $2.50 | Highest success rate in published benchmarks | No free tier; slow average response; simple sites pay full rate |
| ZenRows | $69/mo | Fixed cost per request type | Stable on protected domains | Some domains force render and premium params |
| ScrapingDog | $40/mo | Per-endpoint pricing | Sub-5s responses, parsed JSON from dedicated endpoints | Cheap only inside its catalog |

Scoring the two third-party systems separately: FAUN.dev's index (editorial plus community scores, ISO/IEC 25010 criteria, no publication date given) puts Scrape.do at 72/100, Firecrawl 77, Bright Data Web Unlocker 75, ScraperAPI 69, ZenRows 69 and ScrapingDog 68. On G2, Scrape.do holds 5.0/5. The two systems measure different things, and neither is a substitute for testing your own target list.

One benchmark keeps circulating that you should read with its publisher in mind. Scrape.do's comparison, first published in October 2025 and updated since, covers seven domains (Amazon, Indeed, GitHub, Zillow, Capterra, Google, X) and reports ScraperAPI at 72.57% success, 5.6s average response and an average $4.25 per 1,000. Scrape.do publishes that benchmark, sells a competing API, and does not publish its harness. It is the only success-rate data available for ScraperAPI in this comparison, which is a reason to test rather than a reason to trust.

## Three bills to run before you migrate

**1. Code changes.** Scrape.do gives you HTTP samples and no official SDK, so most teams have hand-rolled a client. Moving to a provider with an official SDK and framework integrations deletes that code; moving to a provider that exposes a proxy port changes almost nothing, because your existing HTTP client keeps working and only the endpoint and credentials change. Estimate the work as "rewrite the client" versus "swap two environment variables" before you compare prices.

**2. Failed requests.** On multiplier pricing, a retry is a second charge. Crawlbase and ScrapingBee bill successful requests only, and Scrape.do charges only for successful calls. Check the failure policy of any target provider before assuming a quoted per-1,000 rate applies to delivered pages.

**3. What happens when the free allowance runs out.** Free tiers are not comparable units. Scrape.do's 1,000 free requests are requests. ScraperAPI's 1,000 free credits are credits, and a JavaScript-rendered page costs 10 of them, so the free plan covers about 100 rendered pages. The 7-day trial's 5,000 credits is the realistic testing window.

👉 [Start the 7-day trial with 5,000 credits, no card required](https://www.scraperapi.com/?fp_ref=coupons)

## Which one to pick

- **Targets inside ScrapingDog's endpoint catalog, and price decides everything** → ScrapingDog. Outside that catalog, look elsewhere.
- **More than half your volume hits serious bot protection** → Bright Data, budgeted at $1.50 to $2.50 per 1,000, and accept the slower responses.
- **You are building retrieval or extraction for an LLM** → Firecrawl Hobby at $19/month, or $16/month annually. The 5,000 credits go fast at 1 credit per page, so size the tier against page count, not request count.
- **Mixed general web plus SERPs or marketplaces, with a budget you have to defend** → ScraperAPI. Start on Hobby to test, then move to Startup as soon as credits run short: the per-credit rate drops from $0.49 to $0.15, which cuts a JS-rendered page from $4.90 to $1.50 per 1,000.
- **Mostly static HTML at low volume, already on Scrape.do** → stay. You are paying $0.116 per 1,000 base and nothing in this list beats that for simple pages.

Two things to confirm before you enter a card number. Check whether your target domains need country-level geotargeting, because Hobby and Startup are US and EU only. And remember that credits reset at renewal rather than accumulating, so an annual plan's full-year allocation is best spent deliberately. The 7-day refund policy gives you room to find out whether the success rate on your own URLs matches anyone's benchmark.

👉 [Compare ScraperAPI's tiers and pick the credit level you actually need](https://www.scraperapi.com/pricing?fp_ref=coupons)
