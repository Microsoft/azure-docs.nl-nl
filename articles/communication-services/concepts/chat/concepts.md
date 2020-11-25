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
ms.openlocfilehash: f0e69e3f62d3b9e4debb5761d877dcdfdd246f60
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94886019"
---
# <a name="chat-concepts"></a>Chatconcepten

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Chat-clientbibliotheken van Azure Communication Services kunnen worden gebruikt om realtime tekstchats aan uw toepassingen toe te voegen. Op deze pagina vindt u een overzicht van de belangrijkste chatconcepten en mogelijkheden.

Raadpleeg het [Overzicht van de chat-clientbibliotheek in Communication Services](./sdk-features.md) voor meer informatie over specifieke talen en mogelijkheden van de clientbibliotheek.

## <a name="chat-overview"></a>Overzicht van chat 

Chats vinden plaats in zogenaamde gesprekken. Een chatgesprek kan veel berichten en veel gebruikers bevatten. Elk bericht behoort tot één gesprek en een gebruiker kan deel uitmaken van een of meerdere gesprekken. 

Elke gebruiker in het chatgesprek wordt een lid genoemd. Een chatgesprek kan maximaal 250 leden bevatten. Alleen leden van een gesprek kunnen berichten verzenden en ontvangen of leden toevoegen aan of verwijderen uit een chatgesprek. De maximaal toegestane berichtgrootte is ongeveer 28 KB. U kunt alle berichten in een chatgesprek ophalen met behulp van de `List/Get Messages`-bewerking. Communication Services slaat de chatgeschiedenis op totdat u een verwijderbewerking uitvoert op het chatgesprek of het chatbericht, of totdat er geen leden meer zijn in het chatgesprek dat op dat moment verlaten is en wordt voorbereid op verwijdering.   

Voor chatgesprekken met meer dan 20 leden zijn leesbevestigingen en type-indicaties uitgeschakeld. 

## <a name="chat-architecture"></a>Chatarchitectuur

De chatarchitectuur bestaat uit twee belangrijke onderdelen: 1) Vertrouwde service en 2) clienttoepassing.

:::image type="content" source="../../media/chat-architecture.png" alt-text="Diagram van de chatarchitectuur in Communication Services.":::

 - **Vertrouwde service:** Om een chatsessie goed te beheren, hebt u een service nodig die u helpt om verbinding te maken met Communication Services via uw verbindingsreeks voor de resource. Deze service is verantwoordelijk voor het maken van chatgesprekken, het beheren van lidmaatschappen en het verlenen van toegangstokens aan gebruikers. Meer informatie over toegangstokens vindt u in onze quickstart over [toegangstokens](../../quickstarts/access-tokens.md).

 - **Client-app:**  De clienttoepassing maakt verbinding met uw vertrouwde service en ontvangt de toegangstokens die worden gebruikt om rechtstreeks verbinding te maken met Communication Services. Nadat deze verbinding tot stand is gebracht, kan uw client-app berichten verzenden en ontvangen.
    
## <a name="message-types"></a>Berichttypen

De chat functie van Communication Services deelt door gebruikers gegenereerde berichten, evenals door het systeem gegenereerde berichten; dit worden **gespreksactiviteiten** genoemd. Gespreksactiviteiten worden gegenereerd als een chatgesprek wordt bijgewerkt. Als u `List Messages` of `Get Messages` aanroept in een chatgesprek bevat het resultaat de door de gebruiker gegenereerde tekstberichten en de systeemberichten in chronologische volgorde. Zo kunt u vaststellen wanneer een lid is toegevoegd of verwijderd of wanneer het onderwerp van het chatgesprek is bijgewerkt. Ondersteunde berichttypen zijn:  

 - `Text`: Het daadwerkelijke bericht dat door de gebruiker is samengesteld en verzonden als onderdeel van het chatgesprek. 
 - `ThreadActivity/AddMember`: Systeembericht dat aangeeft dat een of meer leden aan het chatgesprek zijn toegevoegd. Bijvoorbeeld:

```xml

<addmember>
    <eventtime>1598478187549</eventtime>
    <initiator>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</initiator>
    <detailedinitiatorinfo>
        <friendlyName>User 1</friendlyName>
    </detailedinitiatorinfo>
    <rosterVersion>1598478184564</rosterVersion>
    <target>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</target>
    <detailedtargetinfo>
        <id>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</id>
        <friendlyName>User 1</friendlyName>
    </detailedtargetinfo>
    <target>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</target>
    <detailedtargetinfo>
        <id>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</id>
        <friendlyName>User 2</friendlyName>
    </detailedtargetinfo>
</addmember>

```  

- `ThreadActivity/DeleteMember`: Systeembericht dat aangeeft dat een lid is verwijderd uit het chatgesprek. Bijvoorbeeld:

```xml

<deletemember>
    <eventtime>1598478187642</eventtime>
    <initiator>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</initiator>
    <detailedinitiatorinfo>
        <friendlyName>User 1</friendlyName>
    </detailedinitiatorinfo>
    <rosterVersion>1598478184564</rosterVersion>
    <target>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</target>
    <detailedtargetinfo>
        <id>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_8540c0de-899f-5cce-acb5-3ec493af3800</id>
        <friendlyName>User 2</friendlyName>
    </detailedtargetinfo>
</deletemember>

```

- `ThreadActivity/TopicUpdate`: Systeembericht dat aangeeft dat het onderwerp is bijgewerkt. Bijvoorbeeld:

```xml

<topicupdate>
    <eventtime>1598477591811</eventtime>
    <initiator>8:acs:57b9bac9-df6c-4d39-a73b-26e944adf6ea_0e59221d-0c1d-46ae-9544-c963ce56c10b</initiator>
    <value>New topic</value>
</topicupdate>

```

## <a name="real-time-signaling"></a>Signalering in realtime 

De JavaScript-clientbibliotheek voor chat bevat signalering in realtime. Hiermee kunnen clients luisteren naar realtime updates en inkomende berichten in een chatgesprek zonder de API’s te hoeven gebruiken. Beschikbare gebeurtenissen zijn:

 - `ChatMessageReceived` - Als een bericht wordt verzonden naar een chatgesprek waarvan de gebruiker lid is. Deze gebeurtenis wordt niet verzonden voor automatisch gegenereerde systeemberichten die in het vorige onderwerp zijn besproken.  
 - `ChatMessageEdited` - Als een bericht wordt bewerkt in een chatgesprek waarvan de gebruiker lid is. 
 - `ChatMessageDeleted` - Als een bericht wordt verwijderd in een chatgesprek waarvan de gebruiker lid is. 
 - `TypingIndicatorReceived` - Als een ander lid een bericht typt in een chatgesprek waarvan de gebruiker lid is. 
 - `ReadReceiptReceived` - Als een ander lid het bericht heeft gelezen dat de gebruiker heeft verzonden in een chatgesprek. 

## <a name="chat-events"></a>Chatgebeurtenissen 

Met realtime signalering kunnen uw gebruikers in realtime chatten. Uw services kunnen Azure Event Grid gebruiken om u te abonneren op gebeurtenissen die betrekking hebben op chat. Zie [Het concept Gebeurtenisverwerking](../event-handling.md) voor meer informatie.

## <a name="using-cognitive-services-with-chat-client-library-to-enable-intelligent-features"></a>Cognitive Services gebruiken met de chat-clientbibliotheek om intelligente functies in te schakelen

U kunt [Azure Cognitive-API’s](../../../cognitive-services/index.yml) gebruiken met de chat-clientbibliotheek om intelligente functies aan uw toepassingen toe te voegen. U kunt bijvoorbeeld:

- Gebruikers in staat stellen om met elkaar te chatten in verschillende talen. 
- Een ondersteuningsagent helpen om tickets te prioriteren door een negatief sentiment van een inkomende kwestie van een klant te detecteren.
- De inkomende berichten analyseren voor sleuteldetectie en herkenning van entiteiten en relevante informatie aan de gebruiker vragen in uw app op basis van de inhoud van het bericht.

Een manier om dit te doen, is door uw vertrouwde service te laten fungeren als een lid van een chatgesprek. Stel dat u vertalingen wilt inschakelen. Deze service is verantwoordelijk voor het luisteren naar de berichten die worden uitgewisseld door andere leden [1], het aanroepen van Cognitive-API's om de inhoud naar de gewenste taal te vertalen [2,3] en het verzenden van het vertaalde resultaat als bericht in het chatgesprek [4]. 

Op deze manier bevat de berichtgeschiedenis zowel de oorspronkelijke als de vertaalde berichten. In de clienttoepassing kunt u logica toevoegen om het oorspronkelijke of vertaalde bericht weer te geven. Raadpleeg [deze quickstart](../../../cognitive-services/translator/quickstart-translator.md) om te begrijpen hoe Cognitive-API’s kunnen worden gebruikt om tekst te vertalen naar verschillende talen. 

:::image type="content" source="../media/chat/cognitive-services.png" alt-text="Diagram waarin de interactie van Cognitive Services met Communication Services wordt weergegeven.":::

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aan de slag met chat](../../quickstarts/chat/get-started.md)

De volgende documenten zijn mogelijk interessant voor u:

- Uzelf bekend maken met de [chat-clientbibliotheek](sdk-features.md)