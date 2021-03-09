---
title: Service kosten verminderen met behulp van Azure Advisor
description: Gebruik Azure Advisor om de kosten van uw Azure-implementaties te optimaliseren.
ms.topic: article
ms.date: 09/27/2020
ms.openlocfilehash: 346b790c6970abc8670661e1ec180662957af47d
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503371"
---
# <a name="reduce-service-costs-by-using-azure-advisor"></a>Service kosten verminderen met behulp van Azure Advisor

Azure Advisor helpt u uw totale Azure-uitgaven te optimaliseren en te reduceren door inactieve en gelaagde bronnen te identificeren. U kunt aanbevelingen ontvangen op het tabblad **kosten** van het Advisor-dash board.

## <a name="optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances"></a>Uitgaven aan virtuele machines optimaliseren door de grootte ervan te wijzigen of door onderbenutte instanties af te sluiten 

Hoewel bepaalde toepassings scenario's kunnen leiden tot een laag gebruik door ontwerp, bespaart u vaak geld door de grootte en het aantal van uw virtuele machines te beheren. 

De aanbevolen acties worden afgesloten of het formaat ervan worden gewijzigd, wat specifiek is voor de resource die wordt geëvalueerd.

Het geavanceerde evaluatie model in Advisor houdt in dat u virtuele machines afsluit wanneer al deze instructies waar zijn: 
- De P95th van de maximale waarde van het CPU-gebruik is minder dan 3%. 
- Netwerk gebruik is minder dan 2% gedurende een periode van zeven dagen.
- Geheugen druk is lager dan de drempel waarden

Advisor is van mening dat het formaat van virtuele machines moet worden gewijzigd wanneer het mogelijk is om de huidige belasting in een kleinere SKU (binnen dezelfde SKU-familie) of een kleiner aantal instanties te maken, bijvoorbeeld:
- De huidige belasting gaat niet hoger dan 80% gebruik voor workloads die niet aan de gebruiker zijn gericht. 
- De belasting gaat niet meer dan 40% voor werk belastingen die aan de gebruiker zijn gericht. 

Hier bepaalt Advisor het type werk belasting door de kenmerken van het CPU-gebruik van de werk belasting te analyseren.

Advisor toont de geschatte kosten besparingen voor een aanbevolen actie: verg Roten/verkleinen of afsluiten. Voor verg Roten/verkleinen biedt Advisor huidige en doel-SKU-informatie.

Als u meer wilt weten over het identificeren van gefactureerde virtuele machines, kunt u de CPU-gebruiks regel aanpassen per abonnement.

## <a name="optimize-spend-for-mariadb-mysql-and-postgresql-servers-by-right-sizing"></a>De uitgaven voor MariaDB-, MySQL-en PostgreSQL-servers optimaliseren door de rechter grootte te wijzigen 
Advisor analyseert uw gebruik en evalueert of uw MariaDB-, MySQL-of PostgreSQL-database server bronnen gedurende de afgelopen zeven dagen zijn ondergebruikt gedurende een langere periode. Weinig resource gebruik resulteert in ongewenste uitgaven die u kunt oplossen zonder aanzienlijke invloed op de prestaties. Om uw kosten te verlagen en uw resources efficiënt te beheren, wordt u aangeraden de reken grootte (vCores) per helft te verlagen.

## <a name="reduce-costs-by-eliminating-unprovisioned-expressroute-circuits"></a>Kosten verlagen door oningerichte ExpressRoute-circuits te elimineren

Advisor identificeert Azure ExpressRoute-circuits die zijn opgenomen in de provider status van **niet** meer dan een maand. Het is raadzaam om het circuit te verwijderen als u niet van plan bent om het circuit in te richten met uw connectiviteits provider.

## <a name="reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways"></a>Kosten verlagen door niet-actieve virtuele netwerkgateways te verwijderen of opnieuw te configureren

Advisor identificeert virtuele netwerk gateways die meer dan 90 dagen inactief zijn geweest. Omdat deze gateways per uur worden gefactureerd, moet u overwegen om deze te configureren of te verwijderen als u deze niet meer wilt gebruiken. 

## <a name="buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs"></a>Gereserveerde instanties van virtuele machines kopen om kosten te besparen ten opzichte van betalen per gebruik

Advisor bekijkt uw gebruik van de virtuele machine in de afgelopen 30 dagen om te bepalen of u geld kunt besparen door een Azure-reserve ring aan te schaffen. Advisor toont u de regio's en grootten waar de mogelijke besparingen het beste zijn en de geschatte besparingen van het aanschaffen van reserve ringen. Met Azure-reserve ringen kunt u de basis kosten voor uw virtuele machines vooraf aanschaffen. Kortingen gelden automatisch voor nieuwe of bestaande virtuele machines met dezelfde grootte en regio als uw reserve ringen. [Meer informatie over Azure Reserved VM Instances.](https://azure.microsoft.com/pricing/reserved-vm-instances/)

Advisor waarschuwt u ook voor uw gereserveerde instanties die in de komende 30 dagen verlopen. U wordt aangeraden nieuwe gereserveerde instanties te kopen om prijzen voor betalen per gebruik te voor komen.

## <a name="buy-reserved-instances-for-several-resource-types-to-save-over-your-pay-as-you-go-costs"></a>Gereserveerde instanties voor verschillende resource typen kopen om te besparen op uw betalen per gebruik-kosten

Advisor analyseert gebruiks patronen voor de afgelopen 30 dagen voor de volgende resources en raadt gereserveerde capaciteits aankopen aan die de kosten optimaliseren.

### <a name="azure-cosmos-db-reserved-capacity"></a>Gereserveerde capaciteit Azure Cosmos DB
Advisor analyseert uw Azure Cosmos DB gebruiks patronen voor de afgelopen 30 dagen en raadt u aan om gereserveerde capaciteit te kopen om de kosten te optimaliseren. Door gebruik te maken van gereserveerde capaciteit, kunt u het gebruik van Azure Cosmos DB uur vooraf aanschaffen en besparen u de kosten voor betalen per gebruik. Gereserveerde capaciteit is een facturerings voordeel en is automatisch van toepassing op nieuwe en bestaande implementaties. Advisor berekent spaar prijzen voor afzonderlijke abonnementen met behulp van een reserverings prijs van drie jaar en door de gebruiks patronen te extrapoleren die in de afgelopen 30 dagen zijn waargenomen. Aanbevelingen voor Shared scopes zijn beschikbaar voor gereserveerde capaciteits aankopen en kunnen de besparingen verhogen.

### <a name="sql-database-and-sql-managed-instance-reserved-capacity"></a>Gereserveerde capaciteit van SQL Database en door SQL beheerd exemplaar
Advisor analyseert de gebruiks patronen van SQL Database en SQL Managed instance over de afgelopen 30 dagen. Vervolgens wordt gereserveerde capaciteits aankopen aanbevolen die de kosten optimaliseren. Door gebruik te maken van gereserveerde capaciteit, kunt u het gebruik van SQL DB-uur vooraf aanschaffen en besparen u de kosten voor SQL-berekeningen. Uw SQL-licentie wordt afzonderlijk in rekening gebracht en wordt niet gedisconteerd door de reserve ring. Gereserveerde capaciteit is een facturerings voordeel en is automatisch van toepassing op nieuwe en bestaande implementaties. Advisor berekent spaar prijzen voor afzonderlijke abonnementen met behulp van een reserverings prijs van drie jaar en door de gebruiks patronen te extrapoleren die in de afgelopen 30 dagen zijn waargenomen. Aanbevelingen voor Shared scopes zijn beschikbaar voor gereserveerde capaciteits aankopen en kunnen de besparingen verhogen. Zie voor meer informatie [Azure SQL database gereserveerde capaciteit voor de door SQL beheerde instantie &](../azure-sql/database/reserved-capacity-overview.md).

### <a name="app-service-stamp-fee-reserved-capacity"></a>Gereserveerde capaciteit App Service Stamp-vergoeding
Advisor analyseert het gebruiks patroon van de stempel kosten voor uw Azure App Service geïsoleerde omgeving in de afgelopen 30 dagen en raadt u aan om gereserveerde capaciteits aankopen te doen die de kosten optimaliseren. Door gebruik te maken van gereserveerde capaciteit, kunt u het gebruik van elk uur vooraf kopen voor de kosten voor de geïsoleerde omgevings stempel en besparen op uw betalen naar gebruik-tarieven. Houd er rekening mee dat gereserveerde capaciteit alleen van toepassing is op de stempel kosten en niet op App Service exemplaren. Gereserveerde capaciteit is een facturerings voordeel en is automatisch van toepassing op nieuwe en bestaande implementaties. Advisor berekent het opslaan van schattingen voor afzonderlijke abonnementen met behulp van een reserverings prijs van drie jaar op basis van gebruiks patronen in de afgelopen 30 dagen.

### <a name="blob-storage-reserved-capacity"></a>Gereserveerde capaciteit van Blob-opslag
Advisor analyseert uw Azure Blob-opslag en Azure Data Lake opslag gebruik in de afgelopen 30 dagen. Vervolgens worden de gereserveerde capaciteits aankopen berekend waarmee de kosten worden geoptimaliseerd. Met gereserveerde capaciteit kunt u het gebruik van elk uur vooraf kopen en besparen op uw huidige kosten op aanvraag. Gereserveerde capaciteit van Blob-opslag is alleen van toepassing op gegevens die zijn opgeslagen in Azure Blob v2-en Azure Data Lake Storage Gen2-accounts. Gereserveerde capaciteit is een facturerings voordeel en is automatisch van toepassing op nieuwe en bestaande implementaties. Advisor berekent spaar bedragen voor afzonderlijke abonnementen met behulp van een reserverings prijs van drie jaar en de gebruiks patronen die in de afgelopen 30 dagen zijn waargenomen. Aanbevelingen voor Shared scopes zijn beschikbaar voor gereserveerde capaciteits aankopen en kunnen de besparingen verhogen.

### <a name="mariadb-mysql-and-postgresql-reserved-capacity"></a>Gereserveerde capaciteit voor MariaDB, MySQL en PostgreSQL
Advisor analyseert uw gebruiks patronen voor Azure Database for MariaDB, Azure Database for MySQL en Azure Database for PostgreSQL in de afgelopen 30 dagen. Vervolgens wordt gereserveerde capaciteits aankopen aanbevolen die de kosten optimaliseren. Door gebruik te maken van gereserveerde capaciteit, kunt u MariaDB, MySQL en PostgreSQL per uur kopen en besparen op uw huidige kosten. Gereserveerde capaciteit is een facturerings voordeel en is automatisch van toepassing op nieuwe en bestaande implementaties. Advisor berekent spaar bedragen voor afzonderlijke abonnementen met behulp van een reserverings prijs van drie jaar en de gebruiks patronen die in de afgelopen 30 dagen zijn waargenomen. Aanbevelingen voor Shared scopes zijn beschikbaar voor gereserveerde capaciteits aankopen en kunnen de besparingen verhogen.

### <a name="azure-synapse-analytics-reserved-capacity"></a>Gereserveerde capaciteit van Azure Synapse Analytics
Advisor analyseert uw Azure Synapse Analytics-gebruiks patronen in de afgelopen 30 dagen en raadt u aan om gereserveerde capaciteits aankopen te doen die de kosten optimaliseren. Door gebruik te maken van gereserveerde capaciteit, kunt u het gebruik van Synapse analyses vooraf kopen en besparen op uw kosten op aanvraag. Gereserveerde capaciteit is een facturerings voordeel en is automatisch van toepassing op nieuwe en bestaande implementaties. Advisor berekent spaar bedragen voor afzonderlijke abonnementen met behulp van een reserverings prijs van drie jaar en de gebruiks patronen die in de afgelopen 30 dagen zijn waargenomen. Aanbevelingen voor Shared scopes zijn beschikbaar voor gereserveerde capaciteits aankopen en kunnen de besparingen verhogen.

## <a name="delete-unassociated-public-ip-addresses-to-save-money"></a>Niet-gekoppelde open bare IP-adressen verwijderen om geld te besparen

Advisor identificeert open bare IP-adressen die niet zijn gekoppeld aan Azure-resources, zoals load balancers en Vm's. Er wordt een nominale kosten in rekening gebracht voor deze open bare IP-adressen. Als u deze niet wilt gebruiken, kunt u geld besparen door ze te verwijderen.

## <a name="delete-azure-data-factory-pipelines-that-are-failing"></a>Azure Data Factory-pijplijnen verwijderen die niet werken

Advisor detecteert Azure Data Factory pijp lijnen die herhaaldelijk mislukken. U wordt aangeraden de problemen op te lossen of de pijp lijnen te verwijderen als u deze niet nodig hebt. U wordt gefactureerd voor deze pijp lijnen, zelfs als ze niet voor u zijn, zolang ze niet worden gebruikt.

## <a name="use-standard-snapshots-for-managed-disks"></a>Standaard momentopnamen gebruiken voor beheerde schijven
Voor het besparen van 60% kosten, raden we u aan om uw moment opnamen op te slaan in de standaard opslag, ongeacht het opslag type van de bovenliggende schijf. Deze optie is de standaard optie voor moment opnamen van beheerde schijven. Advisor identificeert moment opnamen die zijn opgeslagen in Premium Storage en raadt u aan om vervolgens te migreren van Premium naar Standard-opslag. [Meer informatie over prijzen voor beheerde schijven.](https://aka.ms/aa_manageddisksnapshot_learnmore)

## <a name="use-lifecycle-management"></a>Levenscyclus beheer gebruiken
Door gebruik te maken van informatie over het aantal Azure Blob Storage-objecten, de totale grootte en de trans acties, detecteert Advisor of u levenscyclus beheer moet inschakelen om gegevens op te slaan in een of meer van uw opslag accounts. U wordt gevraagd om levenscyclus beheer regels te maken om uw gegevens automatisch te voorzien van koele of archief opslag om uw opslag kosten te optimaliseren en om uw gegevens in Azure Blob-opslag te bewaren voor toepassings compatibiliteit.

## <a name="create-an-ephemeral-os-disk-recommendation"></a>Aanbeveling voor maken van een tijdelijke besturingssysteemschijf
Met de [tijdelijke besturingssysteem schijf](../virtual-machines/ephemeral-os-disks.md) kunt u het volgende doen: 
- Bespaar op opslag kosten voor besturingssysteem schijven. 
- Zorg voor minder lees-en schrijf latentie voor besturingssysteem schijven. 
- Versnelde bewerkingen voor VM-installatie kopieën ophalen door het besturings systeem (en de tijdelijke schijf) opnieuw in te stellen op de oorspronkelijke staat.

Het is aan te raden tijdelijke besturingssysteem schijven te gebruiken voor IaaS Vm's of Vm's met stateless werk belastingen. Advisor biedt aanbevelingen voor bronnen die kunnen profiteren van kortstondige besturingssysteem schijf.

## <a name="reduce-azure-data-explorer-table-cache-period-policy-for-cluster-cost-optimization-preview"></a>Azure Data Explorer-tabel cache-periode (beleid) verminderen voor optimalisatie van cluster kosten (preview-versie)
Advisor identificeert bronnen voor het verkleinen van het Table cache-beleid Azure Data Explorer cluster knooppunten met weinig CPU-gebruik, geheugen en een configuratie met een hoge cache grootte.

## <a name="how-to-access-cost-recommendations-in-azure-advisor"></a>Toegang tot kosten aanbevelingen in Azure Advisor

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Zoek en selecteer [**Advisor**](https://aka.ms/azureadvisordashboard) op elke pagina.

1. Op het **Advisor** -dash board selecteert u het tabblad **kosten** .

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over Advisor-aanbevelingen:
* [Inleiding tot Advisor](advisor-overview.md)
* [Advisor-Score](azure-advisor-score.md)
* [Aan de slag met Advisor](advisor-get-started.md)
* [Aanbevelingen voor Advisor-prestaties](advisor-performance-recommendations.md)
* [Aanbevelingen voor hoge Beschik baarheid van Advisor](advisor-high-availability-recommendations.md)
* [Aanbevelingen voor de beveiliging van Advisor](advisor-security-recommendations.md)
* [Aanbevelingen voor operationele uitmuntendheid van Advisor](advisor-operational-excellence-recommendations.md)
