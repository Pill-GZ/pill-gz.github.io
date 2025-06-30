---
layout: page
title: Uncertainty in Model Calibration
description: 2024-06-27
img: assets/img/calibration_CI_after.jpg
date: 2024-06-27
importance: 1
category: work
related_publications: false
giscus_comments: false
---

I built a <a href="https://pill-gz.shinyapps.io/ote-ci-calculator/">web calculator</a> to quantify the uncertainty of model calibration for integer targets.

---

I encountered the following meta-problem at work:

> An ML model predicts the number of occurences of an event (E), among N samples. So we have
> <ul>  <li> N samples </li>
>       <li> Number of predicted events: E (for "expected") </li>
>       <li> Number of actual events: O (for "observed") </li>
> </ul> How calibrated is our model, knowing these numbers?

Ideally, if the model is calibrated, the ratio O/E (or "OTE") should be close to 1.

However, due to
<ul>
    <li> randomness, </li>
    <li> and commonly, non-integer-valued model predictions </li>
</ul>
This ratio unlikely to be exactly 1, even if the model is calibrated. 


Consider the following example where event is rare (say, p = 0.1):

> <ul>
>     <li> N = 3 </li>
>     <li> E = 0.3 </li>
>     <li> O = 0 </li>
> </ul> which yields an OTE ratio of 0.

The model is perfectly calibrated (E = 3 * 0.1), but we have an OTE of 0 with high probability (p = 0.729).


<!-- Here is a fictitious example I encountered at work: managers were trying to identify the segments of borrowers that are under-performing, ranking the observed-to-expected number of loan defaults. One of them is suspicious of the signals in some segments where sample sizes are small; notice the "Noise?" comments in column J.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/calibration_CI_before.jpg" title="example image" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Poeple were ranking by the raw "observed-to-expected" ratio (OTE). The confidence intervals were obtained by bootstrap. This is obviously problematic when sample size is small, or when events are rare.
</div>

Reading the OTE on its own is insufficient. -->

A naive attempt to quantify the uncertainty in the OTE ratio by bootstrap obviously fails here, since the number observed events is 0, leading to a degenerate confidence interval (0, 0)

The solution is to <b>properly account for the uncertainty in the OTE ratio</b>. Details can be found in the "About" page of the <a href="https://pill-gz.shinyapps.io/ote-ci-calculator/">web calculator</a>. 

By constructing proper confidence intervals around OTE, one can now also obtain p-values for calibration-ness of the model.
We can now properly rank the strnegths of evidence using the p-values, and not the raw OTEs.

<!-- Here's what happens when we apply the tool to the data above.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/calibration_CI_after.jpg" title="Calibration CI with confidence interval and p-values" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Using the new methods I proposed, we now ranking issues by p-values, or "surprise index". The ranking is much more reasonable and takes into account when sample size is small or when events are rare. Confidence intervals no longer collapse when we have no positive events. 
</div>

Notice how the smaller segments with OTE>1 decreased in raking, compared to before.
Further, Not all high OTEs warrant concern. Some are insufficient as evidence for model miscalibration, according the p-values. -->

As expected, the sample size plays a role in the uncertainty of the ratio. But not always in a way that aligns with my intuition. 
You can play with the <a href="https://pill-gz.shinyapps.io/ote-ci-calculator/">calculator</a> to see if it does yours.

I find the problem small but neat, and perhaps common and worth sharing.
