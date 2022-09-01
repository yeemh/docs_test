---
title: Create an image classification model
---

# **Create an image classification model**

## Preface

- Tutorial Difficulty: ★☆☆☆☆
- 10 min read
- Languages : [SQL](https://ko.wikipedia.org/wiki/SQL) (100%)
- File location : tutorial/tutorial_en/ml/classification_model/image_classification_en.ipynb
- References : [(AI-Hub) Product image data](https://aihub.or.kr/aihubdata/data/view.do?currMenu=115&topMenu=100&aihubDataSe=realm&dataSetSn=64), [A ConvNet for the 2020s](https://arxiv.org/abs/2201.03545)
- Updated Date : {{ git_revision_date_localized }}

## Tutorial Introduction

!!! note "Understanding Classification Operations"
    Classification is a form of [Machine Learning](https://en.wikipedia.org/wiki/Machine_learning) used to predict the category (Category or Class) to which the target belongs. For example, both binary classifications that classify men or women and multiple classifications that predict animal species (dogs, cats, rabbits, etc.) are included in the classification task.

Since 2010, a contest has been held to classify images using AI models ([ImageNet](https://en.wikipedia.org/wiki/ImageNet)). At the beginning of the competition, the classification performance of the winning models was about 72%, but the 2015 winning [ResNet](https://arxiv.org/abs/1512.03385) model recorded about 96% performance and began to surpass human classification in certain areas.

!!! tip ""
    The human classification capacity of the same data is said to be about 95%.

Accurate image classification requires [data labeling](https://en.wikipedia.org/wiki/Labeled_data) work on large datasets, but methods of re-calibrating and using weights from pre-trained AI models to fit small labeled datasets are widely applied. As a result, even with a relatively small number of data, it enables the training of deep learning models.

ThanoSQL offers a variety of pre-trained artificial intelligence models that allow you to create models through simple query syntax. This allows users to extract potential insights from images that are difficult to quantify features from properly trained image classification models and utilize them in a variety of services.

**Below are examples and utilization of the ThanoSQL image classification model.**

- The ThanoSQL image classification model reduces the repetition of finding the appropriate categories for product registration in online merchandise sales services. You can categorize categories of product pictures with only a simple query syntax. Users can save time on traditional image classification by only correcting some misclassified data.

- When you rent or sell artworks, you can roughly classify tasks that are difficult to classify due to ambiguous criteria, such as how they feel, how they fit, and where they fit.

- You can detect and classify defective products such as scratches and breakages that were visually checked by the manufacturing plant. Signal information, such as laser spectra, can also be applied to image classification models through processing, such as visualization transformations.

!!! tip ""
    You can also store and leverage the behavioral history (purchase or rental history, preferences, etc.) of art lovers in your database to create a classification model that finds the groups you expect to like a particular work. In other words, you can create a model that predicts preferences in age groups (such as 20s, 30s, 40s, etc.), gender (male, female), exhibition sites (home, cafe, company, etc.).

!!! note "In this tutorial"
    :point_right: Build a model that classifies more than 10,000 products using the `Product Image` dataset from [AI-Hub](https://aihub.or.kr/), a leading AI open data sharing platform. The built model can be used as a detection and identification solution in smart logistics warehouses and unmanned stores. Datasets typically consist of over 10,000 commodity datasets of images and label (correct) pairs used to learn image classification techniques, and contain a total of 1,440,000 images. In this tutorial, you will only use 1,800 sheets of training data and 200 sheets of test data to learn how to use ThanoSQL and to check results quickly. <br>

[![Product Image Example](/img/thanosql_ml/classification/classification_convnext/classification_convnext_data_intro.png)](/img/thanosql_ml/classification/classification_convnext/classification_convnext_data_intro.png)

!!! warning "Tutorial Precautions"
    - The image classification model can be used to predict one target (Target, Category/Label/Label) from one image.
    - There must be a column representing the path of the image and a column representing the target value of the image.
    - The base model of the corresponding image classification model (`CONVNEXT`) uses a GPU. Depending on the size and batch size of the model used, GPU memory may be low. In this case, try using a smaller model or reducing the batch size.

## **0. Data Set Preparation**

To use the query syntax of ThanoSQL, you must create an API token and run the query below, as mentioned in [ThanoSQL Workspace](/en/getting_started/how_to_use_ThanoSQL/#5-thanosql-workspace).

```sql
%load_ext thanosql
```

```sql
%thanosql API_TOKEN=<Issued_API_TOKEN>
```

```sql
%%thanosql
COPY product_image_train
OPTIONS(overwrite = True)
FROM "tutorial_data/product_image_data/product_image_train.csv"
```

```sql
%%thanosql
COPY product_image_test
OPTIONS(overwrite = True)
FROM "tutorial_data/product_image_data/product_image_test.csv"
```

!!! note "**Query Details**"
    - Specifies the data set name to copy to the DB using the "**COPY**" query syntax.
    - Specify the options to use for **COPY** via the "**OPTIONS**" query syntax.
        - "overwrite" : Set whether or not a data set with the same name can be overwritten when it exists on the DB. If True, the existing dataset is changed to the new dataset (True|False, DEFAULT: False)

## **1. Check Data Set**

To proceed with this tutorial, we use the <mark style="background-color:#FFEC92">product_image_train</mark> table stored in the ThanoSQL DB. Run the query statement below to verify the contents of the table.

```sql
%%thanosql
SELECT *
FROM product_image_train
LIMIT 5
```

[![IMAGE](/img/thanosql_ml/classification/classification_convnext/train_data_limit_5.png)](/img/thanosql_ml/classification/classification_convnext/train_data_limit_5.png)

!!! note "**Understanding Data**"
    - <mark style="background-color:#D7D0FF ">image_path</mark>: Location information of each image's file
    - <mark style="background-color:#D7D0FF ">div_l</mark> : Large classification of Products
    - <mark style="background-color:#D7D0FF ">div_m</mark> : middle classification of Products
    - <mark style="background-color:#D7D0FF ">div_s</mark> : subclassification of Products
    - <mark style="background-color:#D7D0FF ">div_n</mark> : detailed classification of Products
    - <mark style="background-color:#D7D0FF ">comp_nm</mark> : Manufacturer
    - <mark style="background-color:#D7D0FF ">img_prod_nm</mark> : Product name (image)
    - <mark style="background-color:#D7D0FF ">multi</mark> : Whether it is a multiple product image

```sql
%%thanosql
PRINT IMAGE
AS
SELECT image_path
FROM product_image_train
LIMIT 5
```

[![IMAGE](/img/thanosql_ml/classification/classification_convnext/print_image_train_data.png)](/img/thanosql_ml/classification/classification_convnext/print_image_train_data.png)

## **2. Predict product image classification results using pre-trained models**

By executing the following query statement, you can quickly predict the results using the pre-learned product image classification model, <mark style="background-color:#E9D7FD">tutorial_product_classifier</mark> model.

```sql
%%thanosql
PREDICT USING tutorial_product_classifier
AS
SELECT *
FROM product_image_test
```

[![IMAGE](/img/thanosql_ml/classification/classification_convnext/predict_on_test_data_1.png)](/img/thanosql_ml/classification/classification_convnext/predict_on_test_data_1.png)

## **3. Create an image classification model**

Create an image classification model using the <mark style="background-color:#FFEC92 ">product_image_train</mark> dataset you saw in the previous step. Run the query syntax below to create a model named <mark style="background-color:#E9D7FD ">my_product_classifier</mark>.  
(Expected time required for query execution: 5 min)

```sql
%%thanosql
BUILD MODEL my_product_classifier
USING ConvNeXt_Tiny
OPTIONS (
  image_col='image_path',
  label_col='div_l',
  epochs=1,
  overwrite=True
  )
AS
SELECT *
FROM product_image_train
```

!!! note "Query Details"
    - Use the "**BUILD MODEL**" query syntax to create and train the <mark style="background-color:#E9D7FD">my_product_classifier</mark> model.
    - The "**USING**" query syntax specifies the use of `ConvNeXt_Tiny` as the base model.
    - Specify the options to use for model creation via the "**OPTIONS**" query syntax.
        - "image_col" : Name of column containing image path
        - "label_col" : Name of column containing information of target
        - "epochs : Number of times all learning data sets are learned.

!!! tip ""
    For quick learning, we have designated "epochs" as 1. In general, larger numbers take more computational time, but predictive performance increases as learning progresses.

!!! note ""
    **When overwrite is true**, the user can create a data table with the same name as the previously created data table.  
    On the other hand, when **overwrite is False**, the user cannot create a data table with the same name as the previously created data table.

## **4. Predict product image classification results using the generated model**

Use the product image classification model (<mark style="background-color:#FFEC92 ">my_product_classifier</mark>) created in the previous step to predict the target value of a specific image (data table not used for learning, <mark style="background-color:#D7D0FF">product_image_test</mark>). Once the query has been performed below, the prediction results will be returned in the <mark style="background-color:#D7D0FF">predicted</mark> column.

```sql
%%thanosql
PREDICT USING my_product_classifier
OPTIONS (
    image_col='image_path'
    )
AS
SELECT *
FROM product_image_test
```

[![IMAGE](/img/thanosql_ml/classification/classification_convnext/predict_on_test_data_2.png)](/img/thanosql_ml/classification/classification_convnext/predict_on_test_data_2.png)

!!! note "**Query Details**"
    - Use the <mark style="background-color:#E9D7FD">my_product_classifier</mark> model created in the previous step with the query syntax "**PREDICT USING**".
    - Specify the options to use for prediction via the "**OPTIONS**" query syntax.
        - "image_col" : The name of the column in which the path of the image to be used for prediction is recorded

<br>

## **5. In Conclusion**

In this tutorial, we created an image classification model using the <mark style="background-color:#FFD79C">product image</mark> dataset. As it is an entry-level tutorial, we proceeded with an operation-oriented explanation rather than a process explanation for improving accuracy. Image classification models can improve accuracy with precise tuning for each platform or service, and with only a small amount of data labeling, they can achieve mostly satisfactory results. You can learn base models using your own data, or you can digitize and convert your data using self-supervised models and distribute it using automated machine learning (Auto-ML) techniques. Combine a variety of unstructured data (audio, video, text, etc.) with numerical data to create your own model and provide competitive services.

The next step, the [Create Intermediate Image Classification Model] tutorial, is to explore the image classification model in more depth. If you want to learn more about how to build your own image classification model for my service, proceed with the following tutorials.

- [How to Upload to ThanoSQL DB](/en/how-to_guides/ThanoSQL_connecting/data_upload/)
- [Creating an Intermediate Image Classification Model]
- [Image conversion and creating 'My model' using Auto-ML]
- [Deploy My Image Classification model](/en/how-to_guides/ThanoSQL_connecting/thanosql_api/rest_api_thanosql_query/)

!!! tip "**Inquiries on model distribution for 'my own' service**"
    If you have difficulty creating your own model using ThanoSQL or applying it to service, please feel free to contact me below😊

    Inquiries about building a speech recognition model: contact@smartmind.team
