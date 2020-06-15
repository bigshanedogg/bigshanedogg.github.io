---
layout: post
date: 2019-02-04 19:36
title: Fuzzy C-Means
description: When data point belongs to only one cluster
comments: True
category: 
- summary
tags:
- machine_learning
- clustering
- python
---
### A. Feature
- Each data point can belong to more than one cluster (Soft Clustering).
    - The degree of belonging to each centroid is represented by scalar between 0 and 1.
    - It seems proper to cluster which the boundary is ambiguous such as natural language task.
- Likewise other prototype-based clustering such as K-means family, Fuzzy C-means clustering finds optimal status through repetition.
<br>

<!--more-->

### B. Process
1. Choose a number of clusters, K
2. Assign randomly to each point coefficients for being in the clusters.
3. Compute the centroid for each cluster.
4. For each point, compute its coefficients of being in the clusters.
5. Repeat 3 & 4 until the coefficients' change between two iterations is no more than the given sensitivity threshold
<br>

### C. Option
- distance metric
    - cosine distance
    - euclidean distance
    - mahalanobis distance
<br>

### Python example
#### 1. Generate sample data
{% highlight python %}
# Define three cluster centers
centers = [[4, 2],
           [1, 7],
           [5, 6]]
# Define three cluster sigmas in x and y, respectively
sigmas = [[0.8, 0.3],
          [0.3, 0.5],
          [1.1, 0.7]]

# Generate test data
np.random.seed(42)  # Set seed for reproducibility
xpts = np.zeros(1)
ypts = np.zeros(1)
labels = np.zeros(1)
for i, ((xmu, ymu), (xsigma, ysigma)) in enumerate(zip(centers, sigmas)):
    xpts = np.hstack((xpts, np.random.standard_normal(200) * xsigma + xmu))
    ypts = np.hstack((ypts, np.random.standard_normal(200) * ysigma + ymu))
    labels = np.hstack((labels, np.ones(200) * i))
alldata = np.vstack((xpts, ypts))
{% endhighlight %}
<br>

#### 2. Clustering w/ fuzzy_cmeans class
- [python implementatio](https://github.com/bigshanedogg/python-fuzzy-c-means) is posted on github
{% highlight python linenos %}
n_clusters = 3
m = 2
metric = "euclidean" # "cosine" # "mahalanobis"
optimization = ["weights"] # ["weights", "centers", "error"]

fcm_model = fuzzy_cmeans(n_clusters=n_clusters, m=m, metric=metric, min_error_change=1e-4, min_weights_change=1e-6, max_iter=500, verbose=False)
fcm_model.fit(X, optimization=optimization)
{% endhighlight %}
<br>
<!--language-->

<!--footer-->
<br>

#### Reference and Implementation:
- paper: [FCM: THE FUZZY c-MEANS CLUSTERING ALGORITHM](https://staff.fmi.uvt.ro/~daniela.zaharie/dm2018/RO/TemeProiecte/Biblio/FuzzyCMeans/FCM%20-%20The%20Fuzzy%20c-Means%20Clustering%20Algorithm.pdf), DEC 1984
- implementation: [python implementation](https://github.com/bigshanedogg/python-fuzzy-c-means)
