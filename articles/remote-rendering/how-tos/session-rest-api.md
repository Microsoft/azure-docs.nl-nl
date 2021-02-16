---
title: De sessie beheer REST API
description: Hierin wordt beschreven hoe u sessies beheert
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 9eb1bb87792dbc61e7c85dbc20c136499f23f67c
ms.sourcegitcommit: 7ec45b7325e36debadb960bae4cf33164176bc24
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/16/2021
ms.locfileid: "100530209"
---
# <a name="use-the-session-management-rest-api"></a>De REST API voor sessiebeheer gebruiken

Als u de functionaliteit voor externe rendering van Azure wilt gebruiken, moet u een *sessie* maken. Elke sessie komt overeen met een server die wordt toegewezen in azure waarmee een client apparaat verbinding kan maken. Wanneer een apparaat verbinding maakt, worden de aangevraagde gegevens door de server gerenderd en wordt het resultaat als een video stroom beschouwd. Tijdens het maken van de sessie hebt u gekozen voor welk [type server](../reference/vm-sizes.md) u wilt uitvoeren, waarmee de prijzen worden bepaald. Zodra de sessie niet meer nodig is, moet deze worden gestopt. Als deze niet hand matig wordt gestopt, wordt deze automatisch afgesloten wanneer de *lease tijd* van de sessie verloopt.

## <a name="rest-api-reference"></a>Naslaginformatie over REST-API

De REST API [verwijzing vindt u hier en de](https://docs.microsoft.com/rest/api/mixedreality/2021-01-01preview/remoterendering) [definities van](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mixedreality/data-plane/Microsoft.MixedReality)Swagger.
We bieden een Power shell-script in de voor [beelden-opslag plaats](https://github.com/Azure/azure-remote-rendering) in de map *Scripts* , genaamd *RenderingSession.ps1*, waarin het gebruik van onze service wordt gedemonstreerd. Het script en de bijbehorende configuratie worden hier beschreven: [voor beelden van Power shell-scripts](../samples/powershell-example-scripts.md).
We bieden ook Sdk's voor [.net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/mixedreality/Azure.MixedReality.RemoteRendering), Java en python.

> [!IMPORTANT]
> Latentie is een belang rijke factor bij het gebruik van externe rendering. Voor de beste ervaring maakt u sessies in de regio die het dichtst bij u ligt. De [Azure-latentie test](https://www.azurespeed.com/Azure/Latency) kan worden gebruikt om te bepalen welke regio het dichtst bij u ligt.

> [!IMPORTANT]
> Een ARR runtime SDK is nodig voor een client apparaat om verbinding te maken met een weergave sessie. Deze Sdk's zijn beschikbaar in [.net](https://docs.microsoft.com/dotnet/api/microsoft.azure.remoterendering?view=remoterendering) en [C++](https://docs.microsoft.com/cpp/api/remote-rendering/). Naast het maken van een verbinding met de service kunnen deze Sdk's ook worden gebruikt voor het starten en stoppen van sessies.

## <a name="next-steps"></a>Volgende stappen

* [De Azure frontend-Api's gebruiken voor verificatie](frontend-apis.md)
* [PowerShell-voorbeeldscripts](../samples/powershell-example-scripts.md)
