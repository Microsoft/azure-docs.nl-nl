---
title: Integratie van open source-machine learning
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van open source python machine learning frameworks om end-to-end machine learning oplossingen in Azure Machine Learning te trainen, implementeren en beheren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: luisquintanilla
ms.author: luquinta
ms.date: 01/14/2020
ms.openlocfilehash: 983e037376be48f497118b06cce8b23c430b1501
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98223071"
---
# <a name="open-source-integration-with-azure-machine-learning-projects"></a>Open-source-integratie met Azure Machine Learning projecten

U kunt het end-to-end machine learning proces trainen, implementeren en beheren in Azure Machine Learning met behulp van de open source python machine learning bibliotheken en platformen.  Gebruik hulpprogram ma's voor ontwikkel aars, zoals Jupyter-notebooks en Visual Studio code, om gebruik te maken van uw bestaande modellen en scripts in Azure Machine Learning.  

In dit artikel vindt u meer informatie over deze open source-bibliotheken en-platforms.

## <a name="train-open-source-machine-learning-models"></a>Open-source machine learning modellen trainen

Het machine learning-trainings proces omvat het Toep assen van algoritmen voor uw gegevens om een taak te kunnen uitvoeren of om een probleem op te lossen. Afhankelijk van het probleem kunt u andere algoritmen kiezen die het beste passen bij de taak en uw gegevens. Voor meer informatie over de verschillende branches van machine learning raadpleegt u het [artikel uitgebreide kennis VS en machine learning](./concept-deep-learning-vs-machine-learning.md) en het [venster machine learning Algorithm Cheat](algorithm-cheat-sheet.md).

### <a name="preserve-data-privacy-using-differential-privacy"></a>Behoud de privacy van gegevens met behulp van differentiële privacy

Als u een machine learning model wilt trainen, hebt u gegevens nodig. Soms zijn de gegevens vertrouwelijk en is het belang rijk om ervoor te zorgen dat de gegevens veilig en privé zijn. Differentiële privacy is een techniek voor het behoud van de vertrouwelijkheid van gegevens in een gegevensset. Zie het artikel over het behouden van de [privacy van gegevens](concept-differential-privacy.md)voor meer informatie. 

Met de open source-differentiële privacy-werkset zoals [SmartNoise](https://github.com/opendifferentialprivacy/smartnoise-core-python) kunt u [de privacy van gegevens](how-to-differential-privacy.md) in azure Machine Learning oplossingen bewaren.

### <a name="classical-machine-learning-scikit-learn"></a>Klassieke machine learning: scikit-informatie

Voor trainings taken die betrekking hebben op klassieke machine learning algoritmen taken zoals classificatie, clustering en regressie kunt u bijvoorbeeld Scikit-leren gebruiken. Zie voor meer informatie over het trainen van een bloemen classificatie model het [artikel How to Train with Scikit-Learn](how-to-train-scikit-learn.md).

### <a name="neural-networks-pytorch-tensorflow-keras"></a>Neural Networks: PyTorch, tensor flow, Keras

Open-source machine learning-algoritmen, een subset van machine learning, zijn handig voor het trainen van diepe leer modellen in Azure Machine Learning.

Open-source-frameworks voor uitgebreide lessen en procedures voor hand leidingen zijn:

 *  [PyTorch](https://github.com/pytorch/pytorch): [een classificatie model voor een diepe leer proces trainen met behulp van overboeking Learning](how-to-train-pytorch.md) 
 *  [Tensor flow](https://github.com/tensorflow/tensorflow): [handgeschreven cijfers herkennen met tensor flow](how-to-train-tensorflow.md)
 *  [Keras](https://github.com/keras-team/keras): [een Neural-netwerk bouwen om installatie kopieën te analyseren met Keras](how-to-train-keras.md)

Voor het maken van een diep leer model is vaak veel tijd, gegevens en reken bronnen nodig. U kunt met behulp van overboeking Learning een snelkoppeling maken naar het trainings proces. Overboeking learning is een techniek waarbij kennis wordt toegepast op basis van het oplossen van een probleem naar een ander, maar verwant probleem. Dit betekent dat u een bestaand model kunt maken dat het doel heeft. Raadpleeg het [artikel over uitgebreide kennis en machine learning](concept-deep-learning-vs-machine-learning.md#what-is-transfer-learning) voor meer informatie over overboeking learning.

### <a name="reinforcement-learning-ray-rllib"></a>Educatief leren: Ray RLLib

Educatief leren is een kunst matige intelligentie techniek waarmee modellen worden getraind met behulp van acties, provincies en beloningen: versterking van leer agenten leren om een reeks vooraf gedefinieerde acties uit te voeren die de opgegeven beloningen maximaliseren op basis van de huidige status van hun omgeving. 

Het [Ray RLLib](https://github.com/ray-project/ray) -project heeft een aantal functies waarmee de schaal baarheid tijdens het trainings proces kan worden uitgebreid. Het iteratieve proces is zowel tijd-als resource-intensief als versterking van leer agenten proberen de optimale manier om een taak te bereiken, te ontdekken.  Ray RLLib biedt ook systeem eigen ondersteuning voor diepe leer frameworks zoals tensor flow en PyTorch.  

Zie voor meer informatie over het gebruik van Ray RLLib met Azure Machine Learning de [instructies voor het trainen van een versterkt leer model](how-to-use-reinforcement-learning.md).

### <a name="monitor-model-performance-tensorboard"></a>Model prestaties bewaken: TensorBoard

Voor het trainen van een of meer modellen is de visualisatie en inspectie van de gewenste metrische gegevens vereist om te controleren of het model naar verwachting werkt. U kunt [TensorBoard in azure machine learning gebruiken om experimentele meet waarden bij te houden en te visualiseren](./how-to-monitor-tensorboard.md)

### <a name="frameworks-for-interpretable-and-fair-models"></a>Frameworks voor interpretatieve en eerlijke modellen

Machine learning-systemen worden gebruikt in verschillende regio's van de samenleving, zoals bankieren, onderwijs en gezondheids zorg. Daarom is het belang rijk dat deze systemen verantwoordelijk zijn voor de voor spellingen en aanbevelingen die ze maken om onbedoelde gevolgen te voor komen.

Open source frameworks zoals [InterpretML](https://github.com/interpretml/interpret/) en Fairlearn ( https://github.com/fairlearn/fairlearn) werk samen met Azure machine learning om meer transparante en billijke machine learning modellen te maken.

Raadpleeg de volgende artikelen voor meer informatie over het bouwen van eerlijke en interpretbare modellen:

- [Modelinterpreteerbaarheid in Azure Machine Learning](how-to-machine-learning-interpretability.md)
- [machine learning modellen interpreteren en uitleggen](how-to-machine-learning-interpretability-aml.md)
- [Uitleg over AutoML-modellen](how-to-machine-learning-interpretability-automl.md)
- [Verdeling in machine learning modellen beperken](concept-fairness-ml.md)
- [Azure Machine Learning gebruiken voor activa model verdeling](how-to-machine-learning-fairness-aml.md)

## <a name="model-deployment"></a>Modelimplementatie

Wanneer de modellen zijn getraind en klaar zijn voor productie, moet u kiezen hoe u deze implementeert. Azure Machine Learning biedt verschillende implementatie doelen. Zie voor meer informatie het [artikel waar en hoe u dit implementeert](./how-to-deploy-and-where.md).

### <a name="standardize-model-formats-with-onnx"></a>Model indelingen standaardiseren met ONNX

Na de training wordt de inhoud van het model, zoals geleerd para meters, geserialiseerd en opgeslagen in een bestand. Elk Framework heeft een eigen serialisatie-indeling. Wanneer u werkt met verschillende frameworks en hulpprogram ma's, betekent dit dat u modellen moet implementeren op basis van de vereisten van het Framework. Als u dit proces wilt standaardiseren, kunt u de open Neural Network Exchange (ONNX)-indeling gebruiken. ONNX is een open-source indeling voor kunst matige intelligentie-modellen. ONNX biedt ondersteuning voor interoperabiliteit tussen frameworks. Dit betekent dat u een model kunt trainen in een van de vele populaire machine learning frameworks zoals PyTorch, dit te converteren naar de ONNX-indeling en het ONNX-model in een ander Framework zoals ML.NET te gebruiken.

Raadpleeg de volgende artikelen voor meer informatie over ONNX en het gebruik van ONNX-modellen:

- [ML-modellen maken en versnellen met ONNX](concept-onnx.md)
- [ONNX-modellen gebruiken in .NET-toepassingen](how-to-use-automl-onnx-model-dotnet.md)

### <a name="package-and-deploy-models-as-containers"></a>Modellen inpakken en implementeren als containers

Container technologieën, zoals docker, zijn een manier om modellen te implementeren als webservices. Containers bieden een platform en resource neutraal manier om reproduceer bare software omgevingen te bouwen en te organiseren. Met deze kern technologieën kunt u [vooraf geconfigureerde omgevingen](./how-to-use-environments.md), [vooraf geconfigureerde container installatie kopieën](./how-to-deploy-custom-docker-image.md) of aangepaste gebruiken om uw machine learning modellen te implementeren, zoals [Kubernetes-clusters](./how-to-deploy-azure-kubernetes-service.md?tabs=python). Voor GPU-intensieve werk stromen kunt u gebruikmaken van hulpprogram ma's zoals de NVIDIA Triton-server voor het [afnemen van voor spellingen met behulp van gpu's](how-to-deploy-with-triton.md?tabs=python).

### <a name="secure-deployments-with-homomorphic-encryption"></a>Veilige implementaties met Homomorphic-versleuteling

Het beveiligen van implementaties is een belang rijk onderdeel van het implementatie proces. Gebruik de open-source python-bibliotheek om [versleutelde Services](how-to-homomorphic-encryption-seal.md)voor het afnemen van versleuteling te implementeren `encrypted-inference` . Het `encrypted inferencing` pakket bevat bindingen op basis van [micro soft-zegel](https://github.com/Microsoft/SEAL), een Homomorphic-versleutelings bibliotheek.

## <a name="machine-learning-operations-mlops"></a>Machine Learning bewerkingen (MLOps)

Met Machine Learning Operations (MLOps), dat vaak wordt beschouwd als DevOps voor machine learning, kunt u transparante, robuuste en reproduceer bare machine learning werk stromen bouwen. Zie het [artikel wat is MLOps](./concept-model-management-and-deployment.md) voor meer informatie over MLOps. 

Met behulp van DevOps-procedures zoals doorlopende integratie (CI) en continue implementatie (CD) kunt u de end-to-end-machine learning levenscyclus automatiseren en beheer gegevens hiernaar vastleggen. U kunt uw [machine learning CI/cd-pijp lijn definiëren in github-acties](./how-to-github-actions-machine-learning.md) om Azure machine learning training-en implementatie taken uit te voeren. 

Het vastleggen van software afhankelijkheden, metrische gegevens, meta data en model versie beheer vormt een belang rijk onderdeel van het MLOps-proces om transparante, reproduceer bare en beoordeel bare pijp lijnen te bouwen. Voor deze taak kunt u [MLFlow in azure machine learning gebruiken](how-to-use-mlflow.md) , evenals bij het [trainen van machine learning modellen in azure Databricks](./how-to-use-mlflow-azure-databricks.md). U kunt ook [MLflow-modellen implementeren als een Azure-webservice](how-to-deploy-mlflow-models.md). 
