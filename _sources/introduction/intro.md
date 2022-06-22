# Introduction

*Applying explainable machine learning to national stroke audit data to explore variation in decisions to use thrombolysis*: Part of the NIHR *SAMueL* (Stroke Audit Machine Learning) project.

Kerry Pearn & Michael Allen

```{epigraph}
"Your decision to treat or not treat … That’s the difficult part. That’s the grey area where everyone does a different thing."

-- Stroke Consultant during interviews for SAMueL
```

## Background

Stroke is a common cause of adult disability. Most strokes (about four out of five) are caused by a blood clot in the brain. Expert opinion is that about one in five patients should receive clot-busting drugs to break up the blood clot that is causing their stroke, and this is the target set in the NHS long term plan. This clot-busting treatment is called *thrombolysis*. At the moment only about one in nine patients actually receive this treatment in the UK. There is a lot of variation between hospitals, which means that the same patient might receive different treatment in different hospitals.

In a previous project, [SAMueL-1](https://samuel-book.github.io/samuel-1/introduction/intro.html), we trained machine-learning models to predict whether any individual patient would receive thrombolysis in any hospital. This allows us to investigate what differences in treatment are likely to be due to differences between patients, and what differences in treatment are likely to be due to differences between hospitals rather differences in the patients each hospital sees.

## Aims of this study

The aims of this study were 1) to apply *explainable machine learning* techniques to investigate the most significant features that drive decisions to use thrombolysis at different hospitals, and 2) to model and explain what are the the features that are most important in hospitals making *different* decisions about the same patient.

## What is *Explainable Machine Learning*?

Machine learning models generally learn from large sets of data - learning patterns between aspects of the data and some outcome of interest. In our use case the data contains a range of *features* about the patient, such as their age, sex, a breakdown of their stroke symptoms, etc. And the machine learning model learns the relationship between those features and the *target* that we would like to predict - that is whether the patient receives thrombolysis or not. 

A high level diagram of our machine learning is shown in {numref}`Figure {number} <high_level_md>`. 

:::{figure-md} high_level_md
<img src="./images/ml_model_high_level.png" width="600">

A high level depiction of machine learning models trained to predict use of thrombolysis for any patient given 1) the hospital they attend, 2) patient and clinical information, and 3) pathway and process information. 
:::

There are many different types of machine learning (here we use one called *XG-Boost*), but all are making predictions based on similarities to what the model has seen before. Many machine learning models are what we call *black box* models - that is we give it some information, and it makes a prediction, but we don't know *why* it made that particular prediction. 

*Explainable Machine Learning* seeks to be able to communicate why models make the prediction they do. We seek to understand, and communicate, the general patterns that the model is making (sometimes we call this *global explainability*), as well as why the model made the prediction it did for one particular patient (sometimes we call this *local explainability*). We also try to explain other important aspects about the model such as where the training data came from (and how representative is that data of where the model will be used in practice), and how a sure can we be of the model's predictions - both generally and for any particular prediction.

In this project we are very much on a journey - discovering what different people would like to know about the model. Do patients, clinicians, and other machine learning researchers all want to know the same things, or different things? How can we tailor *explainable machine learning* output to different audience's wishes?

(*Explainable machine learning* may also be known as *Explainable ML*, *Explainable artificial intelligence*, or *Explainable AI*).

## Methods

In this study we used a machine learning method called *XG-Boost* to predict decisions to give thrombolysis at each of 132 hospitals in England and Wales that deal with emergency stroke admissions. 

In order to make the model easier to explain we found the most important features that would predict whether a patient received thrombolysis or not. We found that with 8 features we could get accuracy that was very close to using all available features. These 8 features were:

* *S2BrainImagingTime_min*: Time from arrival at hospital to scan
* *S2StrokeType_Infarction*: Stroke type: clot ('infarction') or bleed ('haemorrhage')
* *S2NihssArrival*: Stroke severity (National Institutes of Health Stroke Scale; NIHSS) on arrival
* *S1OnsetTimeType_Precise*: Is stroke onset time known precisely (or estimated)
* *S2RankinBeforeStroke*: Disability level (modified Rankin Scale) *before* stroke
* *StrokeTeam*: Hospital ID
* *AFAnticoagulent_Yes*: Patient on anticoagulant therapy for atrial fibrillation
* *S1OnsetToArrival_min*: Time from stroke onset to arrival at hospital

Note: The [GitHub repository](https://github.com/samuel-book/samuel_shap_paper_1) also includes XG-Boost models using all available features.

In order to explain model predictions we turned to a method called Shapley values, which we describe below.

### What are Shapley values?

> Shapley values (or *Shap* values)are *'the average expected marginal contribution of one player after all possible combinations have been considered'*.

Or, imagine a pub quiz team with up to 3 people. Any number of people may actually turn up on the night:

* There are 8 possible combinations of players (including no-one turning up).

* The Shapley value for any team member describes the average difference in score when a particular player is present or absent compared to the average of all combinations of players.

The same principle may be applied in machine learning: How does any one feature (e.g. stroke severity, or age), on average, contribute to the prediction after considering all possible combinations of features? What difference does that feature make to the prediction?

## Key findings

### Predicting thrombolysis use with an XG-Boost model

The five most influential features predicting whether thrombolysis would be given or not were (in order of importance):

1. *Stroke type (infarction vs. haemorrhage)*: Use of thrombolysis depended on it being an infarction (clot).
2. *Time from arrival at hospital to time brain imaging was performed*: Predicted probability of using thrombolysis reduced with increasing time to scan.
3. *Stroke severity (NIHSS) on arrival*: Predicted probability of using thrombolysis was low at low NIHSS, rose with increasing NIHSS with a plateau at about NIHSS of 10-20, and then reduced with higher NIHSS.
4. *Stroke onset time type (precise vs. estimated)*: Predicted probability of using thrombolysis is increased with a precisely known  onset.
5. *Disability level (Rankin) before stroke*: Predicted probability of using thrombolysis reduced with increasing disability before stroke.

Shap plots may be used to explain predictions of any individual patient (e.g. {numref}`Figure {number} <waterfall_example>`). 

:::{figure-md} waterfall_example
<img src="./images/xgb_waterfall_low_probability.jpg" width="800">

An example of a Shap *waterfall* plot showing the most influential features in influencing the model's prediction of a patient probability of receiving thrombolysis (in this case a patient with a very low probability of receiving thrombolysis). In this example the three most influential features, reducing the chance of receiving thrombolysis were 1) slow arrival-to-scan time (138 mins), low stroke severity (NIHSS 2), and 3 the hospital attended.
:::

### Predicting *differences* in thrombolysis use between hospitals with an XG-Boost model

When an XG-Boost model was trained to predict different choices in thrombolysis between units with a high or low propensity to use thrombolysis, the five most influential features were:

1. *Disability level (Rankin) before stroke*: lower thrombolysing units had a lower predicted probability of using thrombolysis with increasing disability before stroke.
2. *Stroke severity (NIHSS) on arrival*: lower thrombolysing units had a lower predicted probability of using thrombolysis with lower stroke severity.
3. *Stroke onset time type (precise vs. estimated)*: lower thrombolysing units had a lower predicted probability of using thrombolysis when the stroke onset time had been estimated.
4. *Time from onset to arrival at hospital*: lower thrombolysing units had a lower predicted probability of using thrombolysis with longer onset-to-arrival times.
5. *Time from arrival at hospital to time brain imaging was performed*: lower thrombolysing units had a lower predicted probability of using thrombolysis with longer arrival-to-scan times.

## Conclusions

*Explainable machine learning* techniques give significant insight into models prediction clinical decision-making. At a global level, Shap allows for an understanding of the relationship between feature values and the model prediction, and at an individual level Shap allows for an understanding of the most influential features in any single prediction.




