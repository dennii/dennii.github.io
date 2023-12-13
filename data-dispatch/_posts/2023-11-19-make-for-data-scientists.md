---
title: Make for Data Scientists
layout: data-dispatch-post
---

Make for Data Scientists: [https://paulbutler.org/2012/make-for-data-scientists/](https://paulbutler.org/2012/make-for-data-scientists/).

I recently had occasion to use `make` again (I haven't really used it since I used C last), this time to orchestrate
the components of an ML POC.

The POC has four pieces:

1. Preprocessing (ingestion + cleanup of source data).
2. Feature engineering
3. Modelling (training/predicting).
4. Postprocessing.

The dataset we worked with was small as well, around 300k records.

Despite pretty obvious linear dependencies between the stages, using a Makefile helped keep things nicely organised and
efficient. Definitely something I'll be using regularly going forward👌.

(The link is a nice light intro to using `make`. For details, see the GNU Make documentation:
[https://www.gnu.org/software/make/manual/make.html](https://www.gnu.org/software/make/manual/make.html).)
