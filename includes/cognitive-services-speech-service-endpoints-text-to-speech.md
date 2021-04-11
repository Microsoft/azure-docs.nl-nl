---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: 832cc1d4f9ec3cec4ada6e964e3ab2f6f023dd41
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554833"
---
### <a name="neural-and-standard-voices"></a>Neural en standaard stemmen

Gebruik deze tabel om de **Beschik baarheid van Neural en standaard stemmen** per regio/eind punt te bepalen:

| Regio | Eindpunt |
|--------|----------|
| Australië - oost | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` |
| Brazilië - zuid | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/v1` |
| Canada - midden | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` |
| VS - centraal | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Azië - oost | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` |
| VS - oost | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` |
| VS - oost 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` |
| Frankrijk - centraal | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` |
| India - centraal | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` |
| Japan - oost | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` |
| Japan - west | `https://japanwest.tts.speech.microsoft.com/cognitiveservices/v1` |
| Korea - centraal | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` |
| VS - noord-centraal | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Europa - noord | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` |
| VS - zuid-centraal | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Azië - zuidoost | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` |
| Verenigd Koninkrijk Zuid | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` |
| VS - west-centraal | `https://westcentralus.tts.speech.microsoft.com/cognitiveservices/v1` |
| Europa -west | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` |
| VS - west | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` |
| VS - west 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` |

> [!TIP]
> [Stemmen in Preview](../articles/cognitive-services/Speech-Service/language-support.md#neural-voices-in-preview) zijn alleen beschikbaar in de volgende drie REGIO'S: VS-oost, Europa-West en Zuidoost-Azië.

### <a name="custom-voices"></a>Aangepaste stemmen

Als u een aangepast spraak lettertype hebt gemaakt, gebruikt u het eind punt dat u hebt gemaakt. U kunt ook de onderstaande eind punten gebruiken `{deploymentId}` om de implementatie-id voor uw spraak model te vervangen.

| Regio | Eindpunt |
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

| Regio | Eindpunt |
|--------|----------|
| VS - oost | `https://eastus.customvoice.api.speech.microsoft.com` |
| India - centraal | `https://centralindia.customvoice.api.speech.microsoft.com` |
| Azië - zuidoost | `https://southeastasia.customvoice.api.speech.microsoft.com` |
| Verenigd Koninkrijk Zuid | `https://uksouth.customvoice.api.speech.microsoft.com` |
| Europa -west | `https://westeurope.customvoice.api.speech.microsoft.com` |
