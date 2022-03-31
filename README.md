# Multivariate-Regression-Analysis
Multivariate Regression Model for Explaining House Price
Yangxuan Xu

Section 1. Introduction
The purpose of this project is to fit a multivariate regression model for this real estate valuation data in Xindian District, New Taipei City, Taiwan, focusing on explaining how these factors influence the unit house price in this area.

From a macro point of view, the price of real estate is a significant reference to the local economy and living standards of people. Most people consider buying their own home thus the valuation of house price does matter for them. Real estate price is influenced by numerous factors, some of which cannot be well quantified (like monetary policy, investment expectation, etc). While others like location, house age, and convenience, can be expressed as quantitative numbers. Under this data, I will fit a multivariate regression model to try to figure out how these factors influence real estate price in this area. 

The data comes from Professor I-ChengYeh and Tzu-KuangHsu in Department of Civil Engineering, Tamkang University, Taiwan. It was donated in 2018. Most of the data set was collected from the public database of Ministry of the Inferior during the period of June 2012 to May 2013 in Taipei City. For the data that cannot be directly acquired from the database, like X3 (distance to the nearest MRT station) and X4 (number of convenience stores), the authors got them directly from Google Map (Yeh&Hsu, 2018). Therefore, we can think the data has a valid source. 

The outline of the remainder of this report is as follows. In Section 2, I present a brief introduction of characteristics of the data. Based on these characteristics, in Section 3 I will use R to conduct the multivariate regression steps to find a good model. Discussion of the model deficiency and some important details will be the Section 4. Concluding remarks can be found in Section 5, followed by the appendix which involves data source and references.
Section 2. Data Characteristics
The data was compiled and donated in 2018 by Professor I-ChengYeh and Tzu-KuangHsu in Department of Civil Engineering, Tamkang University, Taiwan. These market historical data set of real estate valuation are collected from Xindian District, New Taipei City, Taiwan.

The total number of instances is 414, without any missing value. This set is a good multivariate regression data set, whose attributes are all integer or real numbers. The response variable Y is the house price of unit area (10000 New Taiwan Dollar/Ping, where Ping is a local unit and 1 Ping=3.3 m2). The other 6 explanatory variables are as follows:
x1=the transaction date (for example, 2013.250=2013 March, 2013.500=2013 June, etc.)
x2=the house age (unit: year)
x3=the distance to the nearest MRT station (unit: meter)
x4=the number of convenience stores in the living circle on foot (integer)
x5=the geographic coordinate, latitude. (unit: degree)
x6=the geographic coordinate, longitude. (unit: degree)

Table 20.1. provides more details on the explanatory variables and response variable (some decimals are omitted).   
Table 20.1.  Real Estate Valuation Summary Statistics (R Output)
 

It’s worth noting that x1 is not a usual form we use to record date. The author used the decimal parts to represent month. Here I still see it as numeric variables since it will be more convenient interpretating the coefficient. From Figure 20.2, we can find the data was collected on certain dates thus possible transformation for x1 is to treat it as ordinal variables, which improves the goodness-of-fit but it’s more difficult to interpret the coefficients.

The x5 and x6 are not preferred to be used directly under this circumstance since they are spatial data and it’s hard to directly relate latitude or longitude to house price. A possible method is to conduct a polar coordination transformation of these two attributes to r(distance to the center) and θ(angular parameter). I will explain how I choose the center point and do the transformation in Section 3.

Figure 20.2. shows the relationship between explanatory variables and the response variable. It can be seen from these plots that the unit house price(y) increases with transaction date(x1), number of convenience stores(x4) and spatial variables(x5&x6). While house age(x2) and distance to the nearest station(x3) seem to have negative relationship with unit house price. 
Figure 20.2.  Relationship between inputs and outputs (R Output)
 


To understand how these explanatory variables influence house price in Xindian District, I will use multivariate regression to fit models, discuss findings and draw the conclusion.


Section 3. Multivariate Regression Analysis
A) Variable Selection & Model Construction
I will construct two different models and make comparison to see which one is better in explaining the house price.

1) Firstly, I fitted the full model with all the variables x1, ..., x6, including interaction terms. Using the step-wise method to do variable selection, checking variance inflation factor (VIF) and p-values, I tried to drop some less important variables or those with VIF>10(multicollinearity problem) and do some transformation, getting the best model (1): 

ln(Y) = β0 + β1Xi1 + β2Xi2 + β3Xi3 + β4Xi4 + β5Xi5 + β6(Xi3*Xi4) + εi        (1)
Taking the natural logarithm of response helps to improve the goodness-of-fit a lot, so I did this transformation. 

2) Another idea is to conduct a polar coordinate transformation of x5(latitude) and x6(longitude). This is a usually considered transformation when confronted with spatial variables. 

Before conducting the transformation, we need to choose the origin. Given the general idea that the house located in the center of a city is often more expensive, I calculated the mean value of x5(latitude) and x6(longitude) for those with unit house price higher than 70. This point is set to be the origin, an approximate center of the “rich area”. And then we use r to denote the distance to the center point, together with the angular coordinate θ. The idea on how to choose the threshold to find origin will be discussed in Section 4.

Following the similar steps in 1), my model (2) is showed below:

     ln(Y) = β0’ + β1’Xi1 + β2’Xi2 + β3’ln(Xi3) + β4’Xi4 + β5’r + εi ‘        (2)
Taking the natural logarithm of some variables also improve the goodness-of-fit. θ is dropped during the variable selection, which means the angular coordinate is not important to decide house price. This is interesting because most of the time θ and r decide the position together and position is an important factor in real estate valuation. A possible explanation is most of the district are rural area (seen from the map Figure 20.4), and there is no big difference in house price among these places far from urban area.
The variable r can be seen as a non-linear transformation of x5 and x6, a better interpretation of the latitude and longitude.

B) Parameter Estimation
We use the summary function in R to get a view of the two models. Table 20.3. gives us the output and we can see the estimated parameters. The p-values are small which means that these variables are significant. The adjusted R square, an important evaluation metric in comparing models, are close between the two models.
Table 20.3.  Summary of model(1) and model(2) (R Output)
    
Model (1)                                              Model (2)

C) Model Diagnostics
Based on the definition of multivariate regression model, four basic assumptions need to be obeyed. If not, the inferences about the model will be less effective. I have performed the following diagnostic check of the basic assumptions made by regression:
1.	Residual plot & Breusch-Pagan Test to check Linearity and Equal Variance
2.	Normal Q-Q plot & Shapiro-Wilks Test to check Normality
3.	Durbin-Watson Test to check Error Independence

The results show that the normality assumption is violated for both two models, with all other assumptions substantially obeyed. Box-Cox transformation has been tried but doesn’t help to improve. The residual not following normal distribution can be seen as a small deficiency of the models, which will be discussed in Section 4 on how it will influence interpreting.

Besides, some influential points and outliers exist. For model (1), there are 18 outliers and 28 influential points. While for model (2), 20 outliers and 24 influential points are found. 

D) Evaluation
From AIC, BIC and adjusted R2, we can see that both models can fit the data well, with model (1) performing slightly better. But based on the likelihood ratio test, since p-value(0.1518) is large, we have no evidence to say model (1) is better than model (2).

From the explaining aspect, the variable r used in model (2) is better than using x5(latitude) or x6(longitude). This variable shows that there is a negative relationship between the distance to center and unit house price, which is more universally meaningful. Instead, the relationship between latitude or longitude and unit house price makes less sense. Besides, r can be seen as a non-linear transformation of x5 and x6, which means that we didn’t drop any initial variables in model (2). 

In conclusion, we choose model (2) as our fitted model to do explain the unit house price in this district.


Section 4. Discussion
1)	The Data Set
In Section 2 I have presented the basic characteristics of this data set. Here I want to discuss some more thinking about the data. 

From the map (Figure 20.4.) we can see that Xindian is a small district of New Taipei City, most of which are rural area. One good thing on focusing on such a small region is that we don’t need to consider macro factors like international trading and house pricing policy in different areas. The data set gives us only 6 explanatory variables and we only need to consider them. More clear and direct relationship can be found in order to explain how they influence unit house price.

Figure 20.4.  Map of Xindian District (from Google Maps)
    

The deficiency of the data comes from the small sample size and limited variables. There will be no problem if we just use them to fit a model and interpret in Xindian, but when considering universality which I will discuss later, the model will be less meaningful if conducted in other places.

In my model (2), I conduct a transformation of x5(latitude) and x6(longitude) to r, which is the distance to the origin. The reason why I did this is that it’s hard to conclude what relationship between latitude & longitude and unit house price. This transformation can be seen as a previous interpretation of the spatial variables. Other possible methods can be tried to interpret the spatial variables, like classifying the district into different small regions and using dummy variables to represent the categories. A better visual environment can also raise the surrounding house price (Liao, X., Deng, M., & Huang, H., 2021). 

Except the spatial variables, explaining some other variables is also a tricky problem. For example, x1(transaction date) cannot be easily correlated with house price. One possible reason for the positive relationship between x1 and y is the appreciation in house value over time, which comes from investment perspective.

2)	Threshold for choosing origin in model (2)
How to choose the threshold for deciding the origin significantly influences model (2). At the end of my R codes, I tried different thresholds (y=0, 30, 50, 80) to choose the center point to fit the models and compared them with model (1). Interestingly, the p-value for likelihood ratio test is small for these thresholds, which means model (1) is better. While when choosing y=70, model (2) is preferred than model (1).

In Figure 20.5. we can see most points have unit house price lower than 70. If I lower the threshold, much more points will be included and the mean value will deviate the most rich area easily. If the threshold raises, too few points are included. Thus choosing the threshold y=70 and calculating the mean value of spatial coordinates of those points helps us to find the center point which denotes so-called “rich area”. Since there is still some intuition in choosing the threshold, better mathematical method can be applied to do the parameter tuning.
Figure 20.5.  Plots for unit house price (R Output)
 

3)	Model Assumption
Based on the normal Q-Q plot and S-W test, we can conclude that the residuals didn’t follow the normal distribution. Figure 20.6. is the Q-Q plot for residuals of model (2). Since the points on the left side are lower and points on the right side are higher than the line, the residuals have a short-tail distribution, which means that the data is more centralized than normal distribution.
Figure 20.6.  Normal Q-Q Plot for residuals of Model (2) (R Output)    

I cannot find good transformation to fix this problem, thus I decide to keep the current model and see whether this problem hinders my goal of doing regression a lot. More centralized data means the variance will be deflated, resulting a smaller p-value. With smaller p-value, we are easier to reject the null hypothesis even though the variable is less important given H0: beta = 0. 

But in general, this problem only matters when the focus is the significance or confidence interval of the variance term (Maas, C. J. M., & Hox, J. J., 2004). Since my focus is interpretation, namely, the parameter estimation and overall good-of-fitness, we are less considerable about normal assumption. Further improvement can be made, but now we still use the model with this deficiency.

4)	Universality
After getting the model from this data set, we need to consider will this model be useful under other circumstances. My conclusion for this question is this model has little universality. 

One main reason is the small sample size and limited variables. Hundreds of factors influence the house price thus 5~6 variables can hardly explain house price in complicated cases. Besides, the choice of the variables in this data set are not important enough. Factors like whether in good school district or whether has good scenery and environment are more important in deciding house price. 

But as I explained before, this model will have a good interpretation in this small area. Less universality is acceptable.

5)	Model Limitation and Further Improvement
In this part I will discuss limitation of the multivariate model and further improvement.

Firstly, I use a linear model so we need to check the patterns between explanatory variables and response variable at the beginning. For this data, some variables have a significant linear pattern but some only a close linear pattern. Thus, some outliers occur which means that these points don’t fit the model well. 

Secondly, there are model assumptions for multivariate linear regression. For this fitted model, normality assumption is violated. If we focus on the confidence interval or variance, the model will be less effective.

To improve this, other non-linear model or model dealing with non-normal distributed residuals can be used. For house price prediction, hedonic method and cluster method can also considered to be conducted (Bourassa, S., Cantoni, E., & Hoesli, M., 2010).


Section 5. Conclusion
Section 1 and 2 present the general background of real estate valuation together with the characteristics of the data. In Section 3, I follow the multivariate regression steps to conduct variable selection, model construction, parameter estimation, model diagnostics, and evaluation. In Section 4, some important discussions are gone through. We talk about the shortcoming/advantage of the data set, the construction of model (2), analyze the influence of normal assumption violation, discuss the universality and model limitation.

We get two feasible model, make comparison, and finally choose Model (2). From the R summary output (Table 20.3.), we can make an interpretation of the coefficients of the variables:
1.	With other variables fixed, one unit increase in the transaction date(x1) leads to 1.1859 units increase in unit house price(y).
2.	With other variables fixed, one unit increase in the house age(x2) leads to 0.9931 units increase in unit house price(y).
3.	With other variables fixed, one unit increase in the natural logarithm of distance to the nearest MRT station(x3) leads to 0.9011 units increase in unit house price(y).
4.	With other variables fixed, one unit increase in the number of convenience stores(x4) lead to 1.0131 units increase in unit house price(y).
5.	With other variables fixed, one unit increase in the distance to the center point(r) leads to 0.00000214 unit increase in unit house price(y).
















Appendix
A0. Data Resource
https://archive.ics.uci.edu/ml/datasets/Real+estate+valuation+data+set

A1. References

Bourassa, S., Cantoni, E., & Hoesli, M. (2010). Predicting House prices with spatial dependence: A comparison of alternative methods. Journal of Real Estate Research, 32(2), 139–160. 

Liao, X., Deng, M., & Huang, H. (2021). Analyzing multiscale spatial relationships between the house price and visual environment factors. Applied Sciences, 12(1), 213.  
Maas, C. J. M., & Hox, J. J. (2004). The influence of violations of assumptions on multi-level parameter estimates and their standard errors. Computational Statistics & Data Analysis, 46(3), 427–440. 
Yeh, I. C., & Hsu, T. K. (2018). Building real estate valuation models with comparative approach through case-based reasoning. Applied Soft Computing, 65, 260-271.

A2. R Codes
