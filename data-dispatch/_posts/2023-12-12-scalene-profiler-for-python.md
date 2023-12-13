---
title: Scalene Profiler for Python
layout: data-dispatch-post
---

Scalene profiler for Python: [https://github.com/plasma-umass/scalene](https://github.com/plasma-umass/scalene).

I'm working on a web scraper using Beautiful Soup, scraping tabular data to CSV. I wanted to speed it up, as there are
a lot of pages I want to scrape, and any gain in performance will help.

The Scalene profiler came to mind, because I used it to great effect earlier this year optimising a Python trading
backtester. I like Scalene because it's fast (minimal overhead) and very accurate. It samples at line level, which
gives a detailed picture of the code hotspots.

Scalene does CPU, GPU, and memory profiling (the latter not on Windows), though I've only had occasion to use it for
CPU.

Highly recommended if you need to optimise the performance of your Python code! 🐍🚀
