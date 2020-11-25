---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 05/06/2019
ms.author: wolfma
ms.openlocfilehash: 650ce0cc9586118b30593767c6a3ddb92f494ac3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95972642"
---
### <a name="standard-and-neural-voices"></a>Standaard en Neural stemmen

Gebruik deze tabel om de beschik baarheid van de standaard-en Neural stemmen per regio/eind punt te bepalen:

| Regio | Eindpunt | Standaard stemmen | Neural stemmen |
|--------|----------|-----------------|---------------|
| Australië - oost | `https://australiaeast.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| Brazil South | `https://brazilsouth.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nee |
| Canada - midden | `https://canadacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| Central US | `https://centralus.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nee |
| Azië - oost | `https://eastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nee |
| VS - oost | `https://eastus.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| VS - oost 2 | `https://eastus2.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nee |
| Frankrijk - centraal | `https://francecentral.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nee |
| India - centraal | `https://centralindia.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| Japan - oost | `https://japaneast.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nee |
| Japan - west | `https://japanwest.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nee |
| Korea - centraal | `https://koreacentral.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nee |
| VS - noord-centraal | `https://northcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nee |
| Europa - noord | `https://northeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nee |
| VS - zuid-centraal | `https://southcentralus.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| Azië - zuidoost | `https://southeastasia.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| Verenigd Koninkrijk Zuid | `https://uksouth.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| Europa -west | `https://westeurope.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |
| VS - west | `https://westus.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Nee |
| VS - west 2 | `https://westus2.tts.speech.microsoft.com/cognitiveservices/v1` | Ja | Ja |

> [!TIP]
> [Stemmen in Preview](../articles/cognitive-services/Speech-Service/language-support.md#neural-voices-in-preview) zijn alleen beschikbaar in de volgende drie REGIO'S: VS-oost, Europa-West en Zuidoost-Azië.

### <a name="custom-voices"></a>Aangepaste stemmen

Als u een aangepast spraak lettertype hebt gemaakt, gebruikt u het eind punt dat u hebt gemaakt. U kunt ook de onderstaande eind punten gebruiken `{deploymentId}` om de implementatie-id voor uw spraak model te vervangen.

| Regio | Eindpunt |
|--------|----------|
| Australië - oost | `https://australiaeast.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Brazilië - zuid | `https://brazilsouth.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Canada - midden | `https://canadacentral.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
| Central US | `https://centralus.voice.speech.microsoft.com/cognitiveservices/v1?deploymentId={deploymentId}` |
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
