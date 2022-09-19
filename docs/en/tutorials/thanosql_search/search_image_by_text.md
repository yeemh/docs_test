---
title: Search Image by Text
---

# __Search Image by Text__

- Tutorial Difficulty : ★★☆☆☆
- 7 min read
- Languages : [SQL](https://en.wikipedia.org/wiki/SQL) (100%)
- File location : tutorial_en/thanosql_search/search_image_by_text.ipynb
- References : [Unsplash Dataset - Lite](https://unsplash.com/data), [Learning Transferable Visual Models From Natural Language Supervision](https://arxiv.org/abs/2103.00020)

## Tutorial Introduction

<div class="admonition note">
    <h4 class="admonition-title">Understanding Vectorizaion</h4>
    <p>For computers to understand human language, it must be vectorized. Recently, studies on pre-built models such as <a href="https://en.wikipedia.org/wiki/BERT_(language_model)">BERT</a> and <a href="https://en.wikipedia.org/wiki/GPT-3">GPT-3</a> have been actively carried out, showing remarkable results. These models identify the meaning of each sentence based on <a href="https://en.wikipedia.org/wiki/Self-supervised_learning">Self-Supervised Learning</a>, and sentences with similar meanings are vectorized and placed close to each other in a low-dimensional space. Self-supervised learning allows learning without labeling by determining whether each sentence/context is true/false. It randomly shuffles the order between sentences or masks some words.</p>
</div>

The handling of different forms of input, such as texts and images, together is called multi-modal. "**CLIP: Connecting Text and Image**" utilizes a multi-modal model to understand low-dimensional vectors. While previous models only trained <a href="https://en.wikipedia.org/wiki/Feature_(machine_learning)">features</a> of the image, the multi-modal model can train both images and texts. It can even learn the features of the texts that describe the images. In addition, by placing texts and images together in a low-dimensional space, the similarity between texts and images can be calculated. From this, a search algorithm can be derived.

ThanoSQL uses machine learning algorithms to vectorize datasets. The vectorized data is stored in a database column, and is used to search for similar images. This can be done by calculating the similarity scores.

__The following are examples and applications of the ThanoSQL text-image search algorithm.__

- Describe the desired scene of an image or video you have with text and search for the image that is the most similar to it. Using text-based descriptions rather than the keywords of a product, search for the most similar product images.
- Search for a timestamp in a Youtube video to place an advertisement in. Easily search for mountain or camping scenes to insert your travel advertisement. 

<div class="admonition note">
    <h4 class="admonition-title">In this tutorial</h4>
    <p>👉 Unsplash released images taken by more than 200,000 photographers for free to be used as an AI dataset. <code>Unsplash Dataset - Lite</code> consists of 25,000 nature-themed images with 25,000 keywords. </p>
</div>

In this tutorial, we will use the text-image search model to search for images from the `Unsplash Dataset - Lite` dataset using text.

## __0. Prepare Dataset and Model__

As mentioned in the [ThanoSQL Workspace](https://docs.thanosql.ai/en/getting_started/how_to_use_ThanoSQL/#5-thanosql-workspace), you must create an API token and run the query below to execute the query of ThanoSQL. 


```python
%load_ext thanosql
%thanosql API_TOKEN=<Issued_API_TOKEN>
```

### __Prepare Dataset__


```python
%%thanosql
GET THANOSQL DATASET unsplash_data
OPTIONS (overwrite=True)
```

    Success


<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>"<strong>GET THANOSQL DATASET</strong>" downloads the specified dataset to the workspace. </li>
        <li>"<strong>OPTIONS</strong>" specifies the option values to be used for the <strong>GET THANOSQL DATASET</strong> clause.
        <ul>
            <li>"overwrite" : determines whether to overwrite a dataset if it already exists. If set as True, the old dataset is replaced with the new dataset (True|False, DEFAULT : False) </li>
        </ul>
        </li>
    </ul>
</div>


```python
%%thanosql
COPY unsplash_data 
OPTIONS (overwrite=True)
FROM 'thanosql-dataset/unsplash_data/unsplash.csv'
```

    Success


<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>"<strong>COPY</strong>" specifies the name of the dataset to be saved as a database table. </li>
        <li>"<strong>OPTIONS</strong>" specifies the option values to be used for the <strong>COPY</strong> clause.
        <ul>
           <li>"overwrite" : determines whether to overwrite a table if it already exists. If set as True, the old table is replaced with the new table (True|False, DEFAULT : False) </li>
        </ul>
        </li>
    </ul>
</div>

### __Prepare the Model__


```python
%%thanosql
GET THANOSQL MODEL tutorial_search_clip
OPTIONS (overwrite=True)
AS tutorial_search_clip
```

    Success


<div class="admonition note">
    <h4 class="admonition-title">Query Details </h4>
    <ul>
        <li>"<strong>GET THANOSQL MODEL</strong>" downloads the specified model to the workspace. </li>
        <li>"<strong>OPTIONS</strong>" specifies the option values to be used for the <strong>GET THANOSQL MODEL</strong> clause.
        <ul>
            <li>"overwrite" : determines whether to overwrite a model if it already exists. If set as True, the old model is replaced with the new model (True|False, DEFAULT : False) </li>
        </ul>
        </li>
        <li>"<strong>AS</strong>" names the given model. If not specified, the model will be named as the default <code>THANOSQL MODEL</code> name.</li>
    </ul>
</div>

## __1. Check Dataset__

To create a text-image search model, we use the `unsplash_data` table. To check the contents of the table, run the query below.


```python
%%thanosql
SELECT photo_id, image_path, photo_image_url, photo_description, ai_description
FROM unsplash_data
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
      <th>photo_id</th>
      <th>image_path</th>
      <th>photo_image_url</th>
      <th>photo_description</th>
      <th>ai_description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>XMyPniM9LF0</td>
      <td>thanosql-dataset/unsplash_data/XMyPniM9LF0.jpg</td>
      <td>https://images.unsplash.com/uploads/1411949294...</td>
      <td>Woman exploring a forest</td>
      <td>woman walking in the middle of forest</td>
    </tr>
    <tr>
      <th>1</th>
      <td>rDLBArZUl1c</td>
      <td>thanosql-dataset/unsplash_data/rDLBArZUl1c.jpg</td>
      <td>https://images.unsplash.com/photo-141633941111...</td>
      <td>Succulents in a terrarium</td>
      <td>succulent plants in clear glass terrarium</td>
    </tr>
    <tr>
      <th>2</th>
      <td>cNDGZ2sQ3Bo</td>
      <td>thanosql-dataset/unsplash_data/cNDGZ2sQ3Bo.jpg</td>
      <td>https://images.unsplash.com/photo-142014251503...</td>
      <td>Rural winter mountainside</td>
      <td>rocky mountain under gray sky at daytime</td>
    </tr>
    <tr>
      <th>3</th>
      <td>iuZ_D1eoq9k</td>
      <td>thanosql-dataset/unsplash_data/iuZ_D1eoq9k.jpg</td>
      <td>https://images.unsplash.com/photo-141487280988...</td>
      <td>Poppy seeds and flowers</td>
      <td>red common poppy flower selective focus phography</td>
    </tr>
    <tr>
      <th>4</th>
      <td>BeD3vjQ8SI0</td>
      <td>thanosql-dataset/unsplash_data/BeD3vjQ8SI0.jpg</td>
      <td>https://images.unsplash.com/photo-141700759404...</td>
      <td>Silhouette near dark trees</td>
      <td>trees during night time</td>
    </tr>
  </tbody>
</table>
</div>



<div class="admonition note">
    <h4 class="admonition-title">Understanding the Data</h4>
    <ul>
        <li><code>photo_id</code>: unique id column of the image.</li>
        <li><code>image_path</code>: the name of the column that stores the image path.</li>
        <li><code>photo_image_url</code>: the name of the column indicating the address of the original image in the unsplash website.</li>
        <li><code>photo_description</code>: the name of the column that represents a short description of the image.</li>
        <li><code>ai_description</code>: the name of the column that describes the image generated by AI.</li>
    </ul>
</div>


```python
%%thanosql
PRINT IMAGE 
AS
SELECT image_path 
FROM unsplash_data 
LIMIT 5
```

    /home/jovyan/thanosql-dataset/unsplash_data/XMyPniM9LF0.jpg



    
![jpeg](/en/img/tutorials/thanosql_search/search_image_by_text/output_16_1.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/rDLBArZUl1c.jpg



    
![jpeg](/en/img/tutorials/thanosql_search/search_image_by_text/output_16_3.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/cNDGZ2sQ3Bo.jpg



    
![jpeg](/en/img/tutorials/thanosql_search/search_image_by_text/output_16_5.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/iuZ_D1eoq9k.jpg



    
![jpeg](/en/img/tutorials/thanosql_search/search_image_by_text/output_16_7.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/BeD3vjQ8SI0.jpg



    
![jpeg](/en/img/tutorials/thanosql_search/search_image_by_text/output_16_9.jpg)
    


## __2. Creating a Vectorization Model__

<div class="admonition danger">
    <h4 class="admonition-title">Notes</h4>
    <p>Because the text-image algorithm takes a long time to train and since it uses a pre-trained model that used 400 million datasets to train, we omit the training process that uses the "<strong>BUILD MODEL</strong>" query in this tutorial. The <code>tutorial_search_clip</code> model utilizes a pre-trained model that uses <code>clipen</code>. When the "<strong>CONVERT USING</strong>" statement is executed, a column containing the vectorized images is created with the name "model_name (<code>tutorial_search_clip</code>)_base algorithm_name (<code>clipen</code>)". When the "<strong>SEARCH IMAGE</strong>" statement is executed, an similarity column is created with the name "model_name (<code>tutorial_search_clip</code>)_base algorithm_name (<code>clipen</code>)_similarity count(1)". "Count" here refers to the number of texts used in the search. When a search is performed with two or more texts, the number of columns is sequentially increased. For more details, see below.</p>
</div>
(Expected time to execute query: 3 min)  

<p>To vectorize the <code>unsplash_data</code> images, run the "<strong>CONVERT USING</strong>" query. Results are stored in the new <mark style="background-color:#D7D0FF">tutorial_search_clip_clipen</mark> column. (The resulting column name will be added as {model_name}_{base_model_name}) </p>


```python
%%thanosql
CONVERT USING tutorial_search_clip
OPTIONS (
    image_col="image_path", 
    table_name="unsplash_data", 
    batch_size=128
    )
AS 
SELECT *
FROM unsplash_data
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
      <th>photo_id</th>
      <th>image_path</th>
      <th>photo_image_url</th>
      <th>photo_description</th>
      <th>ai_description</th>
      <th>tutorial_search_clip_clipen</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>XMyPniM9LF0</td>
      <td>thanosql-dataset/unsplash_data/XMyPniM9LF0.jpg</td>
      <td>https://images.unsplash.com/uploads/1411949294...</td>
      <td>Woman exploring a forest</td>
      <td>woman walking in the middle of forest</td>
      <td>[-0.0148640582, 0.0549194068, 0.0118315415, 0....</td>
    </tr>
    <tr>
      <th>1</th>
      <td>rDLBArZUl1c</td>
      <td>thanosql-dataset/unsplash_data/rDLBArZUl1c.jpg</td>
      <td>https://images.unsplash.com/photo-141633941111...</td>
      <td>Succulents in a terrarium</td>
      <td>succulent plants in clear glass terrarium</td>
      <td>[-0.0345519036, 0.0314463899, -0.0065574604, 0...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>cNDGZ2sQ3Bo</td>
      <td>thanosql-dataset/unsplash_data/cNDGZ2sQ3Bo.jpg</td>
      <td>https://images.unsplash.com/photo-142014251503...</td>
      <td>Rural winter mountainside</td>
      <td>rocky mountain under gray sky at daytime</td>
      <td>[-0.031663157000000004, 0.0538793579, 0.013499...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>iuZ_D1eoq9k</td>
      <td>thanosql-dataset/unsplash_data/iuZ_D1eoq9k.jpg</td>
      <td>https://images.unsplash.com/photo-141487280988...</td>
      <td>Poppy seeds and flowers</td>
      <td>red common poppy flower selective focus phography</td>
      <td>[0.0018179789000000001, 0.009972040500000001, ...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>BeD3vjQ8SI0</td>
      <td>thanosql-dataset/unsplash_data/BeD3vjQ8SI0.jpg</td>
      <td>https://images.unsplash.com/photo-141700759404...</td>
      <td>Silhouette near dark trees</td>
      <td>trees during night time</td>
      <td>[-0.0223454703, 0.0129929734, -0.0019434979, -...</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>24963</th>
      <td>c7OrOMxrurA</td>
      <td>thanosql-dataset/unsplash_data/c7OrOMxrurA.jpg</td>
      <td>https://images.unsplash.com/photo-159300793778...</td>
      <td>None</td>
      <td>black metal fence during daytime</td>
      <td>[-0.0114669325, -0.0021708854, -0.0104937684, ...</td>
    </tr>
    <tr>
      <th>24964</th>
      <td>15IuQ5a0Qwg</td>
      <td>thanosql-dataset/unsplash_data/15IuQ5a0Qwg.jpg</td>
      <td>https://images.unsplash.com/photo-159296761254...</td>
      <td>Pearl earrings and seashells</td>
      <td>white and brown seashell on white surface</td>
      <td>[-0.0289349873, 0.051604818600000005, 0.015792...</td>
    </tr>
    <tr>
      <th>24965</th>
      <td>w8nrcXz8pwk</td>
      <td>thanosql-dataset/unsplash_data/w8nrcXz8pwk.jpg</td>
      <td>https://images.unsplash.com/photo-159299937329...</td>
      <td>None</td>
      <td>leopard on brown tree trunk during daytime</td>
      <td>[0.0069489880000000006, -0.0320789367, -0.0139...</td>
    </tr>
    <tr>
      <th>24966</th>
      <td>n1jHrRhehUI</td>
      <td>thanosql-dataset/unsplash_data/n1jHrRhehUI.jpg</td>
      <td>https://images.unsplash.com/photo-159192792878...</td>
      <td>Floral truck in the streets of Rome</td>
      <td>woman in beige coat and white hat standing on ...</td>
      <td>[0.005270903, -0.0013724641000000001, 0.025223...</td>
    </tr>
    <tr>
      <th>24967</th>
      <td>Ic74ACoaAX0</td>
      <td>thanosql-dataset/unsplash_data/Ic74ACoaAX0.jpg</td>
      <td>https://images.unsplash.com/photo-159240763188...</td>
      <td>None</td>
      <td>green plants on brown rocky mountain under blu...</td>
      <td>[-0.0143270381, 0.0594774038, 0.0045367004, -0...</td>
    </tr>
  </tbody>
</table>
<p>24968 rows × 6 columns</p>
</div>



<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>"<strong>CONVERT USING</strong>" uses the <code>tutorial_search_clip</code> model as an algorithm for image vectorization.</li>
        <li>"<strong>OPTIONS</strong>" specifies the option values required for image vectorization.
        <ul>
            <li>"table_name" : the name of the table</li>
            <li>"image_col" : the name of the column containing the image path </li>
            <li>"batch_size" : the size of dataset bundle utilized in a single cycle of training. The larger the number, the better the learning performance. However, considering the size of the memory, only 128 is used in this case. (DEFAULT: 16) </li>
        </ul>
        </li>
    </ul>
</div>


```python
%%thanosql
SELECT *
FROM unsplash_data
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
      <th>photo_id</th>
      <th>image_path</th>
      <th>photo_image_url</th>
      <th>photo_description</th>
      <th>ai_description</th>
      <th>tutorial_search_clip_clipen</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>XMyPniM9LF0</td>
      <td>thanosql-dataset/unsplash_data/XMyPniM9LF0.jpg</td>
      <td>https://images.unsplash.com/uploads/1411949294...</td>
      <td>Woman exploring a forest</td>
      <td>woman walking in the middle of forest</td>
      <td>[-0.0148640582, 0.0549194068, 0.0118315415, 0....</td>
    </tr>
    <tr>
      <th>1</th>
      <td>rDLBArZUl1c</td>
      <td>thanosql-dataset/unsplash_data/rDLBArZUl1c.jpg</td>
      <td>https://images.unsplash.com/photo-141633941111...</td>
      <td>Succulents in a terrarium</td>
      <td>succulent plants in clear glass terrarium</td>
      <td>[-0.0345519036, 0.0314463899, -0.0065574604, 0...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>cNDGZ2sQ3Bo</td>
      <td>thanosql-dataset/unsplash_data/cNDGZ2sQ3Bo.jpg</td>
      <td>https://images.unsplash.com/photo-142014251503...</td>
      <td>Rural winter mountainside</td>
      <td>rocky mountain under gray sky at daytime</td>
      <td>[-0.031663157000000004, 0.0538793579, 0.013499...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>iuZ_D1eoq9k</td>
      <td>thanosql-dataset/unsplash_data/iuZ_D1eoq9k.jpg</td>
      <td>https://images.unsplash.com/photo-141487280988...</td>
      <td>Poppy seeds and flowers</td>
      <td>red common poppy flower selective focus phography</td>
      <td>[0.0018179789000000001, 0.009972040500000001, ...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>BeD3vjQ8SI0</td>
      <td>thanosql-dataset/unsplash_data/BeD3vjQ8SI0.jpg</td>
      <td>https://images.unsplash.com/photo-141700759404...</td>
      <td>Silhouette near dark trees</td>
      <td>trees during night time</td>
      <td>[-0.0223454703, 0.0129929734, -0.0019434979, -...</td>
    </tr>
  </tbody>
</table>
</div>



## __3. Search__

Perform a text-based image search using the "__SEARCH IMAGE__" query statement and the `tutorial_search_clip` model created. Execute the following query with the text value of "a black cat" and embedded `unsplash_data` images to calculate the similarity. The result values are saved into the newly added <mark style="background-color:#D7D0FF ">tutorial_search_clip_clipen_similarity1</mark> column.


```python
%%thanosql
SEARCH IMAGE text="a black cat"
USING tutorial_search_clip
AS 
SELECT * 
FROM unsplash_data
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
      <th>photo_id</th>
      <th>image_path</th>
      <th>photo_image_url</th>
      <th>photo_description</th>
      <th>ai_description</th>
      <th>tutorial_search_clip_clipen</th>
      <th>tutorial_search_clip_clipen_similarity1</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>XMyPniM9LF0</td>
      <td>thanosql-dataset/unsplash_data/XMyPniM9LF0.jpg</td>
      <td>https://images.unsplash.com/uploads/1411949294...</td>
      <td>Woman exploring a forest</td>
      <td>woman walking in the middle of forest</td>
      <td>[-0.0148640582, 0.0549194068, 0.0118315415, 0....</td>
      <td>0.185725</td>
    </tr>
    <tr>
      <th>1</th>
      <td>rDLBArZUl1c</td>
      <td>thanosql-dataset/unsplash_data/rDLBArZUl1c.jpg</td>
      <td>https://images.unsplash.com/photo-141633941111...</td>
      <td>Succulents in a terrarium</td>
      <td>succulent plants in clear glass terrarium</td>
      <td>[-0.0345519036, 0.0314463899, -0.0065574604, 0...</td>
      <td>0.148399</td>
    </tr>
    <tr>
      <th>2</th>
      <td>cNDGZ2sQ3Bo</td>
      <td>thanosql-dataset/unsplash_data/cNDGZ2sQ3Bo.jpg</td>
      <td>https://images.unsplash.com/photo-142014251503...</td>
      <td>Rural winter mountainside</td>
      <td>rocky mountain under gray sky at daytime</td>
      <td>[-0.031663157000000004, 0.0538793579, 0.013499...</td>
      <td>0.187703</td>
    </tr>
    <tr>
      <th>3</th>
      <td>iuZ_D1eoq9k</td>
      <td>thanosql-dataset/unsplash_data/iuZ_D1eoq9k.jpg</td>
      <td>https://images.unsplash.com/photo-141487280988...</td>
      <td>Poppy seeds and flowers</td>
      <td>red common poppy flower selective focus phography</td>
      <td>[0.0018179789000000001, 0.009972040500000001, ...</td>
      <td>0.177512</td>
    </tr>
    <tr>
      <th>4</th>
      <td>BeD3vjQ8SI0</td>
      <td>thanosql-dataset/unsplash_data/BeD3vjQ8SI0.jpg</td>
      <td>https://images.unsplash.com/photo-141700759404...</td>
      <td>Silhouette near dark trees</td>
      <td>trees during night time</td>
      <td>[-0.0223454703, 0.0129929734, -0.0019434979, -...</td>
      <td>0.218824</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>24963</th>
      <td>c7OrOMxrurA</td>
      <td>thanosql-dataset/unsplash_data/c7OrOMxrurA.jpg</td>
      <td>https://images.unsplash.com/photo-159300793778...</td>
      <td>None</td>
      <td>black metal fence during daytime</td>
      <td>[-0.0114669325, -0.0021708854, -0.0104937684, ...</td>
      <td>0.226402</td>
    </tr>
    <tr>
      <th>24964</th>
      <td>15IuQ5a0Qwg</td>
      <td>thanosql-dataset/unsplash_data/15IuQ5a0Qwg.jpg</td>
      <td>https://images.unsplash.com/photo-159296761254...</td>
      <td>Pearl earrings and seashells</td>
      <td>white and brown seashell on white surface</td>
      <td>[-0.0289349873, 0.051604818600000005, 0.015792...</td>
      <td>0.147114</td>
    </tr>
    <tr>
      <th>24965</th>
      <td>w8nrcXz8pwk</td>
      <td>thanosql-dataset/unsplash_data/w8nrcXz8pwk.jpg</td>
      <td>https://images.unsplash.com/photo-159299937329...</td>
      <td>None</td>
      <td>leopard on brown tree trunk during daytime</td>
      <td>[0.0069489880000000006, -0.0320789367, -0.0139...</td>
      <td>0.227299</td>
    </tr>
    <tr>
      <th>24966</th>
      <td>n1jHrRhehUI</td>
      <td>thanosql-dataset/unsplash_data/n1jHrRhehUI.jpg</td>
      <td>https://images.unsplash.com/photo-159192792878...</td>
      <td>Floral truck in the streets of Rome</td>
      <td>woman in beige coat and white hat standing on ...</td>
      <td>[0.005270903, -0.0013724641000000001, 0.025223...</td>
      <td>0.169803</td>
    </tr>
    <tr>
      <th>24967</th>
      <td>Ic74ACoaAX0</td>
      <td>thanosql-dataset/unsplash_data/Ic74ACoaAX0.jpg</td>
      <td>https://images.unsplash.com/photo-159240763188...</td>
      <td>None</td>
      <td>green plants on brown rocky mountain under blu...</td>
      <td>[-0.0143270381, 0.0594774038, 0.0045367004, -0...</td>
      <td>0.152199</td>
    </tr>
  </tbody>
</table>
<p>24968 rows × 7 columns</p>
</div>



<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>"<strong>SEARCH IMAGE</strong>" searches for images. Input the text description of the image using the "text" variable. </li>
        <li>"<strong>USING</strong>" specifies <code>tutorial_search_clip</code> as the model.</li>
    </ul>
</div>

Execute the query below to view the similarity of the 5 most similar images to 'a black cat'.


```python
%%thanosql
SELECT image_path, tutorial_search_clip_clipen_similarity1 
FROM (
    SEARCH IMAGE text="a black cat"
    USING tutorial_search_clip
    AS 
    SELECT * 
    FROM unsplash_data
    )
ORDER BY tutorial_search_clip_clipen_similarity1 DESC 
LIMIT 5
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
      <th>tutorial_search_clip_clipen_similarity1</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>thanosql-dataset/unsplash_data/UMyfDjQ6Ep8.jpg</td>
      <td>0.316560</td>
    </tr>
    <tr>
      <th>1</th>
      <td>thanosql-dataset/unsplash_data/7XJ3d0xK444.jpg</td>
      <td>0.311931</td>
    </tr>
    <tr>
      <th>2</th>
      <td>thanosql-dataset/unsplash_data/m8HsSWh-y6E.jpg</td>
      <td>0.310819</td>
    </tr>
    <tr>
      <th>3</th>
      <td>thanosql-dataset/unsplash_data/6ST6S6i9IGM.jpg</td>
      <td>0.310214</td>
    </tr>
    <tr>
      <th>4</th>
      <td>thanosql-dataset/unsplash_data/aFyD5aWKu6k.jpg</td>
      <td>0.309158</td>
    </tr>
  </tbody>
</table>
</div>



<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>"<strong>SEARCH IMAGE</strong>" calculates and returns the similarity between the input text and the image.</li>
        <li>The first "<strong>SELECT</strong>" returns the <mark style="background-color:#D7D0FF ">image_path</mark> column and the <mark style="background-color:#D7D0FF ">tutorial_search_clip_clipen_similarity1</mark> column from the result nested query.</li>
        <li>"<strong>ORDER BY</strong>" sorts the results using the <mark style="background-color:#D7D0FF ">tutorial_search_clip_clipen_similarity1</mark> column in descending order ("<strong>DESC</strong>"). The top 5 results ("<strong>LIMIT</strong>" 5) are returned.</li>
    </ul>
</div>

To immediately print out the resulting images, nest the previous query with a "__PRINT__" clause. 


```python
%%thanosql
PRINT IMAGE 
AS (
    SELECT image_path, tutorial_search_clip_clipen_similarity1 
    FROM (
        SEARCH IMAGE text="a black cat"
        USING tutorial_search_clip
        AS 
        SELECT * 
        FROM unsplash_data
        )
    ORDER BY tutorial_search_clip_clipen_similarity1 DESC 
    LIMIT 5
    )
```

    Searching...
    /home/jovyan/thanosql-dataset/unsplash_data/UMyfDjQ6Ep8.jpg



    
![jpeg](/en/img/tutorials/thanosql_search/search_image_by_text/output_29_1.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/7XJ3d0xK444.jpg



    
![jpeg](/en/img/tutorials/thanosql_search/search_image_by_text/output_29_3.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/m8HsSWh-y6E.jpg



    
![jpeg](/en/img/tutorials/thanosql_search/search_image_by_text/output_29_5.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/6ST6S6i9IGM.jpg



    
![jpeg](/en/img/tutorials/thanosql_search/search_image_by_text/output_29_7.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/aFyD5aWKu6k.jpg



    
![jpeg](/en/img/tutorials/thanosql_search/search_image_by_text/output_29_9.jpg)
    


<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <p>This query, combined with the query above, is made of three levels.</p>
    <ul>
        <li>"<strong>SELECT</strong>", in the first parentheses, produces the result of the step immediately above.</li>
        <li>"<strong>PRINT IMAGE</strong>" prints the results of the query as images.</li>
    </ul>
</div>


```python
%%thanosql
PRINT IMAGE 
AS (
    SELECT image_path, tutorial_search_clip_clipen_similarity1 
    FROM (
        SEARCH IMAGE text="a dog on a chair"
        USING tutorial_search_clip
        AS 
        SELECT * 
        FROM unsplash_data
        )
    ORDER BY tutorial_search_clip_clipen_similarity1 DESC 
    LIMIT 5
    )
```

    Searching...
    /home/jovyan/thanosql-dataset/unsplash_data/jZUr3AuI8io.jpg



    
![jpeg](/en/img/tutorials/thanosql_search/search_image_by_text/output_31_1.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/nnKBZTWzlMQ.jpg



    
![jpeg](/en/img/tutorials/thanosql_search/search_image_by_text/output_31_3.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/HG2KpOO0vSc.jpg



    
![jpeg](/en/img/tutorials/thanosql_search/search_image_by_text/output_31_5.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/f6qFneRNwNI.jpg



    
![jpeg](/en/img/tutorials/thanosql_search/search_image_by_text/output_31_7.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/GKY4WDO3QgY.jpg



    
![jpeg](/en/img/tutorials/thanosql_search/search_image_by_text/output_31_9.jpg)
    



```python
%%thanosql
PRINT IMAGE 
AS (
    SELECT image_path, tutorial_search_clip_clipen_similarity1 
    FROM (
        SEARCH IMAGE text="gloomy photos"
        USING tutorial_search_clip
        AS 
        SELECT * 
        FROM unsplash_data
        )
    ORDER BY tutorial_search_clip_clipen_similarity1 DESC 
    LIMIT 5
    )
```

    Searching...
    /home/jovyan/thanosql-dataset/unsplash_data/Xo4vJrtrmmA.jpg



    
![jpeg](/en/img/tutorials/thanosql_search/search_image_by_text/output_32_1.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/QheWOfwEUio.jpg



    
![jpeg](/en/img/tutorials/thanosql_search/search_image_by_text/output_32_3.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/_zHYUQmWrzk.jpg



    
![jpeg](/en/img/tutorials/thanosql_search/search_image_by_text/output_32_5.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/Tu_lH5CFFZw.jpg



    
![jpeg](/en/img/tutorials/thanosql_search/search_image_by_text/output_32_7.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/DfYPBHaOR04.jpg



    
![jpeg](/en/img/tutorials/thanosql_search/search_image_by_text/output_32_9.jpg)
    



```python
%%thanosql
PRINT IMAGE 
AS (
    SELECT image_path, tutorial_search_clip_clipen_similarity1 
    FROM (
        SEARCH IMAGE text="the feeling when your program finally works"
        USING tutorial_search_clip
        AS 
        SELECT * 
        FROM unsplash_data
        )
    ORDER BY tutorial_search_clip_clipen_similarity1 DESC 
    LIMIT 5
    )
```

    Searching...
    /home/jovyan/thanosql-dataset/unsplash_data/nDLYtRqJtMw.jpg



    
![jpeg](/en/img/tutorials/thanosql_search/search_image_by_text/output_33_1.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/qNJpGSCv_Jc.jpg



    
![jpeg](/en/img/tutorials/thanosql_search/search_image_by_text/output_33_3.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/Yb5OBk-OxJY.jpg



    
![jpeg](/en/img/tutorials/thanosql_search/search_image_by_text/output_33_5.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/6etH6346MHE.jpg



    
![jpeg](/en/img/tutorials/thanosql_search/search_image_by_text/output_33_7.jpg)
    


    /home/jovyan/thanosql-dataset/unsplash_data/7GX5aICb5i4.jpg



    
![jpeg](/en/img/tutorials/thanosql_search/search_image_by_text/output_33_9.jpg)
    


## __4. In Conclusion__

In this tutorial, we searched for images in the `unsplash dataset` by text using a multi-modal text/image vectorization model. As this is a beginner-level tutorial, we focused on the process and showing visible results rather than accuracy. The image search can retrieve more accurate results by utilizing various queries.

<div class="admonition tip">
    <h4 class="admonition-title">Inquiries about deploying a model for your own service</h4>
    <p>If you have any difficulties creating your own model using ThanoSQL or applying it to your services, please feel free to contact us below😊</p>
    <p>For inquiries regarding building text-image search models: contact@smartmind.team</p>
</div>
