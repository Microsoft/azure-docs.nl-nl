---
title: Profiel model-geheugen en CPU-gebruik
titleSuffix: Azure Machine Learning
description: Leer uw model te profileren vóór de implementatie. Profile ring bepaalt het geheugen-en CPU-gebruik van uw model.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: gopalv
author: gvashishtha
ms.date: 07/31/2020
ms.topic: conceptual
zone_pivot_groups: aml-control-methods
ms.reviewer: larryfr
ms.custom: deploy
ms.openlocfilehash: b9ae40b3d2673961f9b84ed702f18b25b79b6d0c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93320402"
---
# <a name="profile-your-model-to-determine-resource-utilization"></a>Uw model profileren om het resource gebruik te bepalen

In dit artikel wordt beschreven hoe u een machine learning in een model kunt maken om te bepalen hoeveel CPU en geheugen u moet toewijzen voor het model wanneer u het implementeert als webservice.

## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u een model hebt getraind en geregistreerd met Azure Machine Learning. Zie de voor beeld van de [zelf studie](how-to-train-scikit-learn.md) voor een training en het registreren van een scikit-leer model met Azure machine learning.

## <a name="limitations"></a>Beperkingen

* Profile ring werkt niet wanneer de Azure Container Registry (ACR) voor uw werk ruimte zich achter een virtueel netwerk bevindt.

## <a name="run-the-profiler"></a>Profiler uitvoeren

Zodra u het model hebt geregistreerd en de andere onderdelen hebt voor bereid die nodig zijn voor de implementatie, kunt u de CPU en het geheugen bepalen die de geïmplementeerde service nodig heeft. Profile ring test de service die uw model uitvoert en retourneert informatie zoals het CPU-gebruik, het geheugen gebruik en de reactie latentie. Het bevat ook een aanbeveling voor de CPU en het geheugen op basis van het resource gebruik.

Als u uw model wilt profileren, hebt u het volgende nodig:
* Een geregistreerd model.
* Een Afleidings configuratie op basis van uw instap script en omgevings definitie voor afwijzen.
* Een gegevensset in tabel vorm met één kolom, waarbij elke rij een teken reeks bevat die de voorbeeld aanvraag gegevens vertegenwoordigt.

> [!IMPORTANT]
> Op dit moment bieden we alleen ondersteuning voor het profileren van services die hun aanvraag gegevens naar een teken reeks verwachten, bijvoorbeeld: String serialized JSON, Text, String serialized Image, enzovoort. De inhoud van elke rij van de gegevensset (teken reeks) wordt in de hoofd tekst van de HTTP-aanvraag geplaatst en verzonden naar de service die het model voor het scoren inkapselt.

> [!IMPORTANT]
> We bieden alleen ondersteuning voor profileren van Maxi maal twee Cpu's in de ChinaEast2-en USGovArizona-regio.

Hieronder ziet u een voor beeld van hoe u een invoer-gegevensset kunt samen stellen om een service te profileren die de gegevens van de inkomende aanvraag naar geserializde JSON moet bevatten. In dit geval hebben we een gegevensset gemaakt op basis van 100 exemplaren van dezelfde inhoud van de aanvraag gegevens. In Real-World-scenario's wordt u aangeraden dat u grotere gegevens sets met verschillende invoer gebruikt, met name als uw model resource gebruik/-gedrag afhankelijk is van invoer.

::: zone pivot="py-sdk"

```python
import json
from azureml.core import Datastore
from azureml.core.dataset import Dataset
from azureml.data import dataset_type_definitions

input_json = {'data': [[1, 2, 3, 4, 5, 6, 7, 8, 9, 10],
                       [10, 9, 8, 7, 6, 5, 4, 3, 2, 1]]}
# create a string that can be utf-8 encoded and
# put in the body of the request
serialized_input_json = json.dumps(input_json)
dataset_content = []
for i in range(100):
    dataset_content.append(serialized_input_json)
dataset_content = '\n'.join(dataset_content)
file_name = 'sample_request_data.txt'
f = open(file_name, 'w')
f.write(dataset_content)
f.close()

# upload the txt file created above to the Datastore and create a dataset from it
data_store = Datastore.get_default(ws)
data_store.upload_files(['./' + file_name], target_path='sample_request_data')
datastore_path = [(data_store, 'sample_request_data' +'/' + file_name)]
sample_request_data = Dataset.Tabular.from_delimited_files(
    datastore_path, separator='\n',
    infer_column_types=True,
    header=dataset_type_definitions.PromoteHeadersBehavior.NO_HEADERS)
sample_request_data = sample_request_data.register(workspace=ws,
                                                   name='sample_request_data',
                                                   create_new_version=True)
```

Wanneer u de gegevensset hebt die voorbeeld gegevens voor de aanvraag hebt gemaakt, kunt u een Afleidings configuratie maken. De configuratie voor het afwijzen van interferentie is gebaseerd op de score.py en de omgevings definitie. In het volgende voor beeld ziet u hoe u de configuratie voor afwijzen maakt en profile ring uitvoert:

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.dataset import Dataset


model = Model(ws, id=model_id)
inference_config = InferenceConfig(entry_script='path-to-score.py',
                                   environment=myenv)
input_dataset = Dataset.get_by_name(workspace=ws, name='sample_request_data')
profile = Model.profile(ws,
            'unique_name',
            [model],
            inference_config,
            input_dataset=input_dataset)

profile.wait_for_completion(True)

# see the result
details = profile.get_details()
```

::: zone-end

::: zone pivot="cli"


De volgende opdracht laat zien hoe u een model kunt profielen met behulp van de CLI:

```azurecli-interactive
az ml model profile -g <resource-group-name> -w <workspace-name> --inference-config-file <path-to-inf-config.json> -m <model-id> --idi <input-dataset-id> -n <unique-name>
```

> [!TIP]
> Als u de informatie wilt behouden die door profile ring wordt geretourneerd, gebruikt u Tags of eigenschappen voor het model. Als u labels of eigenschappen gebruikt, worden de gegevens opgeslagen met het model in het model register. In de volgende voor beelden ziet u hoe u een nieuwe tag met de- `requestedCpu` en- `requestedMemoryInGb` informatie toevoegt:
>
> ```python
> model.add_tags({'requestedCpu': details['requestedCpu'],
>                 'requestedMemoryInGb': details['requestedMemoryInGb']})
> ```
>
> ```azurecli-interactive
> az ml model profile -g <resource-group-name> -w <workspace-name> --i <model-id> --add-tag requestedCpu=1 --add-tag requestedMemoryInGb=0.5
> ```

::: zone-end

## <a name="next-steps"></a>Volgende stappen

* [Problemen met een mislukte implementatie oplossen](how-to-troubleshoot-deployment.md)
* [Implementeren naar Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md)
* [Client toepassingen maken voor het gebruik van webservices](how-to-consume-web-service.md)
* [Webservice bijwerken](how-to-deploy-update-web-service.md)
* [Een model implementeren met behulp van een aangepaste docker-installatie kopie](how-to-deploy-custom-docker-image.md)
* [TLS gebruiken om een webservice te beveiligen via Azure Machine Learning](how-to-secure-web-service.md)
* [Uw Azure Machine Learning modellen bewaken met Application Insights](how-to-enable-app-insights.md)
* [Gegevens verzamelen voor modellen in productie](how-to-enable-data-collection.md)
* [Gebeurtenis waarschuwingen en triggers maken voor model implementaties](how-to-use-event-grid.md)

