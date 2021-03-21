---
title: "&-eind punten voor het publiceren van regio's-LUIS"
description: De regio die in de Azure Portal is opgegeven, is hetzelfde als waar u de LUIS-app publiceert en er wordt een eind punt-URL gegenereerd voor deze regio.
ms.service: cognitive-services
ms.subservice: language-understanding
author: aahill
ms.author: aahi
ms.topic: reference
ms.date: 01/21/2021
ms.custom: references_regions
ms.openlocfilehash: 8b43fc472f3247a93414a0b18d9098c6dfb94917
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98681604"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Ontwerpen en publiceren van regio's en de bijbehorende sleutels

LUIS-ontwerp regio's worden ondersteund door de LUIS-Portal. Als u een LUIS-app naar meer dan één regio wilt publiceren, moet u minimaal één sleutel per regio hebben.

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>LUIS-ontwerp regio's

[!INCLUDE [portal consolidation](includes/portal-consolidation.md)]

LUIS heeft een portal die u kunt gebruiken, ongeacht de regio, [www.Luis.ai](https://www.luis.ai). U moet nog steeds in dezelfde regio ontwerpen en publiceren.

Het ontwerp van regio's heeft [gekoppelde failover-regio's](../../best-practices-availability-paired-regions.md)

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Publicatie regio's en Azure-resources

De app wordt gepubliceerd naar alle regio's die zijn gekoppeld aan de LUIS-resources die in de LUIS-portal zijn toegevoegd. Als u bijvoorbeeld een app die is gemaakt op [www.Luis.ai][www.luis.ai], een Luis-of cognitieve service resource in **westus** maakt en [deze toevoegt aan de app als resource](luis-how-to-azure-subscription.md), wordt de app gepubliceerd in die regio.

## <a name="public-apps"></a>Openbare apps
Een open bare app wordt in alle regio's gepubliceerd, zodat een gebruiker met een op regio gebaseerde LUIS-bron sleutel toegang kan krijgen tot de app in welke regio is gekoppeld aan de resource sleutel.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Publicatie regio's zijn gekoppeld aan ontwerp regio's

De ontwerp regio-app kan alleen worden gepubliceerd naar een bijbehorende publicatie regio. Als uw app momenteel deel uitmaakt van de verkeerde ontwerpregio, exporteert u de app en importeert u deze in de juiste ontwerpregio voor de publicatieregio.

> [!NOTE]
> LUIS-apps die zijn gemaakt op, https://www.luis.ai kunnen nu worden gepubliceerd op alle eind punten, met inbegrip van de [Europese](#publishing-to-europe) en [Australische](#publishing-to-australia) regio's.

## <a name="publishing-to-europe"></a>Publiceren naar Europa

 Wereld wijde regio | Ontwerp-API-regio | &-query regio publiceren<br>`API region name`   |  Indeling van eind punt-URL   |
|-----|------|------|------|
| Europa | `westeurope`| Frankrijk - centraal<br>`francecentral`     | `https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Europa | `westeurope`| Europa - noord<br>`northeurope`     | `https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Europa | `westeurope`| Europa -west<br>`westeurope`    |  `https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Europa | `westeurope`| Verenigd Koninkrijk Zuid<br>`uksouth`    |  `https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-australia"></a>Publiceren naar Australië

 Wereld wijde regio | Ontwerp-API-regio | &-query regio publiceren<br>`API region name`   |  Indeling van eind punt-URL   |
|-----|------|------|------|
| Australië | `australiaeast` | Australië - oost<br>`australiaeast`     |  `https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="other-publishing-regions"></a>Andere publicatie regio's

 Wereld wijde regio | Ontwerp-API-regio | &-query regio publiceren<br>`API region name`   |  Indeling van eind punt-URL   |
|-----|------|------|------|
| Afrika | `westus`<br>[www.luis.ai][www.luis.ai]| Zuid-Afrika - noord<br>`southafricanorth` |  `https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Azië | `westus`<br>[www.luis.ai][www.luis.ai]| India - centraal<br>`centralindia` |  `https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Azië | `westus`<br>[www.luis.ai][www.luis.ai]| Azië - oost<br>`eastasia`     |  `https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Azië | `westus`<br>[www.luis.ai][www.luis.ai]| Japan - oost<br>`japaneast`     |   `https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Azië | `westus`<br>[www.luis.ai][www.luis.ai]| Japan - west<br>`japanwest`     |   `https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Azië | `westus`<br>[www.luis.ai][www.luis.ai]| Korea - centraal<br>`koreacentral`     |   `https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Azië | `westus`<br>[www.luis.ai][www.luis.ai]| Azië - zuidoost<br>`southeastasia`     |   `https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Azië | `westus`<br>[www.luis.ai][www.luis.ai]| North VAE<br>`northuae`     |   `https://northuae.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Noord-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | Canada - midden<br>`canadacentral`     |   `https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Noord-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | VS - centraal<br>`centralus`     |   `https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Noord-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | VS - oost<br>`eastus`      |  `https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Noord-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | VS - oost 2<br>`eastus2`     |  `https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Noord-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | VS - noord-centraal<br>`northcentralus`  |  `https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Noord-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | VS - zuid-centraal<br>`southcentralus`  |  `https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Noord-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | VS - west-centraal<br>`westcentralus`    |  `https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Noord-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | VS - west<br>`westus`  |   `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Noord-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | VS - west 2<br>`westus2`    |  `https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| Zuid-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | Brazilië - zuid<br>`brazilsouth`    |  `https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |

## <a name="endpoints"></a>Eindpunten

Meer informatie over de [ontwerp-en Voorspellings eindpunten](developer-reference-resource.md).

## <a name="failover-regions"></a>Failover-regio's

Elke regio heeft een secundaire regio waarvoor een failover moet worden uitgevoerd. In Europa wordt een failover uitgevoerd binnen Europa en Australië in Australië.

Voor het ontwerpen van regio's zijn er [gepaarde failover-regio's](../../best-practices-availability-paired-regions.md).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Naslag informatie voor vooraf gemaakte entiteiten](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai