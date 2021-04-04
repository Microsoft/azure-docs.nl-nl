---
title: Afwijkende detector-container uitvoeren in Azure Container Instances
titleSuffix: Azure Cognitive Services
description: Implementeer de afwijkende detector container in een Azure-container exemplaar en test deze in een webbrowser.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: mbullwin
ms.openlocfilehash: 3979bb82bfa055cc2a134bf3119097c452ffb855
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "94364119"
---
# <a name="deploy-an-anomaly-detector-container-to-azure-container-instances"></a>Een anomalie detectie container implementeren naar Azure Container Instances

Meer informatie over het implementeren van de Cognitive Services [anomalie detectie](../anomaly-detector-container-howto.md) container naar Azure [container instances](../../../container-instances/index.yml). In deze procedure wordt het maken van een afwijkende detector-resource gedemonstreerd. Vervolgens bespreken we het verzamelen van de bijbehorende container installatie kopie. Ten slotte markeren we de mogelijkheid om de indeling van de twee uit een browser uit te oefenen. Door gebruik te maken van containers kan de aandacht van de ontwikkel aars van de infra structuur afnemen in plaats van de ontwikkeling van toepassingen te richten.

[!INCLUDE [Prerequisites](../../containers/includes/container-preview-prerequisites.md)]

[!INCLUDE [Create a Cognitive Services Anomaly Detector resource](../includes/create-anomaly-detector-resource.md)]

[!INCLUDE [Create an Anomaly Detector container on Azure Container Instances](../../containers/includes/create-container-instances-resource-from-azure-cli.md)]

[!INCLUDE [API documentation](../../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>Volgende stappen

* Controleer de [installatie-en uitvoer containers](../anomaly-detector-container-configuration.md) voor het ophalen van de container installatie kopie en voer de container uit
* Controleer [Containers configureren](../anomaly-detector-container-configuration.md) voor configuratie-instellingen
* [Meer informatie over de API-service voor anomalie detectie](https://go.microsoft.com/fwlink/?linkid=2080698&clcid=0x409)