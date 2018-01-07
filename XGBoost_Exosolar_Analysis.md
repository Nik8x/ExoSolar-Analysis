XGBoost\_Exosolar\_Analysis
================
Niket

Extreme Gradient Boosting, XGBoost Model - It does parallel computation on a single machine. This makes xgboost at least 10 times faster than existing gradient boosting implementations. It supports various objective functions, including regression, classification and ranking. It only works with numeric vectors.

``` r
#install.packages('caret')
require(caret)
```

    ## Loading required package: caret

    ## Loading required package: lattice

    ## Loading required package: ggplot2

``` r
#install.packages('e1071')
require(e1071)
```

    ## Loading required package: e1071

    ## Warning: package 'e1071' was built under R version 3.4.3

``` r
# load the new dataset
exosim <- read.csv(file.choose(), header = TRUE) # load exos_new-imp2.csv
```

``` r
#### Converting variable Probability_of_life to factor
exosim$Probability_of_life <- as.factor(exosim$Probability_of_life)
table(exosim$Probability_of_life)
```

    ## 
    ##    0    1 
    ## 3256  328

``` r
# So we have 328 planets in our dataset with some probability of life.
```

``` r
label_1 <- exosim$Probability_of_life # our variable for classification
```

``` r
set.seed(1234)
oneortwo <- sample(1:2 , length(exosim$PlanetIdentifier), replace = TRUE, prob=c(0.8, 0.2)) # generating random values and storing them
```

#### XGBoost

XgBoost accepts the missing values in it's prediction but we'll take our exosim dataframe.

``` r
# install.packages('xgboost')
library(xgboost)
```

    ## Warning: package 'xgboost' was built under R version 3.4.3

``` r
library(readr)
library(stringr)
#library(caret)
#install.packages('car')
#library(car)
```

``` r
# create train data frame
train_3 <- exosim[oneortwo == 1, -26]

# create test data frame
test_3 <- exosim[oneortwo == 2, -26]

# create data frame to apply train and test upon
train_3_label <- label_1[oneortwo == 1]
test_3_label <- label_1[oneortwo == 2]
```

``` r
# convert every variable to numeric, even the integer variables
train_3 <- as.data.frame(lapply(train_3, as.numeric))
test_3 <- as.data.frame(lapply(test_3, as.numeric))
```

We must convert our data type to numeric, otherwise algorithm doesn’t work.

``` r
# convert data to xgboost format
data.train_3 <- xgb.DMatrix(data = data.matrix(train_3[, 1:ncol(train_3)]), label = train_3_label)
data.test_3 <- xgb.DMatrix(data = data.matrix(test_3[, 1:ncol(test_3)]), label = test_3_label)
```

``` r
watchlist <- list(train  = data.train_3, test = data.test_3)
```

``` r
parameters <- list(
    # General Parameters
    booster            = "gbtree",          # default = "gbtree"           # gbtree (tree based) or gblinear (linear function)
    silent             = 0,                 # default = 0                  # silent = 0 will stop results from displaying
    # Booster Parameters
    eta                = 0.3,               # default = 0.3, range: [0,1]  # Low eta value means model is more robust to overfitting.
    gamma              = 0,                 # default = 0,   range: [0,∞]  # Larger the gamma more conservative the algorithm is.
    max_depth          = 2,                 # default = 6,   range: [1,∞]  # less depth so to avoid overfitting
    min_child_weight   = 1,                 # default = 1,   range: [0,∞]  # It might help in logistic regression when class is extremely imbalanced. 
    subsample          = 1,                 # default = 1,   range: (0,1]  # 0.5 means that XGBoost randomly collected half of the data instances to grow trees, this will prevent overfitting.
    colsample_bytree   = 1,                 # default = 1,   range: (0,1]
    colsample_bylevel  = 1,                 # default = 1,   range: (0,1]
    lambda             = 1,                 # default = 1
    alpha              = 0,                 # default = 0
    # Task Parameters
    objective          = "multi:softmax",   # default = "reg:linear"
    eval_metric        = "mlogloss",
    num_class          = 20,
    seed               = 1234               # reproducability seed
    )
```

``` r
life_predicted_7 <- xgb.train(parameters, data.train_3, nrounds = 200, watchlist) # nrounds is like ntrees 
```

    ## [1]  train-mlogloss:0.848595 test-mlogloss:0.855311 
    ## [2]  train-mlogloss:0.648772 test-mlogloss:0.655745 
    ## [3]  train-mlogloss:0.515515 test-mlogloss:0.523792 
    ## [4]  train-mlogloss:0.419316 test-mlogloss:0.428498 
    ## [5]  train-mlogloss:0.349087 test-mlogloss:0.360824 
    ## [6]  train-mlogloss:0.296842 test-mlogloss:0.311005 
    ## [7]  train-mlogloss:0.257246 test-mlogloss:0.272192 
    ## [8]  train-mlogloss:0.226149 test-mlogloss:0.242690 
    ## [9]  train-mlogloss:0.201669 test-mlogloss:0.218959 
    ## [10] train-mlogloss:0.183082 test-mlogloss:0.202119 
    ## [11] train-mlogloss:0.167063 test-mlogloss:0.186259 
    ## [12] train-mlogloss:0.155396 test-mlogloss:0.175815 
    ## [13] train-mlogloss:0.144988 test-mlogloss:0.165979 
    ## [14] train-mlogloss:0.136727 test-mlogloss:0.159935 
    ## [15] train-mlogloss:0.128704 test-mlogloss:0.150701 
    ## [16] train-mlogloss:0.121931 test-mlogloss:0.144297 
    ## [17] train-mlogloss:0.116909 test-mlogloss:0.140568 
    ## [18] train-mlogloss:0.111194 test-mlogloss:0.135960 
    ## [19] train-mlogloss:0.106896 test-mlogloss:0.134084 
    ## [20] train-mlogloss:0.103400 test-mlogloss:0.132121 
    ## [21] train-mlogloss:0.100376 test-mlogloss:0.129357 
    ## [22] train-mlogloss:0.097167 test-mlogloss:0.127099 
    ## [23] train-mlogloss:0.094287 test-mlogloss:0.126002 
    ## [24] train-mlogloss:0.091830 test-mlogloss:0.124055 
    ## [25] train-mlogloss:0.088820 test-mlogloss:0.120556 
    ## [26] train-mlogloss:0.086475 test-mlogloss:0.119502 
    ## [27] train-mlogloss:0.084017 test-mlogloss:0.117415 
    ## [28] train-mlogloss:0.081919 test-mlogloss:0.115052 
    ## [29] train-mlogloss:0.080361 test-mlogloss:0.113580 
    ## [30] train-mlogloss:0.079000 test-mlogloss:0.112525 
    ## [31] train-mlogloss:0.077638 test-mlogloss:0.111750 
    ## [32] train-mlogloss:0.076111 test-mlogloss:0.109939 
    ## [33] train-mlogloss:0.074518 test-mlogloss:0.109353 
    ## [34] train-mlogloss:0.073453 test-mlogloss:0.109388 
    ## [35] train-mlogloss:0.072234 test-mlogloss:0.108091 
    ## [36] train-mlogloss:0.070616 test-mlogloss:0.106934 
    ## [37] train-mlogloss:0.069667 test-mlogloss:0.106663 
    ## [38] train-mlogloss:0.068318 test-mlogloss:0.105516 
    ## [39] train-mlogloss:0.066996 test-mlogloss:0.105927 
    ## [40] train-mlogloss:0.066392 test-mlogloss:0.106127 
    ## [41] train-mlogloss:0.065239 test-mlogloss:0.105590 
    ## [42] train-mlogloss:0.064123 test-mlogloss:0.104901 
    ## [43] train-mlogloss:0.063260 test-mlogloss:0.104247 
    ## [44] train-mlogloss:0.062227 test-mlogloss:0.102890 
    ## [45] train-mlogloss:0.061738 test-mlogloss:0.102245 
    ## [46] train-mlogloss:0.061225 test-mlogloss:0.102168 
    ## [47] train-mlogloss:0.060231 test-mlogloss:0.101023 
    ## [48] train-mlogloss:0.059266 test-mlogloss:0.100216 
    ## [49] train-mlogloss:0.058328 test-mlogloss:0.099188 
    ## [50] train-mlogloss:0.057582 test-mlogloss:0.099112 
    ## [51] train-mlogloss:0.056969 test-mlogloss:0.098774 
    ## [52] train-mlogloss:0.055863 test-mlogloss:0.097858 
    ## [53] train-mlogloss:0.055059 test-mlogloss:0.097515 
    ## [54] train-mlogloss:0.054451 test-mlogloss:0.097429 
    ## [55] train-mlogloss:0.053500 test-mlogloss:0.096530 
    ## [56] train-mlogloss:0.052923 test-mlogloss:0.095841 
    ## [57] train-mlogloss:0.052189 test-mlogloss:0.095961 
    ## [58] train-mlogloss:0.051419 test-mlogloss:0.094917 
    ## [59] train-mlogloss:0.050464 test-mlogloss:0.094052 
    ## [60] train-mlogloss:0.049867 test-mlogloss:0.093754 
    ## [61] train-mlogloss:0.049169 test-mlogloss:0.093121 
    ## [62] train-mlogloss:0.048570 test-mlogloss:0.092559 
    ## [63] train-mlogloss:0.047942 test-mlogloss:0.092062 
    ## [64] train-mlogloss:0.047354 test-mlogloss:0.092253 
    ## [65] train-mlogloss:0.046668 test-mlogloss:0.091667 
    ## [66] train-mlogloss:0.046280 test-mlogloss:0.091385 
    ## [67] train-mlogloss:0.045609 test-mlogloss:0.090770 
    ## [68] train-mlogloss:0.044687 test-mlogloss:0.090097 
    ## [69] train-mlogloss:0.044178 test-mlogloss:0.090249 
    ## [70] train-mlogloss:0.043711 test-mlogloss:0.089977 
    ## [71] train-mlogloss:0.043139 test-mlogloss:0.089398 
    ## [72] train-mlogloss:0.042580 test-mlogloss:0.088702 
    ## [73] train-mlogloss:0.042063 test-mlogloss:0.088169 
    ## [74] train-mlogloss:0.041534 test-mlogloss:0.087914 
    ## [75] train-mlogloss:0.040850 test-mlogloss:0.086330 
    ## [76] train-mlogloss:0.040544 test-mlogloss:0.086387 
    ## [77] train-mlogloss:0.040133 test-mlogloss:0.086306 
    ## [78] train-mlogloss:0.039601 test-mlogloss:0.085747 
    ## [79] train-mlogloss:0.038918 test-mlogloss:0.085404 
    ## [80] train-mlogloss:0.038520 test-mlogloss:0.084772 
    ## [81] train-mlogloss:0.038188 test-mlogloss:0.084433 
    ## [82] train-mlogloss:0.037725 test-mlogloss:0.083896 
    ## [83] train-mlogloss:0.037230 test-mlogloss:0.083635 
    ## [84] train-mlogloss:0.036778 test-mlogloss:0.084065 
    ## [85] train-mlogloss:0.036342 test-mlogloss:0.083603 
    ## [86] train-mlogloss:0.036025 test-mlogloss:0.083622 
    ## [87] train-mlogloss:0.035601 test-mlogloss:0.083172 
    ## [88] train-mlogloss:0.035188 test-mlogloss:0.083192 
    ## [89] train-mlogloss:0.034816 test-mlogloss:0.083973 
    ## [90] train-mlogloss:0.034384 test-mlogloss:0.083362 
    ## [91] train-mlogloss:0.034077 test-mlogloss:0.083263 
    ## [92] train-mlogloss:0.033658 test-mlogloss:0.083287 
    ## [93] train-mlogloss:0.033343 test-mlogloss:0.083819 
    ## [94] train-mlogloss:0.032422 test-mlogloss:0.082636 
    ## [95] train-mlogloss:0.032042 test-mlogloss:0.081769 
    ## [96] train-mlogloss:0.031615 test-mlogloss:0.081415 
    ## [97] train-mlogloss:0.031335 test-mlogloss:0.081030 
    ## [98] train-mlogloss:0.030960 test-mlogloss:0.080596 
    ## [99] train-mlogloss:0.030554 test-mlogloss:0.081521 
    ## [100]    train-mlogloss:0.030226 test-mlogloss:0.081071 
    ## [101]    train-mlogloss:0.029892 test-mlogloss:0.080625 
    ## [102]    train-mlogloss:0.029514 test-mlogloss:0.080129 
    ## [103]    train-mlogloss:0.029214 test-mlogloss:0.079842 
    ## [104]    train-mlogloss:0.028867 test-mlogloss:0.079642 
    ## [105]    train-mlogloss:0.028690 test-mlogloss:0.079486 
    ## [106]    train-mlogloss:0.028341 test-mlogloss:0.079360 
    ## [107]    train-mlogloss:0.028115 test-mlogloss:0.079383 
    ## [108]    train-mlogloss:0.027818 test-mlogloss:0.079274 
    ## [109]    train-mlogloss:0.027366 test-mlogloss:0.078803 
    ## [110]    train-mlogloss:0.026980 test-mlogloss:0.077957 
    ## [111]    train-mlogloss:0.026720 test-mlogloss:0.078344 
    ## [112]    train-mlogloss:0.026528 test-mlogloss:0.078513 
    ## [113]    train-mlogloss:0.026263 test-mlogloss:0.078066 
    ## [114]    train-mlogloss:0.025974 test-mlogloss:0.077814 
    ## [115]    train-mlogloss:0.025569 test-mlogloss:0.077522 
    ## [116]    train-mlogloss:0.025260 test-mlogloss:0.077059 
    ## [117]    train-mlogloss:0.025043 test-mlogloss:0.077645 
    ## [118]    train-mlogloss:0.024822 test-mlogloss:0.077953 
    ## [119]    train-mlogloss:0.024641 test-mlogloss:0.077989 
    ## [120]    train-mlogloss:0.024420 test-mlogloss:0.077802 
    ## [121]    train-mlogloss:0.023585 test-mlogloss:0.075976 
    ## [122]    train-mlogloss:0.023287 test-mlogloss:0.075490 
    ## [123]    train-mlogloss:0.023052 test-mlogloss:0.074915 
    ## [124]    train-mlogloss:0.022861 test-mlogloss:0.074596 
    ## [125]    train-mlogloss:0.022672 test-mlogloss:0.074842 
    ## [126]    train-mlogloss:0.022382 test-mlogloss:0.074389 
    ## [127]    train-mlogloss:0.022146 test-mlogloss:0.074586 
    ## [128]    train-mlogloss:0.021882 test-mlogloss:0.074457 
    ## [129]    train-mlogloss:0.021724 test-mlogloss:0.074303 
    ## [130]    train-mlogloss:0.021475 test-mlogloss:0.074264 
    ## [131]    train-mlogloss:0.021367 test-mlogloss:0.074194 
    ## [132]    train-mlogloss:0.021196 test-mlogloss:0.074086 
    ## [133]    train-mlogloss:0.020948 test-mlogloss:0.073821 
    ## [134]    train-mlogloss:0.020819 test-mlogloss:0.073612 
    ## [135]    train-mlogloss:0.020653 test-mlogloss:0.073450 
    ## [136]    train-mlogloss:0.020459 test-mlogloss:0.072938 
    ## [137]    train-mlogloss:0.020263 test-mlogloss:0.072834 
    ## [138]    train-mlogloss:0.020118 test-mlogloss:0.073371 
    ## [139]    train-mlogloss:0.019993 test-mlogloss:0.073458 
    ## [140]    train-mlogloss:0.019753 test-mlogloss:0.074046 
    ## [141]    train-mlogloss:0.019557 test-mlogloss:0.074278 
    ## [142]    train-mlogloss:0.019390 test-mlogloss:0.073948 
    ## [143]    train-mlogloss:0.019161 test-mlogloss:0.074281 
    ## [144]    train-mlogloss:0.018952 test-mlogloss:0.073473 
    ## [145]    train-mlogloss:0.018785 test-mlogloss:0.073443 
    ## [146]    train-mlogloss:0.018619 test-mlogloss:0.073560 
    ## [147]    train-mlogloss:0.018415 test-mlogloss:0.073345 
    ## [148]    train-mlogloss:0.018248 test-mlogloss:0.072884 
    ## [149]    train-mlogloss:0.018056 test-mlogloss:0.072758 
    ## [150]    train-mlogloss:0.017891 test-mlogloss:0.073371 
    ## [151]    train-mlogloss:0.017747 test-mlogloss:0.073129 
    ## [152]    train-mlogloss:0.017590 test-mlogloss:0.073316 
    ## [153]    train-mlogloss:0.017315 test-mlogloss:0.073177 
    ## [154]    train-mlogloss:0.017052 test-mlogloss:0.072752 
    ## [155]    train-mlogloss:0.016759 test-mlogloss:0.072820 
    ## [156]    train-mlogloss:0.016610 test-mlogloss:0.072883 
    ## [157]    train-mlogloss:0.016448 test-mlogloss:0.072813 
    ## [158]    train-mlogloss:0.016307 test-mlogloss:0.072635 
    ## [159]    train-mlogloss:0.016178 test-mlogloss:0.072117 
    ## [160]    train-mlogloss:0.016071 test-mlogloss:0.072035 
    ## [161]    train-mlogloss:0.015901 test-mlogloss:0.071435 
    ## [162]    train-mlogloss:0.015741 test-mlogloss:0.071214 
    ## [163]    train-mlogloss:0.015620 test-mlogloss:0.071278 
    ## [164]    train-mlogloss:0.015471 test-mlogloss:0.071346 
    ## [165]    train-mlogloss:0.015269 test-mlogloss:0.071145 
    ## [166]    train-mlogloss:0.015139 test-mlogloss:0.071305 
    ## [167]    train-mlogloss:0.015006 test-mlogloss:0.070635 
    ## [168]    train-mlogloss:0.014889 test-mlogloss:0.070713 
    ## [169]    train-mlogloss:0.014743 test-mlogloss:0.070759 
    ## [170]    train-mlogloss:0.014625 test-mlogloss:0.070434 
    ## [171]    train-mlogloss:0.014529 test-mlogloss:0.070516 
    ## [172]    train-mlogloss:0.014429 test-mlogloss:0.070523 
    ## [173]    train-mlogloss:0.014353 test-mlogloss:0.070567 
    ## [174]    train-mlogloss:0.014214 test-mlogloss:0.070252 
    ## [175]    train-mlogloss:0.014055 test-mlogloss:0.070116 
    ## [176]    train-mlogloss:0.013941 test-mlogloss:0.070425 
    ## [177]    train-mlogloss:0.013578 test-mlogloss:0.069230 
    ## [178]    train-mlogloss:0.013433 test-mlogloss:0.069272 
    ## [179]    train-mlogloss:0.013334 test-mlogloss:0.069184 
    ## [180]    train-mlogloss:0.013208 test-mlogloss:0.068508 
    ## [181]    train-mlogloss:0.013011 test-mlogloss:0.068943 
    ## [182]    train-mlogloss:0.012871 test-mlogloss:0.069136 
    ## [183]    train-mlogloss:0.012769 test-mlogloss:0.069324 
    ## [184]    train-mlogloss:0.012670 test-mlogloss:0.069492 
    ## [185]    train-mlogloss:0.012580 test-mlogloss:0.069158 
    ## [186]    train-mlogloss:0.012446 test-mlogloss:0.068685 
    ## [187]    train-mlogloss:0.012354 test-mlogloss:0.068637 
    ## [188]    train-mlogloss:0.012229 test-mlogloss:0.068031 
    ## [189]    train-mlogloss:0.012170 test-mlogloss:0.067779 
    ## [190]    train-mlogloss:0.012096 test-mlogloss:0.067621 
    ## [191]    train-mlogloss:0.011982 test-mlogloss:0.067521 
    ## [192]    train-mlogloss:0.011872 test-mlogloss:0.067190 
    ## [193]    train-mlogloss:0.011759 test-mlogloss:0.067547 
    ## [194]    train-mlogloss:0.011641 test-mlogloss:0.067205 
    ## [195]    train-mlogloss:0.011545 test-mlogloss:0.067332 
    ## [196]    train-mlogloss:0.011492 test-mlogloss:0.067276 
    ## [197]    train-mlogloss:0.011409 test-mlogloss:0.067190 
    ## [198]    train-mlogloss:0.011301 test-mlogloss:0.067195 
    ## [199]    train-mlogloss:0.011208 test-mlogloss:0.067080 
    ## [200]    train-mlogloss:0.011116 test-mlogloss:0.067001

``` r
prediction_7 <- predict(life_predicted_7, data.test_3)
summary(prediction_7)
```

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ##   1.000   1.000   1.000   1.073   1.000   2.000

``` r
# values are (1,2) but we need (0,1)

prediction_7 <- as.numeric(prediction_7 > 1.5) 
summary(prediction_7)
```

    ##    Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
    ## 0.00000 0.00000 0.00000 0.07263 0.00000 1.00000

``` r
# this step to convert values  (1,2) to (0,1) in prediction_7
```

``` r
results_7 <- data.frame(prediction_7, test_3_label)

accuracy_7  <- paste("Accuracy of XGBoost Model is:", sum(prediction_7 == test_3_label)/length(prediction_7))
xgboostXGB <- sum(prediction_7 == test_3_label)/length(prediction_7)
```

``` r
confusionMatrix(table(results_7))
```

    ## Confusion Matrix and Statistics
    ## 
    ##             test_3_label
    ## prediction_7   0   1
    ##            0 650  14
    ##            1   4  48
    ##                                          
    ##                Accuracy : 0.9749         
    ##                  95% CI : (0.9606, 0.985)
    ##     No Information Rate : 0.9134         
    ##     P-Value [Acc > NIR] : 1.1e-11        
    ##                                          
    ##                   Kappa : 0.8286         
    ##  Mcnemar's Test P-Value : 0.03389        
    ##                                          
    ##             Sensitivity : 0.9939         
    ##             Specificity : 0.7742         
    ##          Pos Pred Value : 0.9789         
    ##          Neg Pred Value : 0.9231         
    ##              Prevalence : 0.9134         
    ##          Detection Rate : 0.9078         
    ##    Detection Prevalence : 0.9274         
    ##       Balanced Accuracy : 0.8840         
    ##                                          
    ##        'Positive' Class : 0              
    ## 

We get an accuracy of 97.49% which is best. nrounds = 200 really works, we could further improve accuracy with higher values of nrounds.

------------------------------------------------------------------------
