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

<!-- #region tags=[] -->
# __Search image by keyword__

**Understanding the keyword-image search service**
---
👉 ThanoSQL provides a search function that allows you to return images as results through keywords. Set the keyword desired by the user as a target using an image classification model, etc., and add a column that indexes the updated image using the learned model. If the text-image search function analyzes the meaning from the images and provides similar images, the keyword-image search finds the images corresponding to the desired target value (category).

**In this tutorial**
---
👉 Use the "__SEARCH__" query syntax, which is the unstructured data search syntax provided by ThanoSQL, and the "__SELECT__" query syntax, which is the structured data search syntax provided by the existing PostgreSQL, to search for images using specific keywords.

 
**Dataset description**
---
👉 ‘Introduction of food images and nutritional information’ dataset is the data established by the ‘Artificial Intelligence Learning Data Construction Project’ hosted by the Ministry of Science and ICT and supported by the Korea Intelligent Information Society Agency. It is composed of high-quality image data by selecting 400 types. It consists of 842,000 images, and this tutorial uses only some (10 species, 1,190 photos) photos from that dataset.
<!-- #endregion -->

## __0. Preparing a dataset__


To use the query syntax of ThanoSQL, you must create an API token and run the query below, as mentioned in the [ThanoSQL Workspace](https://docs.thanosql.ai/quick_start/how_to_use_ThanoSQL/#5-thanosql).

```python
%load_ext thanosql
%thanosql API_TOKEN=<Issued_API_TOKEN>
```

```python tags=[]
%%thanosql
COPY diet 
OPTIONS(overwrite=True)
FROM "tutorial_data/diet_data/diet.csv"
```

__OPTIONS__ : 

When __overwrite is true__, the user can create a data table with the same name as the previously created data table.  
On the other hand, when __overwrite is False__, the user cannot create a data table with the same name as the previously created data table.

<!-- #region tags=[] -->
## __1. Check dataset__

Use the data table stored in the ThanoSQL DB to create a keyword-image search model. Run the query syntax below and verify the contents of the table.
<!-- #endregion -->

```python
%%thanosql
SELECT * 
FROM diet
```

The diet table contains the following information.   

-  image : image path 
-  label : File name


## __2. Create a keyword search model__

Image search requires learning existing data tables and creating criteria for future searches. To do this, create an image classification model using the dataset that you saw in the previous step. Run the query syntax below to create a model named dat_image_classification.


(Expected time required for query execution: 3 min)

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

**Query Details**
- Create and train a dat_image_classification model using the query syntax "__BUILD MODEL__".
- The query syntax "__USING__" specifies the use of `ConvNeXt_Tiny` as the base model.
- Specify the options to use for model creation via the query syntax "__OPTIONS__".
    - "image_col" : Name of column containing image path
    - "label_col" : Name of column containing information on target value
    - "epochs" : the number of times all learning datasets are learned


When __overwrite is true__, the user can create a data table with the same name as the previously created data table.  
On the other hand, when __overwrite is False__, the user cannot create a data table with the same name as the previously created data table.


## __3. Use the generated model to view keyword-image search models__

Use the image prediction model dat_image_classification created in the previous step to predict the target value of a specific image. After performing the query below, the prediction results are stored in the predicated column and returned.

```python
%%thanosql
PREDICT USING diet_image_classification
AS 
SELECT *
FROM diet
```

**Query Details**
- Use the data_image_classification model created in the previous step with the query syntax "__PREDICT USING__".
