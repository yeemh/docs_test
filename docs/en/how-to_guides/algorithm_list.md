---
title: Guide
---

# **Guide**

## Preface

- 3 min read
- Updated Date : {{ git_revision_date_localized }}

## Before we start,

ThanoSQL is built on PostgreSQL and features the ability to create and run machine learning and deep learning models using SQL queries.
The primary goal is to replace the Lambda architecture-based unstructured big data platform with one ThanoSQL to enable development, deployment, and operation.

!!! note "What is PostgreSQL?"
    PostgreSQL is an RDBMS developed for high-capacity, complex transaction processing. PostgreSQL is an object-relational database system (ORDBMS), an open-source DBMS that provides commercial RDBMS-class functionality, unlike open-source products such as MySQL or MariaDB, because Oracle, DB2, and PostgreSQL were implemented based on RDBMS white papers written by IBM in the past. It is expanding its recognition in North America and Japan due to its characteristic of open source.

This guide introduces the types of artificial intelligence algorithms that ThanoSQL provides, sample datasets, and query syntax for ThanoSQL.

## **1. Artificial intelligence algorithm provided by ThanoSQL**

ThanoSQL's artificial intelligence algorithm provides a variety of pre-learning models. Create your own model by applying various algorithms through the tutorial below. The pre-learning model will continue to be updated.

For a detailed description of the model, check [the options documentation page](/en/how-to_guides/OPTIONS/).

- [Auto-ML (automated machine learning) for classification tasks](/en/tutorials/thanosql_ml/classification/automl_classification/)
- [Auto-ML (Automated Machine Learning) for Predictive Tasks](/en/tutorials/thanosql_ml/regression/automl_regression/)
- [ConvNeXt for image classification](/en/tutorials/thanosql_ml/classification/classification_convnext)
- [ELECTRA for text classification](/en/tutorials/thanosql_ml/classification/classification_electra/)
- [wav2vec for speech recognition](/en/tutorials/thanosql_ml/audio_recognition/audio_recognition_wav2vec/)
- [CLIP for image search by keyword](/en/tutorials/thanosql_search/image_search/clip_image_search/)
- [SimCLR for Image Similarity Based Search](/en/tutorials/thanosql_search/image_search/simclr_image_search/)

## **2. Sample data set provided by ThanoSQL**

To apply ThanoSQL's artificial intelligence algorithm, we support the following sample data sets.

- [(Kaggle) Titanic - Machine Learning from Disaster](https://www.kaggle.com/competitions/titanic/overview)
- [(Kaggle) Bike Sharing Demand](https://www.kaggle.com/competitions/bike-sharing-demand/overview)
- [(AI-Hub) Product image data](https://aihub.or.kr/aihubdata/data/view.do?currMenu=115&topMenu=100&aihubDataSe=realm&dataSetSn=64)
- [LibriSpeech Data Set](http://www.openslr.org/12)
- [Movielens Data Set](https://grouplens.org/datasets/movielens/)
- [Unsplash Data Set](https://unsplash.com/data)
- [MNIST Data Set](http://yann.lecun.com/exdb/mnist/)

## **3. Query syntax for ThanoSQL**

The query syntax retrieves one or more expressions and returns a table of calculated results. ThanoSQL provides the following query syntax.

- "**BUILD MODEL**" : Train the model using ThanoSQL.
- "**FIT MODEL**" : Retrain the ThanoSQL model.
- "**EVALUATE USING**" : Evaluate the trained ThanoSQL model.
- "**TRANSFORM USING**" : Pre-process with a ThanoSQL model trained for prediction.
- "**PREDICT USING**" : Prediction is performed using the trained ThanoSQL model.
- "**DELETE MODEL**" : Delete the trained ThanoSQL model for model management.
- "**LIST**" : Retrieves the list of models, data tables, and tutorials stored in ThanoSQL DB.
- "**CREATE TABLE**" : Numericalize unstructured data such as images, voices, and videos to create data tables.
- "**CONVERT USING**" : Numericalize unstructured data, such as images, voices, and videos, and add them to the data table you want to use.
- "**SEARCH**" : Search for unstructured data such as images, voices, and videos.
- "**PRINT**" : Outputs unstructured data such as images, voices, and videos.
