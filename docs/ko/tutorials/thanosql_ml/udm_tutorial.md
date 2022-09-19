---
title: User Defined Model in ThanoSQL
---

# __사용자의 모델을 ThanoSQL에서 사용하기__

## 데이터

### Beans 데이터세트

이 데이터 세트는 우간다의 농업 연구를 담당하는 국가 기관인 국립 작물 자원 연구소(NaCRRI)와 협력하여 Makerere AI 연구소가 우간다의 여러 지역에서 현장에서 촬영한 잎 이미지입니다. 데이터는 총 3개의 클래스로 구성되어 있습니다. 2개의 질병 클래스와 건강 클래스이고, 질병은 각각 세균모무늬병(Angular leaf spot)과 콩 녹병(Bean rust)입니다.

홈페이지: <https://github.com/AI-Lab-Makerere/ibean>

#### 데이터 다운로드 및 압축풀기


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

#### 패키지 설치


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


#### 훈련용 데이터 세트 생성

이후의 코드는 <https://pytorch.org/tutorials/beginner/transfer_learning_tutorial.html>에서 가져와 약간의 조정을 거친 것입니다.


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


#### 모델 학습 코드 작성하기


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

        # 각 에폭(epoch)은 학습 단계와 검증 단계를 갖습니다.
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

                # 매개변수 경사도를 0으로 설정
                optimizer.zero_grad()

                # 순전파
                # 학습 단계에서만 연산 기록을 추적
                with torch.set_grad_enabled(phase == "train"):
                    outputs = model(inputs)
                    preds = torch.argmax(outputs, dim=1)
                    loss = criterion(outputs, labels)

                    # 학습 단계인 경우에만 역전파
                    if phase == "train":
                        loss.backward()
                        optimizer.step()

                # 통계
                running_loss += loss.item() * inputs.size(0)
                running_corrects += torch.sum(preds == labels.data)

            epoch_loss = running_loss / dataset_sizes[phase]
            epoch_acc = running_corrects / dataset_sizes[phase]

            print(f"{phase} Loss: {epoch_loss:.4f} Acc: {epoch_acc:.4f}")

            # 모델의 정확도가 기존의 최고 정확도보다 높다면 저장
            if phase == "validation" and epoch_acc > best_acc:
                best_acc = epoch_acc
                best_model_weights = copy.deepcopy(model.state_dict())

        print()

    time_elapsed = time.time() - start_time
    print(f"Training complete in {time_elapsed // 60:.0f}m {time_elapsed % 60:.0f}s")
    print(f"Best val Acc: {best_acc:4f}")

    # 가장 나은 모델 가중치를 불러옴
    model.load_state_dict(best_model_weights)
    return model
```

#### 모델 불러오기

mobilevit v2를 사용합니다. 작고 가볍지만 정확도는 높아 빠른 튜토리얼에 적합합니다.


```python
model = torch.hub.load("rwightman/pytorch-image-models", "mobilevitv2_050", pretrained=True, num_classes=3)
optimizer = torch.optim.Adam(model.parameters(), lr=0.001)
criterion = torch.nn.CrossEntropyLoss()
```

    /opt/conda/lib/python3.9/site-packages/torch/hub.py:266: UserWarning: You are about to download and run code from an untrusted repository. In a future release, this won't be allowed. To add the repository to your trusted list, change the command to {calling_fn}(..., trust_repo=False) and a command prompt will appear asking for an explicit confirmation of trust, or load(..., trust_repo=True), which will assume that the prompt is to be answered with 'yes'. You can also use load(..., trust_repo='check') which will only prompt for confirmation if the repo is not already trusted. This will eventually be the default behaviour
      warnings.warn(
    Downloading: "https://github.com/rwightman/pytorch-image-models/zipball/master" to /home/jovyan/.cache/torch/hub/master.zip
    Downloading: "https://github.com/rwightman/pytorch-image-models/releases/download/v0.1-mvit-weights/mobilevitv2_050-49951ee2.pth" to /home/jovyan/.cache/torch/hub/checkpoints/mobilevitv2_050-49951ee2.pth


#### 모델 훈련 및 저장


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

#### ThanoSQL에 입력할 데이터프레임 생성


```python
import numpy as np
import pandas as pd

test_dataset = ImageFolder("test", data_transforms["validation"])

data = np.stack([img.numpy() for img, _ in test_dataset])
df = pd.DataFrame(pd.Series(data.tolist()), columns=["image"])  # column 이름을 "image"로 지정해야 합니다.
df.to_pickle("test_data.pkl")
```


```python
%load_ext thanosql
%thanosql API_TOKEN=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ3b3Jrc3BhY2VfaWQiOjQsImV4cCI6MTY4OTIxMjk4OH0.FCeDYuKD7a4tO0DfkpDE_sRc1C3DOnBsGiG9C0mkZQQ
%thanosql ws://engine:8000/ws/v1/query
```

    API Token is set as 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ3b3Jrc3BhY2VfaWQiOjQsImV4cCI6MTY4OTIxMjk4OH0.FCeDYuKD7a4tO0DfkpDE_sRc1C3DOnBsGiG9C0mkZQQ'
    API URL is changed to ws://engine:8000/ws/v1/query



```python
%%thanosql
COPY beans_test 
OPTIONS (overwrite=True)
FROM "test/udm_tutorial/test_data.pkl"
```

    Success



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



#### 모델 업로드


```python
%%thanosql
UPLOAD MODEL beans_mobilevit
OPTIONS (overwrite=True, framework="pytorch")
FROM "test/udm_tutorial/trained_model.pth"
```

    Success


#### 업로드한 모델을 통한 예측


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
pred_df = _  # 가장 마지막에 사용된 객체를 불러옵니다.
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


