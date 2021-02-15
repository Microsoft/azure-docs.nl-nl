---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: 6b16dea3c4f9241133b91b092c90c9056da57de0
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100514994"
---
### <a name="standard-and-neural-voices"></a>Standaard en Neural stemmen

Gebruik deze tabel om de beschik baarheid van de standaard-en Neural stemmen per regio/eind punt te bepalen:

| Region | Eindpunt | Neural stemmen | Standaard stemmen |
|--------|----------|-----------------|---------------|
| Australië - oost | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| Brazilië - zuid | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/v1` | Nee | Ja |
| Canada - midden | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| VS - centraal | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` | Nee | Ja |
| Azië - oost | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Nee | Ja |
| VS - oost | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| VS - oost 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` | Nee | Ja |
| Frankrijk - centraal | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` | Nee | Ja |
| India - centraal | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| Japan - oost | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` | Nee | Ja |
| Japan - west | `https://japanwest.tts.speech.microsoft.com/cognitiveservices/v1` | Nee | Ja |
| Korea - centraal | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Nee | Ja |
| VS - noord-centraal | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Nee | Ja |
| Europa - noord | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Nee | Ja |
| VS - zuid-centraal | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| Azië - zuidoost | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| Verenigd Koninkrijk Zuid | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| Europa -west | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| VS - west | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` | Nee | Ja |
| VS - west 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |

> [!TIP]
> [Stemmen in Preview](../articles/cognitive-services/Speech-Service/language-support.md#neural-voices-in-preview) zijn alleen beschikbaar in de volgende drie REGIO'S: VS-oost, Europa-West en Zuidoost-Azië.

### <a name="custom-voices"></a>Aangepaste stemmen

Als u een aangepast spraak lettertype hebt gemaakt, gebruikt u het eind punt dat u hebt gemaakt. U kunt ook de onderstaande eind punten gebruiken `{deploymentId}` om de implementatie-id voor uw spraak model te vervangen.

| Region | Eindpunt |
|--------|----------|
| Australië - oost | `https://australiaeast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Brazilië - zuid | `https://brazilsouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Canada - midden | `https://canadacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| VS - centraal | `https://centralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Azië - oost | `https://eastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| VS - oost | `https://eastus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| VS - oost 2 | `https://eastus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Frankrijk - centraal | `https://francecentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| India - centraal | `https://centralindia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Japan - oost | `https://japaneast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Japan - west | `https://japanwest.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Korea - centraal | `https://koreacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| VS - noord-centraal | `https://northcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Europa - noord | `https://northeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| VS - zuid-centraal | `https://southcentralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Azië - zuidoost | `https://southeastasia.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Verenigd Koninkrijk Zuid | `https://uksouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Europa -west | `https://westeurope.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| VS - west | `https://westus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| VS - west 2 | `https://westus2.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |

### <a name="custom-neural-voice"></a>Aangepaste Neural-stem

De volgende tabel bevat informatie over regionale ondersteuning voor aangepaste Neural-spraak functies.

| Functie | Ondersteunde regio’s |
|---|---|
| Host van het spraak model | VS-Oost, VS-West 2, Zuid-Centraal VS, Zuidoost-Azië, UK-zuid, Europa-west, Australië-oost |
| Realtime tekens | VS-Oost, VS-West 2, Zuid-Centraal VS, Zuidoost-Azië, UK-zuid, Europa-west, Australië-oost |
| Lange audio tekens | VS-Oost, Europa-west, UK-zuid, Zuidoost-Azië, India, Midden |
| Aangepaste Neural-training | VS-Oost, UK-zuid |

### <a name="long-audio-api"></a>Lange audio-API

De lange audio-API is beschikbaar in meerdere regio's met unieke eind punten.

| Region | Eindpunt |
|--------|----------|
| VS - oost | `https://eastus.customvoice.api.speech.microsoft.com` |
| India - centraal | `https://centralindia.customvoice.api.speech.microsoft.com` |
| Azië - zuidoost | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| Verenigd Koninkrijk Zuid | `https://uksouth.customvoice.api.speech.microsoft.com` |
| Europa -west | `https://westeurope.customvoice.api.speech.microsoft.com` |
