---
title: Supervised Clustering with SHAP Values
layout: data-dispatch-post
---

Something more DS-oriented today: [https://www.aidancooper.co.uk/supervised-clustering-shap-values/](https://www.aidancooper.co.uk/supervised-clustering-shap-values/).

This article describes an approach to "supervised clustering" -- instead of clustering the raw observations, it first
finds the corresponding SHAP values, and then clusters those values (with some good results, indicated in the article).

Beyond this being an inherently interesting approach (that I'm very keen to experiment with), it also means:

- The raw data is rescaled into the units of the target variable. (Of course, this means you need a target variable,
  which you don't require for unsupervised clustering.)
- SHAP values weigh the features by their importance to the model, so it automatically eliminates non-useful features.

The one gotcha is the need to train a model on the data, from which the SHAP values are calculated. The values, and
hence the clustering results, will thus be highly dependent on the choice of model.

P.S. If you haven't come across SHAP values -- they are a means to understand how features affect a model's
predictions: [https://shap.readthedocs.io/en/latest/index.html](https://shap.readthedocs.io/en/latest/index.html). They
use a game-theoretic approach, calculating the marginal effect of each feature. Very cool, and they should be a part of
every DS's toolbox 🧰.
