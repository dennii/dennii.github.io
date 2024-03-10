---
title: The State of Metastores
layout: data-dispatch-post
---

The folks at LakeFS recently posted about the current state of data lake metastores. It started with the Hive
Metastore, but of concern is the openness (or lack thereof) of modern replacements.

They discuss a few modern options (Glue Data Catalog, Unity Catalog, and Snowflake Catalog), and the degree of vendor
lock-in implied by the use of these catalogs.

Fortunately we seem to have all agreed on the benefits of Open Table Formats (like Iceberg, Hudi, and Delta). Perhaps
it's time for more effort towards an Open Catalog Standard so we can truly avoid vendor lock in?

Find the article here:
[https://lakefs.io/blog/hive-metastore-vendor-lock/](https://lakefs.io/blog/hive-metastore-vendor-lock/).
