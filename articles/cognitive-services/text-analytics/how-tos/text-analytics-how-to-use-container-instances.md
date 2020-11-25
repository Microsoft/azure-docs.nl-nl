---
title: Azure Container Instances uitvoeren-Text Analytics
titleSuffix: Azure Cognitive Services
description: Implementeer de tekst analyse-containers in de Azure-container instantie en test deze in een webbrowser.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: be43d04672dcefe368eb4052b4d1a929e25327ab
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009917"
---
# <a name="deploy-a-text-analytics-container-to-azure-container-instances"></a>Een Text Analytics-container implementeren op Azure Container Instances

Meer informatie over het implementeren van de Cognitive Services [Text Analytics][install-and-run-containers] -container in azure [container instances][container-instances]. Met deze procedure wordt het maken van een Text Analytics resource, het maken van een gekoppelde Sentimentanalyse installatie kopie en de mogelijkheid om deze indeling van de twee vanuit een browser uit te kunnen oefenen, exemplifies. Door gebruik te maken van containers kan de aandacht van de ontwikkel aars van de infra structuur afnemen in plaats van de ontwikkeling van toepassingen te richten.

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement gebruiken. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/cognitive-services) aan voordat u begint.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics Containers on Azure Container Instances](../../containers/includes/create-container-instances-resource.md)]

#### <a name="key-phrase-extraction"></a>[Sleuteltermextractie](#tab/keyphrase)

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[Taaldetectie](#tab/language)

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[Sentimentanalyse](#tab/sentiment)

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

#### <a name="text-analytics-for-health"></a>[Text Analytics voor status](#tab/health)

[!INCLUDE [Verify the health container instance](../includes/verify-health-container.md)]

***

## <a name="next-steps"></a>Volgende stappen 

* Gebruik meer [Cognitive Services-containers](../../cognitive-services-container-support.md)
* De [Text Analytics verbonden service](../index.yml) gebruiken

[install-and-run-containers]: ./text-analytics-how-to-install-containers.md
[container-instances]: https://docs.microsoft.com/azure/container-instances