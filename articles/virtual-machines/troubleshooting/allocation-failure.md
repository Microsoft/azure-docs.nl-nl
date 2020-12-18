---
title: Problemen met Azure VM-toewijzings fouten oplossen | Microsoft Docs
description: Toewijzings fouten oplossen bij het maken, opnieuw opstarten of het wijzigen van het formaat van een virtuele machine in azure
services: virtual-machines
documentationcenter: ''
author: DavidCBerry13
manager: felixwu
editor: ''
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: 1ef41144-6dd6-4a56-b180-9d8b3d05eae7
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 11/06/2020
ms.author: daberry
ms.openlocfilehash: 79bc043a991404a3ee9da954b9639bf1a41f2c51
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2020
ms.locfileid: "94365870"
---
# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-vms-in-azure"></a>Toewijzingsproblemen bij het maken, opnieuw starten of het wijzigen van het formaat van VM’s in Azure oplossen

Wanneer u een virtuele machine (VM) maakt, opnieuw opstarten gestopt (toewijzing opgeheven) Vm's of het formaat van een VM wijzigt, Microsoft Azure worden reken bronnen toegewezen aan uw abonnement. We investeren voortdurend in extra infra structuur en functies om ervoor te zorgen dat alle VM-typen altijd beschikbaar zijn voor de ondersteuning van de vraag van klanten. Het is echter mogelijk dat u af en toe problemen met de resource toewijzing ondervindt vanwege een ongekende groei in de vraag naar Azure-Services in bepaalde regio's. Dit probleem kan zich voordoen wanneer u virtuele machines in een regio probeert te maken of te starten terwijl de virtuele machines de volgende fout code en dit bericht weer geven:

**Fout code** : AllocationFailed of ZonalAllocationFailed

**Fout bericht** : ' toewijzing is mislukt. Er zijn onvoldoende capaciteit voor de aangevraagde VM-grootte in deze regio. Meer informatie over het verbeteren van de kans op een succes volle toewijzing op https://aka.MS/Allocation-guidance "

> [!NOTE]
> Als u een virtuele-machine Scale set (VMSS) wilt oplossen, is het proces hetzelfde als een standaard-VM. Volg de instructies in dit artikel om het probleem op te lossen.
> 
>**Fout bericht** : ' toewijzing is mislukt. Als u een nieuwe VM wilt toevoegen aan een Schaalset voor virtuele machines met één plaatsings groep of als u een bestaande virtuele machine in een Schaalset voor virtuele machines met één plaatsings groep wilt bijwerken, moet u er rekening mee houden dat een dergelijke toewijzing tot één cluster is beperkt en mogelijk is de capaciteit van het cluster niet meer beschikbaar. Lees meer informatie over het verbeteren van de kans op een succes volle toewijzing op http://aka.MS/Allocation-Guidance. "

In dit artikel worden de oorzaken beschreven van enkele algemene toewijzings fouten en worden mogelijke oplossingen voorgesteld.

Als uw Azure-probleem niet in dit artikel wordt behandeld, gaat u naar de [Azure-forums op MSDN en stack overflow](https://azure.microsoft.com/support/forums/). U kunt uw probleem in deze forums of op @AzureSupport Twitter plaatsen. U kunt ook een Azure-ondersteunings aanvraag indienen door ondersteuning te selecteren op de [ondersteunings](https://azure.microsoft.com/support/options/) site van Azure.

Totdat uw voorkeurs VM-type beschikbaar is in uw voorkeurs regio, adviseren we klanten die implementatie problemen ondervinden om de richt lijnen in de volgende tabel als tijdelijke oplossing te beschouwen. 

Bepaal het scenario dat het beste overeenkomt met uw situatie en voer de toewijzings aanvraag vervolgens opnieuw uit met behulp van de bijbehorende voorgestelde tijdelijke oplossing om de kans op een succes volle toewijzing te verg Roten. U kunt ook later altijd opnieuw proberen. Dit komt doordat er mogelijk voldoende resources zijn vrijgemaakt in het cluster, de regio of de zone om aan uw aanvraag te voldoen. 


## <a name="resize-a-vm-or-add-vms-to-an-existing-availability-set"></a>De grootte van een virtuele machine wijzigen of VM's toevoegen aan een bestaande beschikbaarheidsset

### <a name="cause"></a>Oorzaak

Een aanvraag om het formaat van een virtuele machine of een virtuele machine aan een bestaande beschikbaarheidsset toe te voegen, moet worden geprobeerd op het oorspronkelijke cluster dat als host fungeert voor de bestaande beschikbaarheidsset. De aangevraagde VM-grootte wordt ondersteund door het cluster, maar het cluster heeft mogelijk niet voldoende capaciteit. 

### <a name="workaround"></a>Tijdelijke oplossing

Als de VM deel kan uitmaken van een andere beschikbaarheidsset, maakt u een virtuele machine in een andere beschikbaarheidsset (in dezelfde regio). Deze nieuwe VM kan vervolgens worden toegevoegd aan hetzelfde virtuele netwerk.

Stop de toewijzing van alle virtuele machines in dezelfde beschikbaarheidsset en start deze vervolgens opnieuw.
Stoppen: Klik op resource groepen > [uw resource groep] > resources > [uw beschikbaarheidsset] > Virtual Machines > [uw virtuele machine] > gestopt.
Nadat alle Vm's zijn gestopt, selecteert u de eerste virtuele machine en klikt u vervolgens op starten.
Met deze stap zorgt u ervoor dat een nieuwe toewijzings poging wordt uitgevoerd en dat er een nieuw cluster kan worden geselecteerd dat voldoende capaciteit heeft.

## <a name="restart-partially-stopped-deallocated-vms"></a>Gedeeltelijk gestopte (toewijzing opgeheven) virtuele machines opnieuw opstarten

### <a name="cause"></a>Oorzaak

Gedeeltelijke toewijzing betekent dat u een of meer, maar niet alle virtuele machines in een beschikbaarheidsset hebt gestopt (toewijzing ongedaan gemaakt). Wanneer u de toewijzing van een virtuele machine ongedaan maakt, worden de bijbehorende resources vrijgegeven. Het opnieuw starten van Vm's in een gedeeltelijk toegewezen beschikbaarheidsset is hetzelfde als het toevoegen van Vm's aan een bestaande beschikbaarheidsset. Daarom moet de toewijzings aanvraag worden uitgevoerd op het oorspronkelijke cluster waarop de bestaande beschikbaarheidsset wordt gehost die mogelijk niet voldoende capaciteit heeft.

### <a name="workaround"></a>Tijdelijke oplossing

Stop de toewijzing van alle virtuele machines in dezelfde beschikbaarheidsset en start deze vervolgens opnieuw.
Stoppen: Klik op resource groepen > [uw resource groep] > resources > [uw beschikbaarheidsset] > Virtual Machines > [uw virtuele machine] > gestopt.
Nadat alle Vm's zijn gestopt, selecteert u de eerste virtuele machine en klikt u vervolgens op starten.
Dit zorgt ervoor dat een nieuwe toewijzings poging wordt uitgevoerd en dat er een nieuw cluster kan worden geselecteerd dat voldoende capaciteit heeft.

## <a name="restart-fully-stopped-deallocated-vms"></a>Volledig gestopte (toewijzing opgeheven) virtuele machines opnieuw opstarten

### <a name="cause"></a>Oorzaak

Bij volledige toewijzing betekent dat u alle virtuele machines in een beschikbaarheidsset hebt gestopt (toewijzing ongedaan gemaakt). De toewijzings aanvraag voor het opnieuw opstarten van deze Vm's is gericht op alle clusters die de gewenste grootte in de regio of zone ondersteunen. Wijzig uw toewijzings aanvraag volgens de suggesties in dit artikel en voer de aanvraag opnieuw uit om de kans op voltooiing van de toewijzing te verbeteren. 

### <a name="workaround"></a>Tijdelijke oplossing

Als u oudere VM-reeksen of-grootten gebruikt, zoals Dv1, DSv1, Av1, D15v2 of DS15v2, kunt u overwegen om over te stappen naar nieuwere versies. Bekijk deze aanbevelingen voor specifieke VM-grootten.
Als u niet over de mogelijkheid beschikt om een andere VM-grootte te gebruiken, kunt u het implementeren naar een andere regio binnen hetzelfde geografische gebied. Voor meer informatie over de beschik bare VM-grootten in elke regio op https://aka.ms/azure-regions

Als u beschikbaarheids zones gebruikt, probeert u een andere zone in de regio die mogelijk capaciteit beschikbaar heeft voor de aangevraagde VM-grootte.

Als uw toewijzings aanvraag groot is (meer dan 500 kern geheugens), raadpleegt u de richt lijnen in de volgende secties om de aanvraag op te splitsen in kleinere implementaties.

Probeer [de virtuele machine](./redeploy-to-new-node-windows.md)opnieuw te implementeren. Door de VM opnieuw te implementeren, wordt de virtuele machine toegewezen aan een nieuw cluster binnen de regio.

## <a name="allocation-failures-for-older-vm-sizes-av1-dv1-dsv1-d15v2-ds15v2-etc"></a>Toewijzingsfouten voor oudere VM-formaten (Av1, Dv1, DSv1, D15v2, DS15v2 enz.)

Terwijl we Azure-infra structuur uitbreiden, implementeren we nieuwere hardware die is ontworpen ter ondersteuning van de nieuwste typen virtuele machines. Sommige Vm's van de oudere serie worden niet uitgevoerd op de nieuwste generatie infrastructuur. Daarom kunnen klanten af en toe toewijzings fouten voor deze verouderde Sku's ondervinden. Om dit probleem te voor komen, raden we klanten die gebruikmaken van virtuele machines uit de oude serie, te overwegen over te stappen op de equivalente nieuwere Vm's volgens de volgende aanbevelingen: deze Vm's zijn geoptimaliseerd voor de nieuwste hardware en bieden u de mogelijkheid om te profiteren van betere prijzen en prestaties. 

|Verouderde VM-serie/grootte|Aanbevolen nieuwere VM-serie/-grootte|Meer informatie|
|----------------------|----------------------------|--------------------|
|Av1-serie|[Av2-serie](../av2-series.md)|https://azure.microsoft.com/blog/new-av2-series-vm-sizes/
|Dv1 of DSv1-serie (D1 tot en met D5)|[Dv3 of DSv3-serie](../dv3-dsv3-series.md)|https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/
|Dv1 of DSv1-serie (D11 naar D14)|[Ev3 of ESv3-serie](../ev3-esv3-series.md)|
|D15v2 of DS15v2|Als u een theResource Manager-implementatie model gebruikt om gebruik te kunnen maken van de grotere VM-grootten, overweeg dan om over te stappen op D16v3/DS16v3 of D32v3/DS32v3. Deze zijn ontworpen om te worden uitgevoerd op de nieuwste generatie-hardware. Als u het Resource Manager-implementatie model gebruikt om ervoor te zorgen dat uw VM-exemplaar is geïsoleerd voor hardware die is toegewezen aan één klant, overweeg dan om over te stappen op de nieuwe geïsoleerde VM-grootten, E64i_v3 of E64is_v3, die zijn ontworpen om te worden uitgevoerd op de nieuwste generatie-hardware. |https://azure.microsoft.com/blog/new-isolated-vm-sizes-now-available/

## <a name="allocation-failures-for-large-deployments-more-than-500-cores"></a>Toewijzingsfouten voor grote implementaties (meer dan 500 kernen)

Verminder het aantal exemplaren van de gevraagde VM-grootte en voer de implementatie bewerking opnieuw uit. Daarnaast kunt u voor grotere implementaties de [virtuele-machine schaal sets van Azure](../../virtual-machine-scale-sets/index.yml)evalueren. Het aantal VM-exemplaren kan automatisch worden verg root of verkleind als reactie op de vraag of een gedefinieerd schema, en u hebt een grotere kans op het toewijzen van de toewijzing omdat de implementaties kunnen worden verdeeld over meerdere clusters. 

## <a name="background-information"></a>Achtergrond informatie
### <a name="how-allocation-works"></a>Hoe toewijzing werkt
De servers in Azure-datacenters worden in clusters gepartitioneerd. Normaal gesproken wordt er een toewijzingsaanvraag in meerdere clusters uitgevoerd, maar het is mogelijk dat bepaalde beperkingen van de toewijzingsaanvraag het Azure-platform ertoe dwingen de aanvraag in slechts één cluster uit te voeren. In dit artikel wordt dit als ' vastgemaakt aan een cluster ' genoemd. In diagram 1 hieronder ziet u het geval van een normale toewijzing die wordt geprobeerd in meerdere clusters. Diagram 2 illustreert het geval van een toewijzing die is vastgemaakt aan cluster 2 omdat de bestaande Cloud service CS_1 of beschikbaarheidsset wordt gehost.
![Toewijzings diagram](./media/virtual-machines-common-allocation-failure/Allocation1.png)

### <a name="why-allocation-failures-happen"></a>Waarom toewijzings fouten optreden
Wanneer een toewijzings aanvraag wordt vastgemaakt aan een cluster, is er een hogere kans op het vinden van vrije bronnen omdat de beschik bare resource groep kleiner is. Als uw toewijzings aanvraag is vastgemaakt aan een cluster maar het type van de aangevraagde resource niet wordt ondersteund door dat cluster, mislukt de aanvraag ook als het cluster vrije bronnen heeft. In het volgende diagram 3 ziet u het geval waarin een vastgemaakte toewijzing mislukt, omdat het enige kandidaat-cluster geen vrije resources heeft. Diagram 4 illustreert het geval dat een vastgemaakte toewijzing mislukt omdat het enige kandidaat-cluster de aangevraagde VM-grootte niet ondersteunt, zelfs als het cluster vrije bronnen heeft.

![Fout bij vastgemaakte toewijzing](./media/virtual-machines-common-allocation-failure/Allocation2.png)
