---
title: Extremes, Outliers, and Goats
layout: data-dispatch-post
---

I just watched another fantastic talk by Allen Downey (of the Probably Overthinking It blog and book) from PyData 2023,
titled "Extremes, outliers, and Goats."

Allen discusses the limits of Gaussian models, especially with regards to certain outliers (examples being runners,
chess players, etc., where the "GOATs" are _much_ better than average, much more so than would be predicted by a
Gaussian model).

Allen proposes log-normal models as a better alternative to Gaussian models, and presents possible explanations for why
this might be the case.

My key takeaway: whenever both Gaussian and log-normal models describe a process reasonably well, the latter is
invariably the better model. And hence maybe we should replace Gaussian as the "default" model with log-normal models.

Find the talk here on YouTube:
[https://www.youtube.com/watch?si=SNvkOHlDqhGaiIDX&v=MhA5XWIWWys&feature=youtu.be](https://www.youtube.com/watch?si=SNvkOHlDqhGaiIDX&v=MhA5XWIWWys&feature=youtu.be).
