---
title: Werkruimte gegevens exporteren of verwijderen
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u uw werk ruimte exporteert of verwijdert met de Azure Machine Learning Studio, CLI, SDK en geverifieerde REST Api's.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: lobrien
ms.author: laobri
ms.date: 04/24/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 030e09f0bb1514e8d3828da52604d751a2a8a11a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102520079"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Uw Machine Learning service werkruimte gegevens exporteren of verwijderen

In Azure Machine Learning kunt u uw werkruimte gegevens exporteren of verwijderen via de grafische interface van de portal of de python-SDK. In dit artikel worden beide opties beschreven.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>Uw werkruimte gegevens beheren

In-product gegevens die zijn opgeslagen door Azure Machine Learning, kunnen worden geëxporteerd en verwijderd. U kunt met Azure Machine Learning Studio, CLI en SDK exporteren en verwijderen. Telemetriegegevens kunnen worden geopend via de Azure-privacybeleid. 

In Azure Machine Learning bestaan persoonlijke gegevens uit de gebruikers gegevens in de documenten van de uitvoerings geschiedenis. 

## <a name="delete-high-level-resources-using-the-portal"></a>Resources op hoog niveau verwijderen via de portal

Wanneer u een werk ruimte maakt, maakt Azure een aantal resources binnen de resource groep:

- De werk ruimte zelf
- Een opslagaccount
- Een container register
- Een toepassingen Insights-exemplaar
- Een sleutel kluis

Deze resources kunnen worden verwijderd door ze te selecteren in de lijst en **verwijderen** te kiezen. 

:::image type="content" source="media/how-to-export-delete-data/delete-resource-group-resources.png" alt-text="Scherm opname van de portal, met het pictogram verwijderen gemarkeerd":::

Geschiedenis documenten uitvoeren, die persoonlijke gebruikers gegevens kunnen bevatten, worden opgeslagen in het opslag account in Blob Storage, in submappen van `/azureml` . U kunt de gegevens downloaden en verwijderen uit de portal.

:::image type="content" source="media/how-to-export-delete-data/storage-account-folders.png" alt-text="Scherm afbeelding van de azureml-Directory in het opslag account in de portal":::

## <a name="export-and-delete-machine-learning-resources-using-azure-machine-learning-studio"></a>machine learning resources exporteren en verwijderen met behulp van Azure Machine Learning Studio

Azure Machine Learning Studio biedt een uniforme weer gave van uw machine learning resources, zoals notebooks, gegevens sets, modellen en experimenten. Azure Machine Learning Studio benadrukt het behoud van een record van uw gegevens en experimenten. Reken resources zoals pijp lijnen en reken bronnen kunnen worden verwijderd met behulp van de browser. Voor deze resources gaat u naar de betreffende resource en kiest u **verwijderen**. 

Gegevens sets kunnen niet worden geregistreerd en experimenten kunnen worden gearchiveerd, maar deze bewerkingen worden niet verwijderd. Als u de gegevens volledig wilt verwijderen, moeten de gegevens sets en de data-uitvoering worden verwijderd op het opslag niveau. Het verwijderen van het opslag niveau geschiedt met behulp van de portal, zoals eerder is beschreven.

U kunt trainings artefacten downloaden van experimentele uitvoeringen met behulp van Studio. Kies het **experiment** en **Voer** uit waar u geïnteresseerd bent. Kies **uitvoer en logboeken** en navigeer naar de specifieke artefacten die u wilt downloaden. Kies **...** en **down load**.

U kunt een geregistreerd model downloaden door te navigeren naar het gewenste **model** en **downloaden** te kiezen. 

:::image type="contents" source="media/how-to-export-delete-data/model-download.png" alt-text="Scherm afbeelding van Studio model pagina met download optie gemarkeerd":::

## <a name="export-and-delete-resources-using-the-python-sdk"></a>Resources exporteren en verwijderen met behulp van de python-SDK

U kunt de uitvoer van een bepaalde uitvoering downloaden met behulp van: 

```python
# Retrieved from Azure Machine Learning web UI
run_id = 'aaaaaaaa-bbbb-cccc-dddd-0123456789AB'
experiment = ws.experiments['my-experiment']
run = next(run for run in ex.get_runs() if run.id == run_id)
metrics_output_port = run.get_pipeline_output('metrics_output')
model_output_port = run.get_pipeline_output('model_output')

metrics_output_port.download('.', show_progress=True)
model_output_port.download('.', show_progress=True)
```

De volgende machine learning resources kunnen worden verwijderd met behulp van de python-SDK: 

| Type | Functie aanroep | Notities | 
| --- | --- | --- |
| `Workspace` | [`delete`](/python/api/azureml-core/azureml.core.workspace.workspace#delete-delete-dependent-resources-false--no-wait-false-) | Gebruiken `delete-dependent-resources` om de verwijdering te trapsgewijs |
| `Model` | [`delete`](/python/api/azureml-core/azureml.core.model%28class%29#delete--) | | 
| `ComputeTarget` | [`delete`](/python/api/azureml-core/azureml.core.computetarget#delete--) | |
| `WebService` | [`delete`](/python/api/azureml-core/azureml.core.webservice%28class%29) | |