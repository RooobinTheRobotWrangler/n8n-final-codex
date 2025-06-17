# RSS Feed Node

**Source:** [n8n Docs - RSS Feed node documentation](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.rssfeed/)

## Overview
The RSS Feed node is a **trigger node** that monitors an RSS (Really Simple Syndication) or Atom feed for new entries. When a new item is detected in the feed since the last check, it triggers the workflow, allowing you to automate actions based on new content publications, such as news articles, blog posts, or podcast episodes.

## Configurable Options:

* **URL:**
    * **Description:** The full URL of the RSS or Atom feed you want to monitor.
    * **Accepted values:** A valid URL string (e.g., `https://example.com/feed.xml`).
* **Update Interval:**
    * **Description:** How often (in minutes) the node should check the RSS feed for new items. A shorter interval means quicker detection but more frequent checks.
    * **Accepted values:** A positive integer representing minutes (e.g., `5` for every 5 minutes).
* **Remove Duplicate Items:**
    * **Description:** If enabled, the node will prevent duplicate items from being outputted, even if they appear multiple times in the feed. This is generally recommended.
    * **Accepted values:** Boolean (checkbox).

## Input Structure:
As a trigger node, the RSS Feed node does **not** have an input. It initiates the workflow based on detecting new items in the monitored feed.

## Output Structure:
When the RSS Feed node detects new items, it outputs one item for each new entry found in the feed. The output typically contains parsed data from the RSS feed, including common fields like:

* **`json.title`:** The title of the feed item.
* **`json.link`:** The URL of the feed item.
* **`json.content`:** The main content or description of the item.
* **`json.pubDate`:** The publication date of the item.
* **`json.guid`:** A unique identifier for the item.
* **`json.creator`:** The author of the item (if available).

**Example JSON Output (for a single new feed item):**

```json
[
  {
    "json": {
      "title": "New Blog Post: Understanding n8n",
      "link": "[https://yourblog.com/understanding-n8n](https://yourblog.com/understanding-n8n)",
      "content": "This post dives deep into the fundamentals of n8n workflows...",
      "pubDate": "2025-06-10T15:30:00.000Z",
      "guid": "[https://yourblog.com/understanding-n8n-12345](https://yourblog.com/understanding-n8n-12345)",
      "creator": "Jane Doe"
    },
    "binary": {}
  }
]
Potential Use Cases:
Content Aggregation: Collecting new articles from various news sites or blogs into a single database or document.
News Alerts: Sending notifications (e.g., via Slack or email) whenever a new article related to a specific topic is published.
Podcast Automation: Triggering actions when new podcast episodes are released.
Monitoring Competitors: Tracking new publications from competitor websites or industry news feeds.
Job Board Monitoring: Receiving alerts for new job postings on specific RSS-enabled job boards.
Tips & Best Practices:
Verify RSS URL: Ensure the URL you provide is a valid RSS or Atom feed. You can often find this icon on websites or use a browser extension to detect it.
Set Appropriate Update Interval: Choose an interval that balances promptness with resource usage. Checking every minute might be unnecessary for a daily blog, while an hourly check might be too slow for breaking news.
"Remove Duplicate Items" is Key: This prevents your workflow from re-processing the same content if the feed updates without new unique items.
Combine with Other Nodes: Often, you'll use nodes like HTTP Request (to fetch the full article content), HTML Extract (to parse content), or various database/app nodes to store or process the information from the RSS feed.
<!-- end list -->