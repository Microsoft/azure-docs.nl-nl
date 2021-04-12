---
title: Over waarschuwings berichten
description: Selecteer een waarschuwing in het venster waarschuwingen om de details te bekijken.
ms.date: 3/21/2021
ms.topic: how-to
ms.openlocfilehash: 2fa2b265c7d3983ca6ae2d7507392dd37afabd27
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781480"
---
# <a name="about-alert-messages"></a>Over waarschuwings berichten

Selecteer een waarschuwing in het venster **waarschuwingen** om de details van de waarschuwing te bekijken. Details bevatten de volgende informatie:

- Meta gegevens van waarschuwing

- Informatie over verkeer, apparaten en de gebeurtenis

- Koppelingen naar verbonden apparaten in de apparaattoewijzing

- Opmerkingen die zijn gedefinieerd door beveiligings analisten en beheerders

- Aanbevelingen voor het onderzoeken van de gebeurtenis

## <a name="alert-metadata"></a>Meta gegevens van waarschuwing

Details van de waarschuwing bevatten de volgende meta gegevens van de waarschuwing:

  - Waarschuwings-ID

  - Beleids engine die de waarschuwing heeft geactiveerd

  - De datum en tijd waarop de waarschuwing is geactiveerd

:::image type="content" source="media/how-to-work-with-alerts-sensor/internet-connectivity-detection-unauthorized.png" alt-text="Niet-geautoriseerde Internet connectiviteit gedetecteerd.":::

## <a name="information-about-devices-traffic-and-the-event"></a>Informatie over apparaten, verkeer en de gebeurtenis

Het waarschuwings bericht bevat informatie over:

  - De gedetecteerde apparaten.

  - Het verkeer dat is gedetecteerd tussen de apparaten, zoals protocollen en functie codes.

  - Inzicht in de implicaties van de gebeurtenis.

U kunt deze informatie gebruiken om te bepalen hoe u de waarschuwings gebeurtenis wilt beheren.

## <a name="links-to-connected-devices-in-the-device-map"></a>Koppelingen naar verbonden apparaten in de apparaattoewijzing

Voor meer informatie over apparaten die zijn verbonden met de gedetecteerde apparaten, kunt u een installatie kopie van een apparaat selecteren in de waarschuwing en verbonden apparaten weer geven in de kaart.

:::image type="content" source="media/how-to-work-with-alerts-sensor/rcp-operation-failed.png" alt-text="De RCP-bewerking is mislukt.":::

:::image type="content" source="media/how-to-work-with-alerts-sensor/devices-screen-populated.png" alt-text="Scherm afbeelding van apparaten.":::

De kaart filtert op het apparaat dat u hebt geselecteerd, en andere apparaten die hieraan zijn gekoppeld. De kaart geeft ook het dialoog venster **snelle eigenschappen** weer voor de apparaten die zijn gedetecteerd in de waarschuwingen.

## <a name="comments-defined-by-security-analysts-and-administrators"></a>Opmerkingen die zijn gedefinieerd door beveiligings analisten en beheerders 

Waarschuwingen kunnen een lijst met vooraf gedefinieerde opmerkingen bevatten. Opmerkingen kunnen bijvoorbeeld instructies zijn voor het nemen van beperkende acties of namen van personen die contact kunnen opnemen met de gebeurtenis.

Wanneer u een waarschuwings gebeurtenis beheert, kunt u de opmerkingen of opmerkingen kiezen die het beste overeenkomen met de status van de gebeurtenis of de stappen die u hebt genomen om de waarschuwing te onderzoeken.

De geselecteerde opmerkingen worden opgeslagen in het waarschuwings bericht. Het werken met opmerkingen verbetert de communicatie tussen individuen en teams tijdens het onderzoek van een waarschuwings gebeurtenis. Als gevolg hiervan kunnen opmerkingen de reactie tijd van incidenten versnellen.

Een beheerder of security analist definieert opmerkingen vooraf. Geselecteerde opmerkingen worden niet doorgestuurd naar partner systemen die zijn gedefinieerd in de doorstuur regels.

Nadat u de informatie in een waarschuwing hebt bekeken, kunt u verschillende forensische-stappen uitvoeren om u te helpen bij het beheren van de waarschuwings gebeurtenis. Bijvoorbeeld:

- De recente activiteit van het apparaat analyseren (rapport over gegevens analyse). 

- Analyseer andere gebeurtenissen die tegelijkertijd zijn opgetreden (tijd lijn van gebeurtenissen). 

- Analyseer uitgebreid gebeurtenis verkeer (PCAP-bestand).

## <a name="pcap-files"></a>PCAP-bestanden

In sommige gevallen kunt u vanuit het waarschuwings bericht toegang krijgen tot een PCAP-bestand. Dit kan handig zijn als u meer gedetailleerde informatie wilt over het bijbehorende netwerk verkeer.

Als u een PCAP-bestand wilt downloaden, selecteert u :::image type="content" source="media/how-to-work-with-alerts-sensor/download-pcap.png" alt-text="Download pictogram."::: in de rechter bovenhoek van het dialoog venster **waarschuwings Details** .

## <a name="recommendations-for-investigating-an-event"></a>Aanbevelingen voor het onderzoeken van een gebeurtenis 

In het gebied **aanbeveling** van een waarschuwing wordt informatie weer gegeven die u kan helpen bij het beter begrijpen van een gebeurtenis. Lees deze informatie voordat u de waarschuwings gebeurtenis beheert of een actie onderneemt op het apparaat of het netwerk.

## <a name="see-also"></a>Zie ook

[Waarschuwings werk stromen versnellen](how-to-accelerate-alert-incident-response.md)

[De waarschuwingsgebeurtenis beheren](how-to-manage-the-alert-event.md)
