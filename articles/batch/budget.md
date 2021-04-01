---
title: Kosten analyse ophalen en budgetten instellen voor Azure Batch
description: Meer informatie over het verkrijgen van een kosten analyse, het instellen van een budget en het verlagen van de kosten voor de onderliggende reken resources en software licenties die worden gebruikt voor het uitvoeren van uw batch-workloads.
ms.topic: how-to
ms.date: 01/29/2021
ms.openlocfilehash: d1fc2d15a7037e56a8056efa67d2017badb77ffd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99091324"
---
# <a name="get-cost-analysis-and-set-budgets-for-azure-batch"></a>Kosten analyse ophalen en budgetten instellen voor Azure Batch

Dit onderwerp helpt u bij het begrijpen van de kosten die kunnen worden gekoppeld aan Azure Batch, het instellen van een budget voor een batch-pool of-account en manieren om de kosten voor batch-workloads te verlagen.

## <a name="understand-costs-associated-with-batch-resources"></a>Meer informatie over de kosten van batch-resources

Er zijn geen kosten verbonden aan het gebruik van Azure Batch zelf, hoewel er kosten voor de onderliggende reken resources en software licenties kunnen worden gebruikt om batch-workloads uit te voeren. Kosten kunnen worden gemaakt op basis van virtuele machines (Vm's) in een pool, gegevens overdracht van de virtuele machine of een invoer-of uitvoer gegevens die in de Cloud zijn opgeslagen.

### <a name="virtual-machines"></a>Virtuele machines

Virtuele machines zijn de belangrijkste bronnen die worden gebruikt voor batch verwerking. De kosten voor het gebruik van Vm's voor batch worden berekend op basis van het type, de hoeveelheid en de duur van het gebruik. De facturerings opties voor de virtuele machine omvatten [betalen per gebruik](https://azure.microsoft.com/offers/ms-azr-0003p/) of [reserve ring](../cost-management-billing/reservations/save-compute-costs-reservations.md) (vooraf betalen). Beide betalings opties hebben verschillende voor delen, afhankelijk van de reken werk belasting en beïnvloeden uw factuur anders.

Elke VM in een groep die is gemaakt met de configuratie van de [virtuele machine](nodes-and-pools.md#virtual-machine-configuration) heeft een gekoppelde besturingssysteem schijf die gebruikmaakt van door Azure beheerde schijven. Azure-beheerde schijven hebben extra kosten en andere schijf prestatie lagen hebben ook verschillende kosten.

### <a name="storage"></a>Storage

Wanneer toepassingen worden geïmplementeerd op batch-knoop punten (Vm's) met behulp van [toepassings pakketten](batch-application-packages.md), worden er kosten in rekening gebracht voor de Azure storage resources die door uw toepassing worden gebruikt. U wordt ook gefactureerd voor de opslag van invoer-of uitvoer bestanden, zoals bron bestanden en andere logboek gegevens.

Over het algemeen zijn de kosten voor opslag gegevens die aan de batch zijn gekoppeld, veel lager dan de kosten van reken resources.

### <a name="networking-resources"></a>Netwerk bronnen

Batch-Pools gebruiken netwerk bronnen, waarvan sommige kosten zijn gekoppeld. Met name voor virtuele-machine configuratie groepen worden standaard load balancers gebruikt, waarvoor statische IP-adressen zijn vereist. De load balancers die door batch worden gebruikt, zijn zichtbaar voor [accounts](accounts.md#batch-accounts) die zijn geconfigureerd in de modus gebruikers abonnement, maar niet in de batch-service modus.

Voor de standaard load balancers worden kosten in rekening gebracht voor alle gegevens die worden door gegeven aan en van virtuele machines in batch groepen. Selecteer Batch-Api's voor het ophalen van gegevens uit pool knooppunten (zoals taak/knooppunt bestand ophalen), taak toepassings pakketten, resource-en uitvoer bestanden en container installatie kopieën worden ook kosten in rekening gebracht.

### <a name="additional-services"></a>Extra services

Afhankelijk van de services die u met uw batch-oplossing gebruikt, kunnen er extra kosten in rekening worden gebracht. Raadpleeg de [prijs calculator](https://azure.microsoft.com/pricing/calculator/) om de kosten van elke extra service te bepalen. De volgende services worden meestal gebruikt voor batch verwerking, waaronder kosten:

- Application Insights
- Data Factory
- Azure Monitor
- Virtual Network
- Vm's met grafische toepassingen

## <a name="view-cost-analysis-and-create-a-budget-for-a-pool"></a>Kosten analyse weer geven en een budget voor een pool maken

In de Azure Portal kunt u budgetten en uitgaven voor uw batch-Pools of batch-accounts maken. Budgetten en waarschuwingen zijn handig voor het melden van belanghebbenden van eventuele Risico's van overuitgave, hoewel het mogelijk is dat er een vertraging optreedt in bestedings waarschuwingen en een budget enigszins overschrijdt.

> [!NOTE]
> De groep in dit voor beeld maakt gebruik van de configuratie van de **virtuele machine**, die wordt aanbevolen voor de meeste Pools en waarvoor kosten gelden op basis van de virtual machines prijs structuur. Groepen die gebruikmaken van **Cloud Services configuratie** worden in rekening gebracht op basis van de Cloud Services prijs structuur.

### <a name="view-cost-analysis-for-a-batch-pool"></a>Kosten analyse voor een batch-pool weer geven

1. In de Azure Portal, typt of selecteert u **Cost Management en facturering** .
1. Selecteer uw abonnement in het gedeelte **facturerings bereik** .
1. In **Cost Management** selecteert u **Kostenanalyse**.
1. Selecteer **filter toevoegen**. Selecteer in de eerste vervolg keuzelijst **resource**
1. Selecteer in de tweede vervolg keuzelijst de batch-pool. Wanneer de pool is geselecteerd, ziet u de kosten analyse voor de groep, vergelijkbaar met het voor beeld dat hier wordt weer gegeven.
    ![Scherm opname van de kosten analyse van een pool in de Azure Portal.](./media/batch-budget/pool-cost-analysis.png)

In de analyse van de resulterende kosten worden de kosten van de pool weer gegeven, evenals de resources die bijdragen aan deze kosten. In dit voor beeld zijn de virtuele machines die in de pool worden gebruikt de meest dure resource.

### <a name="create-a-budget-for-a-batch-pool"></a>Een budget voor een batch-pool maken

1. Selecteer op de pagina **kosten analyse** de optie **budget: geen**.
1. Selecteer **nieuwe budget >maken**.
1. Gebruik het resulterende venster om een specifiek budget voor uw groep te configureren. Zie [zelf studie: Azure-budgetten maken en beheren](../cost-management-billing/costs/tutorial-acm-create-budgets.md)voor meer informatie.

## <a name="minimize-costs-associated-with-azure-batch"></a>Kosten minimaliseren die zijn gekoppeld aan Azure Batch

Afhankelijk van uw scenario wilt u mogelijk de kosten zoveel mogelijk verlagen. Overweeg het gebruik van een of meer van deze strategieën om de efficiëntie van uw workloads te maximaliseren en mogelijke kosten te verlagen.

### <a name="use-low-priority-virtual-machines"></a>Virtuele machines met lage prioriteit gebruiken

[Virtuele machines met lage prioriteit](batch-low-pri-vms.md) verlagen de kosten van batch-workloads door gebruik te maken van de capaciteit van de overschot op Azure. Wanneer u virtuele machines met lage prioriteit in uw Pools opgeeft, gebruikt batch dit surplus om uw werk belasting uit te voeren. Er kunnen aanzienlijke kosten besparingen zijn bij het gebruik van Vm's met lage prioriteit in plaats van toegewezen Vm's.

### <a name="select-a-standard-virtual-machine-os-disk-type"></a>Selecteer een standaard besturingssysteem schijf type voor de virtuele machine

Azure biedt meerdere [schijf typen voor VM-besturings systemen](../virtual-machines/disks-types.md). De meeste VM-Series hebben grootten die ondersteuning bieden voor zowel Premium als standaard opslag. Wanneer de VM-grootte voor een groep is geselecteerd, worden met de batch Premium SSD-besturingssysteem schijven geconfigureerd. Wanneer de VM-grootte ' niet-s ' is geselecteerd, wordt het type goed koper, standaard HDD-schijf gebruikt. Premium SSD-besturingssysteem schijven worden bijvoorbeeld gebruikt voor `Standard_D2s_v3` en standaard schijven voor harde schijven worden gebruikt voor `Standard_D2_v3` .

Premium-SSD-besturingssysteem schijven zijn duurder, maar hebben wel betere prestaties. Vm's met Premium-schijven kunnen iets sneller starten dan virtuele machines met standaard schijven voor harde schijven. Met batch wordt de besturingssysteem schijf vaak niet gebruikt, omdat de toepassingen en taak bestanden zich bevinden op de tijdelijke SSD-schijf van de virtuele machine. Daarom kunt u de VM-grootte van ' niet-s ' vaak selecteren om te voor komen dat de hogere kosten voor de Premium SSD worden betaald die worden ingericht wanneer de VM-grootte van de virtuele machine wordt opgegeven.

### <a name="purchase-reservations-for-virtual-machine-instances"></a>Reserve ringen voor instanties van virtuele machines kopen

Als u batch wilt gebruiken gedurende een lange periode, kunt u de kosten van Vm's verlagen door [Azure Reservations](../cost-management-billing/reservations/save-compute-costs-reservations.md) voor uw workloads te gebruiken. Een reserverings tarief is aanzienlijk lager dan het tarief voor betalen naar gebruik. Voor instanties van virtuele machines die worden gebruikt zonder een reserve ring, wordt gefactureerd op basis van het betalen naar gebruik-tarief. Wanneer u een reserve ring koopt, wordt de reserverings korting toegepast.

### <a name="use-automatic-scaling"></a>Automatisch schalen gebruiken

Met [automatisch schalen](batch-automatic-scaling.md) wordt het aantal vm's in de batch-pool dynamisch geschaald op basis van de vereisten van de huidige taak. Door de pool te schalen op basis van de levens duur van een taak, zorgt u er automatisch voor dat virtuele machines worden geschaald en alleen worden gebruikt wanneer er een taak wordt uitgevoerd. Wanneer de taak is voltooid of wanneer er geen taken zijn, worden de Vm's automatisch omlaag geschaald om reken resources op te slaan. Met schalen kunt u de totale kosten van uw batch-oplossing verlagen door alleen de benodigde resources te gebruiken.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure Cost Management en facturering](../cost-management-billing/cost-management-billing-overview.md)
- Meer informatie over [het gebruik van vm's met lage prioriteit met batch](batch-low-pri-vms.md).
