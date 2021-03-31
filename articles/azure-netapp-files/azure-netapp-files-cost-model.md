---
title: Kosten model voor Azure NetApp Files | Microsoft Docs
description: Beschrijft het kosten model voor Azure NetApp Files voor het beheren van onkosten van de service.
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
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 9c4eebae6909c9ef0969bc85bcb9a985db2a7c02
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91325603"
---
# <a name="cost-model-for-azure-netapp-files"></a>Kostenmodel voor Azure NetApp Files 

Wat is het kosten model voor Azure NetApp Files helpt u bij het beheren van uw kosten van de service. 

Zie [kosten model voor replicatie tussen regio's](cross-region-replication-introduction.md#cost-model-for-cross-region-replication)voor een specifiek kosten model voor replicatie tussen regio's.

## <a name="calculation-of-capacity-consumption"></a>Berekening van capaciteits verbruik

Azure NetApp Files wordt in rekening gebracht op de ingerichte opslag capaciteit.  De ingerichte capaciteit wordt toegewezen door het maken van capaciteits groepen.  Capaciteits Pools worden in rekening gebracht op basis van $/provisioned-GiB/month in stappen van elk uur. De minimale grootte voor een groep met één capaciteit is 4 TiB, en capaciteits Pools kunnen vervolgens worden uitgebreid in 1-TiB stappen. Volumes worden gemaakt in capaciteits Pools.  Aan elk volume wordt een quotum toegewezen dat de ingerichte capaciteit van de Pools verlaagt. Het quotum dat kan worden toegewezen aan volumes, bereiken van Mini maal 100 GiB tot Maxi maal 100 TiB.  

Voor een actief volume is het capaciteits verbruik voor quota gebaseerd op logische (efficiënte) capaciteit.

Als het werkelijke capaciteits verbruik van een volume groter is dan het opslag quotum, kan het volume blijven groeien. Schrijf bewerkingen zijn nog steeds toegestaan zolang de werkelijke volume grootte kleiner is dan de systeem limiet (100 TiB).  

De totale gebruikte capaciteit in een capaciteits pool op basis van de ingerichte hoeveelheid is de som van het aantal toegewezen quota of het werkelijke verbruik van alle volumes in de groep: 

   ![Berekening totale gebruikte capaciteit](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

In het onderstaande diagram ziet u deze concepten.  
* We hebben een capaciteits pool met 4 TiB aan ingerichte capaciteit.  De pool bevat drie volumes.  
    * Aan het volume 1 is een quotum van 2 TiB toegewezen en 800 GiB van het verbruik.  
    * Aan volume 2 is een quotum van 1 TiB toegewezen en 100 GiB van het verbruik.  
    * Aan volume 3 is een quotum van 500 GiB toegewezen, maar er is 800 GiB (overschrijding).  
* De capaciteits pool wordt gemeten voor 4 TiB capaciteit (de ingerichte hoeveelheid).  
    3,8 TiB capaciteit wordt verbruikt (2 TiB en 1 TiB quotum van de volumes 1 en 2, en 800 GiB van het werkelijke verbruik voor volume 3). En 200 GiB van de capaciteit is resterend.

   ![Capaciteits pool met drie volumes](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="overage-in-capacity-consumption"></a>Overschrijding in capaciteits verbruik  

Wanneer de totale gebruikte capaciteit van een pool de ingerichte capaciteit overschrijdt, zijn gegevens schrijf bewerkingen nog steeds toegestaan.  Na de respijt periode (één uur), als de gebruikte capaciteit van de pool nog steeds de ingerichte capaciteit overschrijdt, wordt de pool grootte automatisch verhoogd in stappen van 1 TiB totdat de ingerichte capaciteit groter is dan de totale gebruikte capaciteit.  Als in de bovenstaande afbeelding bijvoorbeeld het volume 3 blijft groeien en het werkelijke verbruik 1,2 TiB bereikt, wordt de grootte van de groep na de respijt periode automatisch gewijzigd in 5 TiB.  Het resultaat is dat de ingerichte capaciteit van de groep (5 TiB) de gebruikte capaciteit (4,2 TiB) overschrijdt.  

Hoewel de grootte van de capaciteits groep automatisch groeit om te voldoen aan de vraag van het volume, wordt deze niet automatisch gereduceerd wanneer de grootte van het volume afneemt. Als u de grootte van de capaciteits groep wilt verkleinen nadat een volume grootte afneemt (bijvoorbeeld na het opruimen van een volume), moet u de grootte van de capaciteits groep _hand matig_ verlagen.

## <a name="manual-changes-of-the-pool-size"></a>Hand matige wijzigingen van de pool grootte  

U kunt de groeps grootte hand matig Verg Roten of verkleinen. De volgende beperkingen zijn echter van toepassing:
* Minimale en maximale limieten voor services  
    Zie het artikel over [resource limieten](azure-netapp-files-resource-limits.md).
* Een verhoging van 1-TiB na de eerste 4-TiB-minimale aankoop
* Een minimale facturerings toename van één uur
* De ingerichte grootte van de groep mag niet kleiner zijn dan de totale gebruikte capaciteit in de pool.
* Voor capaciteits Pools met hand matige QoS kan de pool grootte alleen worden verminderd als de grootte en het service niveau meer door Voer bieden dan de daad werkelijke toegewezen door Voer van alle volumes.

## <a name="behavior-of-maximum-size-pool-overage"></a>Gedrag van groep met maximale grootte overschrijding   

De maximale grootte van een capaciteits groep die u kunt maken of aanpassen, is 500 TiB.  Wanneer de totale gebruikte capaciteit in een capaciteits groep groter is dan 500 TiB, worden de volgende situaties weer gegeven:
* Gegevens schrijf bewerkingen zijn nog steeds toegestaan (als het volume lager is dan het systeem maximum van 100 TiB).
* Na de respijt periode van één uur wordt de grootte van de pool automatisch gewijzigd in een interval van 1 TiB, totdat de ingerichte capaciteit van de pool de totale gebruikte capaciteit overschrijdt.
* De extra ingerichte en gefactureerde pool capaciteit van meer dan 500 TiB kan niet worden gebruikt om volume quota toe te wijzen. Het kan ook niet worden gebruikt om QoS-limieten voor prestaties uit te breiden.  
    Zie [service niveaus](azure-netapp-files-service-levels.md) over prestatie limieten en QoS-grootte.

In het onderstaande diagram ziet u deze concepten:
* We hebben een capaciteits pool met een Premium-opslaglaag en een capaciteit van 500 TiB. De pool bevat negen volumes.
    * Aan de volumes 1 tot en met 8 wordt een quotum van 60 TiB toegewezen.  De totale gebruikte capaciteit is 480 TiB.  
        Elk volume heeft een QoS-limiet van 3,75 GiB/s aan door Voer (60 TiB * 64 MiB/s).  
    * Aan volume 9 is een quotum van 20 TiB toegewezen.  
        Volume 9 heeft een QoS-limiet van 1,25 GiB/s aan door Voer (20 TiB * 64 MiB/s).
* Volume 9 is een overschrijding-scenario. Er is 25 TiB van het werkelijke verbruik.  
    * Na de evaluatie periode van één uur wordt de grootte van de capaciteits groep gewijzigd in 505 TiB.  
        Dat wil zeggen, totale gebruikte capaciteit = 8 * 60-TiB quota voor volumes 1 t/m 8 en 25 TiB van het werkelijke verbruik voor volume 9.
    * De gefactureerde capaciteit is 505 TiB.
    * Het volume quotum voor volume 9 kan niet worden verhoogd (omdat de totale toegewezen quota voor de pool niet groter mag zijn dan 500 TiB).
    * Er kan geen aanvullende QoS-door voer worden toegewezen (omdat het totale aantal QoS voor de groep nog steeds is gebaseerd op 500 TiB).

   ![Capaciteits pool met negen volumes](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-nine-vols.png)

## <a name="capacity-consumption-of-snapshots"></a>Capaciteits verbruik van moment opnamen 

Het capaciteits verbruik van moment opnamen in Azure NetApp Files wordt berekend op basis van het quotum van het bovenliggende volume.  Als gevolg hiervan wordt hetzelfde factuur tarief gedeeld als de capaciteits groep waartoe het volume behoort.  Maar in tegens telling tot het actieve volume wordt het momentopname verbruik gemeten op basis van de verbruikte incrementele capaciteit.  Azure NetApp Files moment opnamen zijn differentieel. Afhankelijk van de wijzigings frequentie van de gegevens verbruiken de moment opnamen vaak veel minder capaciteit dan de logische capaciteit van het actieve volume. Stel dat u een moment opname hebt van een 500-GiB-volume dat slechts 10 GiB van differentiële gegevens bevat. De capaciteit die wordt berekend op basis van het volume quotum voor die moment opname, zou 10 GiB zijn, niet 500 GiB. 

## <a name="next-steps"></a>Volgende stappen

* [Pagina met Azure NetApp Files prijzen](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Serviceniveau's voor Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Resourcelimieten voor Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Kosten model voor replicatie tussen regio's](cross-region-replication-introduction.md#cost-model-for-cross-region-replication)
