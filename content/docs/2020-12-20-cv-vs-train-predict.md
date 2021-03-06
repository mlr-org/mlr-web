---
title: 'The "Cross-Validation - Train/Predict" misunderstanding'
authors: ["patrick-schratz"]
date: '2020-12-20'
slug: cv-vs-predict
toc: true
categories:
  - R
tags:
  - mlr3
  - cross-validation
  - train
  - predict
---

## Introduction

Over the past years I've seen multiple posts on Stackoverflow and our GitHub issues which suffer from a conceptual misunderstanding: cross-validation (CV) vs. train/predict.

Because train/predict is an essential part of cross-validation, the point might not be so obvious.
I'll try to make it more clear by providing some exemplary questions:

- "I've done cross-validation. How do I decided which model to use for prediction?"

- "I've used the `resample()` function. How do I decided which hyperparameters are best?"

- "I've benchmarked some algorithms. How do I find the most important features?"

All of these questions have a common problem: users trying to extract something out of the CV to work with it afterwards.
They want to use the "best model" or find the "most important features" or similar.
The thoughtful reader might already infer by now that doing so is probably problematic.

And yes, it is.
One should not try to extract a single component (be it a model, a set of hyperparameters or a set of predictors) out of a CV.

## What happens in a CV, stays in a CV

Here's why:

Every model fit and evaluation in a CV happens on a subset of the main dataset.

1. The dataset is split by a specific method (e.g. randomly) into pre-selected partitions (e.g. 5).
1. (Optional) Optimal hyperparameters are searched and feature selection is performed (in another inner CV cycle)
1. The model is fit on the training set and predicts on the test set
1. The performance of this prediction is evaluated (because "truth" (= test set) is known)

This is done multiple times.
Every time, the dataset is different.
Every time, different hyperparameters are found.
Every time - ok you got it by now.

None of these training/test set combinations represent overall "the best" choice - they only operate in their specific data split setting.
There is also no way to find a model (or similar) within a CV with respect to these criteria.

The **main reason** for this is that in all cases the fitted model was trained on only a subset of the data available.
This was done to evaluate the performance on a subset of the data - because "truth" is known for the hold back data.
Otherwise there would be no need to hide precious data from model fitting.

## Train & Predict

The main purpose of fitting a model is make predictions with it.
For this, you want to use all available data to fit the most robust model possible.
And this is exactly what you should do: take all your data, optimize your hyperparameters, eventually conduct feature selection, and then fit the model.

Yes, do it again - by using the `train()` and `predict()` functions (and their tuning wrappers/pipelines) directly.
Do not use `resample()` or `benchmark()` - these are for CV purposes!

Then, take this one model and predict into **unknown space**.
In this scenario, you cannot know how good your predictions will be because there is no "truth" to evaluate against.
But this is perfectly fine.
This is why a CV was done (beforehand): to have a somewhat unbiased estimate of how your model will perform under different conditions.
You can also analyse the hyperparameters or evaluate the results of a feature selection from this model[^1].

Be careful though: your final model which you fit and predicted into unknown space might exactly have this performance - but it might also be completely off.
You will never find out unless you eventually collect data at some point which you can compare against the predictions made by your model.
You can only state that, with a given uncertainty, your model will have a performance of X when predicting.

[^1]: While it might be ok to look at feature selections of single model fits, it is usually recommended to fit multiple models and look at the average variable important outcomes - e.g. by doing a permutation-based features selection.

## Summary

To make if fully clear again: CV and train/predict are two separate things.
Think of them as two different buckets with no relation to each other.

- CV is done to get an estimate of a model's performance.
- Train/predict is done to create the final predictions (which your boss might use to make some decisions on).

CV is used to explain the performance of your fitted model (which is a single fit of the chosen algorithm on all of your data points).
