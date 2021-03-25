---
title: Chatconcepten in Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Meer informatie over chatconcepten in Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e05bf1df503a13efc8e4ca30b3341216e01e678e
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110828"
---
# <a name="chat-concepts"></a>Chatconcepten 

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]    

U kunt de Sdk's van Azure Communication Services chat gebruiken om chat teksten in realtime toe te voegen aan uw toepassingen. Op deze pagina vindt u een overzicht van de belangrijkste chatconcepten en mogelijkheden.    

Raadpleeg het [overzicht van de communicatie services Chat-SDK](./sdk-features.md) voor meer informatie over specifieke SDK-talen en-mogelijkheden.  

## <a name="chat-overview"></a>Overzicht van chat    

Chats vinden plaats in zogenaamde gesprekken. Een chatgesprek kan veel berichten en veel gebruikers bevatten. Elk bericht behoort tot één gesprek en een gebruiker kan deel uitmaken van een of meerdere gesprekken. Elke gebruiker in de chat-thread heet een deel nemer. Alleen deel nemers aan de thread kunnen berichten verzenden en ontvangen en andere gebruikers toevoegen aan of verwijderen uit een chat-thread. Communicatie services slaat de chat geschiedenis op totdat u een Verwijder bewerking uitvoert op de chat-thread of het bericht, of totdat er geen deel nemers meer zijn in de chat thread. op dat moment is de chat-thread zwevend en in de wachtrij geplaatst voor verwijdering. 
    
## <a name="service-limits"></a>Servicelimieten   

- Het maximum aantal deel nemers dat is toegestaan in een chat thread is 250.   
- De Maxi maal toegestane bericht grootte is ongeveer 28 KB.  
- Voor chat-threads met meer dan 20 deel nemers, worden Lees bevestigingen en type-indicator functies niet ondersteund.    
- 
## <a name="chat-architecture"></a>Chatarchitectuur    

De chatarchitectuur bestaat uit twee belangrijke onderdelen: 1) Vertrouwde service en 2) clienttoepassing.    

:::image type="content" source="../../media/chat-architecture.png" alt-text="Diagram van de chatarchitectuur in Communication Services."::: 

 - **Vertrouwde service:** Als u een chat sessie goed wilt beheren, hebt u een service nodig waarmee u verbinding kunt maken met communicatie Services met behulp van uw resource connection string. Deze service is verantwoordelijk voor het maken van chat-threads, het beheren van thread deelnemers lijsten en het verlenen van toegangs tokens aan gebruikers. Meer informatie over toegangstokens vindt u in onze quickstart over [toegangstokens](../../quickstarts/access-tokens.md).   
 - **Client-app:**  De clienttoepassing maakt verbinding met uw vertrouwde service en ontvangt de toegangstokens die worden gebruikt om rechtstreeks verbinding te maken met Communication Services. Nadat deze verbinding tot stand is gebracht, kan uw client-app berichten verzenden en ontvangen.   
We raden u aan om toegangs tokens te genereren met behulp van de vertrouwde servicelaag. In dit scenario is de server verantwoordelijk voor het maken en beheren van gebruikers en het uitgeven van de tokens.   
        
## <a name="message-types"></a>Berichttypen    

De chat functie van Communication Services deelt door gebruikers gegenereerde berichten, evenals door het systeem gegenereerde berichten; dit worden **gespreksactiviteiten** genoemd. Gespreksactiviteiten worden gegenereerd als een chatgesprek wordt bijgewerkt. Als u `List Messages` of `Get Messages` aanroept in een chatgesprek bevat het resultaat de door de gebruiker gegenereerde tekstberichten en de systeemberichten in chronologische volgorde. Zo kunt u bepalen wanneer een deel nemer is toegevoegd of verwijderd of wanneer het onderwerp van de chat thread is bijgewerkt. Ondersteunde berichttypen zijn:  
    
 - `Text`: Een bericht zonder opmaak dat door een gebruiker is opgesteld en verzonden als onderdeel van een chatgesprek. 
 - `RichText/HTML`: Een bericht met opmaak. Houd er rekening mee dat Communication Services-gebruikers momenteel geen RTF-berichten kunnen verzenden. Dit berichttype wordt ondersteund voor berichten die Teams-gebruikers verzenden naar Communication Services-gebruikers in Teams Interop-scenario's.   
 - `ThreadActivity/ParticipantAdded`: Een systeem bericht dat aangeeft dat een of meer deel nemers zijn toegevoegd aan de chat thread. Bijvoorbeeld: 


``` 
{   
            "id": "1613589626560",  
            "type": "participantAdded", 
            "sequenceId": "7",  
            "version": "1613589626560", 
            "content":  
            {   
                "participants": 
                [   
                    {   
                        "id": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4df6-f40f-343a0d003226",    
                        "displayName": "Jane",  
                        "shareHistoryTime": "1970-01-01T00:00:00Z"  
                    }   
                ],  
                "initiator": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4ce0-f40f-343a0d003224"  
            },  
            "createdOn": "2021-02-17T19:20:26Z" 
        }   
``` 

- `ThreadActivity/ParticipantRemoved`: Systeem bericht dat aangeeft dat een deel nemer is verwijderd uit de chat thread. Bijvoorbeeld:  

``` 
{   
            "id": "1613589627603",  
            "type": "participantRemoved",   
            "sequenceId": "8",  
            "version": "1613589627603", 
            "content":  
            {   
                "participants": 
                [   
                    {   
                        "id": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4df6-f40f-343a0d003226",    
                        "displayName": "Jane",  
                        "shareHistoryTime": "1970-01-01T00:00:00Z"  
                    }   
                ],  
                "initiator": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4ce0-f40f-343a0d003224"  
            },  
            "createdOn": "2021-02-17T19:20:27Z" 
        }   
``` 

- `ThreadActivity/TopicUpdate`: Systeem bericht dat aangeeft dat het onderwerp van de thread is bijgewerkt. Bijvoorbeeld:   
``` 
{   
            "id": "1613589623037",  
            "type": "topicUpdated", 
            "sequenceId": "2",  
            "version": "1613589623037", 
            "content":  
            {   
                "topic": "New topic",   
                "initiator": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4ce0-f40f-343a0d003224"  
            },  
            "createdOn": "2021-02-17T19:20:23Z" 
        }   
``` 

## <a name="real-time-signaling"></a>Signalering in realtime  

De chat-java script SDK bevat real-time signalering. Hiermee kunnen clients luisteren naar realtime updates en inkomende berichten in een chatgesprek zonder de API’s te hoeven gebruiken. Beschikbare gebeurtenissen zijn:

 - `ChatMessageReceived` -Wanneer een nieuw bericht wordt verzonden naar een chat-thread. Deze gebeurtenis wordt niet verzonden voor automatisch gegenereerde systeem berichten die in het vorige onderwerp werden besproken.   
 - `ChatMessageEdited` -Wanneer een bericht wordt bewerkt in een chat-thread. 
 - `ChatMessageDeleted` -Wanneer een bericht wordt verwijderd in een chat-thread.   
 - `TypingIndicatorReceived` -Wanneer een andere deel nemer een bericht in een chat-thread typt.   
 - `ReadReceiptReceived` -Wanneer een andere deel nemer het bericht heeft gelezen dat een gebruiker in een chat-thread heeft verzonden.     
 - `ChatThreadCreated` -Wanneer een chat-thread wordt gemaakt door een communicatie gebruiker. 
 - `ChatThreadDeleted` -Wanneer een chat-thread wordt verwijderd door een communicatie gebruiker. 
 - `ChatThreadPropertiesUpdated` -de eigenschappen van de chat-thread worden bijgewerkt. Momenteel ondersteunen we het onderwerp voor de thread alleen bij te werken.   
 - `ParticipantsAdded` -Wanneer een gebruiker is toegevoegd als deel nemer aan een chat thread.  
 - `ParticipantsRemoved` -Wanneer een bestaande deel nemer uit de chat-thread wordt verwijderd.


## <a name="chat-events"></a>Chatgebeurtenissen  

Met realtime signalering kunnen uw gebruikers in realtime chatten. Uw services kunnen Azure Event Grid gebruiken om u te abonneren op gebeurtenissen die betrekking hebben op chat. Zie [Het concept Gebeurtenisverwerking](https://docs.microsoft.com/azure/event-grid/event-schema-communication-services?tabs=event-grid-event-schema) voor meer informatie.


## <a name="using-cognitive-services-with-chat-sdk-to-enable-intelligent-features"></a>Cognitive Services met chat-SDK gebruiken om intelligente functies in te scha kelen    

U kunt [Azure cognitieve api's](../../../cognitive-services/index.yml) met de chat-SDK gebruiken om intelligente functies aan uw toepassingen toe te voegen. U kunt bijvoorbeeld: 

- Gebruikers in staat stellen om met elkaar te chatten in verschillende talen.  
- Een ondersteuningsagent helpen om tickets te prioriteren door een negatief sentiment van een inkomende kwestie van een klant te detecteren.   
- De inkomende berichten analyseren voor sleuteldetectie en herkenning van entiteiten en relevante informatie aan de gebruiker vragen in uw app op basis van de inhoud van het bericht.

Een manier om dit te doen is door uw vertrouwde service te laten fungeren als een deel nemer van een chat thread. Stel dat u vertalingen wilt inschakelen. Deze service is verantwoordelijk voor het Luis teren naar de berichten die door andere deel nemers worden uitgewisseld [1], waarmee cognitieve Api's worden aangeroepen om de inhoud te vertalen naar de gewenste taal [2, 3] en het vertaalde resultaat te verzenden als een bericht in de chat-thread [4].

Op deze manier bevat de berichtgeschiedenis zowel de oorspronkelijke als de vertaalde berichten. In de clienttoepassing kunt u logica toevoegen om het oorspronkelijke of vertaalde bericht weer te geven. Raadpleeg [deze quickstart](../../../cognitive-services/translator/quickstart-translator.md) om te begrijpen hoe Cognitive-API’s kunnen worden gebruikt om tekst te vertalen naar verschillende talen. 
    
:::image type="content" source="../media/chat/cognitive-services.png" alt-text="Diagram waarin de interactie van Cognitive Services met Communication Services wordt weergegeven."::: 

## <a name="next-steps"></a>Volgende stappen   

> [!div class="nextstepaction"] 
> [Aan de slag met chat](../../quickstarts/chat/get-started.md)    

De volgende documenten zijn mogelijk interessant voor u:  
- Vertrouwd raken met de [Chat-SDK](sdk-features.md)