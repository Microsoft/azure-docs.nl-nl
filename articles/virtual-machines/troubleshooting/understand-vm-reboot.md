---
title: Het opnieuw opstarten van een systeem voor een virtuele Azure-machine begrijpen | Microsoft Docs
description: Een lijst met gebeurtenissen die ertoe kunnen leiden dat een VM opnieuw wordt opgestart
services: virtual-machines
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: c3dcb4a7b92f5aab1336e49a7d299614855b48fd
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100591163"
---
# <a name="understand-a-system-reboot-for-azure-vm"></a>Meer informatie over het opnieuw opstarten van het systeem voor Azure VM

Azure virtual machines (Vm's) kunnen soms niet opnieuw worden opgestart, zonder dat er een bewijs van de opstart bewerking is gestart. In dit artikel worden de acties en gebeurtenissen vermeld waarmee Vm's opnieuw kunnen worden opgestart en inzicht krijgen in de manier om onverwachte herstart problemen te voor komen of de impact van dergelijke problemen te verminderen.

## <a name="configure-the-vms-for-high-availability"></a>Configureer de Vm's voor hoge Beschik baarheid

De beste manier om een toepassing die wordt uitgevoerd op Azure te beveiligen tegen het opnieuw opstarten van de VM en uitval tijd is het configureren van de virtuele machines voor maximale Beschik baarheid.

U wordt aangeraden twee of meer virtuele machines in een beschikbaarheidsset te groeperen om dit niveau van redundantie voor uw toepassing te bieden. Deze configuratie zorgt ervoor dat tijdens een geplande of ongeplande onderhouds gebeurtenis ten minste één virtuele machine beschikbaar is en voldoet aan het 99,95 procent van [Azure Sla](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_5/).

Zie [de beschik baarheid van virtuele machines beheren](../manage-availability.md) voor meer informatie over beschikbaarheids sets.

## <a name="resource-health-information"></a>Resource Health informatie

Azure Resource Health is een service die de status van afzonderlijke Azure-resources weergeeft en praktische richt lijnen biedt voor het oplossen van problemen. In een cloud omgeving waar het niet mogelijk is om rechtstreeks toegang te krijgen tot servers of infrastructuur elementen, is het doel van Resource Health om de tijd te verminderen die u besteedt aan het oplossen van problemen. Met name het doel is om de tijd te verkorten die nodig is om te bepalen of de hoofdmap van het probleem zich in de toepassing bevindt of in een gebeurtenis binnen het Azure-platform. Zie [resource Health begrijpen en gebruiken](../../service-health/resource-health-overview.md)voor meer informatie.

## <a name="actions-and-events-that-can-cause-the-vm-to-reboot"></a>Acties en gebeurtenissen die ertoe kunnen leiden dat de VM opnieuw wordt opgestart

### <a name="planned-maintenance"></a>Gepland onderhoud

Microsoft Azure regel matig updates uitvoeren over de hele wereld om de betrouw baarheid, prestaties en beveiliging te verbeteren van de host-infra structuur die gebruikmaakt van Vm's. Veel van deze updates, waaronder updates op geheugen behoud, worden uitgevoerd zonder dat dit van invloed is op uw virtuele machines of Cloud Services.

Voor sommige updates is het echter nood zakelijk dat de computer opnieuw wordt opgestart. In dergelijke gevallen worden de Vm's afgesloten terwijl de infra structuur wordt bijgewerkt, waarna de Vm's opnieuw worden gestart.

Zie de artikelen die hier worden vermeld als u wilt weten wat het geplande onderhoud van Azure is en hoe dit kan van invloed zijn op de beschik baarheid van uw virtuele Linux-machines. Deze artikelen bieden achtergrondinformatie over het proces van gepland onderhoud in Azure, en over hoe u gepland onderhoud kunt inplannen om de impact ervan nog verder te beperken.

- [Gepland onderhoud voor VM's in Azure](../maintenance-and-updates.md?bc=/azure/virtual-machines/windows/breadcrumb/toc.json&toc=/azure/virtual-machines/windows/toc.json)
- [Gepland onderhoud inplannen voor Azure-VM's](../maintenance-and-updates.md?bc=/azure/virtual-machines/windows/breadcrumb/toc.json&toc=/azure/virtual-machines/windows/toc.json)

### <a name="memory-preserving-updates"></a>Updates met geheugenbehoud

Voor deze klasse van updates in Microsoft Azure hebben gebruikers geen invloed op hun actieve Vm's. Dit zijn veelal updates van onderdelen of services die kunnen worden bijgewerkt zonder de actieve sessie te verstoren. Sommige zijn platform infrastructuur updates op het hostbesturingssysteem dat kan worden toegepast zonder dat de virtuele machines opnieuw moeten worden opgestart.

Deze updates met geheugenbehoud worden uitgevoerd met technologie die in-place livemigratie mogelijk maakt. Wanneer de virtuele machine wordt bijgewerkt, wordt deze in de status *onderbroken* gezet. In deze status blijft de geheugeninhoud in het RAM behouden. Ondertussen ontvangt het besturingssysteem van de onderliggende host de noodzakelijke updates en patches. De VM wordt 30 seconden na het onderbreken weer hervat. Op dat moment wordt de klok van de VM automatisch gesynchroniseerd.

Als gevolg van de korte pauze periode, vermindert het implementeren van updates via dit mechanisme de impact op de Vm's aanzienlijk. Niet alle updates kunnen echter op deze manier worden geïmplementeerd. 

Updates voor meerdere exemplaren (voor VM's in een beschikbaarheidsset) worden telkens voor één updatedomein tegelijk toegepast.

> [!NOTE]
> Linux-machines met oude kernel-versies worden beïnvloed door een kernel-paniek tijdens deze update methode. Als u dit probleem wilt voor komen, werkt u bij met de kernel-versie 3.10.0-327.10.1 of hoger. Voor meer informatie, Zie [een Azure Linux-machine op een 3,10-kernel paniek na een upgrade van een host-knoop punt](https://support.microsoft.com/help/3212236).

### <a name="user-initiated-reboot-or-shutdown-actions"></a>Door de gebruiker geïnitieerde acties voor opnieuw opstarten of afsluiten

Als u de computer opnieuw opstart vanaf de Azure Portal, Azure PowerShell, opdracht regel interface of REST API, kunt u de gebeurtenis vinden in het [Azure-activiteiten logboek](../../azure-monitor/essentials/platform-logs-overview.md).

Als u de actie uitvoert vanuit het besturings systeem van de virtuele machine, kunt u de gebeurtenis in de systeem logboeken vinden.

Andere scenario's die doorgaans ertoe leiden dat de virtuele machine opnieuw wordt opgestart, omvat het meerdere acties voor configuratie wijziging. Normaal gesp roken wordt een waarschuwing weer gegeven dat aangeeft dat het uitvoeren van een bepaalde actie resulteert in het opnieuw opstarten van de virtuele machine. Voor beelden zijn onder andere het wijzigen van VM-bewerkingen, het aanpassen van het wacht woord van het beheerders account en het instellen van een statisch IP-adres.

### <a name="azure-security-center-and-windows-update"></a>Azure Security Center en Windows Update

Azure Security Center bewaakt dagelijks Windows-en Linux-Vm's voor ontbrekende updates van het besturings systeem. Security Center haalt een lijst met beschik bare beveiligings-en essentiële updates op van Windows Update of Windows Server Update Services (WSUS), afhankelijk van welke service is geconfigureerd op een Windows-VM. Security Center controleert ook op de nieuwste updates voor Linux-systemen. Als op uw virtuele machine een systeem update ontbreekt, wordt Security Center aanbevolen om systeem updates toe te passen. De toepassing van deze systeem updates wordt beheerd via de Security Center in de Azure Portal. Nadat u een aantal updates hebt toegepast, is het mogelijk dat het opnieuw opstarten van de VM vereist is. Zie [systeem updates Toep assen in azure Security Center](../../security-center/asset-inventory.md)voor meer informatie.

Net als bij on-premises servers pusht Azure geen updates van Windows Update naar Windows-Vm's, omdat deze machines zijn bedoeld om te worden beheerd door hun gebruikers. U wordt echter aangemoedigd om de instelling automatische Windows Update ingeschakeld te laten. Automatische installatie van updates van Windows Update kan er ook voor zorgen dat de computer opnieuw wordt opgestart nadat de updates zijn toegepast. Zie [Windows Update Veelgestelde vragen](https://support.microsoft.com/help/12373/windows-update-faq)voor meer informatie.

### <a name="other-situations-affecting-the-availability-of-your-vm"></a>Andere situaties die van invloed zijn op de beschik baarheid van uw virtuele machine

Er zijn andere gevallen waarin Azure het gebruik van een virtuele machine actief kan opschorten. U ontvangt e-mail meldingen voordat deze actie wordt uitgevoerd, zodat u de onderliggende problemen kunt oplossen. Voor beelden van problemen die van invloed zijn op de VM-Beschik baarheid, zijn beveiligings schendingen en de verval datum van betalings methoden.

### <a name="host-server-faults"></a>Fouten met hostservers

De virtuele machine wordt gehost op een fysieke server die binnen een Azure-Data Center wordt uitgevoerd. Naast enkele andere onderdelen van Azure voert de fysieke server een agent uit die de Hosta Gent wordt genoemd. Wanneer deze onderdelen van Azure-software op de fysieke server niet meer reageren, activeert het bewakings systeem het opnieuw opstarten van de hostserver om te proberen het herstel uit te voeren. De virtuele machine is doorgaans binnen vijf minuten opnieuw beschikbaar en blijft actief op dezelfde host als voorheen.

Server fouten worden meestal veroorzaakt door hardwarestoringen, zoals het mislukken van een harde schijf of een Solid-State station. Azure bewaakt deze instanties voortdurend, identificeert de onderliggende bugs en implementeert updates nadat de oplossing is geïmplementeerd en getest.

Omdat sommige hostserver fouten specifiek kunnen zijn voor die server, kan een herhaalde VM-opstart situatie worden verbeterd door de virtuele machine hand matig opnieuw te implementeren op een andere hostserver. Deze bewerking kan worden geactiveerd met behulp van de optie opnieuw **implementeren** op de pagina Details van de virtuele machine, of door de virtuele machine te stoppen en opnieuw op te starten in de Azure Portal.

### <a name="auto-recovery"></a>Automatisch herstel

Als de hostserver om welke reden dan ook niet opnieuw kan worden opgestart, initieert het Azure-platform een actie voor automatisch herstel om de defecte hostserver uit te wisselen voor verdere onderzoek. 

Alle Vm's op die host worden automatisch verplaatst naar een andere, gezonde hostserver. Dit proces is meestal binnen vijf tien minuten voltooid. Zie [automatisch herstel van virtuele machines](https://azure.microsoft.com/blog/service-healing-auto-recovery-of-virtual-machines)voor meer informatie over het automatische herstel proces.

### <a name="unplanned-maintenance"></a>Niet-gepland onderhoud

In zeldzame gevallen moet het Azure Operations-team mogelijk onderhouds activiteiten uitvoeren om te zorgen voor de algehele status van het Azure-platform. Dit gedrag kan van invloed zijn op de beschik baarheid van de virtuele machine en resulteert doorgaans in dezelfde automatische herstel actie zoals eerder beschreven.  

Niet-gepland onderhoud omvat het volgende:

- Defragmentatie van urgente knoop punten
- Updates voor dringende netwerk switch

### <a name="vm-crashes"></a>VM loopt vast

Vm's worden mogelijk opnieuw opgestart vanwege problemen binnen de VM zelf. De werk belasting of rol die op de virtuele machine wordt uitgevoerd, kan een fout controle activeren binnen het gast besturingssysteem. Raadpleeg de systeem-en toepassings logboeken voor Windows-Vm's en de seriële logboeken voor Linux Vm's voor meer informatie over het bepalen van de reden voor het vastlopen.

### <a name="storage-related-forced-shutdowns"></a>Aan opslag gerelateerd geforceerd afsluiten

Vm's in azure zijn afhankelijk van virtuele schijven voor besturings systeem en gegevens opslag die worden gehost op de Azure Storage-infra structuur. Wanneer de beschik baarheid of de verbinding tussen de virtuele machine en de bijbehorende schijven langer dan 120 seconden van invloed is op het Azure-platform, worden de Vm's geforceerd afgesloten om beschadiging van gegevens te voor komen. De Vm's worden automatisch ingeschakeld nadat de opslag verbinding is hersteld. 

De duur van de afsluiting kan korter zijn dan vijf minuten, maar kan aanzienlijk langer duren. Hier volgt een van de specifieke gevallen die zijn gekoppeld aan opslag gerelateerd geforceerde afsluitingen: 

**Overschrijden IO-limieten**

Vm's kunnen tijdelijk worden afgesloten wanneer I/O-aanvragen consistent worden beperkt omdat het volume van I/O-bewerkingen per seconde (IOPS) de I/O-limieten voor de schijf overschrijdt. (Standaard schijf opslag is beperkt tot 500 IOPS.) U kunt dit probleem oplossen door schijf striping te gebruiken of de opslag ruimte in de gast-VM te configureren, afhankelijk van de werk belasting. 

### <a name="other-incidents"></a>Andere incidenten

In zeldzame gevallen kan een algemeen probleem van invloed zijn op meerdere servers in een Azure-Data Center. Als dit probleem optreedt, verzendt het Azure-team e-mail meldingen naar de betrokken abonnementen. U kunt het [Azure service Health-dash board](https://azure.microsoft.com/status/) en de Azure Portal controleren op de status van lopende storingen en eerdere incidenten.