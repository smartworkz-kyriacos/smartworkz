+++
author = "Kyriakos Antoniadis"
bg_image = "/images/banner/banner-1.jpg"
categories = ["Human-Learn"]
date = 2022-11-06T23:00:00Z
description = ""
image = "/images/human-learn.jpg"
tags = []
title = "Human-Learn"
type = "post"

+++
## Human-Learn

**Create a Human Learning Model by Drawing**

Excerpt reference from [Khuyen Tran](https://towardsdatascience.com/human-learn-create-rules-by-drawing-on-the-dataset-bcbca229f00)

### Use your Domain Knowledge to Label your Data

![](/images/1_aG1xWjuHYRDP7cBSKwWnHw.gif)

Nowadays, data scientists often give machine learning model data with labels so that they can figure out the rules. These rules can be used to predict the labels of new data.

![](/images/1_lAG4xlZWtRQcFkrt2dkoTg.webp)

This is convenient, but some information may be lost in this process. It is also difficult to know what is going on under the hood and why the machine learning model comes up with a particular prediction.

Instead of letting a machine learning model figure out everything, is there a way that we can use our domain knowledge to set the rules for data labelling?

![](/images/1_5NLMRismhkeXze-BdO5o0A.webp)

That can be done with human-learn.

### What is human-learn?

[Human-learn](https://github.com/koaning/human-learn/) is a tool that allows you to set the rules for data labelling using interactive drawings and custom models. In this article, we will explore how to create a model with interactive drawings using human-learn.

To install human-learn, type

    pip install human-learn

We use Iris data from sklearn to show how human-learn works.

    from sklearn.datasets import load_iris
    from sklearn.model_selection import train_test_split
    import pandas as pd
    #Load data
    X, y = load_iris(return_X_y=True, as_frame=True)
    X.columns = ['sepal_length', 'sepal_width', 'petal_length', 'petal_width']
    #Train test split
    X_train, X_test, y_train, y_test = train_test_split(X, y, random_state=1)
    #Concatenate features and labels of the training data
    train = pd.concat([X_train, pd.DataFrame(y_train)], axis=1)
    train

### Interactive Drawing

#### Drawing

Human-learn allows you to draw over your datasets, and then use your drawings to convert them to models. To demonstrate how this can be useful, imagine you create a scatter plot of your dataset like below:

![](/images/1_C75bN2iFe8AqVN05XoTk1w.webp)

When looking at the plot above, you can see how you can split them into 3 different regions like the below:

![](/images/1_eMnFM9ceLquSPQIlLbuOqw.webp)

However, it can be difficult to write your drawing as rules and put them into a function. That is when humans learn interactive drawing comes in handy.

Now you can start drawing over your chart like below!

![](/images/1_aG1xWjuHYRDP7cBSKwWnHw.gif)

**Drawing instruction**: Use double-click to start drawing a polygon. Then click to create an edge for the polygon. Use double-click again to stop drawing the current polygon. If this is not clear to you, you can watch a video [here](https://www.loom.com/share/5f622a6c40504f2094f4b472fe2b04d0).

We also do the same things with other columns:

![](/images/sepal_files/1_SunRl4nEdKiT173hM2qmeg.png)

![](/images/sepal_files/1_IaRVbTP-D8y7vv-9xghUoQ.png)

![](/images/sepal_files/1__SKQDEhqDNdaWkoK680AUw.png)

### Creating a Model and Predict

Once we finish drawing over our dataset, we can create a model using these drawings:

Cool! By feeding our drawings to the class `InteractiveClassifier` , we can use similar methods to sklearn’s models such as `fit` and `predict_proba` .

The shape of the predictions `preds`is `(150, 3)` . Let’s peek at the first 5 rows of the `preds` :

Note that `predict_proba` gives the probability that a sample has a specific label. For example, the first prediction of `[5.71326574e-01 4.28530630e-01 1.42795945e-04]` means that there is a 57.13% chance that the sample has label 1, a 42.85% chance that the sample has label 2, and a 0.014% chance that the sample has label 0.

### Predict New Data

Now let’s use our model to predict new data and compare the prediction with the real label.

Output:

    The prediction is 0The real label is 0

Nice! The prediction is the same as the real label!

### Interpret the Result

To understand how the model comes up with that prediction, let’s visualize where our new sample is in the scatter plots that we drew previously.

Start with creating a function to visualize our new sample.

Use the function above to plot a new sample on the `petal_length` vs `petal_width` plot whose_points are colo_red by the probability that t_ey have label 0_

![](/images/sepal_files/1_TWiLM1N-yXsetittXBxJ1Q.gif)

![](/images/sepal_files/1_TWiLM1N-yXsetittXBxJ1Q.png)

Note that the **yellow dots** indicate the **high probability** that these dots have a **label of 0**. The more purple the dots are, the less likely that these dots have a label 0.

We can see that the red dot (the new sample) is in the region with many yellow dots. But are there similar patterns in other plots? Let’s find out by applying the same function to other pairs of columns.

![](/images/sepal_files/1__kATnN0TD0bZcQ3yi0m2dg.png)

![](/images/sepal_files/1_s7EFcEElMbz0QnTksd8DnQ.png)

![](/images/sepal_files/1_h3bE7gJ4wj-Zrkasou7bKA.png)

In all plots, we can see the red dot are in the region with many yellow dots! That explains why the model predicts the new sample to have a label 0. Pretty cool, isn’t it?

### Predict and Evaluate Test Data

Now let’s use the model to predict all samples in the test data and evaluate its performance. Start evaluating using a confusion matrix:

    array([[13,  0,  0],       [ 0, 15,  1],       [ 0,  0,  9]])

Wow cool! There are 37 true predictions and only 1 false prediction.

We can also evaluate the results using the F1 score:

    0.9736842105263158

Nice!

### Conclusion

Congratulations! You have just learned how to generate rules to label your data by drawing on the dataset. This is not to say you should eliminate machine learning models but incorporate some kind of human supervision when working with data.

I hope human-learn will encourage you to visualize your data before applying any machine learning algorithms. In the next article, I will show you other ways to create rules using human-learn.

The source code of this article could be found [here]()

For more info, see article from Vincent D Warmerdam:

* [Human-Learn](https://koaning.github.io/human-learn/index.html)
* [At the BLIPZ](https://www.blipz.io/speakers/vincent-warmerdam)