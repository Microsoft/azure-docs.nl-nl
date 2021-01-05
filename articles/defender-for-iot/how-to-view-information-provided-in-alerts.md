---
title: Informatie in waarschuwingen weer geven
description: Selecteer een waarschuwing in het venster waarschuwingen om de details te bekijken.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/03/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 804cdbd6266f2e77b5562d914bac089fce80f645
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2020
ms.locfileid: "97840651"
---
# <a name="view-information-in-alerts"></a>Informatie in waarschuwingen weer geven

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

## <a name="see-also"></a>Zie tevens

[Waarschuwings werk stromen versnellen](how-to-accelerate-alert-incident-response.md)

[De waarschuwings gebeurtenis beheren](how-to-manage-the-alert-event.md)
