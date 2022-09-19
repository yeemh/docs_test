---
title: Create an Image Classification Model
---

# __Create an Image Classification Model__

- Tutorial Difficulty: ★☆☆☆☆
- 10 min read
- Languages : [SQL](https://en.wikipedia.org/wiki/SQL) (100%)
- File location : tutorial_en/thanosql_ml/classification/image_classification.ipynb
- References : [(AI-Hub) Product image data](https://aihub.or.kr/aihubdata/data/view.do?currMenu=115&topMenu=100&aihubDataSe=realm&dataSetSn=64), [A ConvNet for the 2020s](https://arxiv.org/abs/2201.03545)

## Tutorial Introduction

<div class="admonition note">
    <h4 class="admonition-title">Understanding Classification</h4>
    <p>Classification is a type of <a href="https://en.wikipedia.org/wiki/Machine_learning">Machine Learning</a> that predicts which category (Category or Class) the target belongs to. For example, both binary classifications (used for classifying men or women) and multiple classifications (used to predict animal species such as dogs, cats, rabbits, etc.) are included in the classification tasks. <br></p>
</div>

The image classification contest ([ImageNet](https://en.wikipedia.org/wiki/ImageNet)) has been held since 2010. The winning model at the beginning of the competition showed 72% accuracy. In 2015, the [ResNet](https://arxiv.org/abs/1512.03385) model that won showed 96% accuracy and started to surpass human classification capabilities.

<div class="admonition tip">
   <p>The human ability to classify the same data is estimated at about 95%.</p>
</div>

Even though [Data labeling](https://en.wikipedia.org/wiki/Labeled_data) is important for accurate image classification, methods of correcting the dataset using the weights of the pre-trained model are widely used. This method allows deep learning models training even with a relatively small number of data.

ThanoSQL provides a variety of pre-trained models and allows model creation using simple queries. With this, users can derive insights from images with difficult to quantify features with properly trained models and utilize them for various services.

__The following are examples and applications of the ThanoSQL classification model.__

- The ThanoSQL image classification model reduces the process of finding suitable categories for product registration in online sales services. You can categorize product images with a simple query. Users can save time compared to traditional image classification by focusing just on correcting some misclassified data.

- Using the image classification model, you can roughly classify art works that would be otherwise difficult to classify due to their vague criteria, such as the feeling, technique, and suitable location of each work.

- You can detect and classify defective products with scratches and damage from manufacturing plants. 

<div class="admonition tip">
   <p>You can also create a classification model based on the behavior of art enthusiasts to classify who is most likely to enjoy a particular piece of art. In other words, using only artwork images, you can create a model that predicts art preferences based on age, gender, place, and etc.
</div>

<div class="admonition note">
   <h4 class="admonition-title">In this tutorial</h4>
   <p>👉 Build an image classification model to classify more than 10,000 products using the <code>Product Image</code> dataset from <a href="https://aihub.or.kr/">AI-Hub</a>, a data sharing platform. The model can be used for detection and identification in smart warehouses and unmanned stores.
   Dataset consists total of 1,440,000 images. In this tutorial, you will use 1,800 training data and 200 test data to learn how to use ThanoSQL. <br></p>
</div>

<a href="https://docs.thanosql.ai/img/thanosql_ml/classification/image_classification/image_classification_data_intro.png">
   <img alt="Product Image Example" src="https://docs.thanosql.ai/img/thanosql_ml/classification/image_classification/image_classification_data_intro.png">
</a>

<div class="admonition warning">
   <h4 class="admonition-title">Tutorial Precautions</h4>
   <ul>
      <li>The image classification model can be used to predict one target value (Target, Category) from one image.</li>
      <li>Both a column representing the image path and a column representing the target value of the image must exist.</li>
      <li>The base model of the corresponding image classification model (<code>CONVNEXT</code>) uses GPU. Depending on the size and the batch size of the model used, GPU memory may be insufficient. In this case, try using a smaller model or reducing the batch size of the model.</li>
   </ul>
</div>

## __0. Prepare Dataset and Model__


As mentioned in the [ThanoSQL Workspace](https://docs.thanosql.ai/en/getting_started/how_to_use_ThanoSQL/#5-thanosql-workspace), you must create an API token and run the query below to execute the query of ThanoSQL. 


```python
%load_ext thanosql
%thanosql API_TOKEN=<Issued_API_TOKEN>
```

### __Prepare Dataset__


```python
%%thanosql
GET THANOSQL DATASET product_image_data
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
COPY product_image_train
OPTIONS (overwrite=True)
FROM "thanosql-dataset/product_image_data/product_image_train.csv"
```

    Success



```python
%%thanosql
COPY product_image_test
OPTIONS (overwrite=True)
FROM "thanosql-dataset/product_image_data/product_image_test.csv"
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
GET THANOSQL MODEL tutorial_product_classifier
OPTIONS (overwrite=True)
AS tutorial_product_classifier
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

To create the image classification  model, we use the <mark style="background-color:#FFEC92 ">product_image_train</mark> table from the ThanoSQL database. To check the table's contents, run the following query.


```python
%%thanosql
SELECT *
FROM product_image_train
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
      <th>div_l</th>
      <th>div_m</th>
      <th>div_s</th>
      <th>div_n</th>
      <th>comp_nm</th>
      <th>img_prod_nm</th>
      <th>multi</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>thanosql-dataset/product_image_data/product_im...</td>
      <td>유제품</td>
      <td>요구르트</td>
      <td>떠먹는 요구르트</td>
      <td>떠먹는 요구르트</td>
      <td>기타</td>
      <td>토핑오트&amp;애플시나몬</td>
      <td>False</td>
    </tr>
    <tr>
      <th>1</th>
      <td>thanosql-dataset/product_image_data/product_im...</td>
      <td>홈클린</td>
      <td>위생용품</td>
      <td>일반비누</td>
      <td>일반비누</td>
      <td>크리오</td>
      <td>크리오)골드디비누</td>
      <td>True</td>
    </tr>
    <tr>
      <th>2</th>
      <td>thanosql-dataset/product_image_data/product_im...</td>
      <td>면류</td>
      <td>용기면</td>
      <td>국물용기라면</td>
      <td>짬뽕라면</td>
      <td>농심</td>
      <td>농심오징어짬뽕컵67G</td>
      <td>True</td>
    </tr>
    <tr>
      <th>3</th>
      <td>thanosql-dataset/product_image_data/product_im...</td>
      <td>디저트</td>
      <td>디저트/베이커리</td>
      <td>냉장디저트</td>
      <td>냉장디저트</td>
      <td>Dole 코리아</td>
      <td>Dole후룻볼슬라이스복숭아198g</td>
      <td>False</td>
    </tr>
    <tr>
      <th>4</th>
      <td>thanosql-dataset/product_image_data/product_im...</td>
      <td>주류</td>
      <td>기타주류</td>
      <td>칵테일</td>
      <td>칵테일</td>
      <td>롯데주류</td>
      <td>순하리소다톡바나나355ML</td>
      <td>True</td>
    </tr>
  </tbody>
</table>
</div>



<div class="admonition note">
   <h4 class="admonition-title">Understanding the Data</h4>
   <ul>
      <li><mark style="background-color:#D7D0FF ">image_path</mark>: image file's path</li>
      <li><mark style="background-color:#D7D0FF ">div_l</mark> : large classification of products</li>
      <li><mark style="background-color:#D7D0FF ">div_m</mark> : middle classification of products</li>
      <li><mark style="background-color:#D7D0FF ">div_s</mark> : subclassification of products</li>
      <li><mark style="background-color:#D7D0FF ">div_n</mark> : detailed classification of products</li>
      <li><mark style="background-color:#D7D0FF ">comp_nm</mark> : manufacturer</li>
      <li><mark style="background-color:#D7D0FF ">img_prod_nm</mark> : product name (image)</li>
      <li><mark style="background-color:#D7D0FF ">multi</mark> : whether image has multiple products</li>
   </ul>
</div>


```python
%%thanosql
PRINT IMAGE
AS
SELECT image_path
FROM product_image_train
LIMIT 5
```

    /home/jovyan/thanosql-dataset/product_image_data/product_image/10246_00_s_21.png



    
![png](/en/img/tutorials/thanosql_ml/image_classification/output_17_1.png)
    


    /home/jovyan/thanosql-dataset/product_image_data/product_image/10180_60_m_9.png



    
![png](/en/img/tutorials/thanosql_ml/image_classification/output_17_3.png)
    


    /home/jovyan/thanosql-dataset/product_image_data/product_image/10101_30_m_17.png



    
![png](/en/img/tutorials/thanosql_ml/image_classification/output_17_5.png)
    


    /home/jovyan/thanosql-dataset/product_image_data/product_image/10242_60_s_12.png



    
![png](/en/img/tutorials/thanosql_ml/image_classification/output_17_7.png)
    


    /home/jovyan/thanosql-dataset/product_image_data/product_image/10054_30_m_13.png



    
![png](/en/img/tutorials/thanosql_ml/image_classification/output_17_9.png)
    


## __2. Predict Using Pretrained Models__

To predict the results using the <mark style="background-color:#E9D7FD ">tutorial_product_classifier</mark> model, a pretrained image classification model, run the query below.


```python
%%thanosql
PREDICT USING tutorial_product_classifier
AS
SELECT *
FROM product_image_test
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
      <td>thanosql-dataset/product_image_data/product_im...</td>
      <td>생활용품</td>
    </tr>
    <tr>
      <th>1</th>
      <td>thanosql-dataset/product_image_data/product_im...</td>
      <td>소스</td>
    </tr>
    <tr>
      <th>2</th>
      <td>thanosql-dataset/product_image_data/product_im...</td>
      <td>디저트</td>
    </tr>
    <tr>
      <th>3</th>
      <td>thanosql-dataset/product_image_data/product_im...</td>
      <td>음료</td>
    </tr>
    <tr>
      <th>4</th>
      <td>thanosql-dataset/product_image_data/product_im...</td>
      <td>주류</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>197</th>
      <td>thanosql-dataset/product_image_data/product_im...</td>
      <td>의약외품</td>
    </tr>
    <tr>
      <th>198</th>
      <td>thanosql-dataset/product_image_data/product_im...</td>
      <td>소스</td>
    </tr>
    <tr>
      <th>199</th>
      <td>thanosql-dataset/product_image_data/product_im...</td>
      <td>주류</td>
    </tr>
    <tr>
      <th>200</th>
      <td>thanosql-dataset/product_image_data/product_im...</td>
      <td>유제품</td>
    </tr>
    <tr>
      <th>201</th>
      <td>thanosql-dataset/product_image_data/product_im...</td>
      <td>주류</td>
    </tr>
  </tbody>
</table>
<p>202 rows × 2 columns</p>
</div>



## __3. Create an Image Classification Model__

To create an image classification model with the name <mark style="background-color:#E9D7FD ">my_product_classifier</mark> using the <mark style="background-color:#FFEC92 ">product_image_train</mark> dataset, run the following query. 
(Estimated duration of query execution: 5 min)


```python
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

    Building model...
    Success


<div class="admonition note">
    <h4 class="admonition-title">Query Details</h4>
    <ul>
        <li>"<strong>BUILD MODEL</strong>" creates and trains a model named <mark style="background-color:#E9D7FD">my_product_classifier</mark>.</li>
        <li>"<strong>USING</strong>" specifies <code>ConvNeXt_Tiny</code> as the base model</li>
        <li>"<strong>OPTIONS</strong>" specifies the option values used to create the model.
        <ul>
            <li>"image_col" : name of column containing the image path</li>
            <li>"label_col" : name of the column containing information about the target value</li>
            <li>"epochs" : number of times to train with the training dataset.</li>
            <li>"overwrite" : determines whether to overwrite a model if it already exists. If set as True, the old model is replaced with the new model (True|False, DEFAULT : False) </li>
        </ul>
        </li>
    </ul>
</div>

<div class="admonition tip">
    <p>In this example, we set "epochs" to 1 to train the model quickly. In general, larger number of "epochs" increases performance of the inference at the cost of the computation time.</p>
</div>

## __4. Predict__

To use the image classification model created in the previous step for prediction of <mark style="background-color:#FFEC92">product_image_test</mark>, run the following query.


```python
%%thanosql
PREDICT USING my_product_classifier
OPTIONS (
    image_col='image_path'
    )
AS
SELECT *
FROM product_image_test
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
      <td>thanosql-dataset/product_image_data/product_im...</td>
      <td>생활용품</td>
    </tr>
    <tr>
      <th>1</th>
      <td>thanosql-dataset/product_image_data/product_im...</td>
      <td>소스</td>
    </tr>
    <tr>
      <th>2</th>
      <td>thanosql-dataset/product_image_data/product_im...</td>
      <td>디저트</td>
    </tr>
    <tr>
      <th>3</th>
      <td>thanosql-dataset/product_image_data/product_im...</td>
      <td>음료</td>
    </tr>
    <tr>
      <th>4</th>
      <td>thanosql-dataset/product_image_data/product_im...</td>
      <td>주류</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>197</th>
      <td>thanosql-dataset/product_image_data/product_im...</td>
      <td>의약외품</td>
    </tr>
    <tr>
      <th>198</th>
      <td>thanosql-dataset/product_image_data/product_im...</td>
      <td>소스</td>
    </tr>
    <tr>
      <th>199</th>
      <td>thanosql-dataset/product_image_data/product_im...</td>
      <td>주류</td>
    </tr>
    <tr>
      <th>200</th>
      <td>thanosql-dataset/product_image_data/product_im...</td>
      <td>유제품</td>
    </tr>
    <tr>
      <th>201</th>
      <td>thanosql-dataset/product_image_data/product_im...</td>
      <td>주류</td>
    </tr>
  </tbody>
</table>
<p>202 rows × 2 columns</p>
</div>



<div class="admonition note">
    <h4 class="admonition-title">Query details</h4>
    <ul>
        <li>"<strong>PREDICT USING</strong>" predicts the outcome using the <mark style="background-color:#E9D7FD">my_product_classifier</mark>
        <li>"<strong>OPTIONS</strong>" specifies the option values to be used for prediction.
        <ul>
            <li>"image_col" : the name of the column where the path of the image used for prediction is stored.</li>
        </ul>
        </li>
    </ul>
</div>

## __5. In Conclusion__

In this tutorial, we created an image classification model using the <mark style="background-color:#FFD79C">product image</mark> dataset. As this is a beginner-level tutorial, we focused on the process rather than accuracy. The image classification model can be improved in accuracy through fine tuning that is suitable for the user's needs. It is also possible to train the base model using your own data, or to vectorize and transform your data using a self-supervised model, and then distributing it using automated machine learning (Auto-ML) technique. Create your own model and provide competitive services by combining various unstructured data (image, audio, video, etc.) and structured data with ThanoSQL.

For the next step, [Creating an Intermediate Image Classification Model] tutorial, takes a deeper dive into the image classification models. If you want to learn more about building your own image classification model for your service, please proceed with the following tutorials.

- [How to Upload to ThanoSQL DB](https://docs.thanosql.ai/en/getting_started/data_upload/)
- [Creating an Intermediate Image Classification Model]
- [Image conversion and creating My model using Auto-ML]
- [Deploy My Image Classification model](https://docs.thanosql.ai/en/how-to_guides/reference/)

<div class="admonition tip">
    <h4 class="admonition-title">Inquiries about deploying a model for your own service</h4>
    <p>If you have any difficulties creating your own model using ThanoSQL or applying it to your service, please feel free to contact us below😊</p>
    <p>For inquiries regarding building an image classification model: contact@smartmind.team</p>
</div>
