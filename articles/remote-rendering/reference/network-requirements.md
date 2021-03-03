---
title: Netwerkvereisten
description: Netwerk vereisten en aanbevolen netwerk procedures voor optimale ervaring
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: reference
ms.openlocfilehash: fd8686cc396d5fcee20590fbac8bccaf187b024d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101735944"
---
# <a name="network-requirements"></a>Netwerkvereisten

Een stabiele netwerk verbinding met lage latentie met een Azure-Data Center is essentieel voor een goede gebruikers ervaring in azure-rendering op afstand. Slechte netwerk omstandigheden kunnen leiden tot neergezette verbindingen, onstabiele, schokkende of ' springende ' hologrammen en merkbaar vertraging bij het bijwerken van de scène grafiek aan de server zijde.

## <a name="guidelines-for-network-connectivity"></a>Richt lijnen voor netwerk connectiviteit

De exacte netwerk vereisten zijn afhankelijk van uw specifieke use-case, zoals het aantal en de frequentie van wijzigingen in het externe scène diagram en de complexiteit van de weer gave, maar er zijn een aantal richt lijnen om ervoor te zorgen dat uw ervaring zo goed mogelijk is:

* Uw Internet connectiviteit moet ten minste **40 Mbps downstream** en **5 Mbps upstream** consistent ondersteunen voor één gebruikers sessie van Azure remote rendering, ervan uitgaande dat er geen concurrerend verkeer op het netwerk is. We raden hogere tarieven aan voor betere ervaring. 
* **Wi-Fi** is het aanbevolen netwerk type omdat het een lage latentie, hoge band breedte en stabiele verbinding ondersteunt. Sommige mobiele netwerken introduceren een jitter die kan leiden tot een slechte ervaring. 
* Het gebruik van de **5-gigahertz Wi-Fi-band** levert doorgaans betere resultaten op dan de Wi-Fi-band van 2,4 GHz, hoewel beide moeten werken.
* Als er andere Wi-Fi netwerken in de buurt zijn, vermijd dan het gebruik van Wi-Fi-kanalen die door deze andere netwerken worden gebruikt. U kunt de hulpprogram ma's voor netwerk scans zoals [WifiInfoView](https://www.nirsoft.net/utils/wifi_information_view.html) gebruiken om te controleren of de kanalen die uw Wi-Fi netwerk gebruikt, geen concurrerend verkeer zijn.
* **Vermijd het gebruik van Wi-Fi herhalingen** of LAN-over-via-netwerken door sturen strikt.
* **Vermijd concurrerend band breedte-intensief verkeer** , zoals video-of game streaming, op hetzelfde Wi-Fi netwerk.
* Als u meerdere apparaten op hetzelfde toegangs punt hebt, worden de vereisten dienovereenkomstig geschaald. Als u meerdere toegangs punten in een omgeving hebt, moet u de taken verdelen over de toegangs punten, zodat ze gelijkmatig worden gedistribueerd.
* Een **goede Wi-Fi signaal sterkte** is essentieel. Als dat mogelijk is, houdt u de Wi-Fi toegangs punt in de buurt en vermijdt u obstakels tussen uw client apparaat en de toegangs punten.
* Zorg ervoor dat u altijd verbinding maakt met het **dichtstbijzijnde Azure Data Center** voor uw [regio](regions.md). Dichter bij het Data Center, de lagere netwerk latentie, die een enorme invloed heeft op de stabiliteit van de hologram.

> [!NOTE]
> De downstream-band breedte wordt doorgaans gebruikt door de video stroom, die op zijn beurt is verdeeld over kleur-en diepte gegevens (beide 60 Hz, stereo).

## <a name="network-performance-tests"></a>Netwerk prestatie testen

Als u wilt weten of de kwaliteit van uw netwerk connectiviteit voldoende is voor het uitvoeren van de externe rendering van Azure, zijn er bestaande online hulpprogram ma's die u kunt gebruiken. We raden u ten zeerste aan om deze online-hulpprogram ma's uit te voeren vanaf een redelijkerwijs krachtige laptop die is verbonden met dezelfde Wi-Fi als het apparaat waarop u uw Azure-client toepassing voor externe rendering wilt uitvoeren. Resultaten die zijn verkregen van het uitvoeren van de tests op een mobiele telefoon of HoloLens2 zijn doorgaans minder nuttig, omdat ze hebben bewezen dat belang rijke verschillen worden weer gegeven op eindpunt apparaten met een laag vermogen. De locatie waar u de laptop plaatst, moet ongeveer gelijk zijn aan de plaats waar u het apparaat verwacht dat uw Azure-client toepassing voor externe rendering uitvoert.

Hier volgen enkele eenvoudige stappen voor een snelle test van uw netwerk verbinding:

1. **Voer een hulp programma voor netwerk tests uit, zoals www.speedtest.net, om gegevens op te halen over de totale latentie en de upstream/downstream-band breedte van uw netwerk verbinding.**
Kies een server die het dichtst bij u ligt en voer de test uit. Hoewel de server niet het Azure-Data Center is waarmee de externe rendering van Azure wordt gemaakt, zijn de resulterende gegevens nog steeds nuttig om de prestaties van uw Internet verbinding en Wi-Fi te begrijpen.
   * **Minimale vereiste** voor Azure Remote Rendering: ong. 40 Mbps downstream en 5 Mbps upstream.
   * **Aanbevolen** voor Azure Remote Rendering: ong. 100 Mbps downstream en 10 Mbps upstream.
Het is raadzaam om de test meerdere keren uit te voeren en de slechtste resultaten te nemen.
1. **Gebruik een hulp programma zoals www.azurespeed.com dat latentie meet naar Azure-data centers**. Selecteer het Azure-data centrum dat wordt ondersteund door de externe rendering van Azure die het dichtst bij u ligt (Zie [ondersteunde regio's](regions.md)) en voer een **latentie test** uit. Als er variatie is in de getallen die u ziet, geeft u de resultaten enige tijd om te stabiliseren.
   * **Minimum vereiste** voor Azure-rendering op afstand: de latentie moet consequent kleiner zijn dan 80 MS.
   * **Aanbevolen** voor externe rendering van Azure: de latentie moet consequent kleiner zijn dan 40 MS.

Hoewel lage latentie geen garantie is dat de externe rendering van Azure goed werkt op uw netwerk, hebben we normaal gesp roken problemen gezien in situaties waarin deze tests worden geslaagd.
Raadpleeg de [hand leiding](../resources/troubleshoot.md)voor het oplossen van problemen als u artefacten ondervindt zoals onstabiel, jitter of het springen van hologrammen wanneer u Azure remote rendering uitvoert.

## <a name="next-steps"></a>Volgende stappen

* [Snelstart: Een model weergeven met Unity](../quickstarts/render-model.md)
