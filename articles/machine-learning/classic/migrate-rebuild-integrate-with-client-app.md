---
title: 'ML Studio (klassiek): migreren naar Azure Machine Learning-pijp lijn-eind punten gebruiken'
description: Integreer pijp lijn-eind punten met client toepassingen in Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: bf0624e0667c9fc6998fb28898a3376ca409180d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103565004"
---
# <a name="consume-pipeline-endpoints-from-client-applications"></a>Pijplijn eindpunten van client toepassingen gebruiken

In dit artikel leert u hoe u client toepassingen kunt integreren met Azure Machine Learning-eind punten. Zie [een Azure machine learning-eind punt gebruiken](../how-to-consume-web-service.md)voor meer informatie over het schrijven van toepassings code.

Dit artikel maakt deel uit van de Studio (klassiek) om de migratie reeks te Azure Machine Learning. Zie [het artikel migratie overzicht](migrate-overview.md)voor meer informatie over het migreren naar Azure machine learning.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Een Azure Machine Learning-werkruimte. [Een Azure Machine Learning-werkruimte maken](../how-to-manage-workspace.md#create-a-workspace)
- Een [Azure machine learning realtime-eind punt of-eind punt](migrate-rebuild-web-service.md).


## <a name="consume-a-real-time-endpoint"></a>Een real-time eind punt gebruiken 

Als u uw model hebt geïmplementeerd als een **real-time eind punt**, kunt u het rest-eind punt en de vooraf gegenereerde verbruiks code in C#, python en R vinden:

1. Ga naar Azure Machine Learning Studio ([ml.Azure.com](https://ml.azure.com)).
1. Ga naar het tabblad **eind punten** .
1. Selecteer uw realtime-eind punt.
1. Selecteer **verbruik**.

> [!NOTE]
> U kunt ook de Swagger-specificatie voor uw eind punt vinden op het tabblad **Details** . Gebruik de Swagger-definitie om inzicht te krijgen in uw eindpunt schema. Zie voor meer informatie over Swagger-definitie [Swagger officiële documentatie](https://swagger.io/docs/specification/2-0/what-is-swagger/).


## <a name="consume-a-pipeline-endpoint"></a>Een pijplijn eindpunt gebruiken

Er zijn twee manieren om een pijplijn eindpunt te verbruiken:

- REST API-aanroepen
- Integratie met Azure Data Factory

### <a name="use-rest-api-calls"></a>REST API-aanroepen gebruiken

Roep het REST-eind punt aan bij uw client toepassing. U kunt de Swagger-specificatie voor uw eind punt gebruiken om het schema te begrijpen:

1. Ga naar Azure Machine Learning Studio ([ml.Azure.com](https://ml.azure.com)).
1. Ga naar het tabblad **eind punten** .
1. Selecteer **pijplijn eindpunten**.
1. Selecteer het eind punt van de pijp lijn.
1. Selecteer de koppeling onder **rest endpoint-documentatie** in het deel venster Overzicht van het **eind punt van de pijp lijn** .

### <a name="use-azure-data-factory"></a>Azure Data Factory gebruiken

U kunt uw Azure Machine Learning-pijp lijn aanroepen als een stap in een Azure Data Factory-pijp lijn. Zie [Azure machine learning-pijp lijnen in azure Data Factory uitvoeren](../../data-factory/transform-data-machine-learning-service.md)voor meer informatie.


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u schema en voorbeeld code kunt vinden voor de pijplijn eindpunten. Zie [een Azure machine learning-eind punt gebruiken](../how-to-consume-web-service.md)voor meer informatie over het gebruiken van eind punten van de client toepassing.

Zie de rest van de artikelen in de Azure Machine Learning-migratie reeks: 
1. [Overzicht migratie](migrate-overview.md).
1. [Gegevensset migreren](migrate-register-dataset.md).
1. [Bouw een studio-trainings pijplijn (klassiek) opnieuw](migrate-rebuild-experiment.md)op.
1. [Een studio-webservice (klassiek) opnieuw bouwen](migrate-rebuild-web-service.md).
1. **Een Azure machine learning-webservice integreren met client-apps**.
1. [Execute R-script migreren](migrate-execute-r-script.md).