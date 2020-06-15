---
layout: post
date: 2019-02-08 21:41
title: Hierarchical Agglomerative Clustering 
description: Represent clusters into tree
comments: True
category: 
- machine_learning
- summary
tags:
- machine_learning
- clustering
- python
---
### A. Feature
- Make hierarchical tree of clusters, where each depth has different number of clusters and the proper depth can be chose depending on total number of clusters.
- A child node imply one of the meanings parent node indicates in detail.
    - Assuming clustering sentences, Keywords extracted from each cluster(node) can be regarded as hierachchical topic tree from major topic to sub topics by its parent-child relationship.
- The results can vary greatly depending on which metric you use to calculate the distance.
  
 <!--more-->

### B. Process
1. Assume n data is n clusters.
2. Find two clusters with the closest distance and combine them into one cluster.
3. Repeat #2 until one cluster remains
    

### C. Option
- distance metric
    - cosine distance
    - euclidean distance
- linkage (points you use to calculate the distance)
    - complete(max): the distance between the furthest points of each cluster
    - single(min): the distance between the closest points of each cluster
    - ward(average): the distance between average vectors of points of each cluster

### Python example
#### 1. Clustering w/ sickit-learn
```python
import numpy as np
from sklearn.cluster import AgglomerativeClustering

data = np.random.rand(500, 64)
n_cluster = 20
cosine_cl_model = AgglomerativeClustering(affinity="cosine", n_clusters=n_cluster, linkage="complete")
cosine_cl_model.fit(data)
cosine_label_list = list(cosine_cl_model.labels_)
```

#### 2. Make Tree
```python
# make tree
import itertools
ii = itertools.count(data.shape[0])
tree = [[] for i in range(2*len(data)-1)]
for x in cosine_cl_model.children_:
    parent_node = next(ii)
    tree[parent_node].append(x[0]) # left child
    tree[parent_node].append(x[1]) # left child

# get depth list
depth_list = [[] for i in range(len(data))]
root_node = len(data) + len(cosine_cl_model.children_) - 1
get_depth(root_node, 0)
```


#### 3. Arrange Cluster by Depth
```python
# get max depth
max_depth = None
for depth, node_list in enumerate(depth_list):
    if len(node_list)>0:
        continue
    else:
        max_depth = depth-1
        break
            
# number of clusters by depth
cluster_list = [[] for i in range(max_depth+1)]
for depth in range(0, max_depth+1):
    if depth==0:
        cluster_node = set(depth_list[depth])
        cluster_list[depth] = list(cluster_node)
    else:
        #  cluster node of previous depth + cluster node of current depth - cluster node of previous depth which have child node
        cluster_node = set(cluster_list[depth-1] + depth_list[depth])
        parent_node = set([node for node in depth_list[depth-1] if len(tree[node])>0])
        cluster_node = cluster_node.difference(parent_node)
        cluster_list[depth] = list(cluster_node)
    print("depth {depth}: {cluster_num}".format(depth=depth, cluster_num=len(cluster_node)))
```

#### * reference: #TODO
- reference: [Data Mining] Hierarchical agglomerative Clustering
clustering overview posts: Hierarchical agglomerative Clustering

<!--language-->