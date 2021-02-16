---
title: De activa conversie REST API
description: Hierin wordt beschreven hoe u een Asset converteert via de REST API
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 4f8ac72e2b598a6c7631d691cc1bfb82cdba7599
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530260"
---
# <a name="use-the-model-conversion-rest-api"></a>De REST API voor modelconversie gebruiken

De [model conversie](model-conversion.md) service wordt beheerd via een [rest API](https://en.wikipedia.org/wiki/Representational_state_transfer). Deze API kan worden gebruikt voor het maken van conversies, het ophalen van conversie-eigenschappen en het weer geven van bestaande conversies.

## <a name="rest-api-reference"></a>Naslaginformatie over REST-API

De documentatie voor de externe rendering REST API referentie vindt u [hier](https://docs.microsoft.com/rest/api/mixedreality/2021-01-01preview/remoterendering)en [de definities van](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mixedreality/data-plane/Microsoft.MixedReality)Swagger.

We bieden een Power shell-script in de voor [beelden-opslag plaats](https://github.com/Azure/azure-remote-rendering) in de map *Scripts* , genaamd *Conversion.ps1*, waarin het gebruik van onze service wordt gedemonstreerd. Het script en de bijbehorende configuratie worden hier beschreven: [voor beelden van Power shell-scripts](../../samples/powershell-example-scripts.md). We bieden ook Sdk's voor [.net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/mixedreality/Azure.MixedReality.RemoteRendering), Java en python.

## <a name="next-steps"></a>Volgende stappen

- [Azure Blob Storage gebruiken voor modelconversie](blob-storage.md)
- [Modelconversie](model-conversion.md)
