# How to Scrape We Work Remotely: Remote Job Postings via RSS Feed API

> **Run it now, no code: [weworkremotely-jobs-scraper on the Apify Store](https://apify.com/bibian/weworkremotely-jobs-scraper)** &nbsp;|&nbsp; $5.00 per 1,000 job postings &nbsp;|&nbsp; no browser, no proxy

We Work Remotely is one of the largest worldwide remote-job boards, covering programming, design, sales, support, product and more. Its homepage sits behind a Cloudflare challenge, but its own public RSS feed does not. This guide shows how to get its postings as clean structured data: title, company, headquarters, region, category and the full description, with a free Apify Actor, from the console or from code.

Searches this page answers: weworkremotely scraper, remote job postings API, scrape remote jobs RSS feed.

## What you get from the We Work Remotely scraper

- Every current remote posting worldwide, newest first, with title, company, headquarters, region and category.
- Clean header fields (company, headquarters, company website) pulled out of the posting instead of staying mixed into the description.
- The full description text, not a truncated preview.
- A stable `id` and canonical `url` per posting for deduplication across runs.

Export the dataset as JSON, CSV or Excel, or read it through the Apify API. A real sample (five results, personal data removed, long texts truncated) is in [`sample-output.json`](sample-output.json).

## Output fields

| Field | What it is |
|---|---|
| `id`, `url` | Posting id and public link |
| `title` | Job title |
| `companyName`, `companyUrl` | Hiring company and its website (`null` when not shown in the header) |
| `headquarters` | Company headquarters as displayed |
| `region` | Region the posting is open to, for example "Anywhere in the World" |
| `category` | We Work Remotely's own job category (programming, design, sales, support, product, and more) |
| `datePosted` | Publication date |
| `description` | Full plain text of the posting |

## Use cases

- **Monitor worldwide remote hiring by category.** Track which companies are hiring for programming, design, sales or support this week.
- **Build a daily remote job alert.** Schedule a run every morning and send only the new postings to Slack, e-mail or a Google Sheet.
- **Find which companies hire remote-first.** List recurring employers across runs by `companyName`.
- **Feed a job board or newsletter.** Use the structured fields and canonical `url` to power a niche remote-work board.

## Input

| Field | What it does |
|---|---|
| `keywords` | Job title, company or skill, for example `react`. Empty means every posting. |
| `location` | Region the posting is restricted to, for example `Americas` or `Europe`. Empty means every region. |
| `maxItems` | The run stops once this many postings are saved (default 50). |

Example input:

```json
{
  "keywords": "react",
  "location": "",
  "maxItems": 50
}
```

## Three ways to use it

### 1. Apify console, no code

1. Open [the Actor page](https://apify.com/bibian/weworkremotely-jobs-scraper) and click **Try for free** (a free Apify account is enough).
2. Fill in the input form (keywords, region and the maximum number of results) and click **Start**.
3. When the run finishes, open the **Output** tab, check the table, then **Export** as CSV, Excel or JSON.
4. For a daily alert, open **Schedules**, create a daily schedule for the Actor and, optionally, add an integration that sends new results to Slack, e-mail or Google Sheets.

### 2. Apify API with JavaScript

```bash
npm install apify-client
export APIFY_TOKEN="your token from Apify console, Settings, Integrations"
node examples/run.js
```

```js
import { ApifyClient } from 'apify-client';

const client = new ApifyClient({ token: process.env.APIFY_TOKEN });

const run = await client.actor('bibian/weworkremotely-jobs-scraper').call({
    "keywords": "react",
    "location": "",
    "maxItems": 50
});

const { items } = await client.dataset(run.defaultDatasetId).listItems();
for (const item of items) {
    console.log(`${item.title} | ${item.companyName} | ${item.region} | ${item.category}`);
}
```

The complete file is [`examples/run.js`](examples/run.js) (ES module, Node 18 or later).

### 3. Apify API with Python

```bash
pip install apify-client
export APIFY_TOKEN="your token from Apify console, Settings, Integrations"
python examples/run.py
```

```python
import os
from apify_client import ApifyClient

client = ApifyClient(os.environ["APIFY_TOKEN"])

run = client.actor("bibian/weworkremotely-jobs-scraper").call(run_input={
    "keywords": "react",
    "location": "",
    "maxItems": 50,
})

for item in client.dataset(run.default_dataset_id).iterate_items():
    print(f"{item['title']} | {item.get('companyName')} | {item.get('region')} | {item.get('category')}")
```

The complete file is [`examples/run.py`](examples/run.py). Never put your token in the code: read it from the `APIFY_TOKEN` environment variable as above.

## Pricing

Pay per result: **$5.00 per 1,000 job postings** (that is $0.005 per job posting), plus a tiny start fee of $0.00005 per run. You pay only for the results saved to the dataset. Apify's free plan includes monthly credits, enough to try it on a few hundred results. Prices are those shown on the [Store page](https://apify.com/bibian/weworkremotely-jobs-scraper) when this guide was written; the Store page is the reference.

## Limits and honest notes

- The feed lists the site's current live postings; very old postings are not kept once filled.
- Many postings do not show a direct company URL in their header; `companyUrl` is `null` when that is the case.
- The dataset contains job postings only. Any line carrying an e-mail address or phone number, including a generic company inbox, is dropped from `description`.
- This is an unofficial tool, not affiliated with We Work Remotely. Check the site's terms and the data protection rules that apply to your use.

## FAQ

**Does it need a browser or a proxy?**  
No. The Actor reads We Work Remotely's own public RSS feed directly.

**Does it work without `keywords`?**  
Yes. Leave it empty for a productive default run across every current posting.

**Can I filter by region only?**  
Yes, leave `keywords` empty and set `location` to a region, for example `Europe`.

**Can I get the data in Excel or Google Sheets?**  
Yes. Export the dataset as CSV, Excel or JSON from the Apify console, or connect the Apify Google Sheets integration.

## Try it

**[Open weworkremotely-jobs-scraper on the Apify Store](https://apify.com/bibian/weworkremotely-jobs-scraper)**

The code in `examples/` is released under the MIT license (see [LICENSE](LICENSE)). The data belongs to its original publishers.
