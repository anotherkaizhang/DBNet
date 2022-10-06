# DBNet
![](https://img.shields.io/github/license/Naereen/StrapDown.js.svg)
![](https://img.shields.io/badge/python-%3E%3D3.7-green)
![](https://img.shields.io/badge/torch-%3E%3D1.10-blue)
![](https://img.shields.io/badge/numpy-%3E%3D1.19-yellow)
![](https://img.shields.io/badge/matplotlib-%3E%3D3.3-brightgreen)
![](https://img.shields.io/badge/pandas-%3E%3D1.2-green)
![](https://img.shields.io/badge/scikit__learn-%3E%3D1.1-yellowgreen)
![](https://img.shields.io/badge/scipy-%3E%3D1.6-orange)
![](https://img.shields.io/badge/tqdm-%3E%3D4.60-lightgrey)

This is an implementation of our paper: [Zhang K, Jiang X, Madadi M, Chen L, Savitz S, Shams S. DBNet: A novel deep learning framework for mechanical ventilation prediction using electronic health records. InProceedings of the 12th ACM Conference on Bioinformatics, Computational Biology, and Health Informatics 2021 Aug 1](https://dl.acm.org/doi/abs/10.1145/3459930.3469551)

DBNet implements a deep learning encoder-decoder, multimodal learning pipeline to perform classification task (and easily be tailored to do regression task) when the input time series data has missing values. 

![Figure](https://user-images.githubusercontent.com/29695346/194333875-9189daa2-fea9-49d6-86bd-67115c1f3640.PNG)


The idea is to use Multivariant Gaussian Process for missing value imputation. The key note is that the Gaussian Process parameters (mean and covariance matrix) are integrated as parameters of the prediction neural network, therefore, missing data imputation and classification are performed together and jointly trained via back-propagation, which siginificantly boost prediction performance.

We applied it on a disease prediction task and achieved state-of-the-art performance on the same task compared to various deep learning and conventional machine learning models. [A multi-task Gaussian process self-attention neural network for real-time prediction of the need for mechanical ventilators in COVID-19 patients](https://pubmed.ncbi.nlm.nih.gov/35489596/).

In this paper, MGPMS was applied not only to classify (0/1) a future event happens or not, but also generate patient's entire risk score trajectory from the prediction time to the time event happens. We show the risk trajectory has siginificant robustness and consistency compared to traditional Machine Learning Models (Logistic Regression, Cox Regression, etc.) in that it uses target replication to further boost performance.

MGPMS can be used in numerous time series prediction applications when missing values exists, besides the COVID-19 patients Risk Score prediction in this paper.

### Requirements
* matplotlib>=3.3.3
* numpy>=1.19.2
* pandas>=1.2.4
* scikit_learn>=1.1.1
* scipy>=1.6.3
* torch>=1.10.0
* tqdm>=4.60.0


## Input Dataset Format
The input data format is not a usual regular matrix format, as takes input data with missing values, such format usually takes too much space especially when we have a lot of missing observations (as in our data).

![image](https://user-images.githubusercontent.com/29695346/182448267-210fd57a-a658-4bbc-9d3c-e49ade5f5a02.png)

We using observed position indicators to represent the sparse matrix, in the following format. Suppose a patient has a matrix of (row: time stamps, col: featurs) with many missingness. We create the following variables:

- 'labels': integer (binaries) 0/1 (dtype = int8),
- 'times': list, real observational time points (dtype = float64),
- 'values': list, observational observational values at the 'times' stamps (dtype = float64),
- 'ind_lvs': list of observational value indices in the column (dtype = int64),
- 'ind_times': list of observational time indices (dtype = int64),
- 'num_obs_values': integer (dtype = int32),
- 'rnn_grid_times': list, inferred time points (at which the values need to be imputed) (dtype = float64),
- 'num_rnn_grid_times': how many inferred time points, i.e. length of 'rnn_grid_times' (dtype = int32),

Optional: 
- 'meds_on_grid': list of drug prescription history time indices (dtype = float64),
- 'covs': list of covariates (length-n_covs vector), n_covs: number of demographic dimensions (dtype = float64), 

The folder 'data' contains one small dataset for demonstration purposes.

### Running

- Run `simulation.py' to generate data, or simply use the data in the 'data' folder
- Run 'MGP-MS.py', you can custermize hypter parameters and your own dataset(s).

### Contact
kai.zhang.1[at]uth.tmc.edu
