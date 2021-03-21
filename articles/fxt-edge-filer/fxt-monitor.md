---
title: Azure FXT Edge Filer bewaken
description: De hardware-status controleren voor de Azure FXT Edge filer hybride opslag cache
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: how-to
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 86e1d74d5e4ab9f6e799c73bcf0d807d0d874f21
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92219714"
---
# <a name="monitor-azure-fxt-edge-filer-hardware-status"></a>Hardware-status van Azure FXT Edge-bestanden bewaken

Het hybride-opslag cachesysteem van Azure FXT Edge heeft meerdere status lampjes ingebouwd in het chassis, zodat beheerders begrijpen hoe de hardware werkt.

## <a name="system-health-status"></a>Systeem status

Als u de cache bewerkingen op een hoger niveau wilt bewaken, gebruikt u de **dash board** -pagina van het configuratie scherm, zoals beschreven in de [dash board-hand leiding van het configuratie scherm](https://azure.github.io/Avere/legacy/dashboard/4_7/html/ops_dashboard_index.html)

## <a name="hardware-status-leds"></a>Led's voor hardware-status

In deze sectie worden de verschillende status lampjes beschreven die zijn ingebouwd in de FXT edge-hardware van Azure.

### <a name="hard-drive-status-leds"></a>Led's voor de status van harde schijven

![afbeelding van harde schijf vooraan, horizon taal, met labels voor bijschriften 2 (linkerbovenhoek), 1 (linkerbenedenhoek) en 3 (rechts)](media/fxt-monitor/fxt-drive-callouts.png)

Elke schijf provider heeft twee status-Led's: een activiteit indicator (1) en een status indicator (2).

* De activiteit heeft geleid tot een lamp (1) wanneer het station in gebruik is.
* De LED van de status (2) geeft de voor waarde aan van het station met behulp van de codes in de onderstaande tabel.

| Status van LED voor station              | Betekenis  |
|-------------------------------------|----------------------------------------------------------|
| Knippert groen twee keer per seconde      | Station identificeren *of* <br> Station voorbereiden voor verwijdering  |
| Uit (Unlit)                         | Het opstarten van het systeem is niet voltooid *of* <br>Station kan worden verwijderd |
| Knippert groen, geel en uit       | Fout met station is voor speld   |
| Knippert geel vier keer per seconde | Station is mislukt   |
| Effen groen                         | Station is online |

De rechter kant van het station (3) wordt aangeduid met de capaciteit van het station en andere informatie.

Stations worden afgedrukt op de ruimte tussen stations. In de Azure FXT Edge-Bestands server is station 0 het meest linkse station en Station 1 direct onder. Nummering blijft in dat patroon.

![Foto van een Bay van een harde schijf in het FXT-chassis, met stations en capaciteits labels](media/fxt-drives-photo.png)

## <a name="left-control-panel"></a>Control Panel links

Het besturings paneel aan de linkerkant heeft verschillende Indica tors voor de status van LED (1) en een zeer licht gelightde systeem status indicator (2).

![deel venster links status, met 1 Label status indicatoren aan de linkerkant en 2 labelen het lampje van de grote systeem status aan de rechter kant](media/fxt-monitor/fxt-control-panel-left.jpg)

### <a name="control-panel-status-indicators"></a>Status indicatoren van het configuratie scherm

De status indicatoren geven een effen, geel licht weer als er een fout is opgetreden in dat systeem. In de volgende tabel worden mogelijke oorzaken en oplossingen voor de fouten beschreven.

[Neem contact op met de ondersteuning](fxt-support-ticket.md) voor hulp als u de fout nog steeds hebt na het uitvoeren van deze oplossingen.

| Pictogram | Beschrijving | Foutvoorwaarde | Mogelijke oplossingen |
|----------------|---------------|--------------------|----------------------|
| ![pictogram station](media/fxt-monitor/fxt-hd-icon.jpg) | Status van station | Fout met station | Controleer het systeem gebeurtenis logboek om te bepalen of het station een fout heeft of <br>Voer de juiste test voor online diagnostische gegevens uit. Start het systeem opnieuw op en voer Inge sloten diagnostische gegevens uit (ePSA) of <br>Als de stations zijn geconfigureerd in een RAID-matrix, start u het systeem opnieuw en voert u het hulp programma voor configuratie van de hostadapter in |
|![pictogram temperatuur](media/fxt-monitor/fxt-temp-icon.jpg) | Temperatuur status | Thermische fout: een ventilator is bijvoorbeeld mislukt of de omgevings temperatuur ligt buiten het bereik | Controleer op de volgende adresseer bare voor waarden: <br>Een ventilator ontbreekt of is mislukt <br>De cover van het systeem, de lucht Shroud, de geheugen module leeg of het haakje voor de achtergrond vullen worden verwijderd <br>De omgevings temperatuur is te hoog <br>Externe lucht stroom is belemmerd |
|![elektriciteits pictogram](media/fxt-monitor/fxt-electric-icon.jpg) | Elektrische status | Elektrische fout: er is bijvoorbeeld een spanning van het bereik, een mislukte PSU of een mislukte spannings regelaar |  Controleer het systeem gebeurtenis logboek of de systeem berichten op het specifieke probleem. Als er een probleem is met de PSU, controleert u de LED voor PSU-status en maakt u de PSU zo nodig opnieuw. |
|![pictogram geheugen](media/fxt-monitor/fxt-memory-icon.jpg) | Geheugen status | Geheugen fout | Controleer het systeem gebeurtenis logboek of systeem berichten voor de locatie van het geheugen dat is mislukt. plaats de geheugen module opnieuw. |
|![Pictogram PCIe](media/fxt-monitor/fxt-pcie-icon.jpg) | PCIe-status | Fout met PCIe-kaart | Start het systeem opnieuw op. Stuur Programma's voor PCIe-kaarten bijwerken; Installeer de kaart opnieuw |

### <a name="system-health-status-indicator"></a>Status indicator van systeem status

Met de grote lit-knop aan de rechter kant van het besturings paneel links geeft u de algehele systeem status aan en wordt deze ook gebruikt als een eenheid voor unit-Locator in de modus systeem-ID.

Druk op de knop systeem status en-ID om te scha kelen tussen de modus systeem-ID en de modus systeem status.

|Status van de systeem status | Voorwaarde |
|-------------------------------------------|-----------------------------------------------|
| Effen blauw | Normale werking: het systeem is ingeschakeld, werkt normaal en de modus systeem-ID is niet actief. <br/>Druk op de knop systeem status en-ID als u wilt overschakelen naar de modus systeem-ID. |
| Blauwe Knipper | De modus systeem-ID is actief. Druk op de knop systeem status en systeem-ID als u wilt overschakelen naar de modus systeem status. |
| Effen, geel | Het systeem bevindt zich in de veilige modus. [Neem contact op met de klanten service en ondersteuning van micro soft](fxt-support-ticket.md)als het probleem zich blijft voordoen. |
| Knipperend, geel | Systeem fout. Raadpleeg het systeem gebeurtenis logboek voor specifieke fout berichten. Voor informatie over de gebeurtenis-en fout berichten die zijn gegenereerd door de systeem firmware en agents die systeem onderdelen bewaken, raadpleegt u de zoek pagina voor fout codes op qrl.dell.com. |
