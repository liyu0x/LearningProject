# Introduction of ML

## What's Machine Learning?

### 1. Arthur Samuel's Definition

（阿瑟·塞缪尔）

"the field of study that gives computers the ability to learn without being explicitly programmed".

让计算机再不给定明确算法的情况下具有学习能力的研究领域。



### 2. Tom Mitchell's Definition

（汤姆·M·米切尔）

 "A computer program is said to learn from experience E with respect to some class of tasks T and performance measure P, if its performance at tasks in T, as measured by P, improves with experience E."

一个计算机程序从关于某些任务T的经验中P学习，性能表现P，如果它在任务T中的性能P随着经验E提升而提升。

#### example

play checkers（跳棋）

E = the experience of playing many games of checkers

T = the task of playing checkers

P = the probability that the program will win the next game



### 3. how many types



# Unsupervised Learning

## introduction

Given unlabeled data will separate to many clusters by Unsupervised Learning algorithm.

In other words, The algorithem can find some "structure" in given data.

## type

### Clustering Algorithm

#### application

1. Market segmentation
2. Social network analysis
3. Organize computing clusters
4. Astronomical data analysis



#### K-means algorithm

##### feature

1. iterative

##### term

1. cluster centroids（簇中心，随机生成的）



##### principle

the algorithm only do two things:

1. cluster assignment
   It's going through each of the exmaples depending on whether it's closer to a cluster centroids and going to assign each of the eamples to one of the cluster centroids.
2. move centroids step
   It's moving respectively the centroids to the average location in themselves corresponding exmaples.
3. Repeat the above steps utils the centroids do not change further and these example do not change themselves centroids.

##### implemention step

1. input:
   1. K (number of clusters)
   2. Training Set.  $x^{(i)} \in \R^{n}$(drop $x_0=1$ convention)
   
2. pseudocode:
   Ramdonly initialize K cluster centroids $\mu_1,\mu_2,...,\mu_k \in \R^n$
   Repeat{
   
   % This is first step called "Cluster assignment step",  the step wiil minimize J with $c^{(1)},...,c^{(m)}$ and holding $\mu_1,...,\mu_K$ fixed. 
   
   ​		for $i = 10$ to $m$
   ​     	$c^{(i)}$ := index(from 1 to $K$) of cluster centroid closest to $x^{(i)}$
   ​     	($c^{(i)} = \min_{k}||x^{(i)}-\mu_k||^2$)
   
   
   
   
   
   % This is second step called "Move centroid",  the step will minimize J wrt $\mu_1,...,\mu_K$
   
   ​		for $k = 1$ to $K$
   ​			$\mu_k$:=average(mean) of points assigned to cluster k
   }



##### problem & solution

1. If there is a cluster centroid no potins with zero potins assigned to id.
   Commonly, You can eliminate that cluster centroids, then you end up with k-1 clusters. But You really need k clusters, you should randomly reinitialize that cluster centroid.



##### optimization objective

 $c^{(i)}=$index of cluster $(1,2,3,...,K)$ to which example $x^{(i)}$ is currently assigned.（$x^{(i)}$的当前簇索引）

$\mu_k=$ cluster centroid $k$ ($\mu_k \in \R^n$)

$\displaystyle \mu_{c^{(i)}} = $ cluster centroid of cluster to which exmaple $x^{(i)}$ has been assgined. 

following is the <b>cost function</b>:

$\displaystyle J(c^{(1)},...,c^{(m)},\mu_1,...,\mu_K)=\frac{1}{m}\sum^m_{i=1}||x^{(i)}-\mu_{c^{(i)}}||^2$

to find min($c^{(1)},...,c^{(m)},\mu_1,...,\mu_K$) to minimize the function.



##### how to initialize centroids

1. Should have $K < m$
2. Randomly pick $K$ training exmaples.
3. Set $\mu_1,...,\mu_K$ equal to these K examples.(ex: $\mu_1=x^{(i)},\mu_2=x^{(j)},...$)



##### how to avoid stucking in local optimal

try multiple, random initializations not just random initialization once

(此方法用于cluster不多的情况下，如果cluster数量多，则需要指定centroids而不是随机选择)

ex:



For i = 1 to 100{

​	Randomly initialize K-means.

​	Run K-means. Get $c^{(i)}.....,\mu_i......$

​	Compute cost function(distortion)

​			$J(c^{(1)},...,c^{(m)},\mu_1,...,\mu_K)$

}



pick clustering that gave lowest cost J.



##### how to chose the number of clusters

1. Elbow method:

![](.\image\微信截图_20210211171226.png)



2. 取决于实际场景
   ![](.\image\微信截图_20210211173003.png)





### Dimensionality Reduction

#### motivation

##### Data Compression

advantage: <b>use up less memory and disk space</b>, and <b>speed up learning algorithm</b>

in other words, how to chose k?



Reduce data from 2D to 1D:

process:

$x^{(1)} \in R^2 \to z_1 \\ x^{(2)} \in R^2 \to z_2
\\
......\\
x^{(m)} \in R^2 \to z_m$



##### Data Visualization

Simply, Computers  only can show 1-3D plots, so in order to graphics, the higher-D data set must be D-reduction.







#### PCA(主成分分析)

Principal Component Analysis

PCA tries to find a lower dimensional surface,let the distance(projection error) between the surface and each of the  example is minimal.



Reudce from 2-D to 1-D: Find a direction (a vector $u^{(1)} \in R^n$) onto which to project the data so as to minimize the projection error.



Reduce from n-D to k-D: Find $k$ vectors $\mu^{(1)},\mu^{(2)},\mu^{(3)},...,\mu^{(k)}$ onto which to project the data, so as to minimize the projection error.



![](.\image\微信截图_20210214133626.png)



1. Data preprocessing

Training Set: $x^{(1)},x^{(2)},x^{(3)},...,x^{(m)}$

Preprocessing (feature scaling/mean normalization)：

$\displaystyle \mu_j=\frac{1}{m}\sum^m_{i=1}x_j^{(i)}$

replace each $x_j^{(i)}$ with $x_j-\mu_j$

If different features on different scales (e.g., $x_1$=size of house, $x_2$=number of bedrooms)，scale features to have comparable range of value.



2. PCA algorithm

Reduce data from n-D to k-D

Compute "convariance martix(协方差矩阵)"
$\displaystyle \Sigma=\frac{1}{m}\sum^n_{i=1}(x^{(i)})(x^{(i)})^T$

Compute "eigenvectors(特征向量)" of matrix $\Sigma$

$[U,S,V]=svd(Sigma);$  svd = singular value decomposition(奇异值分解)



3. From $[U,S,V]=svd(Sigma)$，we get:

$\displaystyle U=[u^{(1)},u^{(2)},u^{(3)},...,u^{(n)}] \in \R^{n * n}$

take first K columns of the U matrix, we get

$\displaystyle X \in \R ^n \to Z \in \R^k$,  called $\R_{reduce}$

$\displaystyle Z=[(\R_{reduce})^T * X] \in \R^k$



##### reconstruction from Compressed Representation

$\displaystyle Z=[(\R_{reduce})^T * X] \to X_{approx} = (\R_{reduce}) * Z$



##### numebr of Principal Components

in other words, how to chose k?

average squared projection error:$\frac{1}{m}\sum^m_{i=1}||x^{(i)}-x^{(i)}_{approx}||^2$

total variation in the data: $\frac{1}{m}\sum^m_{i=1}||x^{(i)}||^2$

typeically, choose $k$ to be smallest value so that

$\displaystyle \frac{\frac{1}{m}\sum^m_{i=1}||x^{(i)}-x^{(i)}_{approx}||^2}{\frac{1}{m}\sum^m_{i=1}||x^{(i)}||^2} \le 0.01 (1\%)$

"99% of variance is ratained"



if use svd function,

$[U,S,V]=svd(Sigma)$

S is a diagonal matric, $s_{11}, s_{22},...,s_{nn}$

for given k,

$\displaystyle 1 - \frac{\sum^k_{i=1}S_{ii}}{\sum^n_{i=1}S_{ii}} \le 0.01$



##### supervised learning speedup

![](..\image\微信截图_20210214215517.png)





##### bad use of PCA

1. to prevent overfitting

env:

​	Use $z^{(i)}$ instead of $x^{(i)}$ to reduce the number of features to $k < n$

​	Thus, fewer features, less likely to overfit.

result:

​	It's a bad way to  prevent overfitting, Please use regularization instead.



##### when to use pca?

Before implementing PCA, first try runing whatever you wnat to doing with the original/raw data ($x^{(i)}$),Only if that doesn't do what you want(algorithm run slowly or need huge memory etc.), then implement PCA and consider use $z^{(i)}$.

 


## Anomaly Detection(异常检测)

这里的“异常”不是算法的异常或者程序的异常，指的是数据的“异常",及检查非常规数据。

 ![image-20210219155511670](Machine Learning - Andrew Ng(吴恩达).assets/image-20210219155511670.png)



![image-20210219160837747](Machine Learning - Andrew Ng(吴恩达).assets/image-20210219160837747.png)



$p$ is a function check probability of X.



application: Detect whether the user's account has been stolen. 



### Gaussian Distribution(Normal Distribution)

Say $x \in \R$，If $x$ is a distributed Gaussian with mean $\mu$, variance $\sigma^2$

$X \sim N(\mu, \sigma^2) $

$\displaystyle f(x)=\frac{1}{\sigma \sqrt{2\pi}}e^{-\frac{(x-\mu)^2}{2\sigma^2}}$



how to chose parameters?

$\mu = \frac{1}{m}\sum^m_{i=1}x^{(i)}$ 

$\sigma^2=\frac{1}{m}\sum^m_{i=1}(x^{(i)}-\mu)^2$



### algorithm implemention

1. Choose features $x_i$ that you think might be indicative of anomalous examples.
2. Fit parameters $\mu_1,...,\mu_n,\sigma^2_1,...,\sigma^2_n$
   $\displaystyle \mu_j=\frac{1}{m}\sum^m_{i=1}x_j^{(i)}$
   $\displaystyle \sigma^2_j=\frac{1}{m}\sum^m_{i=1}(x^{(i)}_j-\mu_j)^2$
3. Give new example $x$, compute $p(x)$：
   $\displaystyle p(x)=\Pi^n_{(j=1)}p(x_j;\mu_j,\sigma^2_j)=\Pi^n_{j=1}\frac{1}{\sqrt{2\pi}\sigma_j}exp(-\frac{(x_j-u_j)^2}{2\sigma^2_j})$
   Anomaly if $p(x) < \varepsilon $

### building an ADS

ADS(Anomoly Detection System)

![image-20210219203229429](Machine Learning - Andrew Ng(吴恩达).assets/image-20210219203229429.png)

![image-20210219205747048](Machine Learning - Andrew Ng(吴恩达).assets/image-20210219205747048.png)

### Supervised Learning VS. ADS



<b>Distinguish：</b>



![image-20210219211823160](Machine Learning - Andrew Ng(吴恩达).assets/image-20210219211823160.png)

总结下上面的图片，就是AD适合异常情况过多，SL适合异常情况较少（此处的多少不是指的数量而是类型）

然后，AD适合positive较少的例子



<b>Applications：</b>

![image-20210219212816291](Machine Learning - Andrew Ng(吴恩达).assets/image-20210219212816291.png)

总结下，如果你明天遇到的异常很可能是之前没有见过的异常，那么就使用AD



### Choosing What Feartures to Use.



![image-20210219215122122](Machine Learning - Andrew Ng(吴恩达).assets/image-20210219215122122.png)

For Non-gaussian features, use the mathematical methods above.



![image-20210219221220277](Machine Learning - Andrew Ng(吴恩达).assets/image-20210219221220277.png)

Create new features.Like following example:

![image-20210219221737077](Machine Learning - Andrew Ng(吴恩达).assets/image-20210219221737077.png)

Creating new features can easily capture new feature combinations of anomolous data.



### Multivariate Gaussian (Normal) distribution

$x \in \R^n$. Don't model $p(x_1),p(x_2),...,etc.$ separately.

Model $p(x)$ all in one go.

Parameters: $\mu \in \R^n$，$\Sigma \in \R^{n*n}$(covariance matrix)



![image-20210220152819625](Machine Learning - Andrew Ng(吴恩达).assets/image-20210220152819625.png)

![image-20210220152914069](Machine Learning - Andrew Ng(吴恩达).assets/image-20210220152914069.png)

![image-20210220152940715](Machine Learning - Andrew Ng(吴恩达).assets/image-20210220152940715.png)



![image-20210220153912319](Machine Learning - Andrew Ng(吴恩达).assets/image-20210220153912319.png)





### Recommender system



![image-20210220165421955](Machine Learning - Andrew Ng(吴恩达).assets/image-20210220165421955.png)





#### 

