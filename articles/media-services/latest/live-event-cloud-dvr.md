---
title: Gebruik tijd verschuivingen en live uitvoer om video weergave op aanvraag te maken
description: In dit artikel wordt beschreven hoe u gebruik kunt maken van tijd verschuivingen en live uitvoer om live streams vast te leggen en op aanvraag af te spelen.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: f5b475f15a5dc751de154965e3d1e78a85fbd4c9
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897863"
---
# <a name="use-time-shifting-and-live-outputs-to-create-on-demand-video-playback"></a>Gebruik tijd verschuivingen en live uitvoer om video weergave op aanvraag te maken

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In Azure Media Services is een [Live uitvoer](/rest/api/media/liveoutputs) object net als een digitale video recorder waarmee uw live stream wordt onderschept en opgenomen in een asset in uw Media Services-account. De opgenomen inhoud wordt opgeslagen in de container die is gedefinieerd door de bron van de [Asset](/rest/api/media/assets) (de container bevindt zich in het Azure Storage-account dat aan uw account is gekoppeld). Met de live uitvoer kunt u ook enkele eigenschappen van de uitgaande Live Stream beheren, zoals hoeveel van de stroom wordt bewaard in de registratie van het archief (bijvoorbeeld de capaciteit van de Cloud-DVR) of wanneer viewers kunnen beginnen met het bekijken van de live stream. Het archief op schijf is een circulair Archief ' venster ' dat alleen de hoeveelheid inhoud bevat die is opgegeven in de eigenschap **archiveWindowLength** van de actieve uitvoer. Inhoud die buiten dit venster valt, wordt automatisch verwijderd uit de opslag container en kan niet worden hersteld. De waarde archiveWindowLength vertegenwoordigt een duur van ISO-8601 time span (bijvoorbeeld PTHH: MM: SS), waarmee de capaciteit van het DVR wordt opgegeven. De waarde kan worden ingesteld van Mini maal één minuut tot Maxi maal 25 uur.

De relatie tussen een live gebeurtenis en de live uitvoer is vergelijkbaar met de traditionele TV-uitzending, in dat een kanaal (live event) een constante stroom video en een opname (live-uitvoer) is gericht op een specifiek tijds segment (bijvoorbeeld avond nieuws van 6:17.30 tot 7:13:00). Zodra u de stroom naar de live gebeurtenis hebt gestroomd, kunt u de streaming-gebeurtenis starten door een Asset, live output en streaming-Locator te maken. Live uitvoer archiveert de stream en maakt deze beschikbaar voor gebruikers via het [streaming-eind punt](/rest/api/media/streamingendpoints). U kunt meerdere Live outputs (Maxi maal drie) maken voor een live-gebeurtenis met verschillende archief lengten en-instellingen. Zie de sectie [algemene stappen](live-streaming-overview.md#general-steps) voor informatie over de werk stroom voor live streamen.

## <a name="using-a-dvr-during-an-event"></a>Een DVR gebruiken tijdens een gebeurtenis

In deze sectie wordt beschreven hoe u een DVR tijdens een gebeurtenis kunt gebruiken om te bepalen welke gedeelten van de stroom beschikbaar zijn voor terugspoelen.

De `archiveWindowLength` waarde bepaalt hoe ver terug in de tijd die een kijker van de huidige Live positie kan gaan. De `archiveWindowLength` waarde bepaalt ook hoe lang de client manifesten kunnen groeien.

Stel dat u een voetbal wedstrijd wilt streamen en dat deze `ArchiveWindowLength` slechts 30 minuten lang is. Een viewer die begint met het volgen van uw gebeurtenis 45 minuten nadat het spel is gestart, kan naar Maxi maal 15 minuten teruggaan. Uw Live outputs voor het spel worden voortgezet totdat de live-gebeurtenis is gestopt. Inhoud die buiten archiveWindowLength valt, wordt voortdurend verwijderd uit de opslag en kan niet worden hersteld. In dit voor beeld zou de video tussen het begin van de gebeurtenis en de markering van 15 minuten zijn verwijderd uit uw DVR en uit de container in Blob Storage voor de Asset. Het archief kan niet worden hersteld en wordt verwijderd uit de container in Azure Blob Storage.

Een live-gebeurtenis ondersteunt Maxi maal drie gelijktijdig actieve live-uitvoer (u kunt Maxi maal 3 opnamen/archieven tegelijk maken vanuit één live stream). Deze ondersteuning stelt u in staat om verschillende onderdelen van een gebeurtenis naar behoefte te publiceren en te archiveren. Stel dat u een 24x7 live lineaire feed moet uitzenden en "opnamen" van de verschillende Program ma's gedurende de hele dag wilt maken om klanten als inhoud op aanvraag te bieden voor het weer geven van inzoomen. Voor dit scenario maakt u eerst een primaire live uitvoer met een kort archief venster van 1 uur of minder. Dit is de primaire Live Stream die uw viewers zouden afstemmen. U maakt een streaming-Locator voor deze live uitvoer en publiceert deze naar uw app of website als een live-feed. Terwijl de live-gebeurtenis wordt uitgevoerd, kunt u programmatisch een tweede gelijktijdige live-uitvoer maken aan het begin van een programma (of 5 minuten om een aantal ingangen te verkorten). Deze tweede live-uitvoer kan 5 minuten na afloop van het programma worden verwijderd. Met deze tweede Asset kunt u een nieuwe streaming-Locator maken om dit programma te publiceren als een Asset op aanvraag in de catalogus van uw app. U kunt dit proces meerdere keren herhalen voor andere programma grenzen of hooglichten die u wilt delen als Video's op aanvraag, allemaal terwijl de live-feed van de eerste live uitvoer de lineaire feed blijft uitzenden.

## <a name="creating-an-archive-for-on-demand-playback"></a>Een archief maken voor het afspelen op aanvraag

De Asset waarin de live uitvoer wordt gearchiveerd, wordt automatisch een Asset op aanvraag, wanneer de live uitvoer wordt verwijderd. U moet alle Live outputs verwijderen voordat een live gebeurtenis kan worden gestopt. U kunt een optionele vlag [removeOutputsOnStop](/rest/api/media/liveevents/stop#request-body) gebruiken om live-uitvoer automatisch te verwijderen bij het stoppen.

Zelfs nadat u de gebeurtenis hebt gestopt en verwijderd, kunnen gebruikers de gearchiveerde inhoud als een video op aanvraag streamen, mits u de Asset niet verwijdert. Een activum mag niet worden verwijderd als het wordt gebruikt door een gebeurtenis; de gebeurtenis moet eerst worden verwijderd.

Als u het activum van uw live uitvoer hebt gepubliceerd met behulp van een streaming-Locator, blijft de live-gebeurtenis (tot de lengte van het DVR-venster) zichtbaar totdat het verloop of de verwijdering van de streaming-Locator, afhankelijk van wat het eerste komt.

Zie voor meer informatie:

- [Overzicht van live streamen](live-streaming-overview.md)
- [Zelf studie over live streamen](stream-live-tutorial-with-api.md)

> [!NOTE]
> Wanneer u de live uitvoer verwijdert, worden de onderliggende activa en inhoud in de Asset niet verwijderd.

## <a name="next-steps"></a>Volgende stappen

* [Uw Video's subfragmenten](subclip-video-rest-howto.md).
* [Definieer filters voor uw activa](filters-dynamic-manifest-rest-howto.md).
