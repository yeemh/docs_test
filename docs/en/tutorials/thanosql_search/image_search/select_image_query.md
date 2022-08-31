---
title: Search image by keyword
---

# **Search image by keyword**

## Preface

- Tutorial Difficulty : ★☆☆☆☆
- 10 min read
- Languages : [SQL](https://ko.wikipedia.org/wiki/SQL) (100%)
- File location : tutorial/tutorial_en/query/search_image_by_keyword_en.ipynb
- References : [Food Image and Nutrition Text Introduction Dataset](https://aihub.or.kr/aihubdata/data/view.do?currMenu=115&topMenu=100&aihubDataSe=realm&dataSetSn=74)
- Updated Date : {{ git_revision_date_localized }}

## Tutorial Introduction

!!! note "Understanding Keyword-Image Search Service"
    ThanoSQL provides a search function that allows you to return images to results through keywords. Use an image classification model, etc. to set the keyword desired by the user to the target value, and add a column that indexes the updated image using the learned model. If the text-image search feature analyzes meaning from images and provides similar images, keyword-image search finds the image that corresponds to the desired target value (category).

Search has the dictionary meaning of "finding the necessary materials in a book or computer according to its purpose." ThanoSQL keyword-image search works a little differently than searching for information within a DB with the inclusion of a specific word (keyword). Keyword-based image search creates a model that pre-learns and predicts words from the features of an image, and provides the image with the highest probability of being included in a specific keyword through that model.

**Below is an example and utilization of the ThanoSQL keyword image search algorithm.**

- Use the categories of shopping categories as learning data to create a learning model, and use them to create an index column in an existing/new image. The index column combines numerical attributes such as image registration dates to provide more sophisticated searches.
- You can create a My image search service by utilizing various results such as similar image search results and text-image search results covered in the following tutorial.

!!! note "In this tutorial"
    :point_right: Use a combination of the "**SEARCH**" query syntax provided by ThanoSQL and the "**SELECT**" query syntax provided by traditional PostgreSQL to perform the desired image search using specific keywords.

!!! tip "Dataset Description"
    The `Introduction to Food Images and Nutrition Information Text` dataset is organized by the Ministry of Science and ICT and supported by the Korea Intelligence Information Society Agency, and consists of 400 Korean Davido eating out menus and Korean food menus. It consists of 842,000 images, and this tutorial uses only a few (10 types, 1,190 photos) in that dataset.

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
COPY diet
OPTIONS(overwrite=True)
FROM "tutorial_data/diet_data/diet.csv"
```

!!! note "**Query Details**"
    - Specifies the data set name to copy to the DB using the "**COPY**" query syntax. 
    - Specify the options to use for **COPY** via the "**OPTIONS**" query syntax.
        - "overwrite" : Set whether or not a data set with the same name can be overwritten when it exists on the DB. If True, the existing dataset is changed to the new dataset (True|False, DEFAULT: False)

## **1. Check Data Set**

Use the <mark style="background-color:#FFEC92">diet</mark> table stored in the ThanoSQL DB to create a keyword-image search model. Run the query syntax below and verify the contents of the table.

```sql
%%thanosql
SELECT *
FROM diet
```

[![IMAGE](/img/thanosql_search/base_search/select_img1.png)](/img/thanosql_search/base_search/select_img1.png)

!!! note "Understanding Data Tables"
    <mark style="background-color:#FFEC92">diet</mark> The table contains the following information.

    -  <mark style="background-color:#D7D0FF">image_path</mark> : Image Path
    -  <mark style="background-color:#D7D0FF">label</mark> : File Name

## **2. Create Keyword Search Model**

Image search requires learning existing data tables and creating criteria for future searches. To do this, create an image classification model using the data set that you saw in the previous step. Run the query syntax below to create a model named <mark style="background-color:#E9D7FD">date_image_classification</mark>.  
(Expected time required for query execution: 3 min)

```sql
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

!!! note "Query Details"
    - Use the "**BUILD MODEL**" query syntax to create and train the <mark style="background-color:#E9D7FD">diet_image_classification</mark> model.
    - The "**USING**" query syntax specifies the use of `ConvNeXt_Tiny` as the base model.
    - Specify the options to use for model creation via the "**OPTIONS**" query syntax.
        - "image_col" : Name of column containing image path
        - "label_col" : Name of column containing information of target value
        - "epochs" : Number of times to learn all learning data sets
        - "overwrite" : Set overwritable if model with same name exists. If true, the existing model is changed to a new model (True|False, DEFAULT: False)

## **3. Use the generated model to view keyword-image search models**

Use the image prediction model you created in the previous step (<mark style="background-color:#E9D7FD">date_image_classification</mark>) to predict the target value of a specific image. After performing the query below, the prediction results are returned in the <mark style="background-color:#D7D0FF">predicted</mark> column.

```sql
%%thanosql
PREDICT USING diet_image_classification
AS
SELECT *
FROM diet
```

[![IMAGE](/img/thanosql_search/base_search/select_img2.png)](/img/thanosql_search/base_search/select_img2.png)

!!! note "**Query Details**"
    - Use the <mark style="background-color:#E9D7FD">diet_image_classification</mark> model created in the previous step with the query syntax "**PREDICT USING**".

## **4. Search using generated models**

The query syntax for "**PREDICT USING**", "**SELECT**", and "**WHERE**" is now used to search only for data under certain conditions. <mark style="background-color:#E9D7FD">label</mark> can only search for data with 'apple pie' and prediction results also 'apple_pie' and create query syntax as follows:

```sql
%%thanosql
SELECT A.image_path, A.label, B.predicted
FROM diet A
LEFT JOIN (
    SELECT *
    FROM (PREDICT USING diet_image_classification
    AS SELECT * FROM diet)) B
ON A.image_path = B.image_path
WHERE A.label = B.predicted
AND A.label LIKE 'apple_pie'
LIMIT 10
```

[![IMAGE](/img/thanosql_search/base_search/select_img3.png)](/img/thanosql_search/base_search/select_img3.png)

!!! note "**Query Details**"
    - "**SELECT \* FROM (...)**" Select all results of the query syntax starting with "**PREDICT USING**".
    - Set conditions through the "**WHERE**" query syntax. This condition is followed by "**AND**".
        - "label=predicted": Extracts only data with the same values in the <mark style="background-color:#D7D0FF">label</mark> column and the <mark style="background-color:#D7D0FF">predicted</mark> column.
        - "label = 'apple_pie'" : <mark style="background-color:#D7D0FF">label</mark> Extracts only data with column 'apple_pie'

## **5. In Conclusion**

In this tutorial, we constructed and utilized a model to search for food images related to keywords using the `food image dataset`. In this tutorial, we focused on operation rather than accuracy. You can improve the accuracy of your model by adjusting various options, such as the number of lessons learned and the number of data in the build option. Followed by similar image search models, image search tutorials using text descriptions, and create your own various search services.

!!! tip "**Inquiries on model distribution for 'my own' service**"
    If you have difficulty creating your own model using ThanoSQL or applying it to service, please feel free to contact me below😊

    Inquiries about building a speech recognition model: contact@smartmind.team
