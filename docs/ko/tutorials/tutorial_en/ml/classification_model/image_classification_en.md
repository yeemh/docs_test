---
jupyter:
  jupytext:
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.3'
      jupytext_version: 1.14.1
  kernelspec:
    display_name: Python 3 (ipykernel)
    language: python
    name: python3
---

# __Create an image classification model__

**Understanding Classification Operations** 
--- 
👉 Classification is a form of machine learning used to predict the category(or class) to which the target belongs. For example, both binary classifications that classify men or women and multiple classifications that predict animal species (dogs, cats, rabbits, etc.) are included in the classification task.

**In this tutorial**
--- 
👉 Build a model that classifies more than 10,000 products using the `Product Image` dataset from [AI-Hub](https://aihub.or.kr/)), a leading AI open data sharing platform. The built model can be used as a detection and identification solution in smart logistics warehouses and unmanned stores. Datasets typically consist of over 10,000 commodity datasets of images and label (correct) pairs used to learn image classification techniques, and contain a total of 1,440,000 images. In this tutorial, you will only use 1,800 sheets of training data and 200 sheets of test data to learn how to use ThanoSQL and to check results quickly.

__Precautions__    
> - The image classification model can be used to predict one target value (Target, Category/Label) from one image.
> - There must be a column representing the path of the image and a column representing the target value of the image.
> - The base model of the corresponding image classification model ( `CONVNEXT `) uses a GPU.Depending on the size and batch size of the model used, GPU memory may be low. In this case, try using a smaller model or reducing the batch size.


## __0. Preparing a dataset__


To use the query syntax of ThanoSQL, you must create an API token and run the query below, as mentioned in the [ThanoSQL Workspace](https://docs.thanosql.ai/quick_start/how_to_use_ThanoSQL/#5-thanosql).

```python tags=[]
%load_ext thanosql
%thanosql API_TOKEN=<Issued_API_TOKEN>
```

```python tags=[]
%%thanosql
COPY product_image_train
OPTIONS (overwrite=True) 
FROM "tutorial_data/product_image_data/product_image_train.csv"
```

```python tags=[]
%%thanosql
COPY product_image_test
OPTIONS (overwrite=True) 
FROM "tutorial_data/product_image_data/product_image_test.csv"
```

__OPTIONS__ : 

When __overwrite is true__, the user can create a data table with the same name as the previously created data table.  
On the other hand, when __overwrite is False__, the user cannot create a data table with the same name as the previously created data table.


## __1. Check Dataset__

To proceed with this tutorial, we use the <mark style="background-color:#FFEC92 ">product_image_train</mark> table stored in the ThanoSQL DB. Run the query statement below to verify the contents of the table.

```python tags=[]
%%thanosql
SELECT *
FROM product_image_train
LIMIT 5
```

__Understanding data__ 
- image_path : Location information of each image's file
- div_l : Product's 1st Class
- div_s : Product's 2nd Class
- div_n : Product's 3rd Class
- comp_nm : Manufacturer
- img_prod_nm : Product name(in image)
- multi : Multiple product images

```python tags=[]
%%thanosql
PRINT IMAGE
AS
SELECT image_path
FROM product_image_train
LIMIT 5
```

## __2. Predicting Product Image Classification Results Using Pretrained Models__

By executing the following query statement, you can quickly predict the results using the <mark style="background-color:#E9D7FD ">tutorial_product_classifier</mark> model, a pre-trained product image classification model.

```python tags=[]
%%thanosql
PREDICT USING tutorial_product_classifier
AS
SELECT *
FROM product_image_test
```

## __3. Create an image classification model__

Create an image classification model using the <mark style="background-color:#FFEC92 ">product_image_train</mark> dataset that you saw in the previous step. Run the query syntax below to create a model named <mark style="background-color:#E9D7FD ">my_product_classifier</mark>.

(Expected time required for query execution: 5 min)


```python tags=[]
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

__Query Details__ 
- "__BUILD MODEL__" Use the query syntax to create and train the <mark style="background-color:#E9D7FD">my_image_classifier</mark> model.
- "__USING__" The query syntax specifies the use of `ConvNeXt_Tiny` as the base model.
- "__OPTIONS__" Specifies the options used to create the model through the query syntax.
    - "image_col" : Name of column containing image path
    - "label_col" : The name of the column containing information about the target value
    - "epochs : Number of times to learn all learning datasets
    
When __overwrite is true__, the user can create a data table with the same name as the previously created data table.  
On the other hand, when __overwrite is False__, the user cannot create a data table with the same name as the previously created data table.


## __4. Predict product image classification results using the generated model__

Use the product image classification model(<mark style="background-color:#FFEC92 ">my_product_classifier</mark>) created in the previous step to predict the target value of a specific image(Data table not used for learning, <mark style="background-color:#D7D0FF">product_image_test</mark>). Once the query has been performed below, the prediction results will be returned in the 'predicted' column.

```python tags=[]
%%thanosql
PREDICT USING my_product_classifier
OPTIONS (
    image_col='image_path'
    )
AS
SELECT *
FROM product_image_test
```

__Query details__ 
- "__PREDICT USING__" Use the my_image_classifier model created in the previous step through the query syntax for prediction.
- "__OPTIONS__" Specify the options to use for prediction through query syntax.
    - "image_col" : The name of the column in which the path of the image to be used for prediction is recorded

