---
title: Toegankelijkheid van Azure Media Player
description: Meer informatie over de toegankelijkheidsinstellingen van Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 2231c2969bbfce1668002ad4f5f719e0b8e13de5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "81726555"
---
# <a name="accessibility"></a>Toegankelijkheid #

Azure Media Player werkt met mogelijkheden voor schermlezers, zoals Verteller in Windows en VoiceOver in Apple OSX/iOS. Er zijn alternatieve tags beschikbaar voor de gebruikersinterfaceknoppen en de schermlezer kan deze alternatieve tags lezen wanneer de gebruiker ernaar navigeert. Aanvullende configuraties kunnen worden ingesteld op het niveau van het besturingssysteem.

## <a name="captions-and-subtitles"></a>Bijschriften en ondertitels ##

Op dit moment ondersteunt Azure Media Player ondertiteling voor gebeurtenissen op aanvraag met de WebVTT-indeling en voor live gebeurtenissen met behulp van CEA-708. De TTML-indeling wordt momenteel niet ondersteund. Met ondertitels en bijschriften kan de speler een breder publiek te bereiken, waaronder mensen met een gehoorbeperking of personen die de video in een andere taal willen volgen. Ondertiteling verhoogt ook de betrokkenheid bij de video, verbetert het begrip ervan en maakt het mogelijk om de video te bekijken op plekken waar geluid niet kan worden afgespeeld, zoals een werkplek.  

## <a name="high-contrast-mode"></a>De modus Hoog contrast ##

De standaardgebruikersinterface in Azure Media Player is compatibel met de weergavemodi voor hoog contrast van de meeste apparaten/browsers. Configuraties kunnen worden ingesteld op het niveau van het besturingssysteem.

## <a name="mobility-options"></a>Opties voor mobiliteit ##

### <a name="tabbing-focus"></a>Focus voor tabbladen ###

Focus voor tabbladen, die door algemene HTML-standaarden wordt verschaft, is beschikbaar in Azure Media Player. Als u focus voor tabbladen wilt inschakelen, moet u `tabindex=0` (of een andere waarde als u begrijpt hoe de volgorde op het tabblad wordt beïnvloed in HTML) toevoegen aan de HTML `<video>`, en wel als volgt: `<video ... tabindex=0>...</video>`. Op sommige platforms is de focus voor de besturingselementen alleen aanwezig als de besturingselementen zichtbaar zijn en als het platform deze mogelijkheden ondersteunt.

Zodra focus voor tabbladen is ingeschakeld, kan de eindgebruiker de videospeler effectief bedienen en erin navigeren zonder de muis. U kunt naar elk contextmenu of besturingselement navigeren met behulp van de tabtoets en vervolgens op Enter of de spatiebalk drukken om de optie te gebruiken. Wanneer u in een contextmenu op Enter of de spatiebalk drukt, wordt het menu uitgevouwen zodat de eindgebruiker door kan gaan met de tabtoets om een menuopdracht te selecteren. Zodra u de context hebt van het item dat u wilt selecteren, drukt u opnieuw op Enter of de spatiebalk om de selectie te voltooien.

### <a name="hotkeys"></a>Sneltoetsen ###

Azure Media Player ondersteunt besturing via sneltoetsen. In een webbrowser kan het onderliggende video-element alleen worden bestuurd als de focus zich bevindt op de speler. Zodra de focus zich op de speler bevindt, kunnen de functies van de speler worden beheerd met sneltoetsen.  De volgende tabel beschrijft de verschillende sneltoetsen en het bijbehorende werking:

| Sneltoets              | Gedrag                                                                |
|----------------------|-------------------------------------------------------------------------|
| F/f                  | Modus volledig scherm inschakelen/uitschakelen voor de speler                                  |
| M/m                  | Volume van de speler dempen/dempen opheffen                                          |
| Pijl omhoog en omlaag    | Volume van de speler verhogen/verlagen                                    |
| Pijl naar links en rechts | Voortgang van de video versnellen/vertragen                                  |
| 0,1,2,3,4,5,6,7,8,9  | De voortgang van de video wordt 0%\-90%, afhankelijk van de ingedrukte toets |
| Actie bij klikken         | Video wordt afgespeeld/onderbroken                                                   |

## <a name="next-steps"></a>Volgende stappen

<!---Some context for the following links goes here--->
- [Quickstart voor Azure Media Player](azure-media-player-quickstart.md)