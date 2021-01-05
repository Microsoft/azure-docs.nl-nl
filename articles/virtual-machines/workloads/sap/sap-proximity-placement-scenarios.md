---
title: Plaatsings groepen voor Azure nabij voor SAP-toepassingen | Microsoft Docs
description: Beschrijft SAP-implementatie scenario's met Azure proximity placement groups
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/29/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1e6aaf1b37073bf93e0aca8237161bf11af3a872
ms.sourcegitcommit: 42922af070f7edf3639a79b1a60565d90bb801c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2020
ms.locfileid: "97827220"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>Azure proximity placement groups voor optimale netwerk latentie met SAP-toepassingen
SAP-toepassingen die zijn gebaseerd op de architectuur van SAP NetWeaver of SAP S/4HANA, zijn gevoelig voor netwerk latentie tussen de SAP-toepassingslaag en de SAP-gegevenslaag. Deze gevoeligheid is het resultaat van de meeste bedrijfs logica die wordt uitgevoerd in de toepassingslaag. Omdat de SAP-toepassingslaag de bedrijfs logica uitvoert, worden query's naar de data base-laag met een hoge frequentie, met een snelheid van duizenden of tien tallen per seconde, uitgegeven. In de meeste gevallen is de aard van deze query's eenvoudig. Ze kunnen vaak worden uitgevoerd op de database laag in 500 micro seconden of minder.

De tijd die is besteed aan het netwerk om een dergelijke query vanuit de toepassingslaag naar de databaserol te verzenden, heeft een grote invloed op de tijd die nodig is om bedrijfs processen uit te voeren. Deze gevoeligheid voor netwerk latentie is waarom u mogelijk bepaalde maximale netwerk latentie in SAP-implementatie projecten wilt opleveren. Zie [SAP Note #1100926-Veelgestelde vragen: netwerk prestaties](https://launchpad.support.sap.com/#/notes/1100926/E) voor richt lijnen voor het classificeren van de netwerk latentie.

In veel Azure-regio's is het aantal data centers verg root. Tegelijkertijd gebruiken klanten, met name voor hoogwaardige SAP-systemen, meer speciale VM-Sku's van de M-of Mv2-serie of in HANA grote instanties. Deze typen virtuele machines van Azure zijn niet altijd beschikbaar in alle data centers die een Azure-regio aanvullen. Deze feiten kunnen kansen creëren om de netwerk latentie te optimaliseren tussen de SAP-toepassingslaag en de SAP-DBMS-laag.

Azure biedt [proximity placement groups](../../linux/co-location.md)om u de mogelijkheid te bieden om de netwerk latentie te optimaliseren. Proximity-plaatsings groepen kunnen worden gebruikt om de groepering van verschillende VM-typen in één Azure-Data Center af te dwingen, zodat de netwerk latentie tussen deze verschillende VM-typen optimaal kan worden. Tijdens de implementatie van de eerste VM in een dergelijke proximity-plaatsings groep, wordt de VM gebonden aan een specifiek Data Center. Het gebruik van de constructie heeft als doel om te voor komen dat er een aantal beperkingen zijn:

- U kunt niet aannemen dat alle Azure VM-typen beschikbaar zijn in elke en alle Azure-data centers. Als gevolg hiervan kan de combi natie van verschillende VM-typen binnen één proximity-plaatsings groep worden beperkt. Deze beperkingen doen zich voor omdat de host-hardware die nodig is voor het uitvoeren van een bepaald VM-type, mogelijk niet aanwezig is in het Data Center waarop de plaatsings groep is geïmplementeerd
- Bij het wijzigen van de grootte van onderdelen van de virtuele machines die zich binnen één proximity-plaatsings groep bevinden, kunt u niet automatisch aannemen dat het nieuwe VM-type beschikbaar is in hetzelfde Data Center als de andere virtuele machines die deel uitmaken van de plaatsings groep voor nabijheid
- Als de virtuele machine van Azure wordt uitgesteld, kunnen bepaalde Vm's van een proximity-plaatsings groep worden geforceerd naar een ander Azure-Data Center. Lees voor meer informatie over deze situatie het document documenten [samen zoeken voor verbeterde latentie](../../linux/co-location.md#planned-maintenance-and-proximity-placement-groups)  

> [!IMPORTANT]
> Als gevolg van de mogelijke beperkingen moeten proximity-plaatsings groepen worden gebruikt:
>
> - Alleen wanneer dit nodig is
> - Alleen op granulatie van één SAP-systeem en niet voor een volledig systeem landschap of een volledig SAP-landschap
> - Een manier om de verschillende VM-typen en het aantal Vm's binnen een proximity-plaatsings groep naar een minimum te beperken

Stel dat u virtuele machines implementeert door Beschikbaarheidszones op te geven en dezelfde Beschikbaarheidszones te selecteren. de netwerk latentie tussen deze Vm's moet voldoende zijn voor het uitvoeren van SAP NetWeaver en S/4HANA-systemen met de prestaties en door voer. Deze veronderstelling is onafhankelijk van het feit of een bepaalde zone is opgebouwd uit één Data Center of meerdere data centers. De enige reden voor het gebruik van proximity placement groups in zonegebonden-implementaties is het geval dat u virtuele Azure-beschikbaarheids sets wilt toewijzen in combi natie met zonegebonden geïmplementeerde Vm's.


## <a name="what-are-proximity-placement-groups"></a>Wat zijn proximity placement groups? 
Een Azure proximity-plaatsings groep is een logische constructie. Wanneer een proximity-plaatsings groep is gedefinieerd, is deze gebonden aan een Azure-regio en een Azure-resource groep. Wanneer Vm's zijn geïmplementeerd, wordt naar een plaatsings groep voor nabijheid verwezen door:

- De eerste virtuele machine van Azure die is geïmplementeerd in het Data Center. U kunt de eerste virtuele machine beschouwen als een ' scope-VM ' die is geïmplementeerd in een Data Center op basis van Azure-toewijzings algoritmen die uiteindelijk worden gecombineerd met de gebruikers definities voor een specifieke beschikbaarheids zone.
- Alle volgende Vm's die worden geïmplementeerd, verwijzen naar de plaatsings groep, om alle vervolgens geïmplementeerde Azure-Vm's in hetzelfde Data Center als de eerste virtuele machine te plaatsen.

> [!NOTE]
> Als er geen host-hardware is geïmplementeerd waardoor een specifiek VM-type kan worden uitgevoerd in het Data Center waarin de eerste VM is geplaatst, mislukt de implementatie van het aangevraagde VM-type. Er wordt een fout bericht weer gegeven.

Aan één [Azure-resource groep](../../../azure-resource-manager/management/manage-resources-portal.md) kunnen meerdere proximity-plaatsings groepen worden toegewezen. Een proximity-plaatsings groep kan maar aan één Azure-resource groep worden toegewezen.


## <a name="proximity-placement-groups-with-sap-systems-that-use-only-azure-vms"></a>Proximity-plaatsings groepen met SAP-systemen die alleen virtuele Azure-machines gebruiken
Voor de meeste SAP NetWeaver en S/4HANA-systeem implementaties in Azure worden geen [grote exemplaren van Hana](./hana-overview-architecture.md)gebruikt. Voor implementaties die geen gebruik maken van HANA grote instanties, is het belang rijk om optimale prestaties te bieden tussen de SAP-toepassingsobjectlaag en de DBMS-laag. Als u dit wilt doen, definieert u een Azure proximity-plaatsings groep alleen voor het systeem.

In de meeste implementaties van klanten bouwen klanten één [Azure-resource groep](../../../azure-resource-manager/management/manage-resources-portal.md) voor SAP-systemen. In dat geval is er een een-op-een-relatie tussen, bijvoorbeeld de resource groep productie ERP-systeem en de plaatsings groep voor de Proximity. In andere gevallen organiseren klanten hun resource groepen horizon taal en verzamelen ze alle productie systemen in één resource groep. In dit geval hebt u een een-op-veel-relatie tussen de resource groep voor productie SAP-systemen en verschillende proximity-plaatsings groepen voor uw productie SAP ERP, SAP BW, enzovoort.

Vermijd het bundelen van meerdere SAP-productie-of niet-productie systemen in één proximity-plaatsings groep. Wanneer een klein aantal SAP-systemen of een SAP-systeem en sommige omringende toepassingen netwerk communicatie met een lage latentie nodig hebben, kunt u overwegen om deze systemen te verplaatsen naar één proximity-plaatsings groep. Vermijd bundels van systemen omdat de meer systemen die u in een proximity-plaatsings groep hebt gegroepeerd, hoger zijn dan de kans:

- U hebt een VM-type nodig dat niet kan worden uitgevoerd in het specifieke Data Center waarin de Proximity-plaatsings groep ligt.
- Deze bronnen van niet-mainstream Vm's, zoals virtuele machines uit de M-serie, kunnen uiteindelijk niet worden afgehandeld wanneer u meer nodig hebt omdat u gedurende een periode software toevoegt aan een proximity-plaatsings groep.

De ideale configuratie, zoals beschreven, ziet er als volgt uit:

![Proximity-plaatsings groepen met alleen virtuele Azure-machines](./media/sap-proximity-placement-scenarios/ppg-for-all-azure-vms.png)

In dit geval worden enkelvoudige SAP-systemen in één resource groep gegroepeerd, met één proximity-plaatsings groep elk. Er is geen afhankelijkheid van het gebruik van HANA scale-out-of DBMS-schaal configuraties.

## <a name="proximity-placement-groups-and-hana-large-instances"></a>Proximity-plaatsings groepen en HANA grote instanties
Als sommige van uw SAP-systemen zijn gebaseerd op [Hana grote instanties](./hana-overview-architecture.md) voor de toepassingslaag, kunt u aanzienlijke verbeteringen in de netwerk latentie ervaren tussen de Hana-eenheid voor grote instanties en virtuele Azure-machines wanneer u gebruikmaakt van Hana grote instanties-eenheden die zijn geïmplementeerd in [revisie 4 rijen of stem pels](./hana-network-architecture.md#networking-architecture-for-hana-large-instance). Een verbetering is dat HANA-eenheden voor grote instanties, zoals ze zijn geïmplementeerd, implementeren met een proximity-plaatsings groep. U kunt die proximity-plaatsings groep gebruiken om uw Application Layer-Vm's te implementeren. Als gevolg hiervan worden deze Vm's geïmplementeerd in hetzelfde Data Center als de host van uw HANA-eenheid voor grote exemplaren.

Als u wilt bepalen of uw HANA-eenheid voor grote instanties wordt geïmplementeerd in een stempel of rij met revisie 4, raadpleegt u het artikel [Azure Hana-besturings element voor grote instanties via Azure Portal](./hana-li-portal.md#look-at-attributes-of-single-hli-unit). In het overzicht kenmerken van de eenheid voor grote instanties van HANA, kunt u ook de naam van de plaatsings groep bepalen, omdat deze is gemaakt toen de eenheid van de HANA-grote instanties werd geïmplementeerd. De naam die wordt weer gegeven in de kenmerken overzicht is de naam van de plaatsings groep waar u uw Application Layer-Vm's moet implementeren in.

In vergelijking met SAP-systemen die alleen virtuele Azure-machines gebruiken, hebt u bij het gebruik van HANA grote instanties minder flexibiliteit bij het bepalen van het aantal te gebruiken [Azure-resource groepen](../../../azure-resource-manager/management/manage-resources-portal.md) . Alle HANA-eenheden voor grote instanties van een in een bron met een [Hana grote instanties](./hana-know-terms.md) worden gegroepeerd in één resource groep, zoals beschreven in [dit artikel](./hana-li-portal.md#display-of-hana-large-instance-units-in-the-azure-portal). Tenzij u in verschillende tenants implementeert om bijvoorbeeld productie-en niet-productie systemen of andere systemen te scheiden, worden al uw HANA grote instanties-eenheden geïmplementeerd in één HANA grote instanties Tenant. Deze Tenant heeft een een-op-een-relatie met een resource groep. Er wordt echter een afzonderlijke proximity-plaatsings groep voor elk van de afzonderlijke eenheden gedefinieerd.

Als gevolg hiervan zijn de relaties tussen Azure-resource groepen en proximity-plaatsings groepen voor één Tenant, zoals hier wordt weer gegeven:

![Proximity-plaatsings groepen en HANA grote instanties](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)

## <a name="example-of-deployment-with-proximity-placement-groups"></a>Voor beeld van een implementatie met proximity-plaatsings groepen
Hieronder volgen enkele Power shell-opdrachten die u kunt gebruiken voor het implementeren van uw Vm's met Azure proximity placement groups.

De eerste stap, nadat u zich hebt aangemeld bij [Azure Cloud shell](https://azure.microsoft.com/features/cloud-shell/), is om te controleren of u zich in het Azure-abonnement bevindt dat u wilt gebruiken voor de implementatie:

<pre><code>
Get-AzureRmContext
</code></pre>

Als u een ander abonnement wilt wijzigen, kunt u dit doen door de volgende opdracht uit te voeren:

<pre><code>
Set-AzureRmContext -Subscription "my PPG test subscription"
</code></pre>

Maak een nieuwe Azure-resource groep door deze opdracht uit te voeren:

<pre><code>
New-AzResourceGroup -Name "myfirstppgexercise" -Location "westus2"
</code></pre>

Maak de nieuwe proximity-plaatsings groep door deze opdracht uit te voeren:

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose" -Location "westus2"
</code></pre>

Implementeer uw eerste VM in de plaatsings groep voor nabijheid met behulp van een opdracht zoals deze:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

Met de voor gaande opdracht wordt een VM op basis van Windows geïmplementeerd. Nadat deze VM-implementatie is voltooid, wordt het Data Center-bereik van de Proximity-plaatsings groep gedefinieerd in de Azure-regio. Alle volgende VM-implementaties die verwijzen naar de plaatsings groep voor nabijheid, zoals weer gegeven in de voor gaande opdracht, worden geïmplementeerd in hetzelfde Azure-Data Center, zolang het VM-type kan worden gehost op hardware die in dat Data Center wordt geplaatst en de capaciteit voor dat VM-type beschikbaar is.

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>Beschikbaarheids sets en Beschikbaarheidszones combi neren met proximity-plaatsings groepen
Een van de nadelen van het gebruik van Beschikbaarheidszones voor SAP-systeem implementaties is dat u de SAP-toepassingslaag niet kunt implementeren met behulp van beschikbaarheids sets in de specifieke zone. U wilt dat de SAP-toepassingslaag wordt geïmplementeerd in dezelfde zones als de DBMS-laag. Het verwijzen naar een beschikbaarheids zone en een beschikbaarheidsset bij het implementeren van één virtuele machine wordt niet ondersteund. U bent dus eerder gedwongen om uw toepassingslaag te implementeren door te verwijzen naar een zone. U hebt de mogelijkheid om te controleren of de virtuele machines van de toepassingslaag zijn verdeeld over verschillende update-en fout domeinen.

Door gebruik te maken van proximity-plaatsings groepen kunt u deze beperking overs Laan. Hier volgt de implementatie volgorde:

- Maak een proximity-plaatsings groep.
- Implementeer uw anker-VM, meestal de DBMS-server, door te verwijzen naar een beschikbaarheids zone.
- Maak een beschikbaarheidsset die verwijst naar de Proximity-groep van Azure. (Zie de opdracht verderop in dit artikel.)
- Implementeer de Application Layer-Vm's door te verwijzen naar de beschikbaarheidsset en de plaatsings groep.

In plaats van de eerste virtuele machine te implementeren, zoals wordt beschreven in de vorige sectie, verwijst u naar een beschikbaarheids zone en de locatie van de Proximity-groep wanneer u de virtuele machine implementeert:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppganchorvm" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "letsgetclose" -Size "Standard_E16_v3"
</code></pre>

Een geslaagde implementatie van deze virtuele machine host het data base-exemplaar van het SAP-systeem in één beschikbaarheids zone. Het bereik van de plaatsings groep voor proximity is vastgelegd in een van de data centers die de beschikbaarheids zone vertegenwoordigen die u hebt gedefinieerd.

Stel dat u de virtuele machines van de centrale Services op dezelfde manier als de DBMS-Vm's implementeert. deze verwijzen naar dezelfde zone of zones en dezelfde proximity-plaatsings groepen. In de volgende stap moet u de beschikbaarheids sets maken die u wilt gebruiken voor de toepassingslaag van uw SAP-systeem.

Definieer en maak de plaatsings groep voor nabijheid. Voor de opdracht voor het maken van de beschikbaarheidsset is een aanvullende verwijzing naar de groeps-ID voor proximity-plaatsing (niet de naam) vereist. U kunt de ID van de plaatsings groep voor proximity ophalen met behulp van de volgende opdracht:

<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "myfirstppgexercise" -Name "letsgetclose"
</code></pre>

Wanneer u de beschikbaarheidsset maakt, moet u extra para meters overwegen wanneer u beheerde schijven gebruikt (standaard tenzij anders opgegeven) en proximity placement groups:

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "myfirstppgexercise" -Name "myppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

In het ideale geval moet u drie fout domeinen gebruiken. Maar het aantal ondersteunde fout domeinen kan variëren van regio tot regio. In dit geval is het maximum aantal fout domeinen dat mogelijk is voor de specifieke regio's twee. Als u uw Application Layer-Vm's wilt implementeren, moet u een verwijzing naar de naam van de beschikbaarheidsset en de naam van de locatie van de plaatsings groep toevoegen, zoals hier wordt weer gegeven:

<pre><code>
New-AzVm -ResourceGroupName "myfirstppgexercise" -Name "myppgavsetappvm" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "letsgetclose" -Size "Standard_DS11_v2"
</code></pre>

Het resultaat van deze implementatie is:
- Een DBMS-laag en centrale Services voor uw SAP-systeem dat zich in een specifieke beschikbaarheids zone of Beschikbaarheidszones bevindt.
- Een SAP-toepassingslaag die zich bevindt in beschikbaarheids sets in dezelfde Azure-Data Centers als de DBMS-VM of Vm's.

> [!NOTE]
> Omdat u een DBMS-VM in één zone en de tweede DBMS-VM in een andere zone implementeert om een configuratie met een hoge Beschik baarheid te maken, hebt u een andere proximity-plaatsings groep nodig voor elk van de zones. Dit geldt ook voor alle beschikbaarheids sets die u gebruikt.

## <a name="move-an-existing-system-into-proximity-placement-groups"></a>Een bestaand systeem verplaatsen naar proximity-plaatsings groepen
Als er al SAP-systemen zijn geïmplementeerd, wilt u mogelijk de netwerk latentie van een aantal van uw kritieke systemen optimaliseren en de toepassingslaag en de DBMS-laag in hetzelfde Data Center vinden. Als u de Vm's van een volledige Azure-beschikbaarheidsset wilt verplaatsen naar een bestaande proximity-plaatsings groep die al is scoped, moet u alle Vm's van de beschikbaarheidsset afsluiten en de beschikbaarheidsset toewijzen aan de bestaande plaatsings groep via Azure Portal, Power shell of CLI. Als u een virtuele machine die geen deel uitmaakt van een beschikbaarheidsset, wilt verplaatsen naar een bestaande proximity-plaatsings groep, hoeft u de virtuele machine alleen af te sluiten en toe te wijzen aan een bestaande plaatsings groep. 


## <a name="next-steps"></a>Volgende stappen
Bekijk de documentatie:

- [SAP-workloads op Azure: controle lijst voor planning en implementatie](./sap-deployment-checklist.md)
- [Voor beeld: Vm's op proximity-plaatsings groepen implementeren met behulp van Azure CLI](../../linux/proximity-placement-groups.md)
- [Voor beeld: Vm's implementeren op proximity-plaatsings groepen met Power shell](../../windows/proximity-placement-groups.md)
- [Overwegingen voor de implementatie van Azure Virtual Machines DBMS voor SAP-workloads](./dbms_guide_general.md)