---
title: Overzicht van de chat-clientbibliotheek voor Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Meer informatie over de chat-client bibliotheek voor Azure Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 705bd926c2ac6f414464254969b5c511c88891f0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656104"
---
# <a name="chat-client-library-overview"></a>Overzicht van de chat-clientbibliotheek  

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]    

Chat-clientbibliotheken van Azure Communication Services kunnen worden gebruikt om uitgebreide realtime chatmogelijkheden aan uw toepassingen toe te voegen.
    
## <a name="chat-client-library-capabilities"></a>Mogelijkheden van chat-clientbibliotheken 

De volgende lijst bevat de set van functies die momenteel beschikbaar zijn in de Communication Services die chat-clientbibliotheken aanroepen.  

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

## <a name="javascript-chat-client-library-support-by-os-and-browser"></a>Java script chat-client bibliotheek ondersteuning per besturings systeem en browser 

De volgende tabel bevat de set van ondersteunde browsers en versies die momenteel beschikbaar zijn.
    
|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    | iPad OS|
|--------------------------------|----------------|--------------|-------|------|------|------|-------|
| **Chat-client bibliotheek** | Firefox *, Chrome*, nieuwe Edge | Firefox *, Chrome*, Safari * | Chrome*  | Chrome* | Chrome* | Safari | Safari |

* Naast de vorige twee releases wordt de meest recente versie ondersteund.<br/>   

## <a name="next-steps"></a>Volgende stappen   

> [!div class="nextstepaction"] 
> [Aan de slag met chat](../../quickstarts/chat/get-started.md)    

De volgende documenten zijn mogelijk interessant voor u:  
- Uzelf bekend maken met de [chatconcepten](../chat/concepts.md)