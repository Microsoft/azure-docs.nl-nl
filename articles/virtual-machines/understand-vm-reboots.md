---
title: Meer informatie over het opnieuw opstarten van VM'S
description: Meer informatie over het opnieuw opstarten van VM'S-onderhoud versus downtime
author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.openlocfilehash: af371a8f7da5ef32e95d4096b69c5d52ce3e3700
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102510484"
---
# <a name="understand-vm-reboots---maintenance-vs-downtime"></a>Meer informatie over het opnieuw opstarten van de VM-onderhoud versus downtime
Er zijn drie scenario's die kunnen leiden tot het beïnvloeden van virtuele machines in Azure: ongepland onderhoud van hardware, onverwachte downtime en gepland onderhoud.

## <a name="unplanned-hardware-maintenance-event"></a>Ongeplande onderhouds gebeurtenis voor hardware
Ongeplande hardware-onderhoud vindt plaats wanneer het Azure-platform voor spelt dat de hardware of een platform onderdeel dat aan een fysieke machine is gekoppeld, is mislukt. Wanneer via het platform een fout wordt voorspeld, wordt een gebeurtenis voor niet-gepland hardwareonderhoud vrijgegeven om de impact op de virtuele machines die worden gehost op deze hardware, te beperken. In Azure wordt gebruikgemaakt van [livemigratietechnologie](./maintenance-and-updates.md) om de virtuele machines op de hardware waarop de fout optreedt, te migreren naar een gezonde fysieke machine. Livemigratie is een bewerking ter behoud van VM's waardoor de werking van een virtuele machine slechts korte tijd wordt onderbroken. Het geheugen, de geopende bestanden en de netwerkverbindingen blijven behouden, maar de prestaties vóór en/of na de gebeurtenis kunnen minder zijn. In gevallen waarbij livemigratie niet kan worden gebruikt, treedt er onverwachte downtime op de VM op, zoals hieronder wordt beschreven.


## <a name="unexpected-downtime"></a>Onverwachte downtime
Onverwachte downtime is wanneer de hardware of de fysieke infra structuur voor de virtuele machine onverwacht mislukt. Voorbeelden hiervan zijn o.a. lokale netwerkproblemen, lokale schijfdefecten of andere defecten op rack-niveau. Wanneer gedetecteerd, wordt uw virtuele machine door het Azure-platform automatisch gemigreerd naar een in orde zijnde fysieke machine in hetzelfde Data Center. Tijdens deze procedure treedt downtime (opnieuw opstarten) op de virtuele machines op en in sommige gevallen gaat de tijdelijke schijf verloren. Het besturingssysteem en de gegevensschijven die zijn bijgevoegd, blijven altijd behouden.

Virtuele machines kunnen ook downtime ondervinden in het onwaarschijnlijke geval van een storing of ramp die invloed heeft op een volledig Data Center of zelfs een hele regio. Voor deze scenario's biedt Azure beveiligingsopties, waaronder [beschikbaarheidszones](../availability-zones/az-overview.md) en [gekoppelde regio's](regions.md#region-pairs).

## <a name="planned-maintenance-events"></a>Gebeurtenissen voor gepland onderhoud
Geplande onderhouds gebeurtenissen zijn periodieke updates die door micro soft zijn aangebracht in het onderliggende Azure-platform om de algehele betrouw baarheid, prestaties en beveiliging van de platform infrastructuur waarop uw virtuele machines worden uitgevoerd, te verbeteren. De meeste van deze updates worden uitgevoerd zonder dat dit van invloed is op uw Virtual Machines of Cloud Services (Zie [onderhoud waarbij de computer niet opnieuw moet worden opgestart](maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)). Wanneer mogelijk maakt het Azure-platform gebruik van Onderhoud ter behoud van VM's. In zeldzame gevallen kan het echter noodzakelijk zijn om de virtuele machine opnieuw op te starten om de vereiste updates toe te passen op de onderliggende infrastructuur. In dit geval kunt u gepland onderhoud van Azure gebruiken met de bewerking Onderhoud-Opnieuw implementeren door het onderhoud voor de betrokken VM's te initiëren in het geschikte tijdvenster. Zie [Gepland onderhoud voor virtuele machines](maintenance-and-updates.md) voor meer informatie.

## <a name="reduce-downtime"></a>Uitval tijd verminderen
Om de gevolgen van downtime vanwege een of meer van deze gebeurtenissen te beperken, raden we aan voor uw virtuele machines de volgende aanbevolen procedures voor hoge beschikbaarheid te volgen:

* [Beschikbaarheidszones](../availability-zones/az-overview.md) gebruiken om te beschermen tegen storingen in data centers
* Configureer meerdere virtuele machines in een [beschikbaarheidsset](availability-set-overview.md) voor redundantie
* [Geplande gebeurtenissen voor Linux](/linux/scheduled-events.md) of [geplande gebeurtenissen voor Windows](/windows/scheduled-events.md) gebruiken om proactief te reageren op gebeurtenissen die invloed hebben op vm's
* Configureer elke toepassingslaag in afzonderlijke beschikbaarheidssets
* Een [Load Balancer](../load-balancer/load-balancer-overview.md) met beschikbaarheids zones of sets combi neren

## <a name="next-steps"></a>Volgende stappen
Zie [overzicht van Beschik baarheid](availability.md)voor meer informatie over beschikbaarheids opties in Azure.
