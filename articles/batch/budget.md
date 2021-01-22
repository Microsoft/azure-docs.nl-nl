---
title: Kostenanalyse en budgetten
description: Meer informatie over het verkrijgen van een kosten analyse, het instellen van een budget en het verlagen van de kosten voor de onderliggende reken resources en software licenties die worden gebruikt voor het uitvoeren van uw batch-workloads.
ms.topic: how-to
ms.date: 01/21/2021
ms.openlocfilehash: 1a950f23b7ecee11dd7da5414b7eed9e87277850
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98679307"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Kosten analyse en budgetten voor Azure Batch

Er zijn geen kosten verbonden aan het gebruik van Azure Batch zelf, hoewel er kosten voor de onderliggende reken resources en software licenties kunnen worden gebruikt om batch-workloads uit te voeren. Kosten kunnen worden gemaakt op basis van virtuele machines (Vm's) in een pool, gegevens overdracht van de virtuele machine of een invoer-of uitvoer gegevens die in de Cloud zijn opgeslagen. Dit onderwerp helpt u inzicht te krijgen in de herkomst van de kosten, hoe u een budget instelt voor een batch-pool of-account en hoe u de kosten voor batch-workloads kunt verlagen.

## <a name="batch-resources"></a>Batch-resources

Virtuele machines zijn de belangrijkste bronnen die worden gebruikt voor batch verwerking. De kosten voor het gebruik van Vm's voor batch worden berekend op basis van het type, de hoeveelheid en de duur van het gebruik. De facturerings opties voor de virtuele machine omvatten [betalen per gebruik](https://azure.microsoft.com/offers/ms-azr-0003p/) of [reserve ring](../cost-management-billing/reservations/save-compute-costs-reservations.md) (vooraf betalen). Beide betalings opties hebben verschillende voor delen, afhankelijk van de reken werk belasting en beïnvloeden uw factuur anders.

Wanneer toepassingen worden geïmplementeerd op batch-knoop punten (Vm's) met behulp van [toepassings pakketten](batch-application-packages.md), worden er kosten in rekening gebracht voor de Azure storage resources die door uw toepassing worden gebruikt. U wordt ook gefactureerd voor de opslag van invoer-of uitvoer bestanden, zoals bron bestanden en andere logboek gegevens. Over het algemeen zijn de kosten voor opslag gegevens die aan de batch zijn gekoppeld, veel lager dan de kosten van reken resources. Elke VM in een groep die is gemaakt met de configuratie van de [virtuele machine](nodes-and-pools.md#virtual-machine-configuration) heeft een gekoppelde besturingssysteem schijf die gebruikmaakt van door Azure beheerde schijven. Azure-beheerde schijven hebben extra kosten en andere schijf prestatie lagen hebben ook verschillende kosten.

Batch-Pools gebruiken netwerk bronnen. Met name voor **VirtualMachineConfiguration** -groepen worden standaard load balancers gebruikt, waarvoor statische IP-adressen zijn vereist. De load balancers die door batch worden gebruikt, zijn zichtbaar voor accounts voor **gebruikers abonnementen** , maar zijn niet zichtbaar voor **batch-service** accounts. Voor de standaard load balancers worden kosten in rekening gebracht voor alle gegevens die worden door gegeven aan en van Vm's uit de batch-groep; Selecteer Batch-Api's voor het ophalen van gegevens uit pool knooppunten (zoals taak/knooppunt bestand ophalen), taak toepassings pakketten, resource/uitvoer bestanden en container installatie kopieën.

### <a name="additional-services"></a>Extra services

Services met inbegrip van Vm's en opslag kunnen de kosten van uw batch-account in rekening worden.

Andere services die vaak worden gebruikt in combi natie met batch kunnen het volgende omvatten:

- Application Insights
- Data Factory
- Azure Monitor
- Virtual Network
- Vm's met grafische toepassingen

Afhankelijk van de services die u met uw batch-oplossing gebruikt, kunnen er extra kosten in rekening worden gebracht. Raadpleeg de [prijs calculator](https://azure.microsoft.com/pricing/calculator/) om de kosten van elke extra service te bepalen.

## <a name="cost-analysis-and-budget-for-a-pool"></a>Kosten analyse en budget voor een pool

In de Azure Portal kunt u budgetten en uitgaven voor uw batch-Pools of batch-accounts maken. Budgetten en waarschuwingen zijn handig voor het melden van belanghebbenden van eventuele Risico's van overuitgave, hoewel het mogelijk is dat er een vertraging optreedt in bestedings waarschuwingen en een budget enigszins overschrijdt.

In dit voor beeld wordt de kosten analyse van een afzonderlijke batch-pool weer gegeven.

1. In de Azure Portal, typt of selecteert u **Cost Management en facturering** .
1. Selecteer uw abonnement in het gedeelte **facturerings bereik** .
1. In **Cost Management** selecteert u **Kostenanalyse**.
1. Selecteer **filter toevoegen**. Selecteer in de eerste vervolg keuzelijst **resource**
1. Selecteer in de tweede vervolg keuzelijst de batch-pool. Wanneer de pool is geselecteerd, ziet u de kosten analyse voor de groep, vergelijkbaar met het voor beeld dat hier wordt weer gegeven.
    ![Scherm opname van de kosten analyse van een pool in de Azure Portal.](./media/batch-budget/pool-cost-analysis.png)

In de analyse van de resulterende kosten worden de kosten van de pool weer gegeven, evenals de resources die bijdragen aan deze kosten. In dit voor beeld zijn de virtuele machines die in de pool worden gebruikt de meest dure resource.

Als u een budget voor de pool wilt maken, selecteert u **budget: geen** en selecteert u **nieuwe budget >maken**. Gebruik nu het venster om [een specifiek budget](../cost-management-billing/costs/tutorial-acm-create-budgets.md) voor uw groep te configureren.

> [!NOTE]
> Azure Batch is gebaseerd op Azure Cloud Services en Azure Virtual Machines technologie. Wanneer u **Cloud Services configuratie** kiest, worden kosten in rekening gebracht op basis van de Cloud Services prijs structuur. Wanneer u de **configuratie van de virtuele machine** kiest, worden kosten in rekening gebracht op basis van de virtual machines prijs structuur. In het voor beeld op deze pagina wordt de configuratie van de **virtuele machine** gebruikt, die wordt aanbevolen voor de meeste batch-Pools.

## <a name="minimize-cost"></a>Kosten minimaliseren

Het gebruik van verschillende Vm's en Azure-Services voor langere Peri Oden kan kostbaar zijn. Overweeg het gebruik van deze strategieën om de efficiëntie van uw workloads te maximaliseren en uw kosten te verlagen.

### <a name="low-priority-virtual-machines"></a>Virtuele machines met lage prioriteit

[Virtuele machines met lage prioriteit](batch-low-pri-vms.md) verlagen de kosten van batch-workloads door gebruik te maken van de capaciteit van de overschot op Azure. Wanneer u virtuele machines met lage prioriteit in uw Pools opgeeft, gebruikt batch dit surplus om uw werk belasting uit te voeren. Er kunnen aanzienlijke kosten besparingen zijn bij het gebruik van Vm's met lage prioriteit in plaats van toegewezen Vm's.

### <a name="virtual-machine-os-disk-type"></a>Schijf type van het besturings systeem van de virtuele machine

Azure biedt meerdere [schijf typen voor VM-besturings systemen](../virtual-machines/disks-types.md). De meeste VM-Series hebben grootten die ondersteuning bieden voor zowel Premium als standaard opslag. Wanneer de VM-grootte voor een groep is geselecteerd, worden met de batch Premium SSD-besturingssysteem schijven geconfigureerd. Wanneer de VM-grootte ' niet-s ' is geselecteerd, wordt het type goed koper, standaard HDD-schijf gebruikt. Premium SSD-besturingssysteem schijven worden bijvoorbeeld gebruikt voor `Standard_D2s_v3` en standaard schijven voor harde schijven worden gebruikt voor `Standard_D2_v3` .

Premium-SSD-besturingssysteem schijven zijn duurder, maar hebben wel betere prestaties. Vm's met Premium-schijven kunnen iets sneller starten dan virtuele machines met standaard schijven voor harde schijven. Met batch wordt de besturingssysteem schijf vaak niet gebruikt, omdat de toepassingen en taak bestanden zich bevinden op de tijdelijke SSD-schijf van de virtuele machine. Daarom kunt u de VM-grootte van ' niet-s ' vaak selecteren om te voor komen dat de hogere kosten voor de Premium SSD worden betaald die worden ingericht wanneer de VM-grootte van de virtuele machine wordt opgegeven.

### <a name="reserved-virtual-machine-instances"></a>Gereserveerde exemplaren van virtuele machines

Als u batch wilt gebruiken gedurende een lange periode, kunt u de kosten van Vm's verlagen door [Azure Reservations](../cost-management-billing/reservations/save-compute-costs-reservations.md) voor uw workloads te gebruiken. Een reserverings tarief is aanzienlijk lager dan het tarief voor betalen naar gebruik. Voor instanties van virtuele machines die worden gebruikt zonder een reserve ring, wordt gefactureerd op basis van het betalen naar gebruik-tarief. Wanneer u een reserve ring koopt, wordt de reserverings korting toegepast.

### <a name="automatic-scaling"></a>Automatische schaalaanpassing

Met [automatisch schalen](batch-automatic-scaling.md) wordt het aantal vm's in de batch-pool dynamisch geschaald op basis van de vereisten van de huidige taak. Door de pool te schalen op basis van de levens duur van een taak, zorgt u er automatisch voor dat virtuele machines worden geschaald en alleen worden gebruikt wanneer er een taak wordt uitgevoerd. Wanneer de taak is voltooid of wanneer er geen taken zijn, worden de Vm's automatisch omlaag geschaald om reken resources op te slaan. Met schalen kunt u de totale kosten van uw batch-oplossing verlagen door alleen de benodigde resources te gebruiken.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [batch-api's en hulpprogram ma's](batch-apis-tools.md) die beschikbaar zijn voor het maken en bewaken van batch-oplossingen.  
- Meer informatie over [het gebruik van vm's met lage prioriteit met batch](batch-low-pri-vms.md).
