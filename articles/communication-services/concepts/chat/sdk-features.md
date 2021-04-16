---
title: Overzicht van chat-SDK voor Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Meer informatie over de Azure Communication Services Chat SDK.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 561855704d157f9ad826b5db83600a79d9437fc6
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107500682"
---
# <a name="chat-sdk-overview"></a>Overzicht van chat-SDK 

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-chat.md)]

Azure Communication Services Chat-SDK's kunnen worden gebruikt om uitgebreide, realtime chat aan uw toepassingen toe te voegen.
    
## <a name="chat-sdk-capabilities"></a>Chat-SDK-mogelijkheden    

De volgende lijst bevat de set functies die momenteel beschikbaar zijn in de Communication Services chat-SDK's.  

| Groep van functies | Mogelijkheid | Javascript  | Java | .NET | Python | iOS | Android |
|-----------------|-------------------|---|-----|----|-----|----|----|
| Belangrijkste mogelijkheden | Een chatgesprek maken tussen 2 of meer gebruikers                                                     | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |    
|                   | Het onderwerp van een chatgesprek bijwerken                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Deelnemers toevoegen aan of verwijderen uit een chatgesprek                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Kies of u de geschiedenis van chatberichten wilt delen met de deelnemer die wordt toegevoegd                                   | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | Een lijst met deelnemers in een chatgesprek op halen                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   | 
|                   | Een chatgesprek verwijderen                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | Als u een communicatiegebruiker bent, kunt u de lijst met chatthreads op halen waar de gebruiker deel van uitmaakt                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Informatie ophalen voor een bepaald chatgesprek                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Berichten verzenden en ontvangen in een chatgesprek                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | De inhoud van het verzonden bericht bijwerken                                                                               | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |    
|                   | Een bericht verwijderen dat u eerder hebt verzonden                                                                                                      | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |    
|                   | Ontvangstbewijzen lezen voor berichten die zijn gelezen door andere deelnemers in een chatgesprek                                        | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Ontvang een melding wanneer deelnemers actief een bericht in een chatgesprek typen                                         | ✔️   | ✔️   | ✔️    | ✔️    |  ✔️    | ✔️   | 
|                   | Alle berichten in een chatgesprek ophalen                                                                        | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | Unicode-emoji's verzenden als onderdeel van berichtinhoud                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|Realtimemeldingen (ingeschakeld door een eigen signaleringspakket**)|  Chat-clients kunnen zich abonneren om realtime updates te krijgen voor binnenkomende berichten en andere bewerkingen die in een chatgesprek plaatsvinden. Zie Chatconcepten voor een lijst met ondersteunde updates voor realtime [meldingen](concepts.md#real-time-notifications)                                     | ✔️   | ❌    | ❌  | ❌  | ✔️  | ✔️  |   
| Integratie met Azure Event Grid             | Gebruik de chatgebeurtenissen die beschikbaar zijn in Azure Event Grid om aangepaste meldingsservices te plaatsen of om die gebeurtenis te posten op een webhook om bedrijfslogica uit te voeren, zoals het bijwerken van CRM-records nadat een chat is voltooid   | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
| Rapportages </br>(Deze informatie is beschikbaar op het tabblad Bewaking voor uw Communication Services resource op Azure Portal)      | Inzicht in API-verkeer van uw chat-app door de gepubliceerde metrische gegevens in Azure Metrics Explorer en waarschuwingen in te stellen om afwijkingen te detecteren     | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Monitor and debug your Communication Services solution by enabling diagnostic logging for your resource (Uw oplossing bewaken en fouten opsporen door diagnostische logboekregistratie in te stellen voor uw resource)    | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   


**Het eigen signaleringspakket wordt geïmplementeerd met behulp van websockers. Deze wordt terugvallen op lange polling als websockers niet worden ondersteund.  

## <a name="javascript-chat-sdk-support-by-os-and-browser"></a>Ondersteuning voor JavaScript Chat SDK per besturingssysteem en browser    

De volgende tabel bevat de set van ondersteunde browsers en versies die momenteel beschikbaar zijn.
    
|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    | iPad OS|
|--------------------------------|----------------|--------------|-------|------|------|------|-------|
| **Chat-SDK** | Firefox,*Chrome,* nieuwe Edge | Firefox,*Chrome,* Safari* | Chrome*  | Chrome* | Chrome* | Safari* | Safari* |

*Houd er rekening mee dat de nieuwste versie wordt ondersteund naast de vorige twee releases.<br/>   

## <a name="next-steps"></a>Volgende stappen   

> [!div class="nextstepaction"] 
> [Aan de slag met chat](../../quickstarts/chat/get-started.md)    

De volgende documenten zijn mogelijk interessant voor u:  
- Uzelf bekend maken met de [chatconcepten](../chat/concepts.md)
- Inzicht in [de prijzen](../pricing.md#chat) voor chat
