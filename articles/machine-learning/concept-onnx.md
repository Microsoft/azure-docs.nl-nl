---
title: 'ONNX: hoge prestaties, cross-interferentie van platform'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de open Neural Network Exchange (ONNX) kan u helpen de deinterferentie van uw machine learning model te optimaliseren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: prasantp
author: prasanthpul
ms.date: 06/18/2020
ms.custom: seodec18
ms.openlocfilehash: 5a1ed6c43e4d3a96e0e19d7be50e1e16bcab780e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "98071235"
---
# <a name="onnx-and-azure-machine-learning-create-and-accelerate-ml-models"></a>ONNX en Azure Machine Learning: ML-modellen maken en versnellen

Meer informatie over het gebruik van de [Open Neural Network Exchange](https://onnx.ai) (ONNX) kan u helpen de deinterferentie van uw machine learning model te optimaliseren. Defactorion of model Score is de fase waarin het geïmplementeerde model wordt gebruikt voor de voor spelling, meestal op productie gegevens. 

Het optimaliseren van machine learning modellen voor deverzekering (of model scores) is lastig omdat u het model en de tape wisselaar voor het afnemen van problemen moet afstemmen om optimaal van de hardware-mogelijkheden te zorgen. Het probleem wordt zeer hard als u optimale prestaties wilt krijgen op verschillende platformen (Cloud/Edge, CPU/GPU, enzovoort), omdat elk van beide verschillende mogelijkheden en kenmerken heeft. De complexiteit neemt toe als u modellen hebt van verschillende frameworks die op verschillende platforms moeten worden uitgevoerd. Het is zeer tijdrovend om alle verschillende combi Naties van frameworks en hardware te optimaliseren. Een oplossing die eenmaal kan worden getraind in uw voorkeurs raamwerk en overal in de Cloud of Edge moet worden uitgevoerd. Hier wordt ONNX geleverd.

Micro soft en een community van partners hebben ONNX als Open Standard gemaakt voor het vertegenwoordigen van machine learning modellen. Modellen van een [groot aantal Frameworks](https://onnx.ai/supported-tools) , waaronder tensor flow, PyTorch, SciKit-learn, Keras, Chainer, MXNET, MATLAB en SparkML, kunnen worden geëxporteerd of geconverteerd naar de standaard-ONNX-indeling. Zodra de modellen de ONNX-indeling hebben, kunnen ze op verschillende platforms en apparaten worden uitgevoerd.

[ONNX runtime](https://onnxruntime.ai) is een krachtige engine voor het afnemen van prestaties voor het implementeren van ONNX-modellen voor productie. Het is geoptimaliseerd voor zowel Cloud als Edge en werkt op Linux, Windows en Mac. Het is geschreven in C++ en heeft daarnaast C-, Python-, C#-, Java-en Java script-Api's (Node.js) voor gebruik in verschillende omgevingen. ONNX runtime ondersteunt zowel DNN-als traditionele ML modellen en kan worden geïntegreerd met accelerators op verschillende hardware, zoals TensorRT op NVidia-Gpu's, open-processor op Intel-processors, DirectML in Windows en meer. Met behulp van ONNX runtime kunt u profiteren van de uitgebreide optimalisaties, testen en voortdurende verbeteringen van productie kwaliteit.

ONNX runtime wordt gebruikt in grootschalige micro soft-Services, zoals Bing, Office en Azure Cognitive Services. Prestatie verbeteringen zijn afhankelijk van een aantal factoren, maar deze micro soft-Services hebben een __gemiddelde 2x-prestatie verbetering gezien op de CPU__. Naast Azure Machine Learning Services wordt ONNX runtime ook uitgevoerd in andere producten die Machine Learning workloads ondersteunen, waaronder:
+ Windows: de runtime is ingebouwd in Windows als onderdeel van [windows machine learning](/windows/ai/windows-ml/) en wordt uitgevoerd op honderden miljoenen apparaten. 
+ Product familie van Azure SQL: systeem eigen scores uitvoeren op gegevens in [Azure SQL Edge](../azure-sql-edge/onnx-overview.md) en [Azure SQL Managed instance](../azure-sql/managed-instance/machine-learning-services-overview.md).
+ ML.NET: [Voer ONNX-modellen uit in ml.net](/dotnet/machine-learning/tutorials/object-detection-onnx).


[![ONNX-stroom diagram met trainingen, conversie Programma's en implementatie](./media/concept-onnx/onnx.png)](././media/concept-onnx/onnx.png#lightbox)

## <a name="get-onnx-models"></a>ONNX-modellen ophalen

U kunt ONNX modellen op verschillende manieren verkrijgen:
+ Train een nieuw ONNX-model in Azure Machine Learning (Zie de voor beelden onder aan dit artikel) of met behulp van [geautomatiseerde machine learning mogelijkheden](concept-automated-ml.md#automl--onnx)
+ Bestaand model converteren van een andere indeling naar ONNX (Raadpleeg de [zelf studies](https://github.com/onnx/tutorials)) 
+ Een vooraf getraind ONNX-model ophalen uit het [ONNX-model Zoo](https://github.com/onnx/models)
+ Een aangepast ONNX-model genereren vanuit de [Azure Custom Vision-service](../cognitive-services/custom-vision-service/index.yml) 

Veel modellen, waaronder afbeeldings classificatie, object detectie en tekst verwerking, kunnen worden weer gegeven als ONNX modellen. Als u een probleem ondervindt met een model dat niet kan worden geconverteerd, kunt u een probleem oplossen in het GitHub van het respectieve conversie programma dat u hebt gebruikt. U kunt door gaan met het gebruik van uw bestaande indelings model totdat het probleem is opgelost.

## <a name="deploy-onnx-models-in-azure"></a>ONNX-modellen implementeren in azure

Met Azure Machine Learning kunt u uw ONNX-modellen implementeren, beheren en bewaken. Met de Standard- [implementatie werk stroom](concept-model-management-and-deployment.md) en ONNX runtime kunt u een rest-eind punt maken dat wordt gehost in de Cloud. Zie voor beelden van Jupyter-notebooks aan het einde van dit artikel om het zelf te proberen. 

### <a name="install-and-use-onnx-runtime-with-python"></a>ONNX-runtime installeren en gebruiken met python

Python-pakketten voor ONNX runtime zijn beschikbaar op [PyPi.org](https://pypi.org) ([CPU](https://pypi.org/project/onnxruntime), [GPU](https://pypi.org/project/onnxruntime-gpu)). Lees de [systeem vereisten](https://github.com/Microsoft/onnxruntime#system-requirements) voor de installatie. 

 Als u ONNX runtime voor python wilt installeren, gebruikt u een van de volgende opdrachten: 
```python   
pip install onnxruntime       # CPU build
pip install onnxruntime-gpu   # GPU build
```

Gebruik voor het aanroepen van ONNX runtime in uw python-script:    
```python
import onnxruntime
session = onnxruntime.InferenceSession("path to model")
```

De documentatie die het model vergezelt, geeft meestal de invoer en uitvoer voor het gebruik van het model aan. U kunt ook een visualisatie programma, zoals [Netron](https://github.com/lutzroeder/Netron) , gebruiken om het model weer te geven. Met ONNX runtime kunt u ook een query uitvoeren op de meta gegevens, invoer en uitvoer van het model:    
```python
session.get_modelmeta()
first_input_name = session.get_inputs()[0].name
first_output_name = session.get_outputs()[0].name
```

Als u uw model wilt delegeren, gebruikt `run` en geeft u de lijst met uitvoer resultaten op die u wilt retour neren (laat dit leeg als u wilt dat alle) en een kaart van de invoer waarden. Het resultaat is een lijst met de uitvoer.  
```python
results = session.run(["output1", "output2"], {
                      "input1": indata1, "input2": indata2})
results = session.run([], {"input1": indata1, "input2": indata2})
```

Zie [ONNX runtime Reference docs](https://aka.ms/onnxruntime-python)(Engelstalig) voor de volledige Python API-referentie.    

## <a name="examples"></a>Voorbeelden
Zie [How-to-use-azureml/Deployment/onnx](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/onnx) voor een voor beeld van python-notebooks waarmee u onnx-modellen maakt en implementeert.

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

Voor beelden voor gebruik in andere talen vindt u in de [ONNX runtime-github](https://github.com/microsoft/onnxruntime/tree/master/samples).

## <a name="more-info"></a>Meer informatie

Meer informatie over **ONNX** of bijdragen aan het project:
+ [ONNX-project website](https://onnx.ai)
+ [ONNX-code op GitHub](https://github.com/onnx/onnx)

Meer informatie over **ONNX runtime** of bijdragen aan het project:
+ [ONNX runtime-project website](https://onnxruntime.ai)
+ [ONNX runtime GitHub opslag plaats](https://github.com/Microsoft/onnxruntime)