# ExoSolar-Analysis
Predictive and Exploratory Analysis of Exoplanets

One of the most fundamental questions is “are we alone?” Last few years only scientists have
discovered thousands of planets around other stars, and most of those planets are not like Earth
(they're big and gaseous, like Jupiter). And most systems unlike our solar system (big planets orbit
close to their parent star, whereas in Earth's solar system, the large planets orbit further out).
The ultimate goal is to see whether the exoplanet can be termed with possible life which we know
how to interpret. In habitable zone (Goldilocks zone), the region around a star where a planet with
sufficient atmospheric pressure can maintain liquid water on its surface, life has most chance of
arising. With the help of this dataset we have tried to analyze these planets, their host star, their
size, mass, radius , etc. We have not taken into considerations the atmospheric conditions in
planets, and their location inside the galaxy.
Exoplanet definition: An exoplanet is a planet outside our solar system, usually orbiting another
star.

Data
Our first glimpse at planets outside of the solar system we call home came in 1992 when several
terrestrial-mass planets were detected orbiting the pulsar PSR B1257+12. In this dataset, we can
explore by analyzing the characteristics of all discovered exoplanets (plus some familiar faces like
Mars, Saturn, and even Earth). Data fields include planet and host star attributes, discovery
methods and date of discovery.

Dataset Overview
This data was taken from
https://github.com/OpenExoplanetCatalogue/oec_tables/tree/master/comma_separated. The
dataset contains 3584 observations. An other data with habitable planets was taken from
http://openexoplanetcatalogue.com/systems/?filters=habitable. We have merged the both
these datasets.

Scope Of Inference – Generalizability
The scope of inference or the population of interest here are the Exoplanets that are discovered
using multiple types of imaging techniques. Given the variables such as the Age of star, or
disatnce of planet from host star, or period of days, etc. we can predict other variables. The thing
to consider here is that the distances are too large to just directly observe any life( the types we
know or the one’s which are different ), so basically astronomers have to go through a lot of data,
old and new to check out any new planet under life possibility. Usually there is a bias towards
planets with orbit in habitable zone and earth mass but recent study and vast uncertainity in
universe prevents such simple possibilities. So to build a model which could cover a wide margin
of dependencies or variables here is our aim. From our exploratory analysis we prove the
obvious assumptions for cateogarizing life as we think it can be. In simple terms we observe the
relationships between variables using plots and find how each and every variable relates to other.

Scope Of Inference – Causality
We have considered most of the variables, and we find out how each relates to one or more. We
hope to see whether planets considered with Life in our data are nearly earth size, or have a star
comparable to our star, or other variables like the semi-major axis has a relation to period of days
of rotation. These are some possible inference which we hope to analyze here. We merged an other
dataset with a variable with Life = 1 and merged it in our data to perform the analysis.

Predictive Analysis
After doing the exploratory analysis and looking at how the variables relates to each other now
we come on classification. The problem with classifying the planet as with Life or without it
includes many variables. Astronomers usually had to look at the other planets with same values,
or had to check on some formula. The problem with first part arises because of big dataset, in
past they had small data, but now there is a boom in data. For second part having a formula with
so many variables will not be perfect. So our aim is to make a model that can classify our data
and give us correct prediction. 

After the exploratory analysis, where we have observed the relation between attributes, our task
is now to make a Model which can classify a Planet with Life(1) or No Life(0). For this we first
have to fill all the missing data, as most of the models we are working with don't work well or at
all with missing values. We could not remove the missing columns as they were unevenly
distributed across whole dataset. We have used Package Amelia for filling out the missing
values. As our model will need supervised learning machine algorithm, we are using seven of
them here :

1. KNN Model - When we need classification for a new data, the KNN algorithm goes through the
entire dataset to find the k-nearest values to the new data values, or the k number of values most
similar to the new record, and then outputs the mode (most frequent class) for a classification
problem. The value of k is specified by user.

2. Decision Tree Model - it is a type of supervised learning algorithm (having a pre-defined target
variable). It works for both categorical and continuous input and output variables. Here we split
the population or sample into two or more homogeneous set based on most significant splitter /
differentiator in input variables.

3. Random Forest Model - Random Forest (multiple learners) is an improvement over bagged
decision trees (a single learner). It can handle large data set with higher dimensionality. It can
handle thousands of input variables and identify most significant variables so it is considered as
one of the dimensionality reduction methods.

4. Naive Bayes - To calculate the probability that an event will occur, given that another event has
already occurred, we use Bayes’ Theorem. To calculate the probability of an outcome given the
value of some variable. Naive Bayes can handle missing data. ‘naive’ because it assumes that all
the variables are independent of each other. This should not work very well with our model. But
let's look at it too.

5. Logistic Regression Model - Logistic regression predictions are discrete values(Life or no Life).
ITthe output is in the form of probabilities of the default class. As it is a probability, the output
lies in the range of 0-1. The output y-value is generated by log transforming the x-value. Then
we force this probability into a binary classification.

6. GBM Model - A boosting algorithm.It is a machine learning technique for regression and
classification problems.It produces a prediction model in the form of an ensemble of weak
prediction models, typically decision trees.

7. Extreme Gradient Boosting, XGBoost Model - It does parallel computation on a single machine.
This makes xgboost at least 10 times faster than existing gradient boosting implementations. It
supports various objective functions, including regression, classification and ranking. It only
works with numeric vectors.

Accuracy of our Models:
"Accuracy of KNN Model is: 0.914804469273743"
"Accuracy of Decision Tree Model is: 0.951117318435754"
"Accuracy of Random Forest Model is: 0.955307262569832"
"Accuracy of Naive Bayes is: 0.822625698324022"
"Accuracy of Logistic Regression Model is: 0.912011173184358"
"Accuracy of GBM Model is: 0.970670391061452"
"Accuracy of XGBoost Model is: 0.974860335195531"

Conclusion
After having a model with 97.48% accuracy we are pretty sure that we can apply it on unseen
data. So, we will wait for new data to come by. The best thing about our XGBoost model is that
we could further try and increase it’s accuracy if we experiment with the parameters. Also we
can increase the number of trees.
With our models we saw that all of them considered most of the variables to be significant. Also
the categorical variables, being only three in our dataset, played a huge role. We saw with
exploratory analysis that finding a planet with life is a hard job as most of the exoplanets are
either too large, or in a large orbit, or nearly close to but not in habitable zone, so many factors
which can decrease the odds in favour of life. But our models can definitely do a good job.
Future telescopes like James Webb Telescope(JWT), Thirty Metre Telescope, WEBB Space
telescope, will give more parameters to work upon. They will generate data on atmosphere,
water presence, etc. Which we will use to further the accuracy but with higher confidence.
We can further increase the accuracy of the model if we use Deep Neural Network using H2O,
which have proven to be better by people in the field, so this will be our future approach.
