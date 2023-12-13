---
title: Limeline RSS Feed in Script Kit
layout: blog-post
excerpt_separator: <!--more-->
---

A few months back I was looking for an RSS syndicator and found [Limeline](https://limelina.app). If you haven't
come across it, it's well worth a look. I like it because:

- It's simple—I don't need to think too much about how to use it. (If I'm gonna expend brain cycles on tooling, I'd
  prefer to keep them for tools I spend more time in, like my code editor.)
- It looks good—not much to add here: if it looks good, I'm more likely to use it.
- It doesn't require you to sign up—in fact, it has a nifty means to have accounts without having sign ups: it uses
  [UUID's](https://en.wikipedia.org/wiki/Universally_unique_identifier) to identify an account. If you possess the
  account's UUID, you can view or update the RSS feeds (at `https://limeline.app/<uuid>`); if you lose it, tough luck—
  you'll have to create a new account. Every account also has a read-only UUID if you want to share your feed with
  others, that doesn't allow them to update it; e.g., see
  [here](https://limeline.app/share/252d5411-9a0b-48df-ba54-280ce272623a) for mine.

<!--more-->

More recently, I installed [Script Kit](https://www.scriptkit.com/). In a nutshell, it provides a convenient means to
run scripts (written in Javascript) on your computer. You can access Script Kit via a shortcut (I've left at as the
default, `Ctrl + ;`), which opens the Script Kit window:

![Script Kit]({{ site.imgurl }}/script-kit-screenshot.png)

From here you select a script to run; alternatively, you can set shortcuts for scripts.

One of the examples given in Script Kit's tutorial is displaying posts and comments from Hacker News. I immediately
thought of my Limeline feed and whether I could add it to my Script Kit. In the remainder of this post I'll describe
how I went about doing so.

## Creating a new Script Kit script

Script Kit scripts are stored in `.kenv/scripts` folder in the home directory. I added a new one called `limeline.js`,
containing the following skeleton:

```javascript
// Name: Limeline
// Description: Fetches Limeline feed

import "@johnlindquist/kit";

let url = ... // we'll need to populate this with the URL selected
              // from the Limeline feed

// Open a browser at the selected URL
await exec(`start ${url}`);
```

## Reverse engineering the Limeline API

Okay, admittedly calling this "reverse engineering" is a bit of a stretch; but the point is, I needed to get hold of
the data that Limeline uses to populate the feed I see. To find that, I used my browser's Dev Tools, specifically the
Network tab:

![Network Tools]({{ site.imgurl }}/limeline-network-tools.png)

Scrolling through the resources loaded by the website, it's not too hard to find the list of feeds loaded by Limeline:

```
https://api.limeline.app/shares/252d5411-9a0b-48df-ba54-280ce272623a
```

(Here `252d5411-9a0b-48df-ba54-280ce272623a` is the UUID identifying my read-only feed. You can also find the
equivalent endpoint when on the editable feed; it's `https://api.limeline.app/newsfeed/<uuid>`.)

Scrolling further, there's another API endpoint that, upon inspection, yields the feed for each subscription:

```
https://api.limeline.app/sources/<uuid>
```

(The UUID given to this endpoint identifies the particular feed, i.e., it will differ for each RSS feed subscribed to.)

The `/shares` endpoint returns a JSON payload that looks like the following

```json
{
  "id": "252d5411-9a0b-48df-ba54-280ce272623a",
  "shareId": null,
  "layout": "by-source",
  "name": "Dennis Barrett",
  "readOnly": true,
  "sourceDefinitions": [
    {
      "id": "64299ad9-2dcb-4cea-9b36-63a5a1f6f0f5",
      "github": null,
      "maxItems": 5,
      "rss": {
        "url": "https://counting.substack.com/feed"
      },
      "type": "rss",
      "twitter": null
    },
    {
      "id": "4292b078-918e-400a-9847-921267881673",
      "github": null,
      "maxItems": 5,
      "rss": {
        "url": "https://seattledataguy.substack.com/feed"
      },
      "type": "rss",
      "twitter": null
    },
    ...
  ]
}
```

The `sourceDefinitions` array is what we're after—each entry is one of the subscribed feeds. We'll need to grab the
UUIDs in the `id` fields and pass them to the `/sources` endpoint. The latter returns a payload like so:

```json
{
  "icon": "...",
  "items": [
    {
      "comments": "",
      "link": "https://counting.substack.com/p/insights-do-not-equal-utility",
      "publishedAt": "2023-09-12T12:05:46Z",
      "title": "Insights do not equal utility"
    },
    {
      "comments": "",
      "link": "https://counting.substack.com/p/data-design-has-nothing-on-process",
      "publishedAt": "2023-09-07T12:05:04Z",
      "title": "Data design has nothing on process design"
    },
    ...
  ],
  "link": "https://counting.substack.com",
  "title": "Counting Stuff"
}
```

## Finishing our script

Based on what we've seen of the Limeline API, what we'll need to do is:

- For each source returned from `/shares`:
  - Grab the `link` and `title` of the source from `/sources` (so we can show the source title, and provide a link
    directly to the source website).
  - Grab each of the items in the `items` array; specifically, the `link`, `publishedAt`, and `title` fields (so we can
    show the item title, and link to it directly).
- Show a list of the items collected above, sorted by recency, and that link to the article webpage.

The first point can be implemented by updating our script as follows:

```javascript
// Name: Limeline
// Description: Fetches Limeline feed

import "@johnlindquist/kit";

let feedUuid = "252d5411-9a0b-48df-ba54-280ce272623a";

let feeds = await get(`https://api.limeline.app/shares/${feedUuid}`);
let itemsNested = await Promise.all(
  feeds.data.sourceDefinitions.map(async ({ id }) => {
    let source = await get(`https://api.limeline.app/sources/${id}`);

    return source.data.items.map(({ link, publishedAt, title }) => {
      return {
        source: source.data.title,
        sourceLink: source.data.link,
        title: title,
        link: link,
        publishDate: new Date(publishedAt),
      };
    });
  })
);

// Flatten and sort the items in descending order of recency
let items = itemsNested.flat().sort((a, b) => b.publishDate - a.publishDate);

let url = ... // we'll need to populate this with the URL selected
              // from the Limeline feed

// Open a browser at the selected URL
await exec(`start ${url}`);
```

The Script Kit API provides the `arg` function, which allows us to get user input. It can be used in a few different
ways; for instance, direct text input or a selection from a list. We'll use it to display a list of items (the feed
articles), along with some descriptive text (e.g., the originating website). And upon selection, the `arg` function
will return the article's URL, at which point we can open a browser at that URL using `await exec(`start ${url}`);`.

So without further ado (I've commented the code rather than explaining it bit-by-bit):

```javascript
let url = await arg("Select item:", async () => {
  return items.map((it) => {
    // If the source (article) title is too long, then truncate
    // it and show an ellipsis
    let src =
      it.source.length > 47 ? it.source.substring(0, 47) + "..." : it.source;

    // Format the article publish date
    let dat = it.publishDate.toLocaleString("en-UK");

    // Return an object, the format of which is Script Kit-specific
    return {
      name: it.title,
      description: `${src} | ${dat}`,
      value: it.link,

      // This provides a separate preview of the article that opens
      // alongside the link when hovering over an item in the list.
      // We show the source website, the article title, and the
      // publish date.
      preview: async () => {
        return md(`# [${it.source}](${it.sourceLink})

## [${it.title}](${it.link})
## Published: ${dat}`);
      },
    };
  });
});
```

Put all together, this is what the script does:

![Script Kit]({{ site.imgurl }}/script-kit-limeline-script.png)

## Final thoughts

I really like how easy it is to use Script Kit (and especially that it uses Javascript, since I've been using a lot of
Javascript recently), and it's definitely something I could see becoming part of my muscle memory. Having said that: it
is an Electron app, with the concomitant performance issues. You'd want an app like this to open instantaneously, which
doesn't always happen on my machine. But I'm intrigued enough about the possibilities to use it to keep it around for
the foreseeable future.
