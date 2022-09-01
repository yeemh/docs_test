---
title: Search image by text
---

# **Search image by text**

## Preface

- Tutorial Difficulty : ★★☆☆☆
- 7 min read
- Languages : [SQL](https://ko.wikipedia.org/wiki/SQL) (100%)
- File location : tutorial/tutorial_en/query/search_image_by_text_en.ipynb
- References : [Unsplash Dataset - Lite](https://unsplash.com/data), [Learning Transferable Visual Models From Natural Language Supervision](https://arxiv.org/abs/2103.00020)
- Updated Date : {{ git_revision_date_localized }}

## Tutorial Introduction

!!! note "Understanding Text Quantification Techniques"
    For computers to understand natural language, they need to be quantified. Recently, research on pre-learning models such as [BERT](<https://en.wikipedia.org/wiki/BERT_(language_model)>) and [GPT-3](https://en.wikipedia.org/wiki/GPT-3) has been actively conducted and has shown remarkable results. Based on [Self-Supervised Learning](https://en.wikipedia.org/wiki/Self-supervised_learning), these models figure out the meaning of each sentence and represent each sentence with a similar meaning in a low-dimensional space. By randomly mixing the order between sentences or masking some words to determine whether each sentence/context is true or false, it supports learning without labeling.

The problem of handling different forms of input together, such as text and images, is called multi-modal. **"CLIP: Connecting Text and Image"** addresses an understanding of low-dimensional space quantified by a representative multimodal model. If the existing model has learned only the [features](<https://en.wikipedia.org/wiki/Feature_(machine_learning)>) of the image itself, the multimodal model allows you to learn the features of the text describing the image at the same time, using both the image and the text as input. In addition, by placing text and images together in a low-dimensional space, you can determine the similarity between text and images, which can be used as a search algorithm.

ThanoSQL uses artificial intelligence algorithms to quantify data sets. This digitized data is stored within a column in the DB and is used to search for similar images by calculating the numerical results and similarity between the input text.

**Below is an example and utilization of the ThanoSQL text-image search algorithm.**

- Describes the desired scene in the image or movie that you have in your possession as text and searches for the most similar image. Listen to text-based descriptions of the products you search for and expose the most similar product images.
- Search for the time I want to put the advertisement I want in YouTube videos. In order to put travel advertisements, they easily search for scenes with mountains or camping scenes and insert advertisements.
  <br>

!!! note "In this tutorial"
    :point_right: Unsplash released images of more than 200k photographers for free as a dataset for AI. `Unsplash Dataset - Lite` is composed of 25,000 nature-themed images and comes with 25,000 keywords.

In this tutorial, we will use the text-image search model to search for the desired image in text from 25,000 images in the `Unsplash Dataset - Lite` dataset in the ThanoSQL DB.

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
COPY unsplash_data
OPTIONS(overwrite=True)
FROM "tutorial_data/unsplash_data/unsplash.csv"
```

!!! note "**Query Details**"
    - Specifies the data set name to copy to the DB using the "**COPY**" query syntax.
    - Specify the options to use for **COPY** via the "**OPTIONS**" query syntax.
        - "overwrite" : Set whether or not a data set with the same name can be overwritten when it exists on the DB. If True, the existing dataset is changed to the new dataset (True|False, DEFAULT: False)

## **1. Check Data Set**

To create a text-image search model, we use the `unsplash_dataset` table stored in the ThanoSQL DB. Run the query statement below and check the contents of the table.

<br>

```sql
%%thanosql
SELECT photo_id, image_path, photo_image_url, photo_description, ai_description
FROM unsplash_data
LIMIT 5
```

[![IMAGE](/img/thanosql_search/clip_search/select_data.png)](/img/thanosql_search/clip_search/select_data.png)

!!! note "Understanding Data"
    - Unique id column name for image `photo_id`
    - Column name for path where image `image_path` is located
    - Column name for original image address in `photo_image_url` website unsplash
    - Column name for short human-created description of image
    - `ai_description` AI image

```sql
%%thanosql
PRINT IMAGE
AS
SELECT image_path
FROM unsplash_data
LIMIT 5
```

[![IMAGE](/img/thanosql_search/clip_search/print_dataset_image.png)](/img/thanosql_search/clip_search/print_dataset_image.png)

## **2. Create an image quantization model for text search**

!!! danger "Notes for reference"
    Because the text-image search algorithm takes a long time to learn and uses a pre-trained model with a total of 400 million datasets, the learning process using the "**BUILD MODEL**" query syntax is omitted in this tutorial. The `tutorial_search_clip` model is a base algorithm that uses 'clipen' to obtain and use a pre-learned model.
    Running the "**CONVERT USING**" query syntax automatically creates columns that are digitized with the "model name (`tutorial_search_clip`)\*base algorithm name (`clip`), and running the "**SEARCH IMAGE\***" query syntax automatically creates a "model (`tutor_clip`) image name\clip`). The excitation number "number" refers to the number of text used in the search. If a search is performed with more than one text, the number of columns is increased sequentially according to the order. Please refer to the details below for details below.
(Expected time required for query execution: 3 min)

Run the following "**CONVERT USING**" query syntax to quantify the images `unsplash_data`. The quantified results are stored in the new <mark style="background-color:#D7D0FF ">tutorial_search_clip_clipen</mark> column. (The resulting column name is added as {model_name}_{base_model_name})

```sql
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

!!! note "Query Details"
    - "**CONVERT USING**" The query syntax uses the `tutorial_search_clip` model as an algorithm for image quantification.
    - The "**OPTIONS**" query syntax defines the variables required for image quantification.
        - "table_name" : The name of the table to be stored in the ThanoSQL DB
        - "image_col" : the column name containing the image path
        - "batch_size" : The size of the set of data sets to be read in one learning. According to the paper, the larger the learning performance increases, but we use 128 considering the size of the memory. (DEFAULT : 16)

[![IMAGE](/img/thanosql_search/clip_search/select_data_with_embedding.png)](/img/thanosql_search/clip_search/select_data_with_embedding.png)

<br>

## **3. Searching for images in text**

Text-based image retrieval can be performed using the query syntax "**SEARCH IMAGE**" and the `tutorial_search_clip` model you created. Run the following query syntax to calculate the similarity between the text "a black cat" and the embedded `unsplash_data` images. The results are stored in the newly added <mark style="background-color:#D7D0FF">tutorial_search_clip_clip_similarity1</mark> column.

```sql
%%thanosql
SEARCH IMAGE text="a black cat"
USING tutorial_search_clip
AS
SELECT *
FROM unsplash_data
```

[![IMAGE](/img/thanosql_search/clip_search/search_result_raw.png)](/img/thanosql_search/clip_search/search_result_raw.png)

!!! note "Query Details"
    - State that you will use the "**SEARCH IMAGE**" query syntax to locate the image. Use the "text" variable to enter the text content of the image you want to find.
    - The "**USING**" query syntax specifies the use of `tutorial_search_clip` as the model to be used for the search.

Run the query syntax below to determine the similarity of the five images most similar to the text 'a black cat'.

```sql
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

[![IMAGE](/img/thanosql_search/clip_search/search_result_sorted.png)](/img/thanosql_search/clip_search/search_result_sorted.png)

!!! note "Query Details"
    - The "**SEARCH IMAGE**" query syntax calculates and returns the similarity between the entered text and the image.
    - The first "**SELECT**" query syntax selects the <mark style="background-color:#D7D0FF">image_path</mark> column and the <mark style="background-color:#D7D0FF">tutorial_search_policy_mark1</mark> column from the query results in parentheses.<br>
    - The "**ORDER BY**" query syntax sorts the results by the value in the <mark style="background-color:#D7D0FF">tutorial_search_clip_similarity1</mark> column, which is in descending order("**DESC**"), top 5("__LIMIT__" 5) of its outputs.

You can apply the old query syntax with the "**PRINT**" statement to view the resulting image immediately.

```sql
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

[![IMAGE](/img/thanosql_search/clip_search/result_black_cat.png)](/img/thanosql_search/clip_search/result_black_cat.png)

!!! note "Query Details"
    This query consists of three steps, combined with the above query.

    - The "**SELECT**" query syntax in the first bracket produces the results of the steps immediately above.
    - Outputs the image using the "**PRINT IMAGE**" query syntax.

```sql
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

[![IMAGE](/img/thanosql_search/clip_search/result_dog_on_chair.png)](/img/thanosql_search/clip_search/result_dog_on_chair.png)

```sql
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

[![IMAGE](/img/thanosql_search/clip_search/result_gloomy.png)](/img/thanosql_search/clip_search/result_gloomy.png)

```sql
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

[![IMAGE](/img/thanosql_search/clip_search/result_happy.png)](/img/thanosql_search/clip_search/result_happy.png)

## **4. In Conclusion**

In this tutorial, we used a multimodal text/image quantification model to search for images via text in `unsplash dataset`. As it is a beginner's tutorial, we focused on getting visible results through simple queries. If you use image search with a slightly more colorful query, you will get a value closer to the desired result.

!!! tip "**Inquiries on model distribution for 'my own' service**"
    If you have difficulty creating your own model using ThanoSQL or applying it to service, please feel free to contact me below😊

    Inquiries about building a speech recognition model: contact@smartmind.team
