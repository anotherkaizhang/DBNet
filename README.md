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

The idea is to use encoder network to map different modality EHR data to an embedding in a latent space, fusion the embeddings, then the decoder network is for learning the relationships between different modalities. The encoder network consists of multiple channels, each has its own structure to process a single modality data.

We applied it on a disease prediction task using temporal EHR data and achieved state-of-the-art performance on the same task compared to various deep learning and conventional machine learning models. Here the different modalities are different tables in the EHR structured data (lab tests, vitals, prescriptions, treatments, etc.) Other modalities (MRI images, audio signals, clinical notes, etc.) can also be added to the newtork by adding different channels.

Another keynote is that, DBNet does * * not * * need time axis alignment accross different modalities data of a patient, thanks to its specific design to handle irregular sampling data. For each modality data, it also does * * not * * require regular time intervals between two record (i.e. the time interval between any two rows in the input data table can be arbitrary). DBNet uses attention to learn to pay attention to different observed records. 

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
The input data format is a list (or tuple) of matrices (in the form of tensors) for each patient, and his/her label. All patients data form a larger a list (or tuple). Each tensor represents a structured EHR modality, row: records; column: features. Since we do not require time axis alignment accross different modalities, a patient's list of matrices often have different number of rows for each modality.

![Input data format](https://user-images.githubusercontent.com/29695346/194341335-2c161948-435e-4dd5-8f91-3cb15a66f407.PNG)

Note:  
- Each table has same # of columns (i.e. features in the table) among all patients, but may have different # of rows (i.e. observational records) among all patients, because different patients have different number of observations due to having different # of hospital visits.
- Inside each table, majority entries are zero. In a row, only the features that are observed has a nonzero value (lab test result, vital sign value, meds 0/1, treatment 0/1, etc.)
- Because it doesn't requires rows having regular time intervals, when another observation occurs, it simply append a row at the end.
- We set the time unit to be 4-hours for all patients, to avoid a table having too many rows (imagine a table is created by each row represents a second). This avoids too large & sparse data (difficult for the neural network to learn)
- In data preprocessing, if a feature is observed > 1 times within a 4-hour window for a patient, we take the average of the patient's results to create a record in his table. For meds/treatment (0/1 data), it is set to 1 as long as there is a 1 in the 4-hour window. 
- The label is an integer to denote ventilated or not (in our task)

### Running

- Use the '

### Contact
kai.zhang.1[at]uth.tmc.edu
