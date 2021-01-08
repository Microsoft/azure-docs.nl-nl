---
title: "&-eind punten voor het publiceren van regio's-LUIS"
description: De regio die in de Azure Portal is opgegeven, is hetzelfde als waar u de LUIS-app publiceert en er wordt een eind punt-URL gegenereerd voor deze regio.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/09/2020
ms.openlocfilehash: bb2aa2954ab4aed5466858226e385155b9da8ec4
ms.sourcegitcommit: 8f0803d3336d8c47654e119f1edd747180fe67aa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97977027"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>Ontwerpen en publiceren van regio's en de bijbehorende sleutels

[!INCLUDE [LUIS Free account](includes/luis-portal-note.md)]

Drie ontwerp regio's worden ondersteund door de bijbehorende LUIS-portals. Als u een LUIS-app naar meer dan één regio wilt publiceren, moet u minimaal één sleutel per regio hebben.

<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>LUIS-ontwerp regio's
Er zijn drie LUIS voor het ontwerpen van portals, gebaseerd op de regio. U moet in één en dezelfde regio maken en publiceren.

|LUIS|Ontwerp gebied|Naam van Azure-regio|
|--|--|--|
|[www.luis.ai][www.luis.ai] |U.S.<br>niet Europa<br>niet Australië| `westus`|
|[au.luis.ai][au.luis.ai] |Australië| `australiaeast`|
|[eu.luis.ai][eu.luis.ai] |Europa|`westeurope`|

Voor het ontwerpen van regio's zijn er [gepaarde failover-regio's](../../best-practices-availability-paired-regions.md).

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>Publicatie regio's en Azure-resources
De app wordt gepubliceerd naar alle regio's die zijn gekoppeld aan de LUIS-resources die in de LUIS-portal zijn toegevoegd. Als u bijvoorbeeld een app die is gemaakt op [www.Luis.ai][www.luis.ai], een Luis-of cognitieve service resource in **westus** maakt en [deze toevoegt aan de app als resource](luis-how-to-azure-subscription.md), wordt de app gepubliceerd in die regio.

## <a name="public-apps"></a>Openbare apps
Een open bare app wordt in alle regio's gepubliceerd, zodat een gebruiker met een op regio gebaseerde LUIS-bron sleutel toegang kan krijgen tot de app in welke regio is gekoppeld aan de resource sleutel.

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>Publicatie regio's zijn gekoppeld aan ontwerp regio's

De ontwerp regio-app kan alleen worden gepubliceerd naar een bijbehorende publicatie regio. Als uw app momenteel deel uitmaakt van de verkeerde ontwerpregio, exporteert u de app en importeert u deze in de juiste ontwerpregio voor de publicatieregio.

LUIS-apps die https://www.luis.ai zijn gemaakt op, kunnen naar alle eind punten worden gepubliceerd, met uitzonde ring van de [Europese](#publishing-to-europe) en [Australische](#publishing-to-australia) regio's.

## <a name="publishing-to-europe"></a>Publiceren naar Europa

Als u wilt publiceren naar de Europese regio's, maakt u alleen LUIS-apps https://eu.luis.ai . Als u ergens anders probeert te publiceren met een sleutel in de regio Europa, wordt er een waarschuwings bericht weer gegeven in LUIS. Gebruik in plaats daarvan https://eu.luis.ai . LUIS-apps die zijn gemaakt in, [https://eu.luis.ai][eu.luis.ai] worden niet automatisch gemigreerd naar andere regio's. Exporteer de LUIS-app en importeer deze vervolgens om deze te migreren.

## <a name="europe-publishing-regions"></a>Europa-publicatie regio's

 Wereld wijde regio | Ontwerpen van API-regio & website ontwerpen| &-query regio publiceren<br>`API region name`   |  Indeling van eind punt-URL   |
|-----|------|------|------|
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Frankrijk - centraal<br>`francecentral`     | `https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Europa](#publishing-to-europe)| `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Europa - noord<br>`northeurope`     | `https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Europa -west<br>`westeurope`    |  `https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| [Europa](#publishing-to-europe) | `westeurope`<br>[eu.luis.ai][eu.luis.ai]| Verenigd Koninkrijk Zuid<br>`uksouth`    |  `https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-australia"></a>Publiceren naar Australië

Als u wilt publiceren naar de Australische regio's, maakt u alleen LUIS-apps https://au.luis.ai . Als u ergens anders probeert te publiceren met een sleutel in de regio Australië, wordt er een waarschuwings bericht weer gegeven in LUIS. Gebruik in plaats daarvan https://au.luis.ai . LUIS-apps die zijn gemaakt in, [https://au.luis.ai][au.luis.ai] worden niet automatisch gemigreerd naar andere regio's. Exporteer de LUIS-app en importeer deze vervolgens om deze te migreren.

## <a name="australia-publishing-regions"></a>Australië-publicatie regio's

 Wereld wijde regio | Ontwerpen van API-regio & website ontwerpen| &-query regio publiceren<br>`API region name`   |  Indeling van eind punt-URL   |
|-----|------|------|------|
| [Australië](#publishing-to-australia) | `australiaeast`<br>[au.luis.ai][au.luis.ai]| Australië - oost<br>`australiaeast`     |  `https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-other-regions"></a>Publiceren naar andere regio's

Als u wilt publiceren naar de andere regio's, maakt u alleen LUIS-apps [https://www.luis.ai](https://www.luis.ai) .

## <a name="other-publishing-regions"></a>Andere publicatie regio's

 Wereld wijde regio | Ontwerpen van API-regio & website ontwerpen| &-query regio publiceren<br>`API region name`   |  Indeling van eind punt-URL   |
|-----|------|------|------|
| Afrika | `westus`<br>[www.luis.ai][www.luis.ai]| Zuid-Afrika - noord<br>`southafricanorth` |  `https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Azië | `westus`<br>[www.luis.ai][www.luis.ai]| India - centraal<br>`centralindia` |  `https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Azië | `westus`<br>[www.luis.ai][www.luis.ai]| Azië - oost<br>`eastasia`     |  `https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Azië | `westus`<br>[www.luis.ai][www.luis.ai]| Japan East<br>`japaneast`     |   `https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Azië | `westus`<br>[www.luis.ai][www.luis.ai]| Japan - west<br>`japanwest`     |   `https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Azië | `westus`<br>[www.luis.ai][www.luis.ai]| Korea - centraal<br>`koreacentral`     |   `https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Azië | `westus`<br>[www.luis.ai][www.luis.ai]| Azië - zuidoost<br>`southeastasia`     |   `https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Azië | `westus`<br>[www.luis.ai][www.luis.ai]| North VAE<br>`northuae`     |   `https://northuae.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Noord-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | Canada - midden<br>`canadacentral`     |   `https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Noord-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | Central US<br>`centralus`     |   `https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Noord-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | VS - oost<br>`eastus`      |  `https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Noord-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | VS - oost 2<br>`eastus2`     |  `https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Noord-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | VS - noord-centraal<br>`northcentralus`  |  `https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Noord-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | South Central US<br>`southcentralus`  |  `https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Noord-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | VS - west-centraal<br>`westcentralus`    |  `https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| Noord-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | VS - west<br>`westus`  |   `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`  |
| Noord-Amerika |`westus`<br>[www.luis.ai][www.luis.ai] | West US 2<br>`westus2`    |  `https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`  |
| Zuid-Amerika | `westus`<br>[www.luis.ai][www.luis.ai] | Brazil South<br>`brazilsouth`    |  `https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="endpoints"></a>Eindpunten

Meer informatie over de [ontwerp-en Voorspellings eindpunten](developer-reference-resource.md).

## <a name="failover-regions"></a>Failover-regio's

Elke regio heeft een secundaire regio waarvoor een failover moet worden uitgevoerd. In Europa wordt een failover uitgevoerd binnen Europa en Australië in Australië.

Voor het ontwerpen van regio's zijn er [gepaarde failover-regio's](../../best-practices-availability-paired-regions.md).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Naslag informatie voor vooraf gemaakte entiteiten](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
 [au.luis.ai]: https://au.luis.ai
 [eu.luis.ai]: https://eu.luis.ai
