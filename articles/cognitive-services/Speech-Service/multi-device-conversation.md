---
title: Conversatie met meerdere apparaten (preview)-spraak service
titleSuffix: Azure Cognitive Services
description: Met het gesprek met meerdere apparaten kunt u eenvoudig een spraak-of tekst conversatie maken tussen meerdere clients en de berichten coördineren die ertussen worden verzonden.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 7b38bc390b28788003c2de757f45e8ecdfd5c89a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932411"
---
# <a name="what-is-multi-device-conversation-preview"></a>Wat is een uitwisseling van meerdere apparaten (preview-versie)?

Met **meerdere apparaten** kunt u eenvoudig een spraak-of tekst conversatie maken tussen meerdere clients en de berichten coördineren die ertussen worden verzonden.

Met een **conversatie met meerdere apparaten** kunt u het volgende doen:

- Verbind meerdere clients met hetzelfde gesprek en beheer het verzenden en ontvangen van berichten ertussen.
- U kunt eenvoudig audio van elke client transcriberen en de transcriptie naar anderen verzenden, met een optionele vertaling.
- Verzend eenvoudig tekst berichten tussen clients, met een optionele vertaling.

U kunt een functie of oplossing bouwen die werkt op een reeks apparaten. Elk apparaat kan onafhankelijk berichten (transcripties aan audio-of chat berichten) naar alle andere apparaten verzenden.

Terwijl de [**conversatie transcriptie**](conversation-transcription.md) werkt op één apparaat met een meerkanaalse microfoon matrix, is **conversatie met meerdere** apparaten geschikt voor scenario's met meerdere apparaten, elk met één microfoon.

>[!IMPORTANT]
> Conversaties met meerdere apparaten bieden **geen** ondersteuning voor het verzenden van audio bestanden tussen clients: alleen de transcriptie en/of de vertaling.

## <a name="key-features"></a>Belangrijke functies

- **Real-time transcriptie** : iedereen ontvangt een transcript van het gesprek, zodat ze de tekst in realtime kunnen volgen of opslaan voor later.
- **Realtime vertaling** : met meer dan 70 [ondersteunde talen](language-support.md#text-languages) voor tekst omzetting kunnen gebruikers de conversatie omzetten naar de gewenste taal (s).
- **Lees bare transcripten** : de transcriptie en de vertaling zijn eenvoudig te volgen, met interpunctie en zin-einden.
- **Stem of tekst invoer** : elke gebruiker kan op hun eigen apparaat spreken of typen, afhankelijk van de taal ondersteuning die is ingeschakeld voor de gekozen taal van de deel nemer. Raadpleeg de [taal ondersteuning](language-support.md#speech-to-text).
- **Bericht relay** : de conversatie service met meerdere apparaten distribueert berichten die door de ene client naar alle andere worden verzonden, in de taal (s) van hun keuze.
- **Bericht identificatie** : elk bericht dat door gebruikers in de conversatie wordt ontvangen, wordt gelabeld met de bijnaam van de gebruiker die het heeft verzonden.

## <a name="use-cases"></a>Gebruiksvoorbeelden

### <a name="lightweight-conversations"></a>Lichte conversaties

Het is eenvoudig om een gesprek te maken en hieraan deel te nemen. Eén gebruiker fungeert als host en maakt een conversatie, waardoor er een wille keurige vijf conversatie code en een QR-code wordt gegenereerd. Alle andere gebruikers kunnen lid worden van de conversatie door de conversatie code in te voeren of door de QR-code te scannen. 

Omdat gebruikers deel nemen aan de conversatie code en niet zijn vereist voor het delen van contact gegevens, is het eenvoudig om snel discussies te maken.

### <a name="inclusive-meetings"></a>Inclusieve vergaderingen

Realtime-transcriptie en-vertalingen kunnen ervoor zorgen dat discussies toegankelijk zijn voor mensen die verschillende talen spreken en/of doof of slechthorend zijn. Elke persoon kan ook actief deel nemen aan de conversatie door de voor keur taal te spreken of chat berichten te verzenden.

### <a name="presentations"></a>Presentaties

U kunt ook bijschriften bieden voor presentaties en lezingen op het scherm en op de eigen apparaten van de doel groep. Nadat de doel groep is gekoppeld aan de conversatie code, kunnen ze de transcripten zien in hun Voorkeurs taal, op hun eigen apparaat.

## <a name="how-it-works"></a>Uitleg

Alle clients gebruiken de Speech SDK om een conversatie te maken of eraan toe te voegen. De Speech SDK communiceert met de service voor meerdere apparaten, die de levens duur van een gesprek beheert, met inbegrip van de lijst met deel nemers, de gekozen taal/talen van elke client en verzonden berichten.  

Elke client kan audio-of chat berichten verzenden. De service maakt gebruik van spraak herkenning om audio naar tekst te converteren en expres berichten te verzenden. Als clients verschillende talen kiezen, worden alle berichten door de service vertaald naar de opgegeven taal/talen van elke client.

![Overzichts diagram van conversatie met meerdere apparaten](media/scenarios/multi-device-conversation.png)

## <a name="overview-of-conversation-host-and-participant"></a>Overzicht van conversatie, host en deel nemer

Een **gesprek** is een sessie die één gebruiker start voor de andere deelnemende gebruikers die u wilt toevoegen. Alle clients maken verbinding met de conversatie met de **conversatie code** van vijf letters.

Elke conversatie maakt meta gegevens die het volgende bevatten:
-    Tijds tempels van wanneer de conversatie is gestart en beëindigd
-    Lijst met alle deel nemers aan de conversatie, waarbij de geselecteerde bijnaam van de gebruiker en de primaire taal voor spraak of tekst invoer wordt opgenomen.


Er zijn twee soorten gebruikers in een gesprek:  **host** en **deel nemer**.

De **host** is de gebruiker die een gesprek start en die fungeert als beheerder van de conversatie.
- Elke conversatie kan slechts één host hebben
- De host moet zijn verbonden met de conversatie voor de duur van de conversatie. Als de host de conversatie verlaat, wordt de conversatie beëindigd voor alle andere deel nemers.
- De host heeft een aantal extra besturings elementen voor het beheren van de conversatie: 
    - Gesprek vergren delen: bijkomende deel nemers mogen niet worden toegevoegd
    - Alle deel nemers dempen: Voorkom dat andere deel nemers berichten verzenden naar de conversatie, ongeacht of deze zijn overtranscriben vanuit spraak-of chat berichten
    - Afzonderlijke deel nemers dempen
    - Alle deel nemers uitschakelen
    - Afzonderlijke deel nemers ontheffen

Een **deel nemer** is een gebruiker die deelneemt aan een gesprek.
- Een deel nemer kan op elk gewenst moment op hetzelfde gesprek verlaten en opnieuw deel nemen, zonder het gesprek voor andere deel nemers te beëindigen.
- Deel nemers kunnen de conversatie niet vergren delen of anderen dempen/dempen

> [!NOTE]
> Elke conversatie kan Maxi maal 100 deel nemers hebben, waarvan 10 gelijktijdig op elk gewenst moment kan praten.

## <a name="language-support"></a>Taalondersteuning

Bij het maken of toevoegen van een conversatie moet elke gebruiker een **primaire taal** kiezen: de taal waarin ze worden gepraat en expres berichten verzenden in, en ook de taal waarin de berichten van andere gebruikers worden weer geven.

Er zijn twee soorten talen: alleen **spraak naar tekst** en **tekst**:
- Als de gebruiker een **spraak-naar-tekst** -taal kiest als primaire taal, kunnen de spraak-en tekst invoer in de conversatie worden gebruikt.

- Als de gebruiker een taal gebruikt die **alleen uit tekst** is gekozen, kunnen ze alleen tekst invoer gebruiken en expres berichten verzenden in de conversatie. Alleen-tekst talen zijn de talen die worden ondersteund voor tekst omzetting, maar niet voor spraak naar tekst. U kunt de beschik bare talen zien op de pagina [taal ondersteuning](./language-support.md) .

Naast hun primaire taal kan elke deel nemer ook extra talen opgeven voor het vertalen van de conversatie.

Hieronder volgt een samen vatting van wat de gebruiker kan doen in een conversatie met meerdere apparaten, op basis van de gekozen primaire taal.


| Wat de gebruiker in de conversatie kan doen | Spraak naar tekst | Alleen tekst |
|-----------------------------------|----------------|------|
| Spraak invoer gebruiken | ✔️ | ❌ |
| Chat berichten verzenden | ✔️ | ✔️ |
| De conversatie vertalen | ✔️ | ✔️ |

> [!NOTE]
> Zie [ondersteunde talen](./language-support.md)voor lijsten met beschik bare talen voor spraak naar tekst en tekst vertalingen.



## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Gesp rekken in realtime vertalen](quickstarts/multi-device-conversation.md)