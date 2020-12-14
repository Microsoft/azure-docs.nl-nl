---
title: De beschik baarheid van Vm's beheren
description: Meer informatie over het gebruik van meerdere virtuele machines om te zorgen voor hoge Beschik baarheid voor uw toepassingen in azure
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cynthn
ms.openlocfilehash: b2f50ba910e3e47c55a63f58bc9f5e978e80e95a
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401182"
---
# <a name="manage-the-availability-of-linux-virtual-machines"></a>De beschikbaarheid van virtuele Linux-machines beheren

Meer informatie over manieren om meerdere virtuele machines in te stellen en te beheren om hoge Beschik baarheid voor uw Linux-toepassing in azure te garanderen. 


## <a name="understand-vm-reboots---maintenance-vs-downtime"></a>Informatie over het opnieuw opstarten van VM's - onderhoud versus downtime
Er zijn drie scenario's die ertoe kunnen leiden dat een virtuele machine in Azure wordt beïnvloed: niet-gepland hardwareonderhoud, onverwachte downtime, en gepland onderhoud.

* **Gebeurtenis voor niet-gepland hardwareonderhoud** treedt op wanneer via het Azure-platform een fout wordt voorspeld op de hardware of in een platformonderdeel dat is gekoppeld aan een fysieke computer. Wanneer via het platform een fout wordt voorspeld, wordt een gebeurtenis voor niet-gepland hardwareonderhoud vrijgegeven om de impact op de virtuele machines die worden gehost op deze hardware, te beperken. In Azure wordt gebruikgemaakt van [livemigratietechnologie](./maintenance-and-updates.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json%252c%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json%253ftoc%253d%2fazure%2fvirtual-machines%2flinux%2ftoc.json) om de virtuele machines op de hardware waarop de fout optreedt, te migreren naar een gezonde fysieke machine. Livemigratie is een bewerking ter behoud van VM's waardoor de werking van een virtuele machine slechts korte tijd wordt onderbroken. Het geheugen, de geopende bestanden en de netwerkverbindingen blijven behouden, maar de prestaties vóór en/of na de gebeurtenis kunnen minder zijn. In gevallen waarbij livemigratie niet kan worden gebruikt, treedt er onverwachte downtime op de VM op, zoals hieronder wordt beschreven.


* **Een onverwachte downtime** is wanneer de hardware of de fysieke infrastructuur voor de virtuele machine onverwacht een storing ondervindt. Voorbeelden hiervan zijn o.a. lokale netwerkproblemen, lokale schijfdefecten of andere defecten op rack-niveau. Wanneer een dergelijke situatie wordt gedetecteerd, wordt de VM automatisch met behulp van het Azure-platform gemigreerd naar een gezonde fysieke machine in hetzelfde datacenter. Tijdens deze procedure treedt downtime (opnieuw opstarten) op de virtuele machines op en in sommige gevallen gaat de tijdelijke schijf verloren. Het besturingssysteem en de gegevensschijven die zijn bijgevoegd, blijven altijd behouden.

  Virtuele machines kunnen ook downtime ondervinden in het onwaarschijnlijke geval van een storing of ramp die invloed heeft op een volledig datacenter of zelfs een hele regio. Voor deze scenario's biedt Azure beveiligingsopties, waaronder [beschikbaarheidszones](../availability-zones/az-overview.md) en [gekoppelde regio's](regions.md#region-pairs).

* **Gepland onderhoud** bestaat uit periodieke updates van het onderliggende Azure-platform die door Microsoft worden doorgevoerd ter verbetering van de algemene betrouwbaarheid, prestaties en beveiliging van de platform-infrastructuur waarop de virtuele machines worden uitgevoerd. De meeste van deze updates worden uitgevoerd zonder dat dit van invloed is op uw Virtual Machines of Cloud Services (Zie [onderhoud waarbij de computer niet opnieuw moet worden opgestart](maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot)). Wanneer mogelijk maakt het Azure-platform gebruik van Onderhoud ter behoud van VM's. In zeldzame gevallen kan het echter noodzakelijk zijn om de virtuele machine opnieuw op te starten om de vereiste updates toe te passen op de onderliggende infrastructuur. In dit geval kunt u gepland onderhoud van Azure gebruiken met de bewerking Onderhoud-Opnieuw implementeren door het onderhoud voor de betrokken VM's te initiëren in het geschikte tijdvenster. Zie [Gepland onderhoud voor virtuele machines](maintenance-and-updates.md) voor meer informatie.


Om de gevolgen van downtime vanwege een of meer van deze gebeurtenissen te beperken, raden we aan voor uw virtuele machines de volgende aanbevolen procedures voor hoge beschikbaarheid te volgen:

* Beschikbaarheidszones gebruiken om problemen met data centers te beveiligen
* Configureer meerdere virtuele machines in een beschikbaarheidsset voor redundantie
* Beheerde schijven voor VM's in een beschikbaarheidsset gebruiken
* Geplande gebeurtenissen gebruiken om proactief te reageren op gebeurtenissen die invloed hebben op VM's
* Configureer elke toepassingslaag in afzonderlijke beschikbaarheidssets
* Een load balancer combineren met beschikbaarheidszones of -sets
* Beschikbaarheidszones gebruiken om te beschermen tegen fouten op datacenterniveau

## <a name="use-availability-zones-to-protect-from-datacenter-level-failures"></a>Beschikbaarheidszones gebruiken om te beschermen tegen fouten op datacenterniveau

[Beschikbaarheidszones](../availability-zones/az-overview.md) vergroten de controle die u nodig hebt om de beschikbaarheid van de toepassingen en gegevens op uw VM's te behouden. Beschikbaarheidszones zijn unieke, fysieke locaties binnen een Azure-regio. Elke zone bestaat uit een of meer datacenters die zijn voorzien van een onafhankelijke stroomvoorziening, koeling en netwerken. Tolerantie wordt gegarandeerd door aanwezigheid van minimaal drie afzonderlijke zones in alle actieve regio's. De fysieke scheiding tussen beschikbaarheidszones binnen een Azure-regio beschermt toepassingen en gegevens tegen storingen op zoneniveau. Zone-redundante services repliceren uw toepassingen en gegevens in meerdere beschikbaarheidszones om bescherming te bieden tegen Single Points of Failure.

Een beschikbaarheidszone in een Azure-regio is een combinatie van een **foutdomein** en een **updatedomein**. Als u bijvoorbeeld drie of meer virtuele machines in drie zones in een Azure-regio maakt, worden uw virtuele machines effectief over drie foutdomeinen en drie updatedomeinen verdeeld. Het Azure-platform herkent deze verdeling over updatedomeinen om ervoor te zorgen dat virtuele machines in verschillende zones niet op hetzelfde moment worden bijgewerkt.

Met beschikbaarheidszones biedt Azure de beste uptime SLA voor VM’s van de branche, van 99,99%. Door uw oplossingen te ontwikkelen voor het gebruik van gerepliceerde VM's in zones, kunt u uw toepassingen en gegevens beveiligen tegen verlies van een datacenter. Als er een zone wordt aangetast, zijn de gerepliceerde apps en gegevens direct beschikbaar in een andere zone.

![Beschikbaarheidszones](./media/virtual-machines-common-manage-availability/three-zones-per-region.png)

Meer informatie over het implementeren van een VM voor [Windows](./windows/create-powershell-availability-zone.md) of [Linux](./linux/create-cli-availability-zone.md) in een beschikbaarheidszone.

## <a name="configure-multiple-virtual-machines-in-an-availability-set-for-redundancy"></a>Configureer meerdere virtuele machines in een beschikbaarheidsset voor redundantie
Beschikbaarheidssets zijn ook een datacenterconfiguratie om VM-redundantie en beschikbaarheid te bieden. Deze configuratie in een datacenter zorgt ervoor dat tijdens een geplande of onvoorziene onderhoudsgebeurtenis ten minste één virtuele machine beschikbaar is en wordt voldaan aan de 99,95% Azure SLA. Zie de [SLA voor virtuele machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/) voor meer informatie.

> [!IMPORTANT]
> Een virtuele machine met één exemplaar in een beschikbaarheidsset die zelfstandig is ingesteld, moet Premium - SSD of Ultra Disk gebruiken voor alle schijven en gegevensschijven in een besturingssysteem om in aanmerking te komen voor de SLA voor de VM-connectiviteit van ten minste 99,9%. 
> 
> Een virtuele machine van één exemplaar met een standaard SSD heeft een SLA van minstens 99,5%, terwijl een virtuele machine van één exemplaar met een standaard HDD een SLA heeft van minstens 95%.  Raadpleeg [SLA voor virtuele machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/)

Elke virtuele machine in uw beschikbaarheidsset krijgt een **updatedomein** en een **foutdomein** toegewezen door het onderliggende Azure-platform. Voor iedere beschikbaarheidsset worden standaard vijf updatedomeinen toegewezen die niet door gebruiker te bewerken zijn (voor Resource Manager-implementaties kan dit aantal worden opgehoogd tot 20 updatedomeinen), om groepen virtuele machines en onderliggende fysieke hardware aan te duiden die op hetzelfde moment opnieuw kunnen worden opgestart. Wanneer in één beschikbaarheidsset meer dan vijf virtuele machines worden geconfigureerd, wordt de zesde virtuele machine in hetzelfde updatedomein geplaatst als de eerste virtuele machine, de zevende in hetzelfde updatedomein als de tweede virtuele machine, enzovoort. De volgorde waarin updatedomeinen opnieuw worden opgestart, verloopt tijdens gepland onderhoud niet altijd sequentieel, maar er wordt slechts één updatedomein tegelijk opnieuw opgestart. Een updatedomein dat opnieuw is opgestart, heeft 30 minuten om te herstellen voordat onderhoud wordt geïnitieerd op een ander updatedomein.

Foutdomeinen duiden de groep virtuele machines aan die een gemeenschappelijke voeding en switch delen. Standaard worden bij Resource Manager-implementaties de virtuele machines die zijn geconfigureerd in uw beschikbaarheidsset verdeeld over maximaal drie foutdomeinen (twee foutdomeinen bij klassieke implementaties). Hoewel het in een beschikbaarheidsset plaatsen van uw virtuele machines uw toepassing niet beschermt tegen problemen met het besturingssysteem of de toepassing zelf, worden zo wel de gevolgen van mogelijke problemen met de fysieke hardware, netwerkstoringen of stroomonderbrekingen beperkt.

<!--Image reference-->
   ![Concepttekening van een configuratie met een updatedomein en een foutdomein](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

## <a name="use-managed-disks-for-vms-in-an-availability-set"></a>Beheerde schijven voor VM's in een beschikbaarheidsset gebruiken
Als u momenteel Vm's met niet-beheerde schijven gebruikt, raden wij u ten zeerste aan om te converteren van onbeheerd naar beheerde schijven voor [Linux](./linux/convert-unmanaged-to-managed-disks.md) en [Windows](./windows/convert-unmanaged-to-managed-disks.md).

[Beheerde schijven](./managed-disks-overview.md) bieden een betere betrouwbaarheid voor beschikbaarheidssets door ervoor te zorgen dat de schijven van VM's in een beschikbaarheidsset voldoende van elkaar zijn verwijderd, waardoor een SPOF (Single Point Of Failure) wordt voorkomen. Dit doet u door automatisch de schijven in verschillende opslagfoutdomeinen (opslagclusters) te plaatsen en ze te uit te lijnen met het domein van de VM-fout. Wanneer een hardware- of softwarefout optreedt in een foutdomein, wordt alleen het VM-exemplaar met schijven in dit opslagfoutdomein beïnvloed.
![Foutdomeinen van beheerde schijven](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

> [!IMPORTANT]
> Het aantal foutdomeinen voor beheerde beschikbaarheidssets varieert per regio: twee of drie per regio. U kunt het foutdomein voor elke regio weergeven door de volgende scripts uit te voeren.

```azurepowershell-interactive
Get-AzComputeResourceSku | where{$_.ResourceType -eq 'availabilitySets' -and $_.Name -eq 'Aligned'}
```

```azurecli-interactive 
az vm list-skus --resource-type availabilitySets --query '[?name==`Aligned`].{Location:locationInfo[0].location, MaximumFaultDomainCount:capabilities[0].value}' -o Table
```

> [!NOTE]
> Onder bepaalde omstandigheden kunnen twee VM's in dezelfde beschikbaarheidsset een foutdomein delen. U kunt dit bevestigen door naar uw beschikbaarheidsset te gaan en de kolom **Foutdomein** te controleren. Een gedeeld foutdomein kan worden veroorzaakt door de volgende volgorde tijdens de implementatie van de VM's:
> 1. Implementeer de eerste VM.
> 1. Stop de eerste VM of hef de toewijzing ervan op.
> 1. Implementeer de tweede VM.
>
> In deze omstandigheden kan de besturingssysteemschijf van de tweede VM worden gemaakt in hetzelfde foutdomein als de eerste VM, zodat de twee VM's zich in hetzelfde foutdomein bevinden. Wij raden aan VM's niet te stoppen of de toewijzing ervan op te heffen tussen implementaties, om dit probleem te voorkomen.

Als u VM's met niet-beheerde schijven wilt gebruiken, volgt u onderstaande aanbevolen procedures voor opslagaccounts waarbij virtuele harde schijven (VHD's) of VM's zijn opgeslagen als [pagina-blobs](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs#about-page-blobs).

1. **Zorg dat alle schijven (gegevens en besturingssysteem) worden gekoppeld aan een virtuele machine op hetzelfde opslagaccount**
2. **Controleer de [limieten](../storage/blobs/scalability-targets-premium-page-blobs.md) voor het aantal niet-beheerde schijven in een Azure Storage-account** voordat u meer VHD's aan een opslagaccount toevoegt
3. **Gebruik een afzonderlijk opslagaccount voor elke virtuele machine in een beschikbaarheidsset.** Deel opslagaccounts met meerdere VM's niet in dezelfde beschikbaarheidsset. VM's in verschillende beschikbaarheidssets kunnen een gegevensaccount delen, zolang de aanbevolen procedures worden gevolgd ![Foutdomeinen van een onbeheerde schijf](./media/virtual-machines-common-manage-availability/umd-updated.png)

## <a name="use-scheduled-events-to-proactively-respond-to-vm-impacting-events"></a>Geplande gebeurtenissen gebruiken om proactief te reageren op gebeurtenissen die invloed hebben op VM's

Wanneer u zich abonneert op [geplande gebeurtenissen](./linux/scheduled-events.md), wordt uw virtuele machine op de hoogte gesteld van aanstaande onderhoudsgebeurtenissen die van invloed kunnen zijn op uw VM. Wanneer geplande gebeurtenissen zijn ingeschakeld, krijgt de virtuele machine een minimale hoeveelheid tijd voordat de onderhoudsactiviteit wordt uitgevoerd. Zo kunnen host-OS-updates die van invloed zijn op uw virtuele machine, in de wachtrij worden geplaatst als gebeurtenissen waarvoor de impact wordt aangegeven, evenals een tijd waarop het onderhoud wordt uitgevoerd als er geen actie wordt ondernomen. Planningsgebeurtenissen worden ook in de wachtrij geplaatst wanneer Azure een onmiddellijke hardwarefout detecteert die van invloed kan zijn op uw virtuele machine, zodat u kunt bepalen wanneer het herstel moet worden uitgevoerd. Klanten kunnen de gebeurtenis gebruiken om taken uit te voeren vóór het onderhoud, zoals het opslaan van de status, een failover-overschakeling uitvoeren naar de secundaire VM, enzovoort. Nadat u uw logica hebt voltooid voor het op de juiste wijze verwerken van de onderhoudsgebeurtenis, kunt u de uitstaande geplande gebeurtenis goedkeuren zodat het platform door kan gaan met onderhoud.


## <a name="combine-a-load-balancer-with-availability-zones-or-sets"></a>Een load balancer combineren met beschikbaarheidszones of -sets
Combineer de [Azure Load Balancer](../load-balancer/load-balancer-overview.md) met een beschikbaarheidszone of -set om uw toepassingen zo stabiel mogelijk te maken. De Azure Load Balancer verdeelt het verkeer tussen meerdere virtuele machines. De Azure Load Balancer is voor virtuele machines uit de prijscategorie Standard bij de prijs inbegrepen. De Azure Load Balancer is niet bij alle prijscategorieën voor virtuele machines inbegrepen. Zie **virtuele machines met taak verdeling** voor [Linux](linux/tutorial-load-balancer.md) of [Windows](windows/tutorial-load-balancer.md)voor meer informatie over taak verdeling voor uw virtuele machines.

Als de load balancer niet is geconfigureerd om het verkeer te verdelen over meerdere virtuele machines, heeft iedere geplande onderhoudsgebeurtenis invloed op de enkele virtuele machine die uw verkeer afhandelt, waardoor uw applicatielaag onbeschikbaar wordt. Door meerdere virtuele machines van dezelfde laag onder te brengen bij dezelfde load balancer en beschikbaarheidsset, zorgt u ervoor dat verkeer altijd door ten minste één instantie kan worden afgehandeld.

Voor een zelf studie over het verdelen van de belasting over beschikbaarheids zones, Zie [zelf studie: Load Balancing vm's over beschikbaarheids zones met een Standard Load Balancer met behulp van de Azure Portal](../load-balancer/tutorial-load-balancer-standard-public-zone-redundant-portal.md).


## <a name="next-steps"></a>Volgende stappen
Zie [virtuele machines met taak verdeling](../load-balancer/load-balancer-overview.md)voor meer informatie over taak verdeling voor uw virtuele machines.
