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
ms.openlocfilehash: 1d40d90f0f64328bbf7103015f52ea4b132d2e51
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729623"
---
# <a name="chat-sdk-overview"></a>Overzicht van chat-SDK 

De Sdk's van Azure Communication Services kunnen worden gebruikt voor het toevoegen van een rijke, realtime chat voor uw toepassingen.
    
## <a name="chat-sdk-capabilities"></a>Chat-SDK-mogelijkheden    

De volgende lijst bevat de set functies die momenteel beschikbaar zijn in de chat-Sdk's voor communicatie Services.  

| Groep van functies | Mogelijkheid | Javascript  | Java | .NET | Python | iOS | Android |
|-----------------|-------------------|---|-----|----|-----|----|----|
| Belangrijkste mogelijkheden | Een chat-thread maken tussen twee of meer gebruikers                                                     | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |    
|                   | Het onderwerp van een chatgesprek bijwerken                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Deel nemers toevoegen aan of verwijderen uit een chat-thread                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Kies of u de chat bericht geschiedenis wilt delen met de deel nemer die wordt toegevoegd                                   | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | Een lijst met deel nemers in een chat-thread ophalen                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   | 
|                   | Een chatgesprek verwijderen                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | Bekijk aan de hand van een communicatie gebruiker de lijst met chat-threads waarvan de gebruiker deel uitmaakt                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Informatie ophalen voor een bepaald chatgesprek                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Berichten verzenden en ontvangen in een chatgesprek                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | De inhoud van het verzonden bericht bijwerken                                                                               | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |    
|                   | Een bericht verwijderen dat u eerder hebt verzonden                                                                                                      | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |    
|                   | Lees bevestigingen voor berichten die door andere deel nemers zijn gelezen in een chat sessie                                        | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Ontvang een melding wanneer deel nemers een bericht in een chat-thread actief typen                                         | ✔️   | ✔️   | ✔️    | ✔️    |  ✔️    | ✔️   | 
|                   | Alle berichten in een chatgesprek ophalen                                                                        | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | Unicode-emojis verzenden als onderdeel van de bericht inhoud                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|Realtime meldingen (ingeschakeld door een specifiek signalerings pakket * *)|  Chat-clients kunnen zich abonneren om realtime updates te ontvangen voor inkomende berichten en andere bewerkingen die in een chat-thread optreden. Zie [Chat concepten](concepts.md#real-time-notifications) (Engelstalig) voor een lijst met ondersteunde updates voor realtime meldingen                                     | ✔️   | ❌    | ❌  | ❌  | ❌  | ❌  | 
| Integratie met Azure Event Grid             | Gebruik de chat gebeurtenissen die beschikbaar zijn in Azure Event Grid om aangepaste meldings services te koppelen of deze gebeurtenis te plaatsen in een webhook om bedrijfs logica uit te voeren, zoals het bijwerken van CRM-records nadat een chat is voltooid   | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
| Rapportages </br>(Deze informatie is beschikbaar op het tabblad controle voor uw communicatie Services-resource op Azure Portal)      | Inzicht in API-verkeer van uw chat-app door de gepubliceerde metrische gegevens in azure Metrics Explorer te controleren en waarschuwingen in te stellen voor het detecteren van afwijkingen     | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | De oplossing voor communicatie Services controleren en fouten opsporen door diagnostische logboek registratie in te scha kelen voor uw resource    | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   


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
- Begrijpen hoe [prijzen](../pricing.md#chat) werken voor chat
