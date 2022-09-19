---
title: Using the Custom Model in ThanoSQL
---

# __Using the Custom Model in ThanoSQL__ 

## __0. Prepare Dataset and Model__

This tutorial uses the Beans dataset. This dataset is of leaf images taken in the field in different districts in Uganda by the Makerere AI lab in collaboration with the National Crops Resources Research Institute (NaCRRI), the national body in charge of research in agriculture in Uganda.



Reference: <https://github.com/AI-Lab-Makerere/ibean>

### __Prepare Dataset__

#### Download and Unzip Data


```python
import os
from shutil import unpack_archive
from urllib.request import urlretrieve

url = "https://storage.googleapis.com/ibeans"

for split in ["train", "validation", "test"]:
    urlretrieve(f"{url}/{split}.zip", f"{split}.zip")
    unpack_archive(f"{split}.zip", ".")
    os.remove(f"{split}.zip")
```

#### Install Necessary Packages


```python
!pip install torch torchvision
```

    [33mWARNING: The directory '/home/jovyan/.cache/pip' or its parent directory is not owned or is not writable by the current user. The cache has been disabled. Check the permissions and owner of that directory. If executing pip with sudo, you should use sudo's -H flag.[0m[33m
    [0mRequirement already satisfied: torch in /opt/conda/lib/python3.9/site-packages (1.12.1)
    Collecting torchvision
      Downloading torchvision-0.13.1-cp39-cp39-manylinux1_x86_64.whl (19.1 MB)
    [2K     [90m━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━[0m [32m19.1/19.1 MB[0m [31m211.5 MB/s[0m eta [36m0:00:00[0ma [36m0:00:01[0m
    [?25hRequirement already satisfied: typing-extensions in /opt/conda/lib/python3.9/site-packages (from torch) (4.3.0)
    Requirement already satisfied: requests in /opt/conda/lib/python3.9/site-packages (from torchvision) (2.27.1)
    Collecting pillow!=8.3.*,>=5.3.0
      Downloading Pillow-9.2.0-cp39-cp39-manylinux_2_28_x86_64.whl (3.2 MB)
    [2K     [90m━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━[0m [32m3.2/3.2 MB[0m [31m297.4 MB/s[0m eta [36m0:00:00[0m
    [?25hRequirement already satisfied: numpy in /opt/conda/lib/python3.9/site-packages (from torchvision) (1.23.2)
    Requirement already satisfied: charset-normalizer~=2.0.0 in /opt/conda/lib/python3.9/site-packages (from requests->torchvision) (2.0.12)
    Requirement already satisfied: urllib3<1.27,>=1.21.1 in /opt/conda/lib/python3.9/site-packages (from requests->torchvision) (1.26.9)
    Requirement already satisfied: idna<4,>=2.5 in /opt/conda/lib/python3.9/site-packages (from requests->torchvision) (3.3)
    Requirement already satisfied: certifi>=2017.4.17 in /opt/conda/lib/python3.9/site-packages (from requests->torchvision) (2021.10.8)
    Installing collected packages: pillow, torchvision
    Successfully installed pillow-9.2.0 torchvision-0.13.1


#### Create a Training Dataset 
Following code block has been referenced from this [link](https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html) and has been modified for this tutorial's need.


```python
from torch.utils.data import DataLoader
from torchvision import transforms as T
from torchvision.datasets import ImageFolder

data_transforms = {
    "train": T.Compose(
        [
            T.RandomResizedCrop(224),
            T.RandomHorizontalFlip(),
            T.ToTensor(),
            T.Normalize([0.485, 0.456, 0.406], [0.229, 0.224, 0.225]),
        ]
    ),
    "validation": T.Compose(
        [
            T.Resize(224),
            T.CenterCrop(224),
            T.ToTensor(),
            T.Normalize([0.485, 0.456, 0.406], [0.229, 0.224, 0.225]),
        ]
    ),
}

image_datasets = {
    split: ImageFolder(split, data_transforms[split])
    for split in ["train", "validation"]
}
dataloaders = {
    split: DataLoader(image_datasets[split], batch_size=8, shuffle=split == "train")
    for split in ["train", "validation"]
}
dataset_sizes = {split: len(image_datasets[split]) for split in ["train", "validation"]}
```

    /opt/conda/lib/python3.9/site-packages/tqdm/auto.py:22: TqdmWarning: IProgress not found. Please update jupyter and ipywidgets. See https://ipywidgets.readthedocs.io/en/stable/user_install.html
      from .autonotebook import tqdm as notebook_tqdm


### __Prepare the Model__

#### Create a Model Training Function


```python
import time
import copy
import torch


def train_model(model, criterion, optimizer, num_epochs=3):
    start_time = time.time()
    device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
    model.to(device)
    best_model_weights = copy.deepcopy(model.state_dict())
    best_acc = 0.0

    for epoch in range(num_epochs):
        print(f"Epoch {epoch}/{num_epochs - 1}")
        print("-" * 10)

        # Every epoch goes through a training and validation phase
        for phase in ["train", "validation"]:
            if phase == "train":
                model.train()
            else:
                model.eval()

            running_loss = 0.0
            running_corrects = 0

            for inputs, labels in dataloaders[phase]:
                inputs = inputs.to(device)
                labels = labels.to(device)

                optimizer.zero_grad()

                # Forward propagation 
                with torch.set_grad_enabled(phase == "train"):
                    outputs = model(inputs)
                    preds = torch.argmax(outputs, dim=1)
                    loss = criterion(outputs, labels)

                    # Backward propagation during training phase only 
                    if phase == "train":
                        loss.backward()
                        optimizer.step()

                # Statistics 
                running_loss += loss.item() * inputs.size(0)
                running_corrects += torch.sum(preds == labels.data)

            epoch_loss = running_loss / dataset_sizes[phase]
            epoch_acc = running_corrects / dataset_sizes[phase]

            print(f"{phase} Loss: {epoch_loss:.4f} Acc: {epoch_acc:.4f}")

            # Save if the model accuracy is higher than the previous accuracy 
            if phase == "validation" and epoch_acc > best_acc:
                best_acc = epoch_acc
                best_model_weights = copy.deepcopy(model.state_dict())

        print()

    time_elapsed = time.time() - start_time
    print(f"Training complete in {time_elapsed // 60:.0f}m {time_elapsed % 60:.0f}s")
    print(f"Best val Acc: {best_acc:4f}")

    model.load_state_dict(best_model_weights)
    return model
```

#### Load the Model

This tutorial uses mobilevit v2 as it has a high accuracy for a lightweight model. 


```python
model = torch.hub.load("rwightman/pytorch-image-models", "mobilevitv2_050", pretrained=True, num_classes=3)
optimizer = torch.optim.Adam(model.parameters(), lr=0.001)
criterion = torch.nn.CrossEntropyLoss()
```

    /opt/conda/lib/python3.9/site-packages/torch/hub.py:266: UserWarning: You are about to download and run code from an untrusted repository. In a future release, this won't be allowed. To add the repository to your trusted list, change the command to {calling_fn}(..., trust_repo=False) and a command prompt will appear asking for an explicit confirmation of trust, or load(..., trust_repo=True), which will assume that the prompt is to be answered with 'yes'. You can also use load(..., trust_repo='check') which will only prompt for confirmation if the repo is not already trusted. This will eventually be the default behaviour
      warnings.warn(
    Downloading: "https://github.com/rwightman/pytorch-image-models/zipball/master" to /home/jovyan/.cache/torch/hub/master.zip
    Downloading: "https://github.com/rwightman/pytorch-image-models/releases/download/v0.1-mvit-weights/mobilevitv2_050-49951ee2.pth" to /home/jovyan/.cache/torch/hub/checkpoints/mobilevitv2_050-49951ee2.pth


#### Train and Save a Model


```python
trained_model = train_model(model, criterion, optimizer, num_epochs=3)
```

    Epoch 0/2
    ----------
    train Loss: 0.5687 Acc: 0.7853
    validation Loss: 0.1443 Acc: 0.9624
    
    Epoch 1/2
    ----------
    train Loss: 0.3428 Acc: 0.8675
    validation Loss: 0.1300 Acc: 0.9699
    
    Epoch 2/2
    ----------
    train Loss: 0.2868 Acc: 0.8956
    validation Loss: 0.2953 Acc: 0.9098
    
    Training complete in 2m 54s
    Best val Acc: 0.969925



```python
torch.save(trained_model, "trained_model.pth")
```

#### Create a Dataframe to Insert into the ThanoSQL Database 


```python
import numpy as np
import pandas as pd

test_dataset = ImageFolder("test", data_transforms["validation"])

data = np.stack([img.numpy() for img, _ in test_dataset])
df = pd.DataFrame(pd.Series(data.tolist()), columns=["image"])  # column name must be an "image"
df.to_pickle("test_data.pkl")
```

As mentioned in the [ThanoSQL Workspace](https://docs.thanosql.ai/en/getting_started/how_to_use_ThanoSQL/#5-thanosql-workspace), you must create an API token and run the query below to execute the query of ThanoSQL. 


```python
%load_ext thanosql
%thanosql API_TOKEN=<Issued_API_TOKEN>
```


```python
%%thanosql
COPY beans_test 
OPTIONS (overwrite=True)
FROM "test/udm_tutorial/test_data.pkl"
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

## __1.Check Dataset__

To check the table's contents, run the following query.


```python
%%thanosql
SELECT *
FROM beans_test
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
      <th>image</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>[[[-0.02868402, -0.045808773500000004, -0.2170...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>[[[-0.06293352690000001, -0.06293352690000001,...</td>
    </tr>
    <tr>
      <th>2</th>
      <td>[[[1.9577873945, 1.8721636534, 1.7180408239, 1...</td>
    </tr>
    <tr>
      <th>3</th>
      <td>[[[0.2110626549, 0.0569397435, -0.3026800752, ...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>[[[-1.3815395832, -1.4329138994, -1.5014128685...</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
    </tr>
    <tr>
      <th>123</th>
      <td>[[[-0.0971830264, -0.11430778350000001, -0.114...</td>
    </tr>
    <tr>
      <th>124</th>
      <td>[[[-0.5938008428, -1.1589177847, -1.0732940435...</td>
    </tr>
    <tr>
      <th>125</th>
      <td>[[[-0.4396780729, -0.2170563042, -0.2513058186...</td>
    </tr>
    <tr>
      <th>126</th>
      <td>[[[-2.0322802067, -1.9809060097, -1.9124069214...</td>
    </tr>
    <tr>
      <th>127</th>
      <td>[[[-1.27879107, -1.2959158420999999, -1.364414...</td>
    </tr>
  </tbody>
</table>
<p>128 rows × 1 columns</p>
</div>



## __2. Upload Custom Model__

To upload a custom model, run the following query.


```python
%%thanosql
UPLOAD MODEL beans_mobilevit
OPTIONS (overwrite=True, framework="pytorch")
FROM "test/udm_tutorial/trained_model.pth"
```

    Success


## __3. Predict Using a Custom Model__

To predict the result using a custom model, run the following query.


```python
%%thanosql
PREDICT
USING beans_mobilevit
AS
SELECT *
FROM beans_test
ORDER BY RANDOM()
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
      <th>image</th>
      <th>predicted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>[[[-1.9124069214000001, -1.6555356979, -1.4842...</td>
      <td>[-1.3859444857, -0.6104061604000001, 2.0402860...</td>
    </tr>
    <tr>
      <th>1</th>
      <td>[[[0.3823101819, 0.7761794925000001, 1.0501755...</td>
      <td>[1.8987154961, 1.711524725, -3.7767300606000003]</td>
    </tr>
    <tr>
      <th>2</th>
      <td>[[[-0.3883038163, -0.456802845, -0.5595513582,...</td>
      <td>[-0.4351696968, 2.8146388531, -2.7762613297]</td>
    </tr>
    <tr>
      <th>3</th>
      <td>[[[-1.6212861538, -1.6041613817, -1.4671633244...</td>
      <td>[-2.0298011303, -0.8211234808000001, 2.9834887...</td>
    </tr>
    <tr>
      <th>4</th>
      <td>[[[-0.9876701832, -0.8335474133, -0.7136741281...</td>
      <td>[3.9639098644, -2.0307672024, -1.6559199095000...</td>
    </tr>
  </tbody>
</table>
</div>




```python
pred_df = _ 
pred_df["predicted"] = pred_df["predicted"].apply(np.argmax)
pred_df["predicted"] = pred_df["predicted"].apply(test_dataset.classes.__getitem__)
pred_df
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
      <th>image</th>
      <th>predicted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>[[[-1.9124069214000001, -1.6555356979, -1.4842...</td>
      <td>healthy</td>
    </tr>
    <tr>
      <th>1</th>
      <td>[[[0.3823101819, 0.7761794925000001, 1.0501755...</td>
      <td>angular_leaf_spot</td>
    </tr>
    <tr>
      <th>2</th>
      <td>[[[-0.3883038163, -0.456802845, -0.5595513582,...</td>
      <td>bean_rust</td>
    </tr>
    <tr>
      <th>3</th>
      <td>[[[-1.6212861538, -1.6041613817, -1.4671633244...</td>
      <td>healthy</td>
    </tr>
    <tr>
      <th>4</th>
      <td>[[[-0.9876701832, -0.8335474133, -0.7136741281...</td>
      <td>angular_leaf_spot</td>
    </tr>
  </tbody>
</table>
</div>


