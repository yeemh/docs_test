---
title: Search Image by Image
---
 
# __Search Image by Image__

- Tutorial Difficulty : ★☆☆☆☆
- 7 min read
- Languages : [SQL](https://en.wikipedia.org/wiki/SQL) (100%)
- File location : tutorial_en/thanosql_search/search_image_by_image.ipynb
- References : [MNIST DataSet](http://yann.lecun.com/exdb/mnist/), [A Simple Framework for Contrastive Learning of Visual Representations](https://arxiv.org/abs/2002.05709)

## Tutorial Introduction

<div class="admonition note">
    <h4 class="admonition-title">Understanding Image Vectorization</h4>
    <p>Images (height x width x channel [RGB] x color intensity) are meaningless if the information for each pixel is randomly generated. In other words, an image can only be recognized as an image if each pixel has a specific pattern associated with the surrounding pixels. With this information, it can be inferred that an image can be represented on a low-dimensional feature vector. Recently, studies using machine learning to vectorize and express each image in a low-dimensional space based on similarity have been conducted.</p>
</div>

There are several ways to define the similarity of an image. It can refer to the colors being similar, the objects in the image being similar, or the context of the image being similar (ex. a handwritten number). Although it is difficult to give an exact definition of an image similarity, machine learning learns and vectorizes these general features.

ThanoSQL uses the [Self-Supervised Learning Model](https://en.wikipedia.org/wiki/Self-supervised_learning) to input images into the database and retrieve similar images from it. When you upload images to ThanoSQL's database, a machine learning algorithm places similar images together while placing non-similar images apart. Image features are derived from an unlabeled dataset and fine-tuned with a small amount of labeled data. Then, it can be used for classification or regression tasks.

Furthermore, ThanoSQL uses machine learning algorithms to vectorize datasets. The vectorized data is stored as a database column in the image table and is used to calculate the similarity(distance).

__The following are use case examples of ThanoSQL's similar image search algorithm.__

- Input your favorite image and have similar artworks searched for and recommended to you.
- Find similar images within an album containing thousands of photos.
- Store your images in ThanoSQL's database and create your own search engine or machine learning model utilizing the ThanoSQL Auto-ML regression/classification model.
 
<div class="admonition note">
    <h4 class="admonition-title">In this tutorial</h4>
    <p>👉 This tutorial will use the <mark style="background-color:#FFD79C">MNIST handwriting dataset</mark>. Each image consists of a hand written number between 0 and 9 and is correctly labeled. The MNIST handwriting dataset consists of 1,000 train images and 200 test images.</p>
</div>
    
Create a model that uses ThanoSQL to input handwriting data and retrieves similar images from the database.

[![IMAGE](https://docs.thanosql.ai/img/thanosql_search/search_image_by_image/simclr_img7.png "MNIST data") ](https://docs.thanosql.ai/img/thanosql_search/search_image_by_image/simclr_img7.png)

## __0. Prepare Dataset__

As mentioned in the [ThanoSQL Workspace](https://docs.thanosql.ai/en/getting_started/how_to_use_ThanoSQL/#5-thanosql-workspace), you must create an API token and run the query below to execute the query of ThanoSQL. 


```python
%load_ext thanosql
%thanosql API_TOKEN=<Issued_API_TOKEN>
```

### __Prepare Dataset__


```python
%%thanosql
GET THANOSQL DATASET mnist_data
OPTIONS (overwrite=True)
```

    Success


<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>"<strong>GET THANOSQL DATASET</strong>" downloads the specified dataset to the workspace. </li>
        <li>"<strong>OPTIONS</strong>" specifies the option values to be used for the <strong>GET THANOSQL DATASET</strong> clause.
        <ul>
            <li>"overwrite" : Determines whether to overwrite a dataset if it already exists. If set as True, the old dataset is replaced with the new dataset (True|False, DEFAULT : False) </li>
        </ul>
        </li>
    </ul>
</div>


```python
%%thanosql
COPY mnist_train 
OPTIONS (overwrite=True)
FROM "thanosql-dataset/mnist_data/mnist_train.csv"
```

    Success



```python
%%thanosql
COPY mnist_test 
OPTIONS (overwrite=True)
FROM "thanosql-dataset/mnist_data/mnist_test.csv"
```

    Success


<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>"<strong>COPY</strong>" specifies the name of the dataset to be saved as a database table. </li>
        <li>"<strong>OPTIONS</strong>" specifies the option values to be used for the <strong>COPY</strong> clause.
        <ul>
           <li>"overwrite" : Determines whether to overwrite a table if it already exists. If set as True, the old table is replaced with the new table (True|False, DEFAULT : False) </li>
        </ul>
        </li>
    </ul>
</div>

## __1. Checking the Dataset__

To create a handwriting classification model, we use the <mark style="background-color:#FFEC92">mnist_train</mark> table from the ThanoSQL [database](https://en.wikipedia.org/wiki/Database). The <mark style="background-color:#FFEC92">mnist_train</mark> table contains the file name, label information, and <mark style="background-color:#FFD79C">MNIST</mark> images' file path. To check the contents of the table, run the query below.


```python
%%thanosql
SELECT * 
FROM mnist_train 
LIMIT 5
```




<div class="df_size">
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>image_path</th>
      <th>filename</th>
      <th>label</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>thanosql-dataset/mnist_data/train/6782.jpg</td>
      <td>6782.jpg</td>
      <td>5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>thanosql-dataset/mnist_data/train/1810.jpg</td>
      <td>1810.jpg</td>
      <td>5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>thanosql-dataset/mnist_data/train/33617.jpg</td>
      <td>33617.jpg</td>
      <td>5</td>
    </tr>
    <tr>
      <th>3</th>
      <td>thanosql-dataset/mnist_data/train/27802.jpg</td>
      <td>27802.jpg</td>
      <td>5</td>
    </tr>
    <tr>
      <th>4</th>
      <td>thanosql-dataset/mnist_data/train/50677.jpg</td>
      <td>50677.jpg</td>
      <td>5</td>
    </tr>
  </tbody>
</table>
</div>



<div class="admonition note">
    <h4 class="admonition-title">Understanding the Data</h4>
    <p>The <mark style="background-color:#FFEC92">mnist_train</mark> table contains the following information.</p>
    <ul>
        <li><mark style="background-color:#D7D0FF">image_path</mark>: image path</li>
        <li><mark style="background-color:#D7D0FF">filename</mark>: file name</li>
        <li><mark style="background-color:#D7D0FF">label</mark>: image label</li>
    </ul>
</div>

## __2. Create an Image Model__

Create an image vectorization model using the <mark style="background-color:#FFEC92">mnist_train</mark> table referenced in the previous step. Execute the query below to create a model named <mark style="background-color:#E9D7FD">my_image_search_model</mark>.  
(Estimated time required for query execution: 1 min)


```python
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

    Building model...
    Success


<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>"<strong>BUILD MODEL</strong>" creates and trains a model named <mark style="background-color:#E9D7FD">mnist_model</mark>.</li>
        <li>"<strong>USING</strong>" specifies <code>SimCLR</code> as the base model.</li>
        <li>"<strong>OPTIONS</strong>" specifies the option values used to create a model.
        <ul>
            <li>"image_col" : the name of the column containing the image path (Default: "<mark style="background-color:#D7D0FF">image_path</mark>")</li>
            <li>"max_epochs" : number of times to train with the training dataset.</li>
            <li>"overwrite" : determines whether to overwrite a model if it already exists. If set as True, the old model is replaced with the new model. (True|False, DEFAULT: False) </li>
        </ul>
        </li>
    </ul>
</div>

To vectorize the `mnist_test` images run the following "__CONVERT USING__" query. The vectorized results are stored in a column named <mark style="background-color:#D7D0FF">my_image_search_model_simclr</mark> in the `mnist_test` table.


```python
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




<div class="df_size">
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>image_path</th>
      <th>filename</th>
      <th>label</th>
      <th>my_image_search_model_simclr</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>thanosql-dataset/mnist_data/test/5099.jpg</td>
      <td>5099.jpg</td>
      <td>6</td>
      <td>[0.6678586006, 1.389636755, 0.9091223478, 0.02...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>thanosql-dataset/mnist_data/test/9239.jpg</td>
      <td>9239.jpg</td>
      <td>6</td>
      <td>[0.7813836336000001, 1.2771128416, 0.579796552...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>thanosql-dataset/mnist_data/test/2242.jpg</td>
      <td>2242.jpg</td>
      <td>6</td>
      <td>[0.6741020679, 1.0763620138, 0.7042798996, 0.0...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>thanosql-dataset/mnist_data/test/3451.jpg</td>
      <td>3451.jpg</td>
      <td>6</td>
      <td>[0.5613817573000001, 1.3133333921, 0.482067525...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>thanosql-dataset/mnist_data/test/2631.jpg</td>
      <td>2631.jpg</td>
      <td>6</td>
      <td>[0.6203954220000001, 0.9994402528, 0.535834193...</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>195</th>
      <td>thanosql-dataset/mnist_data/test/8045.jpg</td>
      <td>8045.jpg</td>
      <td>8</td>
      <td>[0.5741235018, 1.0216677189, 0.6239830852, 0.0...</td>
    </tr>
    <tr>
      <th>196</th>
      <td>thanosql-dataset/mnist_data/test/9591.jpg</td>
      <td>9591.jpg</td>
      <td>8</td>
      <td>[0.5873312354, 1.1334174871, 0.452459543900000...</td>
    </tr>
    <tr>
      <th>197</th>
      <td>thanosql-dataset/mnist_data/test/7425.jpg</td>
      <td>7425.jpg</td>
      <td>8</td>
      <td>[0.8913798332, 1.4333107471, 0.6113492846, 0.0...</td>
    </tr>
    <tr>
      <th>198</th>
      <td>thanosql-dataset/mnist_data/test/2150.jpg</td>
      <td>2150.jpg</td>
      <td>8</td>
      <td>[0.9459822178, 1.0716240406, 0.4599372149, 0.0...</td>
    </tr>
    <tr>
      <th>199</th>
      <td>thanosql-dataset/mnist_data/test/5087.jpg</td>
      <td>5087.jpg</td>
      <td>8</td>
      <td>[0.6937886477, 1.1040226221, 0.5953108668, 0.0...</td>
    </tr>
  </tbody>
</table>
<p>200 rows × 4 columns</p>
</div>



<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>"<strong>CONVERT USING</strong>" uses <code>my_image_search_model</code> as an algorithm for image vectorizaion.   </li>
        <li>"<strong>OPTIONS</strong>" specifies the options to be used for image vectorization.
        <ul>
            <li>"table_name" : the table name to be stored in the ThanoSQL database. </li>
            <li>"image_col" : the name of the column containing the image path. (default: "image_path")</li>
        </ul>
        </li>
    </ul>
</div>

## __3. Search for Similar Images Using Image Quantization Models__

This step uses the <mark style="background-color:#E9D7FD">my_image_search_model</mark> image vectorization model and the test table to search for images similar to the "923.jpg" image (handwritten 8).

<a href="https://docs.thanosql.ai/img/thanosql_search/search_image_by_image/simclr_img8.png">
    <img alt="IMAGE" src="https://docs.thanosql.ai/img/thanosql_search/search_image_by_image/simclr_img8.png" style="width:100px">
</a>

<p style="text-align:center">923.jpg Image File </p>


```python
%%thanosql
SEARCH IMAGE images='thanosql-dataset/mnist_data/test/923.jpg' 
USING my_image_search_model 
AS
SELECT * 
FROM mnist_test
```

    Searching...





<div class="df_size">
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>image_path</th>
      <th>filename</th>
      <th>label</th>
      <th>my_image_search_model_simclr</th>
      <th>my_image_search_model_simclr_similarity1</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>thanosql-dataset/mnist_data/test/5099.jpg</td>
      <td>5099.jpg</td>
      <td>6</td>
      <td>[0.6678586006, 1.389636755, 0.9091223478, 0.02...</td>
      <td>0.984266</td>
    </tr>
    <tr>
      <th>1</th>
      <td>thanosql-dataset/mnist_data/test/9239.jpg</td>
      <td>9239.jpg</td>
      <td>6</td>
      <td>[0.7813836336000001, 1.2771128416, 0.579796552...</td>
      <td>0.978499</td>
    </tr>
    <tr>
      <th>2</th>
      <td>thanosql-dataset/mnist_data/test/2242.jpg</td>
      <td>2242.jpg</td>
      <td>6</td>
      <td>[0.6741020679, 1.0763620138, 0.7042798996, 0.0...</td>
      <td>0.982109</td>
    </tr>
    <tr>
      <th>3</th>
      <td>thanosql-dataset/mnist_data/test/3451.jpg</td>
      <td>3451.jpg</td>
      <td>6</td>
      <td>[0.5613817573000001, 1.3133333921, 0.482067525...</td>
      <td>0.981672</td>
    </tr>
    <tr>
      <th>4</th>
      <td>thanosql-dataset/mnist_data/test/2631.jpg</td>
      <td>2631.jpg</td>
      <td>6</td>
      <td>[0.6203954220000001, 0.9994402528, 0.535834193...</td>
      <td>0.982224</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>195</th>
      <td>thanosql-dataset/mnist_data/test/8045.jpg</td>
      <td>8045.jpg</td>
      <td>8</td>
      <td>[0.5741235018, 1.0216677189, 0.6239830852, 0.0...</td>
      <td>0.982209</td>
    </tr>
    <tr>
      <th>196</th>
      <td>thanosql-dataset/mnist_data/test/9591.jpg</td>
      <td>9591.jpg</td>
      <td>8</td>
      <td>[0.5873312354, 1.1334174871, 0.452459543900000...</td>
      <td>0.979652</td>
    </tr>
    <tr>
      <th>197</th>
      <td>thanosql-dataset/mnist_data/test/7425.jpg</td>
      <td>7425.jpg</td>
      <td>8</td>
      <td>[0.8913798332, 1.4333107471, 0.6113492846, 0.0...</td>
      <td>0.983526</td>
    </tr>
    <tr>
      <th>198</th>
      <td>thanosql-dataset/mnist_data/test/2150.jpg</td>
      <td>2150.jpg</td>
      <td>8</td>
      <td>[0.9459822178, 1.0716240406, 0.4599372149, 0.0...</td>
      <td>0.985692</td>
    </tr>
    <tr>
      <th>199</th>
      <td>thanosql-dataset/mnist_data/test/5087.jpg</td>
      <td>5087.jpg</td>
      <td>8</td>
      <td>[0.6937886477, 1.1040226221, 0.5953108668, 0.0...</td>
      <td>0.988866</td>
    </tr>
  </tbody>
</table>
<p>200 rows × 5 columns</p>
</div>



<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>"<strong>SEARCH IMAGE [images|audio|videos]</strong>" defines the image|audio|video file type to search for.  <br></li>
        <li>"<strong>USING</strong>" defines the model used for image vectorization.<br></li>
        <li>"<strong>AS</strong>" defines the embedding table to be used for searches. In this example, the <code>mnist_test</code> table is used. </li>
    </ul>
</div>

To /en/img/tutorials/thanosql_search/search_image_by_image/output the "__SEARCH__" result using the "__PRINT__" clause to /en/img/tutorials/thanosql_search/search_image_by_image/output the top four most similar images, run the following query. Though we've only done a minimal amount of training, you can see that images similar to 8 are returned.


```python
%%thanosql
PRINT IMAGE 
AS (
    SELECT image_path, my_image_search_model_simclr_similarity1 
    FROM (
        SEARCH IMAGE images='thanosql-dataset/mnist_data/test/923.jpg' 
        USING my_image_search_model 
        AS 
        SELECT * 
        FROM mnist_test
        )
    ORDER BY my_image_search_model_simclr_similarity1 DESC 
    LIMIT 4
    )
```

    Searching...
    /home/jovyan/thanosql-dataset/mnist_data/test/923.jpg



    
![jpeg](/en/img/tutorials/thanosql_search/search_image_by_image/output_24_1.jpg)
    


    /home/jovyan/thanosql-dataset/mnist_data/test/7645.jpg



    
![jpeg](/en/img/tutorials/thanosql_search/search_image_by_image/output_24_3.jpg)
    


    /home/jovyan/thanosql-dataset/mnist_data/test/5087.jpg



    
![jpeg](/en/img/tutorials/thanosql_search/search_image_by_image/output_24_5.jpg)
    


    /home/jovyan/thanosql-dataset/mnist_data/test/4391.jpg



    
![jpeg](/en/img/tutorials/thanosql_search/search_image_by_image/output_24_7.jpg)
    


<div class="admonition danger">
    <h4 class="admonition-title">Note</h4>
    <p>The training options of the algorithm recognize the image regardless of the image's left-right inversion and color differences. This is because a dog's picture should be recognized as a dog even if it is flipped or has a color difference. If the color feature is important, such as clothing images, or if vertical and horizontal inversions are important, such as numbers, the training options can be changed.</p>
</div>

## __4. In Conclusion__

In this tutorial, we used the `MNIST` handwriting dataset to vectorize images and perform image search. As this is a beginner-level tutorial, we focused on the process rather than accuracy. The model's accuracy can be improved by adding precise tuning and small amounts of labeling to each dataset. You can create your own image vectorization model to add search capabilities to various types of unstructured datasets and deploy your own model using Auto-ML.
<br>
For the next step, explore the various "__OPTIONS__" and training methods of image vectorization models. If you want to learn more about building your own accurate image model, proceed with the following tutorials.

- [How to Upload to ThanoSQL DB](https://docs.thanosql.ai/en/getting_started/data_upload/)
- [Creating an Intermediate Similar Image Search Model]

<div class="admonition tip">
    <h4 class="admonition-title">Inquiries about deploying a model for your own service</h4>
    <p>If you have any difficulties creating your own model using ThanoSQL or applying it to your services, please feel free to contact us below😊</p>
    <p>For inquiries regarding building an image similarity search model: contact@smartmind.team</p>
</div>
