---
title: Fouten opsporen tijdens het ontwerpen van een toepassing voor aangepaste opdrachten (preview)
titleSuffix: Azure Cognitive Services
description: In dit artikel leert u hoe u fouten kunt opsporen tijdens het ontwerpen van de toepassing aangepaste opdrachten.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: aeb90e8e064c44f4d17f920261ed58310f0e55f0
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95025698"
---
# <a name="debug-errors-when-authoring-a-custom-commands-application"></a>Fouten opsporen tijdens het ontwerpen van een toepassing voor aangepaste opdrachten

In dit artikel wordt beschreven hoe u fouten oplost tijdens het maken van de toepassing aangepaste opdrachten. 

## <a name="errors-when-creating-an-application"></a>Fouten bij het maken van een toepassing
Aangepaste opdrachten maken ook een toepassing in [Luis](https://www.luis.ai/) bij het maken van een toepassing voor aangepaste opdrachten. 

[Luis beperkt 500-toepassingen per ontwerp bron](../luis/luis-limits.md). Het maken van een LUIS-toepassing kan mislukken als u een ontwerp bron gebruikt die al 500 toepassingen bevat. 

Zorg ervoor dat de geselecteerde LUIS-ontwerp bron minder dan 500 toepassingen bevat. Als dat niet het geval is, kunt u een nieuwe LUIS-ontwerp bron maken, overschakelen naar een andere, of proberen om uw LUIS-toepassingen op te schonen.  

## <a name="errors-when-deleting-an-application"></a>Fouten bij het verwijderen van een toepassing
### <a name="cant-delete-luis-application"></a>Kan de LUIS-toepassing niet verwijderen
Wanneer u een toepassing voor aangepaste opdrachten verwijdert, kunnen aangepaste opdrachten ook proberen om de LUIS-toepassing te verwijderen die is gekoppeld aan de toepassing Custom commands.

Als het verwijderen van een LUIS-toepassing is mislukt, gaat u naar uw [Luis](https://www.luis.ai/) -account om ze hand matig te verwijderen.

### <a name="toomanyrequests"></a>TooManyRequests
Wanneer u een groot aantal toepassingen tegelijk probeert te verwijderen, worden er waarschijnlijk ' TooManyRequests-fouten weer geven. Deze fouten betekenen dat uw verwijderings aanvragen worden beperkt door Azure. 

Vernieuw de pagina en probeer minder toepassingen te verwijderen.

## <a name="errors-when-modifying-an-application"></a>Fouten bij het wijzigen van een toepassing

### <a name="cant-delete-a-parameter-or-a-web-endpoint"></a>Kan geen para meter of Web-eind punt verwijderen
U mag geen para meter verwijderen als deze wordt gebruikt. Verwijder alle verwijzingen van de para meter in alle spraak reacties, voorbeeld zinnen, voor waarden, acties en probeer het opnieuw.

### <a name="cant-delete-a-web-endpoint"></a>Kan geen web-eind punt verwijderen
U mag geen web-eind punt verwijderen wanneer het wordt gebruikt. Verwijder de actie **Webeindpunt** van de oproep die gebruikmaakt van dit webeindpunt voordat u een webeindpunt verwijdert.

## <a name="errors-when-training-an-application"></a>Fouten bij het trainen van een toepassing
### <a name="built-in-intents"></a>Built-In-intentie
LUIS heeft ingebouwde Ja/geen intenties. Als er voorbeeld zinnen met alleen ' ja ' en ' nee ' worden toegevoegd, mislukt de training. 

| Zoek | Variaties | 
| ------- | --------- | 
| Yes | Zeker, OK |
| No | Klopt, niet | 

### <a name="common-sample-sentences"></a>Algemene voorbeeld zinnen
Aangepaste opdrachten staan geen veelvoorkomende voorbeeld zinnen toe die worden gedeeld tussen verschillende opdrachten. De training van een toepassing kan mislukken als sommige voorbeeld zinnen in één opdracht al zijn gedefinieerd in een andere opdracht. 

Zorg ervoor dat er geen veelvoorkomende voorbeeld zinnen worden gedeeld tussen verschillende opdrachten. 

Voor best practice van het verdelen van uw voorbeeld zinnen over verschillende opdrachten, verwijzen wij u naar [LUIS best practice](../luis/luis-concept-best-practices.md).

### <a name="empty-sample-sentences"></a>Lege voorbeeld zinnen
U moet ten minste één voorbeeld zin voor elke opdracht hebben.

### <a name="undefined-parameter-in-sample-sentences"></a>Niet-gedefinieerde para meter in voorbeeld zinnen
In de voorbeeld zinnen worden een of meer para meters gebruikt, maar deze zijn niet gedefinieerd.

### <a name="training-takes-too-long"></a>De training duurt te lang
LUIS training is bedoeld om snel te leren werken met minder voor beelden. Voeg niet te veel voorbeeld zinnen toe. 

Als er veel voorbeeld zinnen zijn die vergelijkbaar zijn, definieert u een para meter, geeft u deze op in een patroon en voegt u deze toe aan voorbeeld zinnen.

U kunt bijvoorbeeld een para meter {tuig} definiëren voor de onderstaande voorbeeld zinnen en alleen een {voer tuig boeken} toevoegen aan voorbeeld zinnen.

| Voorbeeld zinnen | Patroon | 
| ------- | ------- | 
| Een auto boeken | Een {-voer tuig boeken} | 
| Een vlucht boeken | Een {-voer tuig boeken} |
| Een taxi boeken | Een {-voer tuig boeken} |

Raadpleeg [LUIS best practice](../luis/luis-concept-best-practices.md)voor Best Practice van Luis training.

## <a name="cant-update-luis-key"></a>Kan de LUIS-sleutel niet bijwerken
### <a name="reassign-to-e0-authoring-resource"></a>Opnieuw toewijzen aan E0-ontwerp bron
LUIS biedt geen ondersteuning voor het opnieuw toewijzen van LUIS-toepassing aan E0 authoring resource.

Als u uw ontwerp bron wilt wijzigen van F0 in E0 of als u wilt overschakelen naar een andere E0-resource, maakt u de toepassing opnieuw. 

Als u een bestaande toepassing snel wilt exporteren en wilt importeren in een nieuwe toepassing, raadpleegt u [doorlopende implementatie met Azure DevOps](./how-to-custom-commands-deploy-cicd.md).

### <a name="save-button-is-disabled"></a>De knop Opslaan is uitgeschakeld
Als u een LUIS-Voorspellings resource nooit aan uw toepassing toewijst, wordt de knop opslaan uitgeschakeld wanneer u probeert om uw ontwerp bron te wijzigen zonder een Voorspellings bron toe te voegen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Bekijk de voor beelden op GitHub](https://aka.ms/speech/cc-samples)