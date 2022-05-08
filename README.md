# decision-tree and random forests

> library(rpart)
> #grow tree
> model1<-rpart(Return~profile+num_dragons+num_friends_invited+gender+age+num_days,method="class",data=chasingdragons)

printcp(model1)#display the results
plotcp(model1)#visulize cross-validation results
summary(model1)#detailed summary of thresholds picked to transform to binary

#plot tree
plot(model1,uniform=TRUE,main="Classfication Tree for chasing dragons")
text(model1,use.n=TRUE,all=TRUE,cex=.8)






> require(ggplot2)
> data(diamonds)
> head(diamonds)
# A tibble: 6 x 10
  carat cut       color clarity depth table price     x     y     z
  <dbl> <ord>     <ord> <ord>   <dbl> <dbl> <int> <dbl> <dbl> <dbl>
1  0.23 Ideal     E     SI2      61.5    55   326  3.95  3.98  2.43
2  0.21 Premium   E     SI1      59.8    61   326  3.89  3.84  2.31
3  0.23 Good      E     VS1      56.9    65   327  4.05  4.07  2.31
4  0.29 Premium   I     VS2      62.4    58   334  4.2   4.23  2.63
5  0.31 Good      J     SI2      63.3    58   335  4.34  4.35  2.75
6  0.24 Very Good J     VVS2     62.8    57   336  3.94  3.96  2.48
> #plot a histogram with a line marking 12,000
> ggplot(diamonds)+geom_histogram(aes(x=price))+geom_vline(xintercept=12000)
`stat_bin()` using `bins = 30`. Pick better value with `binwidth`.
  ![image](https://user-images.githubusercontent.com/47287187/167313056-ca4da1ac-2878-43a6-bca0-661098f78494.png)

 > #build a TRUE/FALSE variable indicating if the price is above our threshold
> diamonds$Expensive<-ifelse(diamonds$price<=12000,1,0)
> head(diamonds)
# A tibble: 6 x 11
  carat cut       color clarity depth table price     x     y     z Expensive
  <dbl> <ord>     <ord> <ord>   <dbl> <dbl> <int> <dbl> <dbl> <dbl>     <dbl>
1  0.23 Ideal     E     SI2      61.5    55   326  3.95  3.98  2.43         1
2  0.21 Premium   E     SI1      59.8    61   326  3.89  3.84  2.31         1
3  0.23 Good      E     VS1      56.9    65   327  4.05  4.07  2.31         1
4  0.29 Premium   I     VS2      62.4    58   334  4.2   4.23  2.63         1
5  0.31 Good      J     SI2      63.3    58   335  4.34  4.35  2.75         1
6  0.24 Very Good J     VVS2     62.8    57   336  3.94  3.96  2.48         1
> #get rid of the price column
> diamonds$price<-NULL
> ##glmnet
> require(glmnet)
载入需要的程辑包：glmnet
载入需要的程辑包：Matrix

载入程辑包：‘Matrix’

The following objects are masked from ‘package:tidyr’:

    expand, pack, unpack

Loaded glmnet 4.1-4
Warning message:
程辑包‘glmnet’是用R版本4.1.3 来建造的 
> #build the predicator matrix, we are leaving out the last column which is our response
> x<-model.matrix(~.,diamonds[,-ncol(diamonds)])
> #build the response vector
> y<-as.matrix(diamonds$Expensive)
> #run the glmnet
> system.time(modGlmnet<-glmnet(x=x,y=y,family="binomial"))
用户 系统 流逝 
5.20 0.01 5.23 
> #plot the coefficient path
> plot(modGlmnet,label=TRUE)
![image](https://user-images.githubusercontent.com/47287187/167313355-580cf756-bcee-4033-9eb2-5e59cc1bd4fa.png)
    
    
 > #this illustrates that setting a seed allows you to recreate random results
> #run them bith a few times
> set.seed(48872
+ )
> sample(1:10
+ )
 [1]  1  9 10  6  4  7  8  5  2  3
> ##decision tree
> require(rpart)
> #fire a simple decision tree
> modTree<-rpart(Expensive~.,data=diamonds)
> #plot the splits
> plot(modTree)
> text(modTree)
![image](https://user-images.githubusercontent.com/47287187/167313531-74370223-3485-4003-87ce-5e47496c7d88.png)

    
    
    
    > ##bagging (or bootstrap aggregating)
> require(boot)
载入需要的程辑包：boot

载入程辑包：‘boot’

The following object is masked from ‘package:lattice’:

    melanoma

> mean(diamonds$carat)
[1] 0.7979397
> sd(diamonds$carat)
[1] 0.4740112
> #function for bootstrapping the mean
> boot.mean<-function(x,i)
+ {
+ mean(x[i])
+ }
> #allow us to find the variability of thr mean
> boot(data=diamonds$carat,statistic=boot.mean,R=120)

ORDINARY NONPARAMETRIC BOOTSTRAP


Call:
boot(data = diamonds$carat, statistic = boot.mean, R = 120)


Bootstrap Statistics :
     original        bias    std. error
t1* 0.7979397 -0.0001400182 0.002126477
> require(adabag)
载入需要的程辑包：adabag
Warning message:
In library(package, lib.loc = lib.loc, character.only = TRUE, logical.return = TRUE,  :
  不存在叫‘adabag’这个名字的程辑包
> install.packages("adabag")
将程序包安装入‘C:/Users/hezij/Documents/R/win-library/4.1’
(因为‘lib’没有被指定)
还安装相依关系‘doParallel’

试开URL’https://cran.ma.imperial.ac.uk/bin/windows/contrib/4.1/doParallel_1.0.17.zip'
Content type 'application/zip' length 191867 bytes (187 KB)
downloaded 187 KB

试开URL’https://cran.ma.imperial.ac.uk/bin/windows/contrib/4.1/adabag_4.2.zip'
Content type 'application/zip' length 122024 bytes (119 KB)
downloaded 119 KB

package ‘doParallel’ successfully unpacked and MD5 sums checked
package ‘adabag’ successfully unpacked and MD5 sums checked

The downloaded binary packages are in
        C:\Users\hezij\AppData\Local\Temp\RtmpmeyACo\downloaded_packages
> require(adabag)
载入需要的程辑包：adabag
载入需要的程辑包：foreach

载入程辑包：‘foreach’

The following objects are masked from ‘package:purrr’:

    accumulate, when

载入需要的程辑包：doParallel
载入需要的程辑包：iterators
载入需要的程辑包：parallel
Warning messages:
1: 程辑包‘adabag’是用R版本4.1.3 来建造的 
2: 程辑包‘doParallel’是用R版本4.1.3 来建造的 
> modBag<-bagging(formula=Species~.,iris,mfinal=10)
> ##boosting
> require(mboost)
载入需要的程辑包：mboost
Warning message:
In library(package, lib.loc = lib.loc, character.only = TRUE, logical.return = TRUE,  :
  不存在叫‘mboost’这个名字的程辑包
> install.packages(mboost)
Error in install.packages(mboost) : 找不到对象'mboost'
> install.packages("mbbost")
将程序包安装入‘C:/Users/hezij/Documents/R/win-library/4.1’
(因为‘lib’没有被指定)
Warning message:
package ‘mbbost’ is not available for this version of R

A version of this package for your version of R might be available elsewhere,
see the ideas at
https://cran.r-project.org/doc/manuals/r-patched/R-admin.html#Installing-packages 
> require(mboost)
载入需要的程辑包：mboost
Warning message:
In library(package, lib.loc = lib.loc, character.only = TRUE, logical.return = TRUE,  :
  不存在叫‘mboost’这个名字的程辑包
> install.packaes("mboost")
Error in install.packaes("mboost") : 没有"install.packaes"这个函数
> install.packages("mboost")
将程序包安装入‘C:/Users/hezij/Documents/R/win-library/4.1’
(因为‘lib’没有被指定)
还安装相依关系‘libcoin’, ‘mvtnorm’, ‘Formula’, ‘inum’, ‘stabs’, ‘nnls’, ‘quadprog’, ‘partykit’

试开URL’https://cran.ma.imperial.ac.uk/bin/windows/contrib/4.1/libcoin_1.0-9.zip'
Content type 'application/zip' length 1005140 bytes (981 KB)
downloaded 981 KB

试开URL’https://cran.ma.imperial.ac.uk/bin/windows/contrib/4.1/mvtnorm_1.1-3.zip'
Content type 'application/zip' length 271312 bytes (264 KB)
downloaded 264 KB

试开URL’https://cran.ma.imperial.ac.uk/bin/windows/contrib/4.1/Formula_1.2-4.zip'
Content type 'application/zip' length 177681 bytes (173 KB)
downloaded 173 KB

试开URL’https://cran.ma.imperial.ac.uk/bin/windows/contrib/4.1/inum_1.0-4.zip'
Content type 'application/zip' length 39395 bytes (38 KB)
downloaded 38 KB

试开URL’https://cran.ma.imperial.ac.uk/bin/windows/contrib/4.1/stabs_0.6-4.zip'
Content type 'application/zip' length 140536 bytes (137 KB)
downloaded 137 KB

试开URL’https://cran.ma.imperial.ac.uk/bin/windows/contrib/4.1/nnls_1.4.zip'
Content type 'application/zip' length 51509 bytes (50 KB)
downloaded 50 KB

试开URL’https://cran.ma.imperial.ac.uk/bin/windows/contrib/4.1/quadprog_1.5-8.zip'
Content type 'application/zip' length 50391 bytes (49 KB)
downloaded 49 KB

试开URL’https://cran.ma.imperial.ac.uk/bin/windows/contrib/4.1/partykit_1.2-15.zip'
Content type 'application/zip' length 2455287 bytes (2.3 MB)
downloaded 2.3 MB

试开URL’https://cran.ma.imperial.ac.uk/bin/windows/contrib/4.1/mboost_2.9-7.zip'
Content type 'application/zip' length 2505194 bytes (2.4 MB)
downloaded 2.4 MB

package ‘libcoin’ successfully unpacked and MD5 sums checked
package ‘mvtnorm’ successfully unpacked and MD5 sums checked
package ‘Formula’ successfully unpacked and MD5 sums checked
package ‘inum’ successfully unpacked and MD5 sums checked
package ‘stabs’ successfully unpacked and MD5 sums checked
package ‘nnls’ successfully unpacked and MD5 sums checked
package ‘quadprog’ successfully unpacked and MD5 sums checked
package ‘partykit’ successfully unpacked and MD5 sums checked
package ‘mboost’ successfully unpacked and MD5 sums checked

The downloaded binary packages are in
        C:\Users\hezij\AppData\Local\Temp\RtmpmeyACo\downloaded_packages
> require(mboost)
载入需要的程辑包：mboost
载入需要的程辑包：stabs

载入程辑包：‘mboost’

The following object is masked from ‘package:glmnet’:

    Cindex

The following object is masked from ‘package:tidyr’:

    extract

The following object is masked from ‘package:ggplot2’:

    %+%

Warning messages:
1: 程辑包‘mboost’是用R版本4.1.3 来建造的 
2: 程辑包‘stabs’是用R版本4.1.3 来建造的 
> system.time(modflmBoost<-glmboost(as.facyor(Expensive)~.,data=diamonds,family=Binomial(link="logit")))
Error in as.facyor(Expensive) : 没有"as.facyor"这个函数
Timing stopped at: 0 0 0
> system.time(modflmBoost<-glmboost(as.factor(Expensive)~.,data=diamonds,family=Binomial(link="logit")))
用户 系统 流逝 
2.71 0.13 2.83 
> summary(modglmboost)
Error in h(simpleError(msg, call)) : 
  在为'summary'函数选择方法时评估'object'参数出了错: 找不到对象'modglmboost' 
> summary(modglmBoost)
Error in h(simpleError(msg, call)) : 
  在为'summary'函数选择方法时评估'object'参数出了错: 找不到对象'modglmBoost' 
> summary(modflmBoost)

         Generalized Linear Models Fitted via Gradient Boosting

Call:
glmboost.formula(formula = as.factor(Expensive) ~ ., data = diamonds,     family = Binomial(link = "logit"))


         Negative Binomial Likelihood (logit link) 

Loss function: { 
     f <- pmin(abs(f), 36) * sign(f) 
     p <- exp(f)/(exp(f) + exp(-f)) 
     y <- (y + 1)/2 
     -y * log(p) - (1 - y) * log(1 - p) 
 } 
 

Number of boosting iterations: mstop = 100 
Step size:  0.1 
Offset:  1.339691 

Coefficients: 

NOTE: Coefficients from a Binomial model are half the size of coefficients
 from a model fitted via glm(... , family = 'binomial').
See Warning section in ?coef.mboost

(Intercept)       carat   clarity.L 
  1.5155105  -1.5387675  -0.1823627 
attr(,"offset")
[1] 1.339691

Selection frequencies:
      carat (Intercept)   clarity.L 
       0.50        0.42        0.08 

> ?blackboost
starting httpd help server ... done
> ##random foreset
> require(randomForest)
载入需要的程辑包：randomForest
randomForest 4.7-1
Type rfNews() to see new features/changes/bug fixes.

载入程辑包：‘randomForest’

The following object is masked from ‘package:dplyr’:

    combine

The following object is masked from ‘package:ggplot2’:

    margin

Warning message:
程辑包‘randomForest’是用R版本4.1.3 来建造的 
> system.time(modForest<-randomForest(Species~.,data=iris,importance=TRUE,proximity=TRUE))
用户 系统 流逝 
0.05 0.00 0.05 

  
  
  
  
  
  
  
  
  
  
