---
title: Search Image by Keyword
---

# __Search Image by Keyword__

- Tutorial Difficulty : ★☆☆☆☆
- 10 min read
- Languages : [SQL](https://en.wikipedia.org/wiki/SQL) (100%)
- File location : tutorial_en/thanosql_search/search_image_by_keyword.ipynb
- References : [Food Image and Nutrition Text Introduction Dataset](https://aihub.or.kr/aihubdata/data/view.do?currMenu=115&topMenu=100&aihubDataSe=realm&dataSetSn=74)

## Tutorial Introduction

<div class="admonition note">
    <h4 class="admonition-title">Understanding Keyword-Image Search</h4>
    <p>ThanoSQL provides image search using keywords. The search uses an image classification model to set a keyword as the target value, then adds an index column with the images updated from the trained model. In other words, the keyword-image search finds images that correspond to the desired target value (category). </p>
</div>

The dictionary defines "search" as "finding the necessary materials in a book or computer according to its purpose." The ThanoSQL keyword-image search does not search for information that includes a specific word (keyword). Instead, it creates a model that predicts words from the features of an image and returns the image with the highest relevance.

**The following are examples and usages of the ThanoSQL keyword image search algorithm.**

- Use shopping categories to create a model, and use it to create an index column. The index column combined with attributes such as image registration dates, provides more accurate searches.
- You can create your own image search service by utilizing image-image search and text-image search from their respective tutorials.

<div class="admonition note">
    <h4 class="admonition-title"> In this tutorial</h4>
    <p>👉 Use a combination of the "<strong>SEARCH</strong>" query and the "<strong>SELECT</strong>" query to search images using specific keywords. </p>
</div>

<div class="admonition tip">
    <h4 class="admonition-title">Dataset Description</h4>
    <p>The <code>Introduction to Food Images and Nutrition Information Text</code> dataset was organized by the Ministry of Science and ICT and is supported by the Korea Intelligence Information Society Agency. It consists of 400 food items and 842,000 images. This tutorial uses only a few (10 types, 1,190 photos) images from that dataset. </p>
</div>

## __0. Prepare Dataset__

As mentioned in the [ThanoSQL Workspace](https://docs.thanosql.ai/en/getting_started/how_to_use_ThanoSQL/#5-thanosql-workspace), you must create an API token and run the query below to execute the query of ThanoSQL. 


```python
%load_ext thanosql
%thanosql API_TOKEN=<Issued_API_TOKEN>
```

### __Prepare Dataset__


```python
%%thanosql
GET THANOSQL DATASET diet_data
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
COPY diet 
OPTIONS (overwrite=True)
FROM "thanosql-dataset/diet_data/diet.csv"
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

## __1. Check Dataset__

To create a keyword-image search model, we use the <mark style="background-color:#FFEC92">diet</mark> table. Run the query below to check the contents of the table.


```python
%%thanosql
SELECT * 
FROM diet
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
      <th>label</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>thanosql-dataset/diet_data/diet/백향과/0_A220148X...</td>
      <td>백향과</td>
    </tr>
    <tr>
      <th>1</th>
      <td>thanosql-dataset/diet_data/diet/백향과/0_A220148X...</td>
      <td>백향과</td>
    </tr>
    <tr>
      <th>2</th>
      <td>thanosql-dataset/diet_data/diet/백향과/1_A220148X...</td>
      <td>백향과</td>
    </tr>
    <tr>
      <th>3</th>
      <td>thanosql-dataset/diet_data/diet/백향과/0_A220148X...</td>
      <td>백향과</td>
    </tr>
    <tr>
      <th>4</th>
      <td>thanosql-dataset/diet_data/diet/백향과/0_A220148X...</td>
      <td>백향과</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>1185</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/0_A020511...</td>
      <td>사과파이</td>
    </tr>
    <tr>
      <th>1186</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/0_A020511...</td>
      <td>사과파이</td>
    </tr>
    <tr>
      <th>1187</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/1_A020511...</td>
      <td>사과파이</td>
    </tr>
    <tr>
      <th>1188</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/0_A020511...</td>
      <td>사과파이</td>
    </tr>
    <tr>
      <th>1189</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/0_A020511...</td>
      <td>사과파이</td>
    </tr>
  </tbody>
</table>
<p>1190 rows × 2 columns</p>
</div>



<div class="admonition note">
    <h4 class="admonition-title">Understanding the Data</h4>
    <p>The <mark style="background-color:#FFEC92">diet</mark> table contains the following information. </p>
    <ul>
        <li><mark style="background-color:#D7D0FF">image_path</mark> : image path </li>
        <li><mark style="background-color:#D7D0FF">label</mark> : filename</li>
    </ul>
</div>

## __2. Create an Image Search Model__

To search for images, it is necessary to train the existing data. To do this, we create an image classification model using the dataset mentioned prior. Run the query below to create a model named <mark style="background-color:#E9D7FD ">diet_image_classification</mark>.  
(Estimated duration of query execution: 3 min)


```python
%%thanosql
BUILD MODEL diet_image_classification
USING ConvNeXt_Tiny
OPTIONS (
    image_col='image_path', 
    label_col='label', 
    epochs=1,
    overwrite=True
    )
AS 
SELECT *
FROM diet
```

    Building model...
    Success


<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>"<strong>BUILD MODEL</strong>" creates and trains a model named <mark style="background-color:#E9D7FD ">diet_image_classification</mark>.</li>
        <li>"<strong>USING</strong>" specifies <code>ConvNeXt_Tiny</code> as the base model.</li>
        <li>"<strong>OPTIONS</strong>" specifies the option values used to create a model.
        <ul>
            <li>"image_col" : the name of the column containing the image path.</li>
            <li>"label_col" : the name of the column containing information about the target.</li>
            <li>"epochs" : number of times to train with the training dataset.</li>
            <li>"overwrite": determines whether to overwrite a dataset if it already exists. If set as True, the old dataset is replaced with the new dataset (True|False, DEFAULT : False)</li>
        </ul>
        </li>
    </ul>
</div>

## __3. Predict Specific Images__

To predict a specific image, use the prediction model created in the previous step(<mark style="background-color:#E9D7FD ">diet_image_classification</mark>). After running the query below, the prediction result is stored and returned in the <mark style="background-color:#D7D0FF">predicted</mark> column.


```python
%%thanosql
PREDICT USING diet_image_classification
AS 
SELECT *
FROM diet
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
      <th>predicted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>thanosql-dataset/diet_data/diet/백향과/0_A220148X...</td>
      <td>백향과</td>
    </tr>
    <tr>
      <th>1</th>
      <td>thanosql-dataset/diet_data/diet/백향과/0_A220148X...</td>
      <td>백향과</td>
    </tr>
    <tr>
      <th>2</th>
      <td>thanosql-dataset/diet_data/diet/백향과/1_A220148X...</td>
      <td>백향과</td>
    </tr>
    <tr>
      <th>3</th>
      <td>thanosql-dataset/diet_data/diet/백향과/0_A220148X...</td>
      <td>백향과</td>
    </tr>
    <tr>
      <th>4</th>
      <td>thanosql-dataset/diet_data/diet/백향과/0_A220148X...</td>
      <td>백향과</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>1185</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/0_A020511...</td>
      <td>사과파이</td>
    </tr>
    <tr>
      <th>1186</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/0_A020511...</td>
      <td>사과파이</td>
    </tr>
    <tr>
      <th>1187</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/1_A020511...</td>
      <td>사과파이</td>
    </tr>
    <tr>
      <th>1188</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/0_A020511...</td>
      <td>사과파이</td>
    </tr>
    <tr>
      <th>1189</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/0_A020511...</td>
      <td>사과파이</td>
    </tr>
  </tbody>
</table>
<p>1190 rows × 2 columns</p>
</div>



<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>Use the <mark style="background-color:#E9D7FD ">diet_image_classification</mark> model for prediction with the "<strong>PREDICT USING</strong>" query.</li>
    </ul>
</div>

## __4. Search__

To retrieve data with specific conditions, run a query using the "__PREDICT USING__", "__SELECT__", "__WHERE__" clauses. You can search data where the <mark style="background-color:#E9D7FD ">label</mark> is '사과파이'('apple pie') and where the prediction result is also '사과파이' by running the following query.


```python
%%thanosql
SELECT A.image_path, A.label, B.predicted 
FROM diet A
LEFT JOIN (
    SELECT * 
    FROM (PREDICT USING diet_image_classification 
    AS SELECT * FROM diet)) B 
ON A.image_path = B.image_path
WHERE A.label = B.predicted
AND A.label LIKE '사과파이'
LIMIT 10
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
      <th>label</th>
      <th>predicted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/0_A020511...</td>
      <td>사과파이</td>
      <td>사과파이</td>
    </tr>
    <tr>
      <th>1</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/0_A020511...</td>
      <td>사과파이</td>
      <td>사과파이</td>
    </tr>
    <tr>
      <th>2</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/0_A020511...</td>
      <td>사과파이</td>
      <td>사과파이</td>
    </tr>
    <tr>
      <th>3</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/0_A020511...</td>
      <td>사과파이</td>
      <td>사과파이</td>
    </tr>
    <tr>
      <th>4</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/0_A020511...</td>
      <td>사과파이</td>
      <td>사과파이</td>
    </tr>
    <tr>
      <th>5</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/0_A020511...</td>
      <td>사과파이</td>
      <td>사과파이</td>
    </tr>
    <tr>
      <th>6</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/0_A020511...</td>
      <td>사과파이</td>
      <td>사과파이</td>
    </tr>
    <tr>
      <th>7</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/0_A020511...</td>
      <td>사과파이</td>
      <td>사과파이</td>
    </tr>
    <tr>
      <th>8</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/0_A020511...</td>
      <td>사과파이</td>
      <td>사과파이</td>
    </tr>
    <tr>
      <th>9</th>
      <td>thanosql-dataset/diet_data/diet/사과파이/0_A020511...</td>
      <td>사과파이</td>
      <td>사과파이</td>
    </tr>
  </tbody>
</table>
</div>



<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>"<strong>SELECT * FROM (...)</strong>" selects all the results of the nested "<strong>PREDICT USING</strong>" query.</li>
        <li>"<strong>WHERE</strong>" sets the selection condition. "<strong>AND</strong>" allows multiple conditions.
        <ul>
            <li>"label = predicted" : Queries only data where the<mark style="background-color:#D7D0FF ">label</mark> column and <mark style="background-color:#D7D0FF ">predicted</mark> column are equal.</li>
            <li>"label = '사과파이'" : Queries data where the <mark style="background-color:#D7D0FF ">label</mark> value is 'apple pie'.</li>
        </ul>
        </li>
    </ul>
</div>

## **5. In Conclusion**

In this tutorial, we created an image search model to search for food images from the `food image dataset` using keywords. As this is a beginner-level tutorial, we focused on the process rather than accuracy. The model's accuracy can be improved by adjusting various options, such as increasing the epoch or dataset size. Furthermore, follow along with the image-image and image-text search tutorials to create your own search services.

<div class="admonition tip">
    <h4 class="admonition-title">Inquiries about deploying a model for your own service</h4>
    <p>If you have any difficulties creating your own model using ThanoSQL or applying it to your services, please feel free to contact us below😊</p>
    <p>For inquiries regarding building keyword-image search models: contact@smartmind.team</p>
</div>
