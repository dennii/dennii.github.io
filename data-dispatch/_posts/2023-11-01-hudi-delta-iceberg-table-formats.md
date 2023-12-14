---
title: Hudi vs Delta Lake vs Iceberg
layout: data-dispatch-post
---

Hudi vs Delta Lake vs Iceberg: [https://www.onehouse.ai/blog/apache-hudi-vs-delta-lake-vs-apache-iceberg-lakehouse-feature-comparison](https://www.onehouse.ai/blog/apache-hudi-vs-delta-lake-vs-apache-iceberg-lakehouse-feature-comparison).

I have a DE side-project using web-scraped horse racing data, which I want to land in a (medallion-architecture) data
lakehouse. I've used similar approaches in my day job, but only using Databricks and Delta Lake. So I thought this
would be a good opportunity to learn about some competing data lake formats.

This article gives a decent high-level overview of the three main data lake formats (Apache Hudi, Delta Lake, and
Apache Iceberg). It has a feature comparison (incl. feature highlights of the different formats), some benchmark
results, and a general discussion of which format you might want to use.

One caveat: OneHouse uses Hudi, so the article does push Hudi as the best choice. I still think the article is useful,
but I'll be looking for other comparisons before making a choice. If I find anything interesting, I'll post it as a
follow-up.
