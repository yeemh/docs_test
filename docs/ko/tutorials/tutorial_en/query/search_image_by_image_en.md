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

# __Search image by image__

**Understanding image digitization techniques**
---
👉 Images are high-dimensional data (height x width x channel (RGB) x color intensity), which means nothing if the information of each pixel is randomly generated. In other words, each pixel can only be recognized as an image if it has a specific pattern associated with the surrounding pixel. This means that an image can be represented on a lower-dimensional characteristic vector than it really is. Recently, studies using artificial intelligence to numericalize and express each image in a low-dimensional space according to the similarity of the meaning of each image have been conducted in various ways, and these have been used in various names such as image digitization, vectorization, and embedding.

**In this tutorial**
---
👉 This tutorial will use the MNIST handwriting dataset. Each image is a fixed size (28x28 = 784 pixels) with a value between 0-1 and provides a number from 0-9 written by many people with the correct answer. It consists of 1,000 learning datasets and 200 testing datasets.

Let's create a model that uses ThanoSQL to enter handwriting data and search for images similar to the input image within the DB.


## __0. Check dataset__


To use the query syntax of ThanoSQL, you must create an API token and run the query below, as mentioned in the [ThanoSQL Workspace](https://docs.thanosql.ai/quick_start/how_to_use_ThanoSQL/#5-thanosql).

```python tags=[]
%load_ext thanosql
%thanosql API_TOKEN=<Issued_API_TOKEN>
```

```python
%%thanosql
COPY mnist_train 
OPTIONS(overwrite = True)
FROM "tutorial_data/mnist_data/mnist_train.csv"
```

```python
%%thanosql
COPY mnist_test 
OPTIONS(overwrite = True)
FROM "tutorial_data/mnist_data/mnist_test.csv"
```

__OPTIONS__ : 

When __overwrite is true__, the user can create a data table with the same name as the previously created data table.  
On the other hand, when __overwrite is False__, the user cannot create a data table with the same name as the previously created data table.

<!-- #region tags=[] -->
## __1. Check dataset__
<!-- #endregion -->

Use the mnist_train table stored in the ThanoSQL DB to create a handwriting classification model. The mnist_train table is a table containing the path, file name, and label information where MNIST image files are stored. Run the query statement below and check the contents of the table.

```python
%%thanosql
SELECT * 
FROM mnist_train 
LIMIT 5
```

The mnist_train table contains the following information: The "6782.jpg" image file is a handwritten image with the number 5.

- img_path: Image Path
- filename: file name
- label : image label


## __2. Creating an Image Numerical Model__


Create an image quantification model using the mnist_train table from the previous step. Execute the query syntax below to create a model named my_image_search_model.


(Estimated time required for query execution: 1 min)

```python
%%thanosql
BUILD MODEL my_image_search_model
USING SimCLR
OPTIONS (
    image_col="img_path",
    max_epochs=5,
    overwrite=True
    )
AS 
SELECT * 
FROM mnist_train
```

# __Query Details__ 
- Create and train a model called my_image_search_model using the query syntax "__BUILD MODEL__".
- The "__USING__" query syntax specifies the use of the SimCLR model as the base model.
- Specify the options to use for model creation via the query syntax "__OPTIONS__".  
    -  "image_col" : Column containing image path in data table (Default: "image_path")    
    -  "max_epochs" : Number of dataset learnings to generate image quantization models


Use the query syntax below to view the results of the image quantification. Embedding images `mnist_test` using the query syntax '__CONVERTUSING__' `my_image_search_model`


```python
%%thanosql
CONVERT USING my_image_search_model
OPTIONS (
    table_name= "mnist_test",
    image_col="img_path"
    )
AS 
SELECT * 
FROM mnist_test
```

__Query Details__ 
- The query syntax "__CONVERT USING__" uses `my_image_search_model` as an algorithm for image quantification.   
- Define the variables required for image quantification with the query syntax "__OPTIONS__". 
    - "table_name" : Defines the table name to be stored in the ThanoSQL DB.
    - "image_col" : Defines the column name containing the image file path in the data table. (DEFAULT: "image_path")


Create a new column named `my_image_search_model_simclr` in the table `mnist_test` and save the quantization results.


## __3. Search for similar images using image quantization models__


This step uses the my_image_search_model image quantization model and the test table to search for images similar to the "923.jpg" image file (handwriting 8).

```python
%%thanosql
SEARCH IMAGE images='tutorial_data/mnist_data/test/923.jpg' 
USING my_image_search_model 
AS
SELECT * 
FROM mnist_test
```

__Query Details__  

- The query syntax "___SEARCH IMAGE images=__" defines the image file you want to search for.  <br>
- "__USING__" defines the model used for image quantification.<br>
- The "__AS__" query syntax defines the embedding table to use for searches. Use table `mnist_embds`


Run the following query to output the "__SEARCH__" result using the "__PRINT__" query syntax in ThanoSQL to output the top four most similar. We've only done a little bit of learning, but you can see that it's outputting an image similar to 8.

```python
%%thanosql
PRINT IMAGE 
AS (
    SELECT image_path, my_image_search_model_simclr_similarity1 
    FROM (
        SEARCH IMAGE images='tutorial_data/mnist_data/test/923.jpg' 
        USING my_image_search_model 
        AS 
        SELECT * 
        FROM mnist_test
        )
    ORDER BY my_image_search_model_simclr_similarity1 DESC 
    LIMIT 4
    )
```

__Notes for reference__ 

The basic learning options of the image similarity search algorithm are learned to recognize the image as the same regardless of the image's left-right inversion, color change, etc. This is because a dog's picture should be recognized as a dog even if it is flipped or changed in color. If color changes are important, such as clothing images, or if vertical and horizontal twists are important, such as numbers, the options should be changed when learning. This tutorial shows the characteristics of these image similarity searches.
