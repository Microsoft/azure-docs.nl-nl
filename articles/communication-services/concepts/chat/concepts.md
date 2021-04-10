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
ms.openlocfilehash: 389d2282812406c50cddf255be2219fa203b0895
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729657"
---
# <a name="chat-concepts"></a>Chatconcepten 

U kunt de Sdk's van Azure Communication Services chat gebruiken om chat teksten in realtime toe te voegen aan uw toepassingen. Op deze pagina vindt u een overzicht van de belangrijkste chatconcepten en mogelijkheden.    

Raadpleeg het [overzicht van de communicatie services Chat-SDK](./sdk-features.md) voor meer informatie over specifieke SDK-talen en-mogelijkheden.  

## <a name="chat-overview"></a>Overzicht van chat    

Chat gesprekken worden uitgevoerd binnen **Chat-threads**. Chat-threads hebben de volgende eigenschappen:

- Een chat thread wordt uniek geïdentificeerd door de `ChatThreadId` . 
- Chat-threads kunnen een of meer gebruikers hebben als deel nemers aan wie berichten kunnen worden verzonden. 
- Een gebruiker kan deel uitmaken van een of meer chat-threads. 
- Alleen de deel nemers van de thread hebben toegang tot een gegeven chat-thread en alleen ze kunnen thread bewerkingen van de chat bewerking uitvoeren. Deze bewerkingen omvatten het verzenden en ontvangen van berichten, het toevoegen van deel nemers en het verwijderen van deel nemers. 
- Gebruikers worden automatisch toegevoegd als een deel nemer aan alle chat-threads die ze maken.

### <a name="user-access"></a>Gebruikerstoegang
De maker van de thread en de deel nemers hebben normaal gesp roken hetzelfde toegangs niveau voor de thread en kunnen alle gerelateerde bewerkingen uitvoeren die beschikbaar zijn in de SDK, inclusief het verwijderen ervan. Deel nemers hebben geen schrijf toegang tot berichten die door andere deel nemers worden verzonden. Dit betekent dat alleen de afzender van het bericht hun verzonden berichten kan bijwerken of verwijderen. Als een andere deel nemer dit probeert te doen, wordt er een fout melding weer geven. 

Als u de toegang tot chat functies voor een set gebruikers wilt beperken, kunt u de toegang configureren als onderdeel van uw vertrouwde service. Uw vertrouwde service is de service die de verificatie en autorisatie van chat deel nemers organiseert. Hieronder vindt u meer informatie.  

### <a name="chat-data"></a>Chat gegevens 
De chat geschiedenis wordt opgeslagen voordat de communicatie Services expliciet worden verwijderd. Deel nemers van de chat-thread kunnen gebruiken `ListMessages` om de bericht geschiedenis voor een bepaalde thread weer te geven. Gebruikers die zijn verwijderd uit een chat-thread, kunnen eerdere bericht geschiedenis bekijken, maar ze kunnen geen nieuwe berichten verzenden of ontvangen als onderdeel van de chat-thread. Een volledig inactieve thread zonder deel nemers wordt na 30 dagen automatisch verwijderd. Raadpleeg de documentatie over [Privacy](../privacy.md)voor meer informatie over de gegevens die worden opgeslagen door communicatie Services.  

### <a name="service-limits"></a>Servicelimieten  
- Het maximum aantal deel nemers dat is toegestaan in een chat thread is 250.   
- De Maxi maal toegestane bericht grootte is ongeveer 28 KB.  
- Voor chat-threads met meer dan 20 deel nemers, worden Lees bevestigingen en type-indicator functies niet ondersteund.    

## <a name="chat-architecture"></a>Chatarchitectuur    

De chatarchitectuur bestaat uit twee belangrijke onderdelen: 1) Vertrouwde service en 2) clienttoepassing.    

:::image type="content" source="../../media/chat-architecture.png" alt-text="Diagram van de chatarchitectuur in Communication Services."::: 

 - **Vertrouwde service:** Als u een chat sessie goed wilt beheren, hebt u een service nodig waarmee u verbinding kunt maken met communicatie Services met behulp van uw resource connection string. Deze service is verantwoordelijk voor het maken van chat-threads, het toevoegen en verwijderen van deel nemers en het verlenen van toegangs tokens aan gebruikers. Meer informatie over toegangstokens vindt u in onze quickstart over [toegangstokens](../../quickstarts/access-tokens.md).  
 - **Client-app:**  De client toepassing maakt verbinding met uw vertrouwde service en ontvangt de toegangs tokens die door gebruikers worden gebruikt om rechtstreeks verbinding te maken met communicatie Services. Zodra de vertrouwde service de chat thread heeft gemaakt en gebruikers heeft toegevoegd als deel nemers, kunnen ze de client-app gebruiken om verbinding te maken met de chat-thread en berichten te verzenden. Gebruik de functie voor realtime meldingen, die hieronder wordt beschreven, in uw client-app om u te abonneren op bericht & thread-updates van andere deel nemers.
    
        
## <a name="message-types"></a>Berichttypen    

Als onderdeel van de bericht geschiedenis deelt chat berichten die door de gebruiker zijn gegenereerd en door het systeem gegenereerde berichten. Systeem berichten worden gegenereerd wanneer een chat thread wordt bijgewerkt en kan helpen bepalen wanneer een deel nemer is toegevoegd of verwijderd of wanneer het onderwerp van de chat thread is bijgewerkt. Wanneer u `List Messages` een chat-thread oproept of aanroept `Get Messages` , bevat het resultaat beide typen berichten in chronologische volg orde.

Voor door de gebruiker gegenereerde berichten kan het bericht type worden ingesteld in `SendMessageOptions` bij het verzenden van een bericht naar een chat thread. Als er geen waarde wordt gegeven, wordt door de communicatie services standaard `text` getypt. Het instellen van deze waarde is belang rijk bij het verzenden van HTML. Wanneer `html` is opgegeven, wordt de inhoud opgeschoond door communicatie Services om ervoor te zorgen dat deze veilig worden weer gegeven op client apparaten.
 - `text`: Een bericht met tekst zonder opmaak dat is samengesteld en verzonden door een gebruiker als onderdeel van een chat-thread. 
 - `html`: Een opgemaakt bericht met HTML, opgesteld en verzonden door een gebruiker als onderdeel van de chat-thread. 

Typen systeem berichten: 
 - `participantAdded`: Systeem bericht dat aangeeft dat een of meer deel nemers zijn toegevoegd aan de chat thread.
 - `participantRemoved`: Systeem bericht dat aangeeft dat een deel nemer is verwijderd uit de chat thread.
 - `topicUpdated`: Systeem bericht dat aangeeft dat het onderwerp van de thread is bijgewerkt.

## <a name="real-time-notifications"></a>Realtime meldingen  

Sommige Sdk's (zoals de Java script chat-SDK) ondersteunen realtime-meldingen. Met deze functie kunnen clients Luis teren naar communicatie Services voor realtime-updates en inkomende berichten naar een chat-thread zonder dat ze de Api's hoeven te pollen. De client-app kan zich abonneren op de volgende gebeurtenissen:
 - `chatMessageReceived` -Wanneer een nieuw bericht door een deel nemer wordt verzonden naar een chat-thread.
 - `chatMessageEdited` -Wanneer een bericht wordt bewerkt in een chat-thread. 
 - `chatMessageDeleted` -Wanneer een bericht wordt verwijderd in een chat-thread.   
 - `typingIndicatorReceived` -Wanneer een andere deel nemer een type-indicator naar de chat thread verzendt.    
 - `readReceiptReceived` -Wanneer een andere deel nemer een lees bevestiging verzendt voor een bericht dat ze hebben gelezen.  
 - `chatThreadCreated` -Wanneer een chat-thread wordt gemaakt door een communicatie Services-gebruiker.    
 - `chatThreadDeleted` -Wanneer een chat-thread wordt verwijderd door een communicatie Services-gebruiker.    
 - `chatThreadPropertiesUpdated` -de eigenschappen van de chat-thread worden bijgewerkt. op dit moment wordt alleen het onderwerp voor de thread bijgewerkt. 
 - `participantsAdded` -Wanneer een gebruiker wordt toegevoegd als een deel nemer van een chat-thread.     
 - `participantsRemoved` -Wanneer een bestaande deel nemer uit de chat-thread wordt verwijderd.

Realtime meldingen kunnen worden gebruikt om een realtime chat ervaring te bieden voor uw gebruikers. Als u push meldingen wilt verzenden voor berichten die zijn gemist door uw gebruikers, terwijl deze afwezig waren, kunnen communicatie services worden geïntegreerd met Azure Event Grid voor het publiceren van chat gerelateerde gebeurtenissen (post-bewerking) die kan worden aangesloten op uw aangepaste app-meldings service. Zie [Server Events](https://docs.microsoft.com/azure/event-grid/event-schema-communication-services?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fcommunication-services%2Ftoc.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)(Engelstalig) voor meer informatie.


## <a name="build-intelligent-ai-powered-chat-experiences"></a>Slimme, AI-chat ervaring bouwen   

U kunt [Azure cognitieve api's](../../../cognitive-services/index.yml) met de chat SDK gebruiken om use cases als volgt te bouwen:

- Gebruikers in staat stellen om met elkaar te chatten in verschillende talen.  
- Help een ondersteunings agent om tickets te priori teren door een negatieve sentiment van een binnenkomend bericht van een klant te detecteren. 
- De inkomende berichten analyseren voor sleuteldetectie en herkenning van entiteiten en relevante informatie aan de gebruiker vragen in uw app op basis van de inhoud van het bericht.

Een manier om dit te doen is door uw vertrouwde service te laten fungeren als een deel nemer van een chat thread. Stel dat u vertalingen wilt inschakelen. Deze service is verantwoordelijk voor het Luis teren naar de berichten die door andere deel nemers worden uitgewisseld [1], waarmee cognitieve Api's worden aangeroepen om de inhoud te vertalen naar de gewenste taal [2, 3] en het vertaalde resultaat te verzenden als een bericht in de chat-thread [4].

Op deze manier bevat de berichtgeschiedenis zowel de oorspronkelijke als de vertaalde berichten. In de clienttoepassing kunt u logica toevoegen om het oorspronkelijke of vertaalde bericht weer te geven. Raadpleeg [deze quickstart](../../../cognitive-services/translator/quickstart-translator.md) om te begrijpen hoe Cognitive-API’s kunnen worden gebruikt om tekst te vertalen naar verschillende talen. 
    
:::image type="content" source="../media/chat/cognitive-services.png" alt-text="Diagram waarin de interactie van Cognitive Services met Communication Services wordt weergegeven."::: 

## <a name="next-steps"></a>Volgende stappen   

> [!div class="nextstepaction"] 
> [Aan de slag met chat](../../quickstarts/chat/get-started.md)    

De volgende documenten zijn mogelijk interessant voor u:  
- Vertrouwd raken met de [Chat-SDK](sdk-features.md)
