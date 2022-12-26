---
title: ThanoSQL Workspace Manual
---

# **ThanoSQL Workspace Manual**

ThanoSQL`s workspace is a web-based computing environment based on [Jupyter Lab](https://github.com/jupyterlab/jupyterlab).

To use **ThanoSQL** in this environment, you must first load the **thanosql** cell magic.

!!! tip ""
    You can press the run button at the top, or you can run it with either the **Ctrl + Enter** or **Shift + Enter** shortcuts.

## **1. Call up ThanoSQL cell magic**

```sql
%load_ext thanosql
```

## **2. Set API_TOKEN**

Next, to set the workspace's API_TOKEN, press the **Get API_TOKEN** button at the top of the browser to copy the API Token then paste it into the cell as shown.
!!! danger
    You can always get a new API token, but please note that when you get a new one, you can no longer use the previously issued token.

!!! tip "With the generated API token, you can use all of the ThanoSQL`s REST APIs"
    For more information about using the ThanoSQL's REST API, see [__ThanoSQL REST API Reference__](/en/how-to_guides/reference/#thanosql-rest-api-reference)

```sql
%thanosql API_TOKEN=<Issued_API_TOKEN>
```

ex)

```sql
%thanosql API_TOKEN=eyGVFDdfafddvczs
```
[![IMAGE](/img/thanosql_api/restapi_token_img2.jpg)](/img/thanosql_api/restapi_token_img2.jpg) 

!!! notice "How to create a query statement in the ThanoSQL workspace" 
    You can use either a one-line or multi-line format when writing the ThanoSQL queries.

    - One-line queries are primarily used when returning queries in table form and assigning tables to variables. It is also used to assign the ThanoSQL cell magic and a token, as shown in steps 1 and 2.

    - Multi-line queries provide the same user experience as other DBMSs and is used to query tables or run the ThanoSQL's extended syntax.

## **3. Check the list of the ThanoSQL models and datasets using the LIST query syntax**

All preparations to use **ThanoSQL** are complete.

To view a list of prebuilt the ThanoSQL models, run the ThanoSQL statement below.

```sql
%%thanosql
LIST THANOSQL MODEL
```

[![IMAGE](/img/getting_started/img8.png)](/img/getting_started/img8.png)

To see the list of datasets used by the tutorials, run the statement below.

```sql
%%thanosql
LIST THANOSQL DATASET
```

[![IMAGE](/img/getting_started/img9.png)](/img/getting_started/img9.png)

## __4. Get Tutorial__

You can check out the available tutorials from the [tutorials in the ThanoSQL Technical Documentation](/en/tutorials/algorithm_list/). 
Running the statement below allows you to clone all the ThanoSQL's tutorial into your workspace.

```sql
!git clone https://github.com/smartmind-team/thanosql-tutorial.git
```

If you want to import only certain tutorials into your workspace, use the wget method as shown below.

!!!note "___Github URL of the ThanoSQL tutorials___"

```python
!wget [Tutorials' Github URL]
# Example 
## wget https://raw.githubusercontent.com/smartmind-team/thanosql-tutorial/main/tutorial_en/thanosql_search/search_image_by_keyword.ipynb
```

| Tutorial | URL |
| :---------: |  :----------------------------------: |
| `Search images by keywords` | https://raw.githubusercontent.com/smartmind-team/thanosql-tutorial/main/tutorial_en/thanosql_search/search_image_by_keyword.ipynb |
| `Search images by images` | https://raw.githubusercontent.com/smartmind-team/thanosql-tutorial/main/tutorial_en/thanosql_search/search_image_by_image.ipynb |
| `Search images by text` | https://raw.githubusercontent.com/smartmind-team/thanosql-tutorial/main/tutorial_en/thanosql_search/search_image_by_text.ipynb |
| `Search text by text` | https://raw.githubusercontent.com/smartmind-team/thanosql-tutorial/main/tutorial_en/thanosql_search/search_text_by_text.ipynb |
| `Search video by text` | https://raw.githubusercontent.com/smartmind-team/thanosql-tutorial/main/tutorial_en/thanosql_search/search_video_by_text.ipynb |
| `Create classification model using Auto-ML` | https://raw.githubusercontent.com/smartmind-team/thanosql-tutorial/main/tutorial_en/thanosql_ml/classification/automl_classification.ipynb |
| `Create Image Classification Model` | https://raw.githubusercontent.com/smartmind-team/thanosql-tutorial/main/tutorial_en/thanosql_ml/classification/image_classification.ipynb |
| `Create text classification model` | https://raw.githubusercontent.com/smartmind-team/thanosql-tutorial/main/tutorial_en/thanosql_ml/classification/text_classification.ipynb |
| `Create regression model using Auto-ML` | https://raw.githubusercontent.com/smartmind-team/thanosql-tutorial/main/tutorial_en/thanosql_ml/regression/automl_regression.ipynb |
| `Create voice recognition model that dictates audio files` | https://raw.githubusercontent.com/smartmind-team/thanosql-tutorial/main/tutorial_en/thanosql_ml/audio_recognition/speech_recognition.ipynb |
| `Using a Speech Recognition Model that dictates and translate audio files` | https://raw.githubusercontent.com/smartmind-team/thanosql-tutorial/main/tutorial_en/thanosql_ml/audio_recognition/speech_recognition2.ipynb |
| `Use the Visual Question Answering Model to find an appropriate answer to a question` | https://raw.githubusercontent.com/smartmind-team/thanosql-tutorial/main/tutorial_en/thanosql_ml/question_answering/visual_question_answering.ipynb |  
|`Use your model in ThanoSQL`| https://raw.githubusercontent.com/smartmind-team/thanosql-tutorial/main/tutorial_en/thanosql_ml/udm_tutorial.ipynb |


