Deep Learning Outlier Detection (DLOD)
===============================


DLOD is a comprehensive and scalable **Python toolkit** for **detecting outlying objects** in 
multivariate data. This exciting yet challenging field is commonly referred as 
`Outlier Detection <https://en.wikipedia.org/wiki/Anomaly_detection>`_
or `Anomaly Detection <https://en.wikipedia.org/wiki/Anomaly_detection>`_.
Since 2017, DLOD has been successfully used in various academic researches and
commercial products [#Li2019MADGAN]_ [#Zhao2019LSCP]_.
It is also well acknowledged by the machine learning community with various dedicated posts/tutorials, including
`Analytics Vidhya <https://www.analyticsvidhya.com/blog/2019/02/outlier-detection-python-DLOD/>`_,
`KDnuggets <https://www.kdnuggets.com/2019/02/outlier-detection-methods-cheat-sheet.html>`_,
`Towards Data Science <https://towardsdatascience.com/anomaly-detection-for-dummies-15f148e559c1>`_,
`Computer Vision News <https://rsipvision.com/ComputerVisionNews-2019March/18/>`_, and
`awesome-machine-learning <https://github.com/josephmisiti/awesome-machine-learning#python-general-purpose>`_.


DLOD is featured for:

* **Unified APIs, detailed documentation, and interactive examples** across various algorithms.
* **Advanced models**\ , including **Neural Networks/Deep Learning** and **Outlier Ensembles**.
* **Optimized performance with JIT and parallelization** when possible, using `numba <https://github.com/numba/numba>`_ and `joblib <https://github.com/joblib/joblib>`_.
* **Compatible with both Python 2 & 3**.


**Note on Python 2.7**\ :
The maintenance of Python 2.7 will be stopped by January 1, 2020 (see `official announcement <https://github.com/python/devguide/pull/344>`_).
To be consistent with the Python change and DLOD's dependent libraries, e.g., scikit-learn, we will
stop supporting Python 2.7 in the near future (dates are still to be decided). We encourage you to use
Python 3.5 or newer for the latest functions and bug fixes. More information can
be found at `Moving to require Python 3 <https://python3statement.org/>`_.


**API Demo**\ :


   .. code-block:: python


       # train the KNN detector
       from DLOD.models.knn import KNN
       clf = KNN()
       clf.fit(X_train)

       # get outlier scores
       y_train_scores = clf.decision_scores_  # raw outlier scores
       y_test_scores = clf.decision_function(X_test)  # outlier scores


**Key Links and Resources**\ :


* `View the latest codes on Github <https://github.com/yzhao062/DLOD>`_
* `Execute Interactive Jupyter Notebooks <https://mybinder.org/v2/gh/yzhao062/DLOD/master>`_
* `Anomaly Detection Resources <https://github.com/yzhao062/anomaly-detection-resources>`_


**Table of Contents**\ :


* `Installation <#installation>`_
* `API Cheatsheet & Reference <#api-cheatsheet--reference>`_
* `Implemented Algorithms <#implemented-algorithms>`_
* `Algorithm Benchmark <#algorithm-benchmark>`_
* `Quick Start for Outlier Detection <#quick-start-for-outlier-detection>`_
* `Quick Start for Combining Outlier Scores from Various Base Detectors <#quick-start-for-combining-outlier-scores-from-various-base-detectors>`_
* `How to Contribute <#how-to-contribute>`_
* `Inclusion Criteria <#inclusion-criteria>`_


----


Installation
^^^^^^^^^^^^

It is recommended to use **pip** for installation. Please make sure
**the latest version** is installed, as DLOD is updated frequently:



.. code-block:: bash

   git clone https://github.com/yzhao062/DLOD.git
   cd DLOD
   pip install .


**Note on Python 2.7**\ :
The maintenance of Python 2.7 will be stopped by January 1, 2020 (see `official announcement <https://github.com/python/devguide/pull/344>`_)
To be consistent with the Python change and DLOD's dependent libraries, e.g., scikit-learn, we will
stop supporting Python 2.7 in the near future (dates are still to be decided). We encourage you to use
Python 3.5 or newer for the latest functions and bug fixes. More information can
be found at `Moving to require Python 3 <https://python3statement.org/>`_.


**Required Dependencies**\ :


* Python 2.7, 3.5, 3.6, or 3.7
* numpy>=1.13
* numba>=0.35
* scipy>=0.19.1
* scikit_learn>=0.19.1

**Optional Dependencies (see details below)**\ :


* keras (optional, required for AutoEncoder)
* matplotlib (optional, required for running examples)
* pandas (optional, required for running benchmark)
* tensorflow (optional, required for AutoEncoder, other backend works)
* xgboost (optional, required for XGBOD)

**Warning 1**\ :
DLOD has multiple neural network based models, e.g., AutoEncoders, which are
implemented in Keras. However, DLOD does **NOT** install **keras** and/or
**tensorFlow** for you. This reduces the risk of interfering with your local copies.
If you want to use neural-net based models, please make sure Keras and a backend library, e.g., TensorFlow, are installed.
Instructions are provided: `neural-net FAQ <https://github.com/yzhao062/DLOD/wiki/Setting-up-Keras-and-Tensorflow-for-Neural-net-Based-models>`_.
Similarly, models depending on **xgboost**, e.g., XGBOD, would **NOT** enforce xgboost installation by default.

**Warning 2**\ :
Running examples needs **matplotlib**, which may throw errors in conda
virtual environment on mac OS. See reasons and solutions `mac_matplotlib <https://github.com/yzhao062/DLOD/issues/6>`_.

**Warning 3**\ :
DLOD contains multiple models that also exist in scikit-learn. However, these two
libraries' API is not exactly the same--it is recommended to use only one of them
for consistency but not mix the results. Refer `Differences between sckit-learn and DLOD <https://DLOD.readthedocs.io/en/latest/issues.html>`_
for more information.


----


API Cheatsheet & Reference
^^^^^^^^^^^^^^^^^^^^^^^^^^



* **fit(X)**\ : Fit detector.
* **decision_function(X)**\ : Predict raw anomaly score of X using the fitted detector.
* **predict(X)**\ : Predict if a particular sample is an outlier or not using the fitted detector.
* **predict_proba(X)**\ : Predict the probability of a sample being outlier using the fitted detector.
* **fit_predict(X)**\ : **[Deprecated in V0.6.9]** Fit detector first and then predict whether a particular sample is an outlier or not.
* **fit_predict_score(X, y)**\ : **[Deprecated in V0.6.9]** Fit the detector, predict on samples, and evaluate the model by predefined metrics, e.g., ROC.


Key Attributes of a fitted model:


* **decision_scores_**\ : The outlier scores of the training data. The higher, the more abnormal.
  Outliers tend to have higher scores.
* **labels_**\ : The binary labels of the training data. 0 stands for inliers and 1 for outliers/anomalies.


**Note** \ : fit_predict() and fit_predict_score() are deprecated in V0.6.9 due
to consistency issue and will be removed in V0.8.0. To get the binary labels
of the training data X_train, one should call clf.fit(X_train) and use
clf.labels\_, instead of calling clf.predict(X_train).


----

Implemented Algorithms
^^^^^^^^^^^^^^^^^^^^^^

DLOD toolkit consists of three major functional groups:

**(i) Individual Detection Algorithms** :

===================  ================  ======================================================================================================  =====  ========================================
Type                 Abbr              Algorithm                                                                                               Year   Ref
===================  ================  ======================================================================================================  =====  ========================================
Linear Model         PCA               Principal Component Analysis (the sum of weighted projected distances to the eigenvector hyperplanes)   2003   [#Shyu2003A]_
Linear Model         MCD               Minimum Covariance Determinant (use the mahalanobis distances as the outlier scores)                    1999   [#Hardin2004Outlier]_ [#Rousseeuw1999A]_
Linear Model         OCSVM             One-Class Support Vector Machines                                                                       2001   [#Scholkopf2001Estimating]_
Proximity-Based      LOF               Local Outlier Factor                                                                                    2000   [#Breunig2000LOF]_
Proximity-Based      COF               Connectivity-Based Outlier Factor                                                                       2002   [#Tang2002Enhancing]_
Proximity-Based      CBLOF             Clustering-Based Local Outlier Factor                                                                   2003   [#He2003Discovering]_
Proximity-Based      LOCI              LOCI: Fast outlier detection using the local correlation integral                                       2003   [#Papadimitriou2003LOCI]_
Proximity-Based      HBOS              Histogram-based Outlier Score                                                                           2012   [#Goldstein2012Histogram]_
Proximity-Based      kNN               k Nearest Neighbors (use the distance to the kth nearest neighbor as the outlier score)                 2000   [#Ramaswamy2000Efficient]_
Proximity-Based      AvgKNN            Average kNN (use the average distance to k nearest neighbors as the outlier score)                      2002   [#Angiulli2002Fast]_
Proximity-Based      MedKNN            Median kNN (use the median distance to k nearest neighbors as the outlier score)                        2002   [#Angiulli2002Fast]_
Proximity-Based      SOD               Subspace Outlier Detection                                                                              2009   [#Kriegel2009Outlier]_
Probabilistic        ABOD              Angle-Based Outlier Detection                                                                           2008   [#Kriegel2008Angle]_
Probabilistic        FastABOD          Fast Angle-Based Outlier Detection using approximation                                                  2008   [#Kriegel2008Angle]_
Probabilistic        SOS               Stochastic Outlier Selection                                                                            2012   [#Janssens2012Stochastic]_
Outlier Ensembles    IForest           Isolation Forest                                                                                        2008   [#Liu2008Isolation]_
Outlier Ensembles                      Feature Bagging                                                                                         2005   [#Lazarevic2005Feature]_
Outlier Ensembles    LSCP              LSCP: Locally Selective Combination of Parallel Outlier Ensembles                                       2019   [#Zhao2019LSCP]_
Outlier Ensembles    XGBOD             Extreme Boosting Based Outlier Detection **(Supervised)**                                               2018   [#Zhao2018XGBOD]_
Neural Networks      AutoEncoder       Fully connected AutoEncoder (use reconstruction error as the outlier score)                                    [#Aggarwal2015Outlier]_ [Ch.3]
Neural Networks      SO_GAAL           Single-Objective Generative Adversarial Active Learning                                                 2019   [#Liu2019Generative]_
Neural Networks      MO_GAAL           Multiple-Objective Generative Adversarial Active Learning                                               2019   [#Liu2019Generative]_
===================  ================  ======================================================================================================  =====  ========================================


**(ii) Outlier Ensembles & Outlier Detector Combination Frameworks**:

===================  ================  =====================================================================================================  =====  ========================================
Type                 Abbr              Algorithm                                                                                              Year   Ref
===================  ================  =====================================================================================================  =====  ========================================
Outlier Ensembles                      Feature Bagging                                                                                        2005   [#Lazarevic2005Feature]_
Outlier Ensembles    LSCP              LSCP: Locally Selective Combination of Parallel Outlier Ensembles                                      2019   [#Zhao2019LSCP]_
Combination          Average           Simple combination by averaging the scores                                                             2015   [#Aggarwal2015Theoretical]_
Combination          Weighted Average  Simple combination by averaging the scores with detector weights                                       2015   [#Aggarwal2015Theoretical]_
Combination          Maximization      Simple combination by taking the maximum scores                                                        2015   [#Aggarwal2015Theoretical]_
Combination          AOM               Average of Maximum                                                                                     2015   [#Aggarwal2015Theoretical]_
Combination          MOA               Maximization of Average                                                                                2015   [#Aggarwal2015Theoretical]_
===================  ================  =====================================================================================================  =====  ========================================


**(iii) Utility Functions**:

===================  ======================  =====================================================================================================================================================  ======================================================================================================================================
Type                 Name                    Function                                                                                                                                               Documentation
===================  ======================  =====================================================================================================================================================  ======================================================================================================================================
Data                 generate_data           Synthesized data generation; normal data is generated by a multivariate Gaussian and outliers are generated by a uniform distribution                  `generate_data <https://DLOD.readthedocs.io/en/latest/DLOD.utils.html#module-DLOD.utils.data.generate_data>`_
Data                 generate_data_clusters  Synthesized data generation in clusters; more complex data patterns can be created with multiple clusters                                              `generate_data_clusters <https://DLOD.readthedocs.io/en/latest/DLOD.utils.html#DLOD.utils.data.generate_data_clusters>`_
Stat                 wpearsonr               Calculate the weighted Pearson correlation of two samples                                                                                              `wpearsonr <https://DLOD.readthedocs.io/en/latest/DLOD.utils.html#module-DLOD.utils.stat_models.wpearsonr>`_
Utility              get_label_n             Turn raw outlier scores into binary labels by assign 1 to top n outlier scores                                                                         `get_label_n <https://DLOD.readthedocs.io/en/latest/DLOD.utils.html#module-DLOD.utils.utility.get_label_n>`_
Utility              precision_n_scores      calculate precision @ rank n                                                                                                                           `precision_n_scores <https://DLOD.readthedocs.io/en/latest/DLOD.utils.html#module-DLOD.utils.utility.precision_n_scores>`_
===================  ======================  =====================================================================================================================================================  ======================================================================================================================================

----


Algorithm Benchmark
^^^^^^^^^^^^^^^^^^^

**The comparison among of implemented models** is made available below
(\ `Figure <https://raw.githubusercontent.com/yzhao062/DLOD/master/examples/ALL.png>`_\ ,
`compare_all_models.py <https://github.com/yzhao062/DLOD/blob/master/examples/compare_all_models.py>`_\ ,
`Interactive Jupyter Notebooks <https://mybinder.org/v2/gh/yzhao062/DLOD/master>`_\ ).
For Jupyter Notebooks, please navigate to **"/notebooks/Compare All Models.ipynb"**.


.. image:: https://raw.githubusercontent.com/yzhao062/DLOD/master/examples/ALL.png
   :target: https://raw.githubusercontent.com/yzhao062/DLOD/master/examples/ALL.png
   :alt: Comparision_of_All

A benchmark is supplied for select algorithms to provide an overview of the implemented models.
In total, 17 benchmark datasets are used for comparison, which
can be downloaded at `ODDS <http://odds.cs.stonybrook.edu/#table1>`_.

For each dataset, it is first split into 60% for training and 40% for testing.
All experiments are repeated 10 times independently with random splits.
The mean of 10 trials is regarded as the final result. Three evaluation metrics
are provided:

- The area under receiver operating characteristic (ROC) curve
- Precision @ rank n (P@N)
- Execution time



----


Quick Start for Outlier Detection
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

DLOD has been well acknowledged by the machine learning community with a few featured posts and tutorials.

**Analytics Vidhya**: `An Awesome Tutorial to Learn Outlier Detection in Python using DLOD Library <https://www.analyticsvidhya.com/blog/2019/02/outlier-detection-python-DLOD/>`_

**KDnuggets**: `Intuitive Visualization of Outlier Detection Methods <https://www.kdnuggets.com/2019/02/outlier-detection-methods-cheat-sheet.html>`_, `An Overview of Outlier Detection Methods from DLOD <https://www.kdnuggets.com/2019/06/overview-outlier-detection-methods-DLOD.html>`_

**Towards Data Science**: `Anomaly Detection for Dummies <https://towardsdatascience.com/anomaly-detection-for-dummies-15f148e559c1>`_

**Computer Vision News (March 2019)**: `Python Open Source Toolbox for Outlier Detection <https://rsipvision.com/ComputerVisionNews-2019March/18/>`_

`"examples/knn_example.py" <https://github.com/yzhao062/DLOD/blob/master/examples/knn_example.py>`_
demonstrates the basic API of using kNN detector. **It is noted that the API across all other algorithms are consistent/similar**.

More detailed instructions for running examples can be found in `examples directory <https://github.com/yzhao062/DLOD/blob/master/examples>`_.


#. Initialize a kNN detector, fit the model, and make the prediction.

   .. code-block:: python


       from DLOD.models.knn import KNN   # kNN detector

       # train kNN detector
       clf_name = 'KNN'
       clf = KNN()
       clf.fit(X_train)

       # get the prediction label and outlier scores of the training data
       y_train_pred = clf.labels_  # binary labels (0: inliers, 1: outliers)
       y_train_scores = clf.decision_scores_  # raw outlier scores

       # get the prediction on the test data
       y_test_pred = clf.predict(X_test)  # outlier labels (0 or 1)
       y_test_scores = clf.decision_function(X_test)  # outlier scores

#. Evaluate the prediction by ROC and Precision @ Rank n (p@n).

   .. code-block:: python


       # evaluate and print the results
       print("\nOn Training Data:")
       evaluate_print(clf_name, y_train, y_train_scores)
       print("\nOn Test Data:")
       evaluate_print(clf_name, y_test, y_test_scores)


#. See a sample output & visualization.


   .. code-block:: python


       On Training Data:
       KNN ROC:1.0, precision @ rank n:1.0

       On Test Data:
       KNN ROC:0.9989, precision @ rank n:0.9

   .. code-block:: python


       visualize(clf_name, X_train, y_train, X_test, y_test, y_train_pred,
           y_test_pred, show_figure=True, save_figure=False)


.. image:: https://raw.githubusercontent.com/yzhao062/DLOD/master/examples/KNN.png
   :target: https://raw.githubusercontent.com/yzhao062/DLOD/master/examples/KNN.png
   :alt: kNN example figure


----

Quick Start for Combining Outlier Scores from Various Base Detectors
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Outlier detection often suffers from model instability due to its unsupervised
nature. Thus, it is recommended to combine various detector outputs, e.g., by averaging,
to improve its robustness. Detector combination is a subfield of outlier ensembles;
refer [#Aggarwal2017Outlier]_ for more information.


Four score combination mechanisms are shown in this demo:


#. **Average**: average scores of all detectors.
#. **maximization**: maximum score across all detectors.
#. **Average of Maximum (AOM)**: divide base detectors into subgroups and take the maximum score for each subgroup. The final score is the average of all subgroup scores.
#. **Maximum of Average (MOA)**: divide base detectors into subgroups and take the average score for each subgroup. The final score is the maximum of all subgroup scores.


"examples/comb_example.py" illustrates the API for combining the output of multiple base detectors
(\ `comb_example.py <https://github.com/yzhao062/DLOD/blob/master/examples/comb_example.py>`_\ ,
`Jupyter Notebooks <https://mybinder.org/v2/gh/yzhao062/DLOD/master>`_\ ). For Jupyter Notebooks,
please navigate to **"/notebooks/Model Combination.ipynb"**


#. Import models and generate sample data.

   .. code-block:: python

       from DLOD.models.knn import KNN
       from DLOD.models.combination import aom, moa, average, maximization
       from DLOD.utils.data import generate_data

       X, y = generate_data(train_only=True)  # load data

#. First initialize 20 kNN outlier detectors with different k (10 to 200), and get the outlier scores.

   .. code-block:: python

       # initialize 20 base detectors for combination
       k_list = [10, 20, 30, 40, 50, 60, 70, 80, 90, 100, 110, 120, 130, 140,
                   150, 160, 170, 180, 190, 200]

       train_scores = np.zeros([X_train.shape[0], n_clf])
       test_scores = np.zeros([X_test.shape[0], n_clf])

       for i in range(n_clf):
           k = k_list[i]

           clf = KNN(n_neighbors=k, method='largest')
           clf.fit(X_train_norm)

           train_scores[:, i] = clf.decision_scores_
           test_scores[:, i] = clf.decision_function(X_test_norm)

#. Then the output scores are standardized into zero mean and unit variance before combination.
   This step is crucial to adjust the detector outputs to the same scale.


   .. code-block:: python

       from DLOD.utils.utility import standardizer
       train_scores_norm, test_scores_norm = standardizer(train_scores, test_scores)

#. Then four different combination algorithms are applied as described above.

   .. code-block:: python

       comb_by_average = average(test_scores_norm)
       comb_by_maximization = maximization(test_scores_norm)
       comb_by_aom = aom(test_scores_norm, 5) # 5 groups
       comb_by_moa = moa(test_scores_norm, 5)) # 5 groups

#. Finally, all four combination methods are evaluated with ROC and Precision @ Rank n.

   .. code-block:: bash

       Combining 20 kNN detectors
       Combination by Average ROC:0.9194, precision @ rank n:0.4531
       Combination by Maximization ROC:0.9198, precision @ rank n:0.4688
       Combination by AOM ROC:0.9257, precision @ rank n:0.4844
       Combination by MOA ROC:0.9263, precision @ rank n:0.4688

----






Inclusion Criteria
^^^^^^^^^^^^^^^^^^

Similarly to `scikit-learn <https://scikit-learn.org/stable/faq.html#what-are-the-inclusion-criteria-for-new-algorithms>`_,
We mainly consider well-established algorithms for inclusion.
A rule of thumb is at least two years since publication, 50+ citations, and usefulness.

However, we encourage the author(s) of newly proposed models to share and add your implementation into DLOD
for boosting ML accessibility and reproducibility.
This exception only applies if you could commit to the maintenance of your model for at least two year period.


----

Reference
^^^^^^^^^


.. [#Aggarwal2015Outlier] Aggarwal, C.C., 2015. Outlier analysis. In Data mining (pp. 237-263). Springer, Cham.

.. [#Aggarwal2015Theoretical] Aggarwal, C.C. and Sathe, S., 2015. Theoretical foundations and algorithms for outlier ensembles.\ *ACM SIGKDD Explorations Newsletter*\ , 17(1), pp.24-47.

.. [#Aggarwal2017Outlier] Aggarwal, C.C. and Sathe, S., 2017. Outlier ensembles: An introduction. Springer.

.. [#Angiulli2002Fast] Angiulli, F. and Pizzuti, C., 2002, August. Fast outlier detection in high dimensional spaces. In *European Conference on Principles of Data Mining and Knowledge Discovery* pp. 15-27.

.. [#Breunig2000LOF] Breunig, M.M., Kriegel, H.P., Ng, R.T. and Sander, J., 2000, May. LOF: identifying density-based local outliers. *ACM Sigmod Record*\ , 29(2), pp. 93-104.

.. [#Goldstein2012Histogram] Goldstein, M. and Dengel, A., 2012. Histogram-based outlier score (hbos): A fast unsupervised anomaly detection algorithm. In *KI-2012: Poster and Demo Track*\ , pp.59-63.

.. [#Hardin2004Outlier] Hardin, J. and Rocke, D.M., 2004. Outlier detection in the multiple cluster setting using the minimum covariance determinant estimator. *Computational Statistics & Data Analysis*\ , 44(4), pp.625-638.

.. [#He2003Discovering] He, Z., Xu, X. and Deng, S., 2003. Discovering cluster-based local outliers. *Pattern Recognition Letters*\ , 24(9-10), pp.1641-1650.

.. [#Janssens2012Stochastic] Janssens, J.H.M., Huszár, F., Postma, E.O. and van den Herik, H.J., 2012. Stochastic outlier selection. Technical report TiCC TR 2012-001, Tilburg University, Tilburg Center for Cognition and Communication, Tilburg, The Netherlands.

.. [#Kriegel2008Angle] Kriegel, H.P. and Zimek, A., 2008, August. Angle-based outlier detection in high-dimensional data. In *KDD '08*\ , pp. 444-452. ACM.

.. [#Kriegel2009Outlier] Kriegel, H.P., Kröger, P., Schubert, E. and Zimek, A., 2009, April. Outlier detection in axis-parallel subspaces of high dimensional data. In *Pacific-Asia Conference on Knowledge Discovery and Data Mining*\ , pp. 831-838. Springer, Berlin, Heidelberg.

.. [#Lazarevic2005Feature] Lazarevic, A. and Kumar, V., 2005, August. Feature bagging for outlier detection. In *KDD '05*. 2005.

.. [#Li2019MADGAN] Li, D., Chen, D., Jin, B., Shi, L., Goh, J. and Ng, S.K., 2019, September. MAD-GAN: Multivariate anomaly detection for time series data with generative adversarial networks. In *International Conference on Artificial Neural Networks* (pp. 703-716). Springer, Cham.

.. [#Liu2008Isolation] Liu, F.T., Ting, K.M. and Zhou, Z.H., 2008, December. Isolation forest. In *International Conference on Data Mining*\ , pp. 413-422. IEEE.

.. [#Liu2019Generative] Liu, Y., Li, Z., Zhou, C., Jiang, Y., Sun, J., Wang, M. and He, X., 2019. Generative adversarial active learning for unsupervised outlier detection. *IEEE Transactions on Knowledge and Data Engineering*.

.. [#Papadimitriou2003LOCI] Papadimitriou, S., Kitagawa, H., Gibbons, P.B. and Faloutsos, C., 2003, March. LOCI: Fast outlier detection using the local correlation integral. In *ICDE '03*, pp. 315-326. IEEE.

.. [#Ramaswamy2000Efficient] Ramaswamy, S., Rastogi, R. and Shim, K., 2000, May. Efficient algorithms for mining outliers from large data sets. *ACM Sigmod Record*\ , 29(2), pp. 427-438.

.. [#Rousseeuw1999A] Rousseeuw, P.J. and Driessen, K.V., 1999. A fast algorithm for the minimum covariance determinant estimator. *Technometrics*\ , 41(3), pp.212-223.

.. [#Scholkopf2001Estimating] Scholkopf, B., Platt, J.C., Shawe-Taylor, J., Smola, A.J. and Williamson, R.C., 2001. Estimating the support of a high-dimensional distribution. *Neural Computation*, 13(7), pp.1443-1471.

.. [#Shyu2003A] Shyu, M.L., Chen, S.C., Sarinnapakorn, K. and Chang, L., 2003. A novel anomaly detection scheme based on principal component classifier. *MIAMI UNIV CORAL GABLES FL DEPT OF ELECTRICAL AND COMPUTER ENGINEERING*.

.. [#Tang2002Enhancing] Tang, J., Chen, Z., Fu, A.W.C. and Cheung, D.W., 2002, May. Enhancing effectiveness of outlier detections for low density patterns. In *Pacific-Asia Conference on Knowledge Discovery and Data Mining*, pp. 535-548. Springer, Berlin, Heidelberg.

.. [#Zhao2018XGBOD] Zhao, Y. and Hryniewicki, M.K. XGBOD: Improving Supervised Outlier Detection with Unsupervised Representation Learning. *IEEE International Joint Conference on Neural Networks*\ , 2018.

.. [#Zhao2019LSCP] Zhao, Y., Nasrullah, Z., Hryniewicki, M.K. and Li, Z., 2019, May. LSCP: Locally selective combination in parallel outlier ensembles. In *Proceedings of the 2019 SIAM International Conference on Data Mining (SDM)*, pp. 585-593. Society for Industrial and Applied Mathematics.
