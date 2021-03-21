---
title: Prestatie overwegingen voor Azure NetApp Files | Microsoft Docs
description: Meer informatie over prestaties voor Azure NetApp Files, waaronder de relatie tussen quota en doorvoer limieten en hoe u volume quota dynamisch kunt verhogen of verlagen.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2021
ms.author: b-juche
ms.openlocfilehash: f963c87148c08a4855befc5afb79d9c5ea0f4481
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101713385"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Prestatie-overwegingen voor Azure NetApp Files

De [doorvoer limiet](azure-netapp-files-service-levels.md) voor een volume met automatische QoS wordt bepaald door een combi natie van het quotum dat is toegewezen aan het volume en het geselecteerde service niveau. Voor volumes met hand matige QoS, kan de doorvoer limiet afzonderlijk worden gedefinieerd. Wanneer u prestatie plannen maakt over Azure NetApp Files, moet u rekening houden met verschillende overwegingen. 

## <a name="quota-and-throughput"></a>Quota en door Voer  

De doorvoer limiet is slechts één determinant van de werkelijke prestaties die worden gerealiseerd.  

Typische overwegingen voor opslag prestaties, waaronder lees-en schrijf mix, de overdrachts grootte, wille keurige of sequentiële patronen en veel andere factoren dragen bij aan de totale prestaties die worden geleverd.  

De maximale empirische door Voer die is waargenomen in tests is 4.500 MiB/s.  Bij de laag Premium-opslag wordt door een automatische QoS-volume quotum van 70,31 TiB een doorvoer limiet ingericht die hoog genoeg is om dit prestatie niveau te verzorgen.  

Als u in het geval van automatische QoS-volumes overweegt om volume quotum bedragen toe te wijzen groter dan 70,31 TiB, kan extra quotum worden toegewezen aan een volume voor het opslaan van aanvullende gegevens. Het toegevoegde quotum resulteert echter niet in een verdere toename van de werkelijke door voer.  

Hetzelfde empirische doorvoer plafond is van toepassing op volumes met hand matige QoS. De maximale door Voer kan worden toegewezen aan een volume is 4.500 MiB/s.

## <a name="automatic-qos-volume-quota-and-throughput"></a>Automatische QoS-volume quota en-door Voer

In deze sectie wordt het quotum beheer en de door Voer voor volumes met het automatische QoS-type beschreven.

### <a name="overprovisioning-the-volume-quota"></a>Het volume quotum overtreffen

Als de prestaties van een werk belasting afhankelijk zijn van de door Voer limiet, is het mogelijk om de automatische QoS-volume quota te verwijderen en zo een hoger doorvoer niveau in te stellen en hogere prestaties te leveren.  

Als bijvoorbeeld een automatische QoS-volume in de laag voor Premium-opslag slechts 500 GiB aan gegevens bevat, maar 128 MiB/s van door Voer vereist is, kunt u het quotum instellen op 2 TiB, zodat het doorvoer niveau dienovereenkomstig wordt ingesteld (64 MiB/s per TB * 2 TiB = 128 MiB/s).  

Als u een volume voor een hogere door Voer consistent overneemt, kunt u overwegen de hand matige QoS-volumes te gebruiken of een hoger service niveau te gebruiken.  In het bovenstaande voor beeld kunt u dezelfde doorvoer limiet bereikt met de helft van het automatische QoS-volume quotum door gebruik te maken van de ultra Storage-laag, in plaats daarvan (128 MiB/s per TiB * 1 TiB = 128 MiB/s).

### <a name="dynamically-increasing-or-decreasing-volume-quota"></a>Volume quota dynamisch verg Roten of verkleinen

Als uw prestatie vereisten tijdelijk zijn, of als u de prestatie behoeften voor een bepaalde periode hebt verbeterd, kunt u het volume quotum dynamisch verg Roten of verkleinen om de doorvoer limiet direct aan te passen.  Houd rekening met de volgende overwegingen: 

* Volume quota kunnen worden verg root of verkleind zonder dat er IO hoeft te worden onderbroken en de toegang tot het volume wordt niet onderbroken of beïnvloed.  

    U kunt het quotum aanpassen tijdens een actieve I/O-trans actie op basis van een volume.  Houd er rekening mee dat volume quota nooit kleiner kunnen zijn dan de hoeveelheid logische gegevens die in het volume worden opgeslagen.

* Wanneer het volume quotum wordt gewijzigd, is de overeenkomstige wijziging in de doorvoer limiet bijna onmiddellijk. 

    De wijziging wordt niet onderbroken of heeft geen invloed op de toegang tot het volume of I/O.  

* Het aanpassen van het volume quotum kan een wijziging in de capaciteits pool grootte vereisen.  

    De grootte van de capaciteits pool kan dynamisch worden aangepast en zonder invloed op de beschik baarheid van volumes of I/O.

## <a name="manual-qos-volume-quota-and-throughput"></a>Hand matige QoS-volume quota en-door Voer 

Als u hand matige QoS-volumes gebruikt, hoeft u het volume quotum niet te verwijderen om een hogere door voer te krijgen, omdat de door Voer afzonderlijk aan elk volume kan worden toegewezen. U moet er echter wel voor zorgen dat de capaciteits pool vooraf is ingericht met voldoende door Voer voor uw prestatie behoeften. De door Voer van een capaciteits pool wordt ingericht op basis van de grootte en het service niveau. Zie [service niveaus voor Azure NetApp files](azure-netapp-files-service-levels.md) voor meer informatie.


## <a name="next-steps"></a>Volgende stappen

- [Prestatie Calculator Azure NetApp Files](https://cloud.netapp.com/azure-netapp-files/tco?hs_preview=tIKQbfoF-41214739590)
- [Serviceniveau's voor Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Prestatiebenchmarks voor Linux](performance-benchmarks-linux.md)
