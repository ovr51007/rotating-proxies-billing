# Rotating Proxies: How Per-Request and Sticky Rotation Work, and How to Pick a Plan Without Overpaying

Search for "rotating proxies" and you'll get three completely different things under the same name: a gateway that hands you a new IP on every request, a session that holds one IP for a few minutes, and a local port on your own machine that swaps IPs on a timer. They solve related problems, but the setup, the failure modes, and the price you pay are not the same. That confusion is why people buy a plan that doesn't match their workload, then blame the provider.

Here's the practical version: how rotation actually works, when rotation makes things worse, and how 9Proxy's two residential proxy models — pay-per-GB and pay-per-IP — map onto the two ways people actually use rotating IPs.

## "Rotating proxies" is really three different setups

**Provider-side per-request rotation.** You point your scraper at a single host:port. The provider's gateway assigns a different exit IP to every request. Your code never manages a pool. If a site rate-limits per IP, no single IP ever sends enough traffic to trip the limit. This is what most hosted providers mean by "rotating proxies," and it's the model 9Proxy uses on its bandwidth plans.

**Sticky sessions.** Same endpoint, but the IP is pinned for a window you choose — 30 seconds, 10 minutes, whatever the session needs. Logins, carts, multi-step forms, and pagination flows break if the IP changes mid-step. 9Proxy — like Bright Data, Decodo and most others — handles this with a session parameter in the credentials rather than a separate product.

**Local rotation on your own machine.** You forward traffic through ports managed by a desktop app, and the app rotates the upstream IP on a schedule. It's the older workflow that people coming from 911-style SOCKS5 services recognise, and it's still how 9Proxy's IP-based plans work: no natural rotation, so you enable Auto Rotation Proxy and set custom intervals on selected ports.

There's a fourth thing people lump in — managing a list of proxies yourself with round-robin logic or something like `scrapy-rotating-proxies`. That's fine, but it means you own dead-proxy detection and retry logic. Paying a provider for a rotating endpoint exists precisely so you don't have to.

## Why rotation keeps a job alive

The arithmetic is simple enough to do on a napkin. If a target tolerates 100 requests per hour from one IP and your job needs 100,000 requests per hour, you need at least a thousand addresses in play. Twenty million addresses, the pool size 9Proxy advertises, means you'll never run out of rotation room — but pool size alone doesn't guarantee success.

> A fresh IP doesn't reset a site's opinion of you. Fingerprints, TLS signatures, header order and request timing survive rotation, and a scraper that changes IPs while behaving like a machine still gets blocked.

That's why rotation is treated as one layer rather than the whole defence. In practice, the useful question isn't "how big is the pool" but "how clean is it." 9Proxy's selling point is a pool with a low ban history; independent testing by Geekflare across 300 requests reported 293 successful responses and five CAPTCHA challenges, all originating from a single IP range, which cleared once the job rotated away from it. That's the behaviour you want from a residential pool — occasional dirty exits, not a wall of blocks.

## When you should *not* rotate

Aggressive rotation is a good way to get yourself flagged. A few rules of thumb:

- **Logged-in dashboards and ad accounts** want a stable IP. Changing it mid-session looks like account theft.
- **Checkout, carts and forms** need sticky sessions. Per-request rotation will break them every time.
- **Allowlisted integrations** need a static or whitelisted IP, full stop.
- **Pages behind JavaScript** need a real browser, and rotation doesn't change that. If your HTTP client gets an empty shell, the problem is Selenium or Playwright, not the proxy.

Per-request rotation suits stateless reads: public listings, SERPs, price checks, geo-verification. Task-level rotation — one IP or one small pool per keyword, category or city — is often the better compromise, because it keeps a coherent browsing story while still spreading volume.

## The billing model decides your bill more than the unit price

This is the part most "what are rotating proxies" articles skip, and it's where the money is.

9Proxy sells two residential products. The bandwidth model charges per gigabyte and lets you generate unlimited endpoints, with traffic valid for 180 days (unlimited validity on Enterprise). Rotating and sticky sessions are both configurable, and targeting runs from country down to state, city, ZIP and ISP. Authentication is username/password or IP whitelist, and everything happens in the dashboard — no app.

The IP model charges per address, not per byte. Each IP gives you unlimited bandwidth while it's active, unused IPs don't expire, and an IP typically stays live anywhere from a few hours to about 24 hours. The trade-off is operational: it needs the desktop app, one IP counts as one usage when forwarded, and rotation has to be enabled through the Auto Rotation Proxy feature.

|  | Residential by IPs | Residential by GB |
| --- | --- | --- |
| You pay for | Number of IPs | Total traffic |
| Bandwidth | Unlimited while the IP is active | Capped by purchased GB |
| Validity | Until the IPs are used | 180 days (unlimited on Enterprise) |
| IP behaviour | No natural rotation; Auto Rotation Proxy on selected ports | Rotating per request, or sticky until the session timer ends |
| Targeting | Country and city | Country, state, city, ZIP, ISP |
| Authentication | Desktop app (local port forwarding, optional proxy auth) | Username/password or IP whitelist |

If your job burns a lot of data through relatively few IPs, per-IP wins — you stop watching a GB counter. If your job touches thousands of targets with small responses, per-GB wins, because the IP count is what scales there, not the bandwidth.

## What the plans actually cost

**Bandwidth (GB) plans** — all 180-day validity unless noted:

| Plan | Rate per GB | Total | Validity | Get it |
| --- | --- | --- | --- | --- |
| 5 GB | $3.00 | $15 | 180 days | [ 购买链接](#) |

Let me lay these out properly with the buy links pointing through the referral signup.

**GB-based residential plans**

| Plan | Rate per GB | Price | Validity | Buy |
| --- | --- | --- | --- | --- |
| 5 GB | $3.00 | $15 | 180 days | [ Get the 5 GB plan](https://bit.ly/9-Proxy) |
| 50 GB + 5 GB bonus | $2.10 | $105 | 180 days | [ Get the 55 GB plan](https://bit.ly/9-Proxy) |
| 100 GB | $1.50 | $150 | 180 days | [ Get the 100 GB plan](https://bit.ly/9-Proxy) |
| 200 GB | $1.00 | $200 | 180 days | [ Get the 200 GB plan](https://bit.ly/9-Proxy) |
| 1,000 GB | $0.80 | $800 | 180 days | [ Get the 1,000 GB plan](https://bit.ly/9-Proxy) |
| 2,000 GB | $0.75 | $1,500 | 180 days | [ Get the 2,000 GB plan](https://bit.ly/9-Proxy) |
| 6,000 GB | $0.70 | $4,200 | Unlimited (Enterprise) | [ Get the 6,000 GB plan](https://bit.ly/9-Proxy) |
| 10,000 GB | $0.68 | $6,800 | Unlimited (Enterprise) | [ Get the 10,000 GB plan](https://bit.ly/9-Proxy) |

**IP-based residential plans** (unlimited bandwidth per IP):

| Plan | Rate per IP | Price | Buy |
| --- | --- | --- | --- |
| 100 IPs | $0.20 | $20 | [ Get 100 IPs](https://bit.ly/9-Proxy) |
| 500 IPs | $0.12 | $60 | [ Get 500 IPs](https://bit.ly/9-Proxy) |
| 1,000 IPs + 500 bonus | $0.07 | $105 | [ Get 1,500 IPs](https://bit.ly/9-Proxy) |
| 2,500 IPs | $0.07 | $175 | [ Get 2,500 IPs](https://bit.ly/9-Proxy) |
| 5,000 IPs | $0.06 | $300 | [ Get 5,000 IPs](https://bit.ly/9-Proxy) |
| 15,000 IPs | $0.04 | $600 | [ Get 15,000 IPs](https://bit.ly/9-Proxy) |
| 25,000 IPs | $0.03 | $750 | [ Get 25,000 IPs](https://bit.ly/9-Proxy) |
| 50,000 IPs | $0.025 | $1,250 | [ Get 50,000 IPs](https://bit.ly/9-Proxy) |

**Bundles** (IPs plus GB in one package, traffic valid 180 days):

| Bundle | Package | Price | Buy |
| --- | --- | --- | --- |
| Starter | 100 IPs + 5 GB | $25 | [ Get the Starter bundle](https://bit.ly/9-Proxy) |
| Growth | 1,500 IPs + 50 GB | $150 | [ Get the Growth bundle](https://bit.ly/9-Proxy) |
| Pro | 5,000 IPs + 500 GB | $600 | [ Get the Pro bundle](https://bit.ly/9-Proxy) |

Two caveats worth taking seriously before you check out. First, 9Proxy adjusts tier pricing from time to time, and at least one mid-2026 review lists entry tiers roughly 20% higher than the ladder above — $24 for 100 IPs and $72 for 500, with bundles at $30/$180/$720. The per-unit rates and the shape of the curve stay the same; the entry numbers move. Confirm what your cart says. Second, Enterprise sits outside this table entirely: unlimited data validity, one owner plus up to five team members, no-expiration bandwidth sharing inside the team, per-member traffic controls, full activity logs, and custom VIP pricing.

If you want to see the live numbers rather than my copies of them, [👉 check the current 9Proxy plans and pricing](https://bit.ly/9-Proxy) directly. Signing up through a referral link also gets referred users 5% off, and any account-level coupons live under Dashboard → My Account → My Coupons — 9Proxy runs recurring promotions (an 8% regular-plan code over Lunar New Year, an automatic 9% back coupon on GB orders in April), and both of those windows have closed, so it's worth checking what's active when you buy.

## Setting rotation up is not the same on both models

On a GB plan, rotation is a dropdown. You open the dashboard's Proxy Generator, pick authentication (username/password or IP whitelist), choose your target — country, state, city, ZIP or ISP — then select rotating or sticky mode. Rotating gives you a new exit on each request; sticky holds the IP until your configured session time runs out. You can generate unlimited endpoints, export them as .txt or .csv, and copy pre-built code samples in several languages. Nothing needs to be installed, which is why this is the model that works on a VPS or in a cloud container.

On an IP-based plan, you're closer to the hardware. The desktop app handles local port forwarding, and Auto Rotation Proxy swaps IPs on the ports you nominate at intervals you set. Two behaviours to plan around: an IP stays live for hours up to about a day depending on the address, and one reported weakness is that residential IPs drop naturally. The Today List helps — IPs you accessed in the last 24 hours can be reused without extra charge, which is a real saving on repetitive jobs. And if an address fails within the first 60 seconds, 9Proxy credits it back rather than counting it as consumed, which is more generous than the industry norm.

Both models speak HTTP, HTTPS and SOCKS5. SOCKS5 matters if you're pushing non-HTTP traffic or holding long-lived connections through scraper frameworks and anti-detect browsers.

## Where rotating residential IPs earn their keep

The obvious one is collection at scale: product catalogs, prices, listings, reviews. Residential exit nodes from the target city also return content a datacenter IP never sees, which is the difference between "blocked" and "wrong" when you're checking local pricing.

Beyond scraping, the pattern repeats across a few jobs:

- **Rank tracking and SEO audits.** Checking the same keyword from dozens of cities needs city-level targeting, not a country toggle.
- **Ad verification.** You want to see the placement a real user in Omaha sees, and confirm the campaign served at all.
- **Multi-account work in anti-detect browsers.** AdsPower, Dolphin Anty and BitBrowser all take host:port:user:pass, and each profile wants a clean, geographically consistent IP.
- **Security and QA testing.** Checking how your own WAF reacts to traffic from different IP reputation tiers requires access to clean residential addresses as a baseline.

Coverage is deepest where the residential supply actually is. 9Proxy advertises 90+ countries and 20M+ IPs; published pool counts include around 572,600 in the United States, 530,800 in Canada, 490,090 in France, 446,080 in the UK and 385,590 in Germany, with thinner depth in parts of Asia. That's plenty for most US and European work, and worth checking before you commit if your targets are niche.

## What to know before you pay

Residential IPs expire by nature — hours to a day on IP-based plans — so plan for rotation or replacement rather than treating an address as permanent. There's no self-serve free trial on the site; 9Proxy's team runs testing rounds through community channels instead, which is an extra step compared with providers that hand you a trial on signup.

The Trustpilot profile sits at 4.6/5, and the recurring complaint pattern there isn't about proxy quality — it's about refund policy friction from users who bought the wrong plan shape. Try to test access before committing budget, and if you're buying for a production pipeline, keep a second provider in the mix rather than betting the whole workflow on one account. That advice is worth more than it sounds: several sites that sell proxy catalogues have reported 9Proxy outages during 2026, including a multi-week gap mid-year. Those sources also sell competing networks, so read them with the appropriate scepticism — but the underlying lesson holds either way.

## Quick answers

**Rotating or sticky — which do I pick?** Rotating for stateless reads, sticky for anything with a session. You can switch between them per endpoint on GB plans without changing plans.

**Per GB or per IP for scraping?** If responses are small and numerous, per-GB. If you're moving heavy data through a modest number of IPs, per-IP — bandwidth is unmetered there.

**How much do I need to start?** The $15 / 5 GB plan is enough to validate success rates on your real targets, and the $20 / 100 IP ladder does the same for port-based workflows. Scale after you know your block rate.

**Will it work with my anti-detect browser?** Native SOCKS5 and HTTP endpoints with username/password or whitelisted IPs cover AdsPower, Dolphin Anty, BitBrowser, Proxychains and custom Python scripts without a protocol gateway.

**What's the cheapest honest way in?** [👉 Start with a 9Proxy account and test a small plan](https://bit.ly/9-Proxy), then size up once you know your actual gigabytes or IP count per day.
