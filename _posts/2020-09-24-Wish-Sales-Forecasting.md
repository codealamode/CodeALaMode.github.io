---
layout: post
title: Shoppers on Wish: What's their wish?
subtitle: Predicting Sales Volume of Products on Wish.com
cover-img: /assets/img/Wish Logo.png
thumbnail-img: /assets/img/UFO graph lines.PNG
share-img: /assets/img/thumb.jpg
tags: [Wish, Sales, Forecasting]
---

Wish.com, founded in 2010, is an e-commerce site that has gained popularity twenty-some years after the dot-com boom, to which sites like Ebay and Amazon owe their success.  So how did this late bloomer compete with the internet's used goods retail giants?  According to Sam Hollis, a blogger at jilt.com, "Wish has focused their business model on one thing and one thing only: price. They’re not looking to expand into the media space, build tech gadgets, or even offer next-day delivery. Wish built their company to provide...products at the lowest possible price."

Of course, shoppers know that lower prices generally mean lower quality.  To gather public opinion on the quality of Wish.com's goods, one need look no further than the Google search bar suggestions for Wish vs. Amazon:

![Google Search Bar for Wish](/assets/img/Wish Google Search.png)
![Google Search Bar for Amazon](/assets/img/Amazon Google Search.png)

By looking at what is commonly searched for after "Wish is", it's clear that Americans are hesitant to trust the site, perhaps because of the deep discounts, or perhaps because it's newer to the scene.

To see what actually drives sales on Wish, I used a dataset of product information scraped from the website in August, 2020.  Below is an example of the information used to predict sales volume of each product.

![Wish Search Results](/assets/img/Wish Search Page.png)

Outside of these data, I also had product rating, merchant rating, and how many total ratings they had received.  I ended up removing the latter, as it was leaking information into the label.  In other words, when a product sells a high volume, it will organically recieve more feedback than a product that sells at low volume.  So the predictor depended on the prediction, not the other way around.

You might have noticed that the search results page doesn't list exact quantities sold, only figures like "10+", "50+", "100+", and so on.  This means that the model will output a category (low sales, medium sales, or high sales) instead of a number.

But first let's make sure the data makes sense.  It would make sense that merchants with high ratings will sell products that tend to also have high ratings.  Looking at a 3D partial dependence plot of the two shows that there is indeed a peak toward the area where merchant rating and product rating are high.

![Merchant/Product Rating PDP](/assets/img/Wish PDP.png)

40% of the sales were 'high', so if we predicted 'high sales' for every single product, we'd have 40% accuracy.  In other words, a model that scores above 40% is the goal.  This particular random forest classifier reached an accuracy of 55% after removing leakage.  (With leakage, the model was near 90% accuracy.)  The model performed relatively well in predicting when sales would be high or low.  It's predictions for medium, however, were low in accuracy.  A confusion matrix of the prediction accuracies on a validation set is below:

![Wish Confusion Matrix](/assets/img/Wish Confusion Matrix.png)

After tuning parameters, I chose to stick with this model, erring on the side of an incorrect prediction of 'medium' rather than an incorrect prediction of 'high' or 'low'.  This is because a seller will suffer more if sales are predicted as high, but end up being low, and vice versa.  Incorrectly predicting 'medium sales' will only ever be off by on category, never by two.

After buildling the model and tuning its hyperparameters, perhaps the most informative information is not its predictive power, but rather a mathematical explanation of which features affected prices the most.  In technical terms, I permuted each feature's values, then measure the mean average error (MAE) after the permutation and subtracted it from the MAE before the permutation.  Below are the differences in MAE pre- and post- permutation.

![Wish Permutation Importances](/assets/img/Wish Permutation Importance.png)

