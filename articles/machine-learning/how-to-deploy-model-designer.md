---
title: De Studio gebruiken voor het implementeren van modellen die zijn getraind in de ontwerp functie
titleSuffix: Azure Machine Learning
description: Gebruik Azure Machine Learning Studio om machine learning-modellen te implementeren zonder dat u maar één regel code hoeft te schrijven.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.reviewer: peterlu
ms.date: 10/29/2020
ms.topic: conceptual
ms.custom: how-to, deploy, studio, designer
ms.openlocfilehash: 35acfc51ae76fdacef11f03b1fbd91ad58650ae6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "97722620"
---
# <a name="use-the-studio-to-deploy-models-trained-in-the-designer"></a>De Studio gebruiken voor het implementeren van modellen die zijn getraind in de ontwerp functie

In dit artikel leert u hoe u een ontwerp model kunt implementeren als een realtime-eind punt in Azure Machine Learning Studio.

Zodra de registratie is geregistreerd of gedownload, kunt u getrainde modellen op dezelfde manier als elk ander model gebruiken. Geëxporteerde modellen kunnen worden geïmplementeerd in use-cases zoals Internet der dingen (IoT) en lokale implementaties.

De implementatie in Studio bestaat uit de volgende stappen:

1. Registreer het getrainde model.
1. Down load het item script en Conda afhankelijkheden voor het model.
1. Beschrijving Het invoer script configureren.
1. Implementeer het model op een compute-doel.

U kunt modellen ook rechtstreeks in de ontwerp functie implementeren om model registratie en download stappen voor bestanden over te slaan. Dit kan handig zijn voor snelle implementatie. Zie [een model implementeren met de ontwerp functie](tutorial-designer-automobile-price-deploy.md)voor meer informatie.

Modellen die zijn getraind in de Designer kunnen ook worden geïmplementeerd via de SDK of de opdracht regel interface (CLI). Zie [uw bestaande model implementeren met Azure machine learning](how-to-deploy-existing-model.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

* [Een Azure Machine Learning-werk ruimte](how-to-manage-workspace.md)

* Een voltooide trainings pijplijn met een van de volgende modules:
    - [Train model-module](./algorithm-module-reference/train-model.md)
    - [Model module voor detectie van anomalieën trainen](./algorithm-module-reference/train-anomaly-detection-model.md)
    - [Module clustering model trainen](./algorithm-module-reference/train-clustering-model.md)
    - [Pytorch-model module trainen](./algorithm-module-reference/train-pytorch-model.md)
    - [SVD-advies module trainen](./algorithm-module-reference/train-svd-recommender.md)
    - [Vowpal Wabbit model module trainen](./algorithm-module-reference/train-vowpal-wabbit-model.md)
    - [Uitgebreide model module voor Train &](./algorithm-module-reference/train-wide-and-deep-recommender.md)

## <a name="register-the-model"></a>Het model registreren

Nadat de trainings pijplijn is voltooid, registreert u het getrainde model voor uw Azure Machine Learning-werk ruimte om toegang te krijgen tot het model in andere projecten.

1. Selecteer de [module Train model](./algorithm-module-reference/train-model.md).
1. Selecteer in het rechterdeel venster het tabblad **uitvoer en logboeken** .
1. Selecteer de scherm afbeelding voor het **model pictogram registreren** ![ van het tandwiel pictogram ](./media/how-to-deploy-model-designer/register-model-icon.png) .

    ![Scherm afbeelding van het rechterdeel venster van Train model module](./media/how-to-deploy-model-designer/train-model-right-pane.png)

1. Voer een naam in voor uw model en selecteer vervolgens **Opslaan**.

Nadat u het model hebt geregistreerd, kunt u het vinden op de Asset-pagina voor **modellen** in de Studio.
    
![Scherm opname van het geregistreerde model op de Asset-pagina modellen](./media/how-to-deploy-model-designer/models-asset-page.png)

## <a name="download-the-entry-script-file-and-conda-dependencies-file"></a>Down load het script bestand voor vermelding en Conda afhankelijkheden bestand

U hebt de volgende bestanden nodig voor het implementeren van een model in Azure Machine Learning studio:

- **Invoer script bestand** : Hiermee wordt het getrainde model geladen, worden invoer gegevens uit aanvragen verwerkt, worden er real-time-verantwoordingen gedaan en wordt het resultaat geretourneerd. De Designer genereert automatisch een `score.py` invoer script bestand wanneer de module **Train model** is voltooid.

- **Conda-afhankelijkheden bestand** : Hiermee geeft u op welk PIP-en Conda-pakketten uw webservice van afhankelijk is. De ontwerp functie maakt automatisch een `conda_env.yaml` bestand wanneer de module **Train model** is voltooid.

U kunt deze twee bestanden downloaden in het rechterdeel venster van de module **Train model** :

1. Selecteer de module **Train Model**.
1. Selecteer de map op het tabblad **uitvoer en logboeken** `trained_model_outputs` .
1. Down load het `conda_env.yaml` bestand en `score.py` bestand.

    ![Scherm opname van Download bestanden voor implementatie in het rechterdeel venster](./media/how-to-deploy-model-designer/download-artifacts-in-right-pane.png)

U kunt de bestanden ook downloaden van de Asset-pagina **modellen** nadat u het model hebt geregistreerd:

1. Ga naar de pagina **modellen** activa.
1. Selecteer het model dat u wilt implementeren.
1. Selecteer het tabblad **artefacten** .
1. Selecteer de map `trained_model_outputs`.
1. Down load het `conda_env.yaml` bestand en `score.py` bestand.  

    ![Scherm opname van Download bestanden voor implementatie in model detail pagina](./media/how-to-deploy-model-designer/download-artifacts-in-models-page.png)

> [!NOTE]
> Het `score.py` bestand biedt vrijwel dezelfde functionaliteit als de **score model** -modules. Sommige modules zoals [Score SVD aanbevelen](./algorithm-module-reference/score-svd-recommender.md), [Score breed en diep aanbevolen](./algorithm-module-reference/score-wide-and-deep-recommender.md), en [Score Vowpal Wabbit model](./algorithm-module-reference/score-vowpal-wabbit-model.md) hebben para meters voor verschillende Score modi. U kunt deze para meters ook wijzigen in het invoer script.
>
>`score.py`Zie de sectie [Configure the entry script](#configure-the-entry-script)(Engelstalig) voor meer informatie over het instellen van para meters in het bestand.

## <a name="deploy-the-model"></a>Het model implementeren

Nadat u de benodigde bestanden hebt gedownload, bent u klaar om het model te implementeren.

1. Selecteer op de pagina **modellen** activa het geregistreerde model.
1. Selecteer de knop **implementeren** .
1. Voer in het menu configuratie de volgende gegevens in:

    - Voer een naam in voor het eind punt.
    - Selecteer deze optie om het model te implementeren in [Azure Kubernetes service](how-to-deploy-azure-kubernetes-service.md) of [Azure container instance](how-to-deploy-azure-container-instance.md).
    - Upload het `score.py` voor het **vermelding script bestand**.
    - Upload het `conda_env.yml` bestand voor **Conda-afhankelijkheden**. 

    >[!TIP]
    > In **Geavanceerde** instelling kunt u de CPU/geheugen capaciteit en andere para meters voor implementatie instellen. Deze instellingen zijn belang rijk voor bepaalde modellen, zoals PyTorch-modellen, die een aanzienlijke hoeveelheid memery verbruiken (ongeveer 4 GB).

1. Selecteer **implementeren** om uw model te implementeren als een real-time-eind punt.

    ![Scherm afbeelding van de pagina model implementeren in model-Asset](./media/how-to-deploy-model-designer/deploy-model.png)

## <a name="consume-the-real-time-endpoint"></a>Het real-time-eind punt gebruiken

Nadat de implementatie is voltooid, kunt u het real-time eind punt vinden op de Asset-pagina met **eind punten** . Vervolgens vindt u een REST-eind punt dat clients kunnen gebruiken om aanvragen naar het real-time-eind punt te verzenden. 

> [!NOTE]
> De Designer genereert ook een JSON-voorbeeld bestand voor het testen van gegevens, dat u kunt downloaden `_samples.json` in de map **trained_model_outputs** .

Gebruik het volgende code voorbeeld om een real-time-eind punt te gebruiken.

```python

import json
from pathlib import Path
from azureml.core.workspace import Workspace, Webservice
 
service_name = 'YOUR_SERVICE_NAME'
ws = Workspace.get(
    name='WORKSPACE_NAME',
    subscription_id='SUBSCRIPTION_ID',
    resource_group='RESOURCEGROUP_NAME'
)
service = Webservice(ws, service_name)
sample_file_path = '_samples.json'
 
with open(sample_file_path, 'r') as f:
    sample_data = json.load(f)
score_result = service.run(json.dumps(sample_data))
print(f'Inference result = {score_result}')
```

### <a name="consume-computer-vision-related-real-time-endpoints"></a>Computer vision-gerelateerde realtime-eind punten gebruiken

Wanneer u met computer vision gerelateerde realtime-eind punten gebruikt, moet u afbeeldingen naar bytes converteren, omdat de webservice alleen teken reeksen kan accepteren als invoer. Hieronder ziet u de voorbeeld code:

```python
import base64
import json
from copy import deepcopy
from pathlib import Path
from azureml.studio.core.io.image_directory import (IMG_EXTS, image_from_file, image_to_bytes)
from azureml.studio.core.io.transformation_directory import ImageTransformationDirectory

# image path
image_path = Path('YOUR_IMAGE_FILE_PATH')

# provide the same parameter setting as in the training pipeline. Just an example here.
image_transform = [
    # format: (op, args). {} means using default parameter values of torchvision.transforms.
    # See https://pytorch.org/docs/stable/torchvision/transforms.html
    ('Resize', 256),
    ('CenterCrop', 224),
    # ('Pad', 0),
    # ('ColorJitter', {}),
    # ('Grayscale', {}),
    # ('RandomResizedCrop', 256),
    # ('RandomCrop', 224),
    # ('RandomHorizontalFlip', {}),
    # ('RandomVerticalFlip', {}),
    # ('RandomRotation', 0),
    # ('RandomAffine', 0),
    # ('RandomGrayscale', {}),
    # ('RandomPerspective', {}),
]
transform = ImageTransformationDirectory.create(transforms=image_transform).torch_transform

# download _samples.json file under Outputs+logs tab in the right pane of Train Pytorch Model module
sample_file_path = '_samples.json'
with open(sample_file_path, 'r') as f:
    sample_data = json.load(f)

# use first sample item as the default value
default_data = sample_data[0]
data_list = []
for p in image_path.iterdir():
    if p.suffix.lower() in IMG_EXTS:
        data = deepcopy(default_data)
        # convert image to bytes
        data['image'] = base64.b64encode(image_to_bytes(transform(image_from_file(p)))).decode()
        data_list.append(data)

# use data.json as input of consuming the endpoint
data_file_path = 'data.json'
with open(data_file_path, 'w') as f:
    json.dump(data_list, f)
```

## <a name="configure-the-entry-script"></a>Het invoer script configureren

Sommige modules in de Designer zoals [Score SVD aanbevelen](./algorithm-module-reference/score-svd-recommender.md), [Score breed en diep aanbevolen](./algorithm-module-reference/score-wide-and-deep-recommender.md)en [Score Vowpal Wabbit model](./algorithm-module-reference/score-vowpal-wabbit-model.md) hebben para meters voor verschillende Score modi. 

In deze sectie leert u hoe u deze para meters in het vermelding script bestand kunt bijwerken.

In het volgende voor beeld wordt het standaard gedrag voor een getraind **breed & diep aanbevolen** model bijgewerkt. Het `score.py` bestand vertelt de webservice standaard om classificaties tussen gebruikers en items te voors pellen. 

U kunt het script bestand voor de vermelding wijzigen om item aanbevelingen te doen en aanbevolen items te retour neren door de `recommender_prediction_kind` para meter te wijzigen.


```python
import os
import json
from pathlib import Path
from collections import defaultdict
from azureml.studio.core.io.model_directory import ModelDirectory
from azureml.designer.modules.recommendation.dnn.wide_and_deep.score. \
    score_wide_and_deep_recommender import ScoreWideAndDeepRecommenderModule
from azureml.designer.serving.dagengine.utils import decode_nan
from azureml.designer.serving.dagengine.converter import create_dfd_from_dict

model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'trained_model_outputs')
schema_file_path = Path(model_path) / '_schema.json'
with open(schema_file_path) as fp:
    schema_data = json.load(fp)


def init():
    global model
    model = ModelDirectory.load(load_from_dir=model_path)


def run(data):
    data = json.loads(data)
    input_entry = defaultdict(list)
    for row in data:
        for key, val in row.items():
            input_entry[key].append(decode_nan(val))

    data_frame_directory = create_dfd_from_dict(input_entry, schema_data)

    # The parameter names can be inferred from Score Wide and Deep Recommender module parameters:
    # convert the letters to lower cases and replace whitespaces to underscores.
    score_params = dict(
        trained_wide_and_deep_recommendation_model=model,
        dataset_to_score=data_frame_directory,
        training_data=None,
        user_features=None,
        item_features=None,
        ################### Note #################
        # Set 'Recommender prediction kind' parameter to enable item recommendation model
        recommender_prediction_kind='Item Recommendation',
        recommended_item_selection='From All Items',
        maximum_number_of_items_to_recommend_to_a_user=5,
        whether_to_return_the_predicted_ratings_of_the_items_along_with_the_labels='True')
    result_dfd, = ScoreWideAndDeepRecommenderModule().run(**score_params)
    result_df = result_dfd.data
    return json.dumps(result_df.to_dict("list"))
```

Voor **Wide & diep aanbevolen** en **Vowpal Wabbit** -modellen kunt u de para meter voor de Score modus configureren met behulp van de volgende methoden:

- De parameter namen zijn de combi natie van kleine en onderstrepings tekens voor de naam van de para meters van de [Score Vowpal Wabbit model](./algorithm-module-reference/score-vowpal-wabbit-model.md) en de [Score breedte en diepe aanbeveling](./algorithm-module-reference/score-wide-and-deep-recommender.md).
- Parameter waarden van het type modus zijn teken reeksen van de corresponderende optie namen. Voer een voor **spellingen** van de bovenstaande codes uit, zoals in het voor beeld, de waarde kan `'Rating Prediction'` of zijn `'Item Recommendation'` . Andere waarden zijn niet toegestaan.

Voor het getrainde model voor **SVD aanbevelen** kunnen de parameter namen en-waarden minder duidelijk zijn en kunt u de tabellen hieronder opzoeken om te bepalen hoe para meters moeten worden ingesteld.

| Naam van de para meter in [Score SVD aanbevelen](./algorithm-module-reference/score-svd-recommender.md)                           | Parameter naam in het vermelding script bestand |
| ------------------------------------------------------------ | --------------------------------------- |
| Voorspellings type Aanbevolen                                  | prediction_kind                         |
| Aanbevolen item selectie                                   | recommended_item_selection              |
| Minimale grootte van de aanbevelings groep voor één gebruiker    | min_recommendation_pool_size            |
| Maximum aantal items dat aan een gebruiker kan worden aanbevolen               | max_recommended_item_count              |
| Hiermee wordt aangegeven of de voorspelde classificaties van de items samen met de labels moeten worden geretourneerd | return_ratings                          |

De volgende code laat zien hoe u para meters kunt instellen voor een SVD-aanbeveling, waarbij alle zes para meters worden gebruikt om geclassificeerde items met voorspelde classificaties aan te bevelen.

```python
score_params = dict(
        learner=model,
        test_data=DataTable.from_dfd(data_frame_directory),
        training_data=None,
        # RecommenderPredictionKind has 2 members, 'RatingPrediction' and 'ItemRecommendation'. You
        # can specify prediction_kind parameter with one of them.
        prediction_kind=RecommenderPredictionKind.ItemRecommendation,
        # RecommendedItemSelection has 3 members, 'FromAllItems', 'FromRatedItems', 'FromUndatedItems'.
        # You can specify recommended_item_selection parameter with one of them.
        recommended_item_selection=RecommendedItemSelection.FromRatedItems,
        min_recommendation_pool_size=1,
        max_recommended_item_count=3,
        return_ratings=True,
    )
```


## <a name="next-steps"></a>Volgende stappen

* [Een model trainen in de ontwerp functie](tutorial-designer-automobile-price-train-score.md)
* [Modellen implementeren met Azure Machine Learning SDK](how-to-deploy-and-where.md)
* [Problemen met een mislukte implementatie oplossen](how-to-troubleshoot-deployment.md)
* [Implementeren naar Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Client toepassingen maken voor het gebruik van webservices](how-to-consume-web-service.md)
* [Webservice bijwerken](how-to-deploy-update-web-service.md)
