---
title: bestand opnemen
description: bestand opnemen
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 12/11/2020
ms.openlocfilehash: 09dd6e9a9d69797c2c33270d1620e861a052efe2
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505117"
---
Het rekendoel dat u gebruikt als host voor uw model, is van invloed op de kosten en beschikbaarheid van het geïmplementeerde eindpunt. Gebruik deze tabel om een geschikt rekendoel te kiezen.

| Rekendoel | Gebruikt voor | GPU-ondersteuning | FPGA-ondersteuning | Beschrijving |
| ----- | ----- | ----- | ----- | ----- |
| [Lokale&nbsp;web&nbsp;service](../articles/machine-learning/how-to-deploy-local-container-notebook-vm.md) | Testen/fouten opsporen | &nbsp; | &nbsp; | Gebruiken voor testen en problemen oplossen. Hardwareversnelling is afhankelijk van het gebruik van bibliotheken in het lokale systeem.
| [Azure Kubernetes Service (AKS)](../articles/machine-learning/how-to-deploy-azure-kubernetes-service.md) | Realtime deductie |  [Ja](../articles/machine-learning/how-to-deploy-inferencing-gpus.md) (webservice-implementatie) | [Ja](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |Gebruiken voor grootschalige productie-implementaties. Biedt een snelle reactietijd en automatische schaalaanpassing van de geïmplementeerde service. Automatische schaalaanpassing van clusters wordt niet ondersteund via de Azure Machine Learning SDK. Als u de knooppunten in het AKS-cluster wilt wijzigen, gebruikt u de gebruikersinterface voor uw AKS-cluster in de Azure-portal. <br/><br/> Ondersteund in de ontwerpfunctie. |
| [Azure Container Instances](../articles/machine-learning/how-to-deploy-azure-container-instance.md) | Testen of ontwikkelen | &nbsp;  | &nbsp; | Gebruiken voor lage CPU-werkbelastingen waarvoor minder dan 48 GB RAM-geheugen nodig is. <br/><br/> Ondersteund in de ontwerpfunctie. |
| [Azure Machine Learning-rekenclusters](../articles/machine-learning/tutorial-pipeline-batch-scoring-classification.md) | Batch&nbsp;deductie | [Ja](../articles/machine-learning/tutorial-pipeline-batch-scoring-classification.md) (machine learning-pijplijn) | &nbsp;  | Batchscoreberekening uitvoeren op serverloze berekening. Ondersteunt VM's met normale en lage prioriteit. Realtime deductie wordt niet ondersteund.|

> [!NOTE]
> Hoewel GPU voor trainings- en testdoeleinden wordt ondersteund voor rekendoelen als lokale Azure Machine Learning-rekeninstanties en Azure Machine Learning-rekenclusters, wordt het gebruik van GPU voor deductie _bij implementatie als een webservice_ alleen ondersteund in AKS.
>
> Het gebruik van een GPU voor deductie _bij scoreberekening met een machine learning-pijplijn_ wordt alleen ondersteund in Azure Machine Learning Compute.
> 
> Bij het kiezen van een cluster-SKU moet u eerst omhoog schalen en vervolgens uitbreiden. Begin met een machine met 150% van het RAM-geheugen dat voor uw model is vereist, profileer het resultaat en zoek een machine met de prestaties die u nodig hebt. Zodra u dat weet, verhoogt u het aantal machines dat u nodig hebt voor gelijktijdige deductie.

> [!NOTE]
> * Containerinstanties zijn alleen geschikt voor kleine modellen met een grootte van minder dan 1 GB.
> * Gebruik AKS clusters met één knooppunt voor het ontwikkelen en testen van grotere modellen.