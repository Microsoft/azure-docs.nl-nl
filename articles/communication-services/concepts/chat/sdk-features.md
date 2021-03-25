---
title: Overzicht van de chat-SDK voor Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Meer informatie over de Azure Communication Services chat SDK.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: b275c3af2e92dc5af677120b5082751d19676b2e
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110811"
---
# <a name="chat-sdk-overview"></a>Overzicht van chat-SDK 

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]    

De Sdk's van Azure Communication Services kunnen worden gebruikt voor het toevoegen van een rijke, realtime chat voor uw toepassingen.
    
## <a name="chat-sdk-capabilities"></a>Chat-SDK-mogelijkheden    

De volgende lijst bevat de set functies die momenteel beschikbaar zijn in de chat-Sdk's voor communicatie Services.  

| Groep van functies | Mogelijkheid | Javascript  | Java | .NET | Python | iOS | Android |
|-----------------|-------------------|---|-----|----|-----|----|----|
| Belangrijkste mogelijkheden | Een chatgesprek maken tussen 2 of meer gebruikers (max. 250 gebruikers)                                                       | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |    
|                   | Het onderwerp van een chatgesprek bijwerken                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Deel nemers toevoegen aan of verwijderen uit een chat-thread                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Kies of u de chat bericht geschiedenis wilt delen met de deel nemer die wordt toegevoegd                                   | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | Een lijst met deel nemers in een chat-thread ophalen                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   | 
|                   | Een chatgesprek verwijderen                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | Bekijk aan de hand van een communicatie gebruiker de lijst met chat-threads waarvan de gebruiker deel uitmaakt                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Informatie ophalen voor een bepaald chatgesprek                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Berichten verzenden en ontvangen in een chatgesprek                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | De inhoud van een verzonden bericht bewerken                                                                                | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Een bericht verwijderen                                                                                                       | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Lees bevestigingen voor berichten die door andere deel nemers zijn gelezen in een chat sessie <br/> *Niet beschikbaar als er meer dan twintig deel nemers zijn in een chat thread*    | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Ontvang een melding wanneer deel nemers een bericht in een chat-thread actief typen <br/> *Niet beschikbaar wanneer een chatgesprek meer dan 20 leden bevat*      | ✔️   | ✔️   | ✔️    | ✔️    |  ✔️    | ✔️   | 
|                   | Alle berichten in een chatgesprek ophalen <br/>                                                                         | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Unicode-emojis verzenden als onderdeel van de bericht inhoud                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|Realtime-Signa lering (ingeschakeld door een specifiek signalerings pakket * *)|  Abonneer u om realtime-updates te ontvangen voor inkomende berichten en andere bewerkingen in uw chat-app. Zie [Chat concepten](concepts.md#real-time-signaling) (Engelstalig) voor een lijst met ondersteunde updates voor realtime-Signa lering                                     | ✔️   | ❌    | ❌  | ❌  | ❌  | ❌  |    
| Ondersteuning voor Event Grid             | Gebruik integratie met Azure Event Grid en configureer uw communicatie service om bedrijfs logica uit te voeren op basis van chat activiteit of om een aangepaste service voor push meldingen te koppelen   | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
| Bewaking        | Gebruik de metrische gegevens van de API-aanvraag die zijn verzonden in de Azure Portal om Dash boards te bouwen, de status van uw chat-app te controleren en waarschuwingen in te stellen voor het detecteren van afwijkingen      | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | Uw communicatie Services-bron configureren voor het ontvangen van operationele logboeken voor het bewaken en diagnosticeren          | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  


* * Het pakket met de eigen signalering wordt geïmplementeerd met behulp van web sockets. Dit leidt tot een lange polling als web sockets niet worden ondersteund.  

## <a name="javascript-chat-sdk-support-by-os-and-browser"></a>Java script chat SDK-ondersteuning per besturings systeem en browser    

De volgende tabel bevat de set van ondersteunde browsers en versies die momenteel beschikbaar zijn.
    
|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    | iPad OS|
|--------------------------------|----------------|--------------|-------|------|------|------|-------|
| **Chat-SDK** | Firefox *, Chrome*, nieuwe Edge | Firefox *, Chrome*, Safari * | Chrome*  | Chrome* | Chrome* | Safari | Safari |

* Naast de vorige twee releases wordt de meest recente versie ondersteund.<br/>   

## <a name="next-steps"></a>Volgende stappen   

> [!div class="nextstepaction"] 
> [Aan de slag met chat](../../quickstarts/chat/get-started.md)    

De volgende documenten zijn mogelijk interessant voor u:  
- Uzelf bekend maken met de [chatconcepten](../chat/concepts.md)