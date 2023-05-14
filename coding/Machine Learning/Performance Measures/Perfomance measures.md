
### Confusion matrix

 > [!info] 
> It tells us what the machine learning algorithm did right and what it did wrong
> 


![[confusion matrix.png]]

- **TP** (true +) - actually has and predicted to have
- **TN** (true -) - actually does not have and predicted to not have
- **FP** (false +) - actually does not have and predicted to have
- **FN** (false -) - actually has and predicted to not have

> [!tip] 
>  Size of confusion matrix depends on no. of things we want to predict




### Sensitivity and Specificity

> [!info] 
> -> ***Sensitivity*** - actual +s correctly predicted
> -> ***Specificity*** - actual -s correctly predicted 

> [!important] 
> $$Sensitivity=TP\;(positive\;predictions)\;/\;(TP+FN)\;(total\;positives)$$
$$Specificity=TN\;(negative\;predictions)\;/\;(TN+FP)\;(total\;negatives)$$ 

> [!tip] 
> - > Choose method with higher sensitivity if correctly identifying positives is required
> - > And method with higher specificity if correctly predicting negatives is more important
> - > Sensitivity and Specificity are inversely proportional


### Bias and Variance

> [!info] 
>-> Inability of ML method (like linear regression) to capture true relationship is ***bias***.
>-> DIfference in fits between datasets (like training vs testing) is called ***variance***.

![[squiggly line method.png]]
<u>Squiggly method</u> - high variance and low bias (over-fitting)

![[linear regression.png]]
<u>Linear regression</u> - low variance and high bias

> [!tip] 
> Three common methods to find model with right fit:
> 	1. Regularization
> 	2. Boosting
> 	3. Bagging

### ROC and AUC

> [!info] 
>->  ***ROC*** (receiving operating characteristics) graph summarizes all of the confusion matrices that each threshold produced.
>-> ***AUC*** (area under curve) - to compare ROC curves

![[roc & auc.png]]

> [!tip] 
> Greater the AUC, better the method (red is better than blue)
> 

> [!important] 
> If there is an imbalance between true negatives and true positives (rare disease), we use precision in ROC curve instead of false positive rate

### Precision and Recall

> [!info] 
> -> Precision is the proportion of positive results correctly classified.
>  $$Precision=TP/TP+FP$$
>  ->









