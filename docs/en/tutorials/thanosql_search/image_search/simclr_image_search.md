---
title: Search image by image
---

# **Search image by image**

## Preface

- Tutorial Difficulty : ★☆☆☆☆
- 7 min read
- Languages : [SQL](https://ko.wikipedia.org/wiki/SQL) (100%)
- File location : tutorial/tutorial_en/query/search_image_by_image_en.ipynb
- References : [MNIST Data Set](http://yann.lecun.com/exdb/mnist/), [A Simple Framework for Contrastive Learning of Visual Representations](https://arxiv.org/abs/2002.05709)
- Updated Date : {{ git_revision_date_localized }}

## Tutorial Introduction

!!! note "Understand image quantization techniques"
    Images are high-dimensional data (height x width x channel [RGB] x color intensity), which means nothing if the information for each pixel is randomly generated. In other words, each pixel can only be recognized as an image if it has a specific pattern associated with the surrounding pixel. This means that an image can be represented on a lower-dimensional characteristic vector than it really is. Recently, studies using artificial intelligence to numericalize and express each image in a low-dimensional space according to the similarity of the meaning of each image have been conducted in various ways, and these have been used in various names such as image digitization, vectorization, and embedding.

There are many ways to define the similarity of an image. The colors may be similar, the objects in the image may be similar, or the meanings may be the same, like handwriting. Although it is difficult to define similar images accurately, artificial intelligence learns and quantifies the general features that images possess.

ThanoSQL uses the [Self-Supervised Learning Model](https://en.wikipedia.org/wiki/Self-supervised_learning) to enter images and search for similar images in the DB. When you upload images held by users to ThanoSQL's DB, similar images are placed close and other images are placed far away through artificial intelligence algorithms and self-learning proceeds. You can learn the general representation of an image from a dataset with no correct answers and fine-tune it to an image with a small target (Target) for classification or regression.

In addition, ThanoSQL uses artificial intelligence algorithms to quantify data sets. This digitized data is stored within a column in the DB and is used to search for similar images through similarity (distance) calculations between images.

**Below is an example and utilization of the ThanoSQL Similar-image search algorithm.**

- When the user receives the image he or she likes, he or she searches for similar-feeling artworks among the artworks stored in the DB and recommends them to the user.<br>
- Similar images can be found on albums containing thousands of photos.<br>
- Store the digitized values of the images in the DB of ThanoSQL and use the ThanoSQL Auto-ML regression/classification prediction model to create your own search engine or artificial intelligence model. <br>

!!! note "In this tutorial"
    :point_right: This tutorial will use <mark style="background-color:#FFD79C">MNIST handwriting dataset</mark>. Each image is a fixed size (28x28 = 784 pixels) with a value between 0 and 1 and provides a number from 0 to 9 written by many people with the correct answer. It consists of 1,000 learning datasets and 200 testing datasets. <br>

Let's create a model that uses ThanoSQL to enter handwriting data and search for images similar to the input image within the DB.

[![IMAGE](/img/thanosql_search/simclr_search/simclr_img7.png "MNIST data")](/img/thanosql_search/simclr_search/simclr_img7.png)

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
COPY mnist_train
OPTIONS(overwrite=True)
FROM "tutorial_data/mnist_data/mnist_train.csv"
```

```sql
%%thanosql
COPY mnist_test
OPTIONS(overwrite=True)
FROM "tutorial_data/mnist_data/mnist_test.csv"
```

!!! note "**Query Details**"
    - Specifies the data set name to copy to the DB using the "**COPY**" query syntax.
    - Specify the options to use for **COPY** via the "**OPTIONS**" query syntax.
        - "overwrite" : Set whether or not a data set with the same name can be overwritten when it exists on the DB. If True, the existing dataset is changed to the new dataset (True|False, DEFAULT: False)

## **1. Check Data Set**

Use the <mark style="background-color:#FFEC92">mnist_train</mark> table stored in ThanoSQL [DB](https://en.wikipedia.org/wiki/Database) to create a handwriting classification model. The <mark style="background-color:#FFEC92">mnist_train</mark> table is a table containing the path, file name, and label information where <mark style="background-color:#FFD79C">MNIST</mark> image files are stored. Run the query statement below and check the contents of the table.

```sql
%%thanosql
SELECT *
FROM mnist_train
LIMIT 5
```

[![IMAGE](/img/thanosql_search/simclr_search/simclr_img1.png)](/img/thanosql_search/simclr_search/simclr_img1.png)

!!! note "Understanding Data Tables"
    The <mark style="background-color:#FFEC92">mnist_train</mark> table contains the following information: The "6782.jpg" image file is a handwritten image with the number 5.

    - <mark style="background-color:#D7D0FF">image_path</mark>: Image Path
    - <mark style="background-color:#D7D0FF">filename</mark>: File Name
    - <mark style="background-color:#D7D0FF">label</mark> : Label images

## **2. Creating an Image Quantization Model**

Create an image quantization model using the <mark style="background-color:#FFEC92">mnist_train</mark> table that you saw in the previous step. Run the query syntax below to create a model named <mark style="background-color:#E9D7FD">my_image_search_model</mark>.  
(Expected time required for query execution: 1 min)

```sql
%%thanosql
BUILD MODEL my_image_search_model
USING SimCLR
OPTIONS (
    image_col="image_path",
    max_epochs=1,
    overwrite=True
    )
AS
SELECT *
FROM mnist_train
```

!!! note "Query Details"
    - Use the "**BUILD MODEL**" query syntax to create and train a model called <mark style="background-color:#E9D7FD">mnist_model</mark>.
    - The "**USING**" query syntax specifies the use of the <mark style="background-color:#E9D7FD">SimCLR</mark> model as the base model.
    - Specify the options to use for model creation via the "**OPTIONS**" query syntax.
        - "image_col" : A column containing the path of an image in the data table (Default: "<mark style="background-color:#D7D0FF" >image_path</mark>")
        - "max_epochs" : Number of data sets to generate an image quantization model
        - "overwrite" : If the model of the same name exists. If true, the existing model is changed to a new model (True|False, DEFAULT: False)

Run the "**CONVERT USING**" query syntax to quantify the 'mnist_test' images. The quantized results are stored in a column named <mark style="background-color:#D7D0FF">my_image_search_model_simclr</mark> in the table 'mnist_test'.

```sql
%%thanosql
CONVERT USING my_image_search_model
OPTIONS (
    table_name= "mnist_test",
    image_col="image_path"
    )
AS
SELECT *
FROM mnist_test

```

[![IMAGE](/img/thanosql_search/simclr_search/simclr_img3.png)](/img/thanosql_search/simclr_search/simclr_img3.png)

!!! note "Query Details"
    - The "**CONVERT USING**" query syntax uses 'my_image_search_model' as an algorithm for image quantification.
    - Define the variables required for image quantification through the "**OPTIONS**" query syntax.
        - "table_name" : Defines the name of the table to be stored in the ThanoSQL DB.
        - "image_col" : Column containing the path of the image in the data table (default: "image_path")

## **3. To search for similar images using image quantization models**

This step uses the `my_image_search_model` image quantization model and the test table to search for images similar to the "923.jpg" image file (handwriting 8).<br>

[![IMAGE](/img/thanosql_search/simclr_search/simclr_img8.png){: style="width:100px"}](/img/thanosql_search/simclr_search/simclr_img8.png)

<p style = "text-align:center">923.jpg Image file</p>

```sql
%%thanosql
SEARCH IMAGE images='tutorial_data/mnist_data/test/923.jpg'
USING my_image_search_model
AS
SELECT *
FROM mnist_test
```

[![IMAGE](/img/thanosql_search/simclr_search/simclr_img4.png)](/img/thanosql_search/simclr_search/simclr_img4.png)

!!! note "Query Details"
    - "**SEARCH IMAGE [images|audio|video]**" query syntax defines the image|audio|video file you want to search for. <br>
    - "**USING**" defines the model used for image quantification.<br>
    - The "**AS**" query syntax defines the embedding table to use for searches. Use table `mnist_embds`

Run the following query to output the "**SEARCH**" results using ThanoSQL's "**PRINT**" query syntax to output the top four most similar. We've only done a little bit of learning, but you can see that it's outputting an image similar to 8.

```sql
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

[![IMAGE](/img/thanosql_search/simclr_search/simclr_img5.png)](/img/thanosql_search/simclr_search/simclr_img5.png)

!!! danger "Notes for reference"
    The basic learning options of the image similarity search algorithm are learned to recognize the image as the same regardless of the image's left-right inversion, color change, etc. This is because a dog's picture should be recognized as a dog even if it is flipped or changed in color. If color changes are important, such as clothing images, or if vertical and horizontal twists are important, such as numbers, the options should be changed when learning. This tutorial shows the characteristics of these image similarity searches.

## **4. In Conclusion**

In this tutorial, we used the `MNIST` handwriting data set to perform image quantification and similar image search based on quantification results. In this tutorial, we explained the operation rather than the accuracy of image similarity. Image quantification models can improve accuracy by adding precise tuning and small amounts of correct answers to each image data set during learning. You can create your own image quantization model to add search capabilities to various types of unstructured data sets and deploy your own model using Auto-ML techniques.
<br>
The next step is to explore the various "**OPTIONS**" query syntax and learning methods of image quantification models. If you want to learn more about how to build your own accurate image conversion model, go ahead with the following tutorials.

- [How to Upload to ThanoSQL DB](/en/how-to_guides/ThanoSQL_connecting/data_upload/)
- [Creating an Intermediate Similar Image Search Model]

!!! tip "**Inquiries on model distribution for 'my own' service**"
    If you have difficulty creating your own model using ThanoSQL or applying it to service, please feel free to contact me below😊

    Inquiries about building a speech recognition model: contact@smartmind.team
