---
title: Action–Position Data Quality Assessment Framework
layout: data-dispatch-post
---

First up is an article about data quality (ever the bane of my existence):
[https://medium.com/israeli-tech-radar/action-position-data-quality-assessment-framework-d833f6b77b7](https://medium.com/israeli-tech-radar/action-position-data-quality-assessment-framework-d833f6b77b7).

Written by Yerachmiel Feltzman, the article proposes a framework for describing approaches to data quality in terms of
their action (what happens in the event of a DQ problem) and their position (where the DQ checks actually take place).
An example is the write-audit-publish pattern used by Netflix. Here the DQ checks occur after writing data (e.g., to a
staging location) but before publishing it to end users, and the action in the event of a data problem is to fail the
pipeline.

I do a lot of ETL/ELT-type work in Databricks, and I naturally started implementing similar patterns in my work before
I knew about this framework. But having them crystalised in this way is very useful, and has helped me formalise and
clarify the approach I take to data quality handling.
