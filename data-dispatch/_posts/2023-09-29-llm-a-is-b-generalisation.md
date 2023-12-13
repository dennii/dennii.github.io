---
title: LLM "A is B" Generalisation (or Lack Thereof)
layout: data-dispatch-post
---

LLMs like ChatGPT are all the rage at the moment, with a lot of active research and experimentation happening, not to
mention a lot of kool aid being drunk🥤😂. What's not clear yet is the extent to which they can "think" by forming
internal models of the world.

A new paper (by Berglund, et al.) has come out revealing a potential limitation of these models, which the authors have
dubbed the Reversal Curse. In short, LLMs trained on statements of the form "A is B" can't generalise to understand
that "B is A". From the paper's abstract: "For instance, if a model is trained on 'Olaf Scholz was the ninth Chancellor
of Germany', it will not automatically be able to answer the question, 'Who was the ninth Chancellor of Germany?'"

This isn't necessarily an inherent limitation of the models (at least, that hasn't been demonstrated), more so the
training set up. But the authors attempt a number of different training set ups, all of which lead to the problem.

You can find the paper here ([https://arxiv.org/abs/2309.12288](https://arxiv.org/abs/2309.12288)), or a discussion by
Gary Marcus here ([https://garymarcus.substack.com/p/elegant-and-powerful-new-result-that](https://garymarcus.substack.com/p/elegant-and-powerful-new-result-that)).
