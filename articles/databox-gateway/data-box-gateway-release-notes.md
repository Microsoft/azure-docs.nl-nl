---
title: Azure Data Box Gateway algemene Beschik baarheid release opmerkingen | Microsoft Docs
description: Hierin worden essentiële openstaande problemen en oplossingen beschreven voor het Azure Data Box Gateway uitvoeren van algemene Beschik baarheid.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 11/11/2020
ms.author: alkohli
ms.openlocfilehash: 2984f7990b9570c5ec57633de7f7e50162fb6f46
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96582319"
---
# <a name="azure-data-box-edgeazure-data-box-gateway-general-availability-release-notes"></a>Opmerkingen bij de release van Azure Data Box Edge/Azure Data Box Gateway algemene Beschik baarheid

## <a name="overview"></a>Overzicht

De volgende opmerkingen bij de release identificeren de kritieke openstaande problemen en de opgeloste problemen voor de algemene Beschik baarheid (GA) voor Azure Data Box Edge en Azure Data Box Gateway. 

De release opmerkingen worden voortdurend bijgewerkt en als er kritieke problemen worden gedetecteerd die een tijdelijke oplossing vereisen, worden deze toegevoegd. Lees de informatie in de release opmerkingen aandachtig door voordat u uw Data Box Edge/Data Box Gateway implementeert.

De GA-release komt overeen met de software versies:

- **Data Box Gateway 1903 (1.5.814.447)**
- **Data Box Edge 1903 (1.5.814.447)**


## <a name="whats-new"></a>Nieuwe functies

- **Nieuwe installatie kopieën van virtuele schijven** : nieuwe VHDX en VMDK zijn nu beschikbaar in de Azure Portal. Down load deze installatie kopieën om nieuwe Data Box Gateway GA-apparaten in te richten, te configureren en te implementeren. De Data Box Gateway-apparaten die zijn gemaakt in de eerdere Preview-versies, kunnen niet worden bijgewerkt naar deze versie. Ga voor meer informatie naar [Voorbereiding voor implementatie van Azure Data Box Gateway](data-box-gateway-deploy-prep.md).
- **Ondersteuning voor NFS** : NFS-ondersteuning is momenteel beschikbaar in de preview-versie van v 3.0 en v 4.1-clients die toegang hebben tot de Data Box Edge en data Box gateway apparaten.
- **Tolerantie van opslag** : uw data Box edge-apparaat kan een storing van één gegevens schijf met de functie voor opslag tolerantie hebben. Deze functie is momenteel beschikbaar als preview-product. U kunt de tolerantie voor opslag inschakelen door de optie voor **robuuste** toegang te selecteren in de **opslag instellingen** in de lokale webgebruikersinterface.


## <a name="known-issues-in-ga-release"></a>Bekende problemen in GA release

De volgende tabel bevat een overzicht van bekende problemen voor de Data Box Gateway uitvoering van release.

| Nee. | Functie | Probleem | Tijdelijke oplossing/opmerkingen |
| --- | --- | --- | --- |
| **1.** |Bestands typen | De volgende bestands typen worden niet ondersteund: teken bestanden, blok keren bestanden, sockets, sluizen en symbolische koppelingen.  |Als u deze bestanden kopieert, worden er bestanden met een lengte van 0 gemaakt op de NFS-share. Deze bestanden blijven in een fout status en worden ook gerapporteerd in *error.xml*. <br> Symbolische koppelingen naar directory's leiden ertoe dat directory's nooit offline worden gemarkeerd. Als gevolg hiervan is het mogelijk dat de grijze cross-by-directory's niet worden weer gegeven die aangeven dat de directory's offline zijn en dat alle bijbehorende inhoud volledig is geüpload naar Azure. |
| **2.** |Verwijdering | Als gevolg van een fout in deze release, wordt de share mogelijk niet verwijderd als een NFS-share wordt verwijderd. Bij de status van de share wordt het *verwijderen* weer gegeven.  |Dit gebeurt alleen wanneer de share een niet-ondersteunde bestands naam gebruikt. |
| **3.** |Kopiëren | Het kopiëren van de gegevens is mislukt met de volgende fout: de aangevraagde bewerking kan niet worden voltooid vanwege een beperking van het bestands systeem.  |De alternatieve gegevens stroom (ADS) die is gekoppeld aan de bestands grootte van meer dan 128 KB wordt niet ondersteund.   |


## <a name="next-steps"></a>Volgende stappen

- [Bereid u voor op de implementatie van Azure data Box gateway](data-box-gateway-deploy-prep.md).
- [Bereid u voor op de implementatie van Azure data Box Edge](../databox-online/azure-stack-edge-deploy-prep.md).
