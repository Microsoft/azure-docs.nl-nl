---
title: Overzicht van het vCore-aankoopmodel
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Met het vCore-aankoopmodel kunt u reken- en opslagbronnen onafhankelijk schalen, on-premises prestaties matchen en de prijs optimaliseren voor Azure SQL Database en Azure SQL Managed Instance.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 01/15/2021
ms.openlocfilehash: 3bd617f052d52339ae35e5a088c6ee85b797fb48
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779179"
---
# <a name="vcore-model-overview---azure-sql-database-and-azure-sql-managed-instance"></a>Overzicht van vCore-model - Azure SQL Database en Azure SQL Managed Instance 
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Het aankoopmodel voor virtuele kernen (vCore) dat wordt gebruikt door Azure SQL Database en Azure SQL Managed Instance biedt verschillende voordelen:

- Hogere reken-, geheugen-, I/O- en opslaglimieten.
- Controle over het genereren van hardware om beter overeen te komen met de reken- en geheugenvereisten van de workload.
- Prijskortingen [voor Azure Hybrid Benefit (AHB)](../azure-hybrid-benefit.md) en gereserveerde instantie [(RI).](reserved-capacity-overview.md)
- Meer transparantie in de hardwaredetails die de rekenkracht aankracht geven, zodat migraties vanuit on-premises implementaties gemakkelijker kunnen worden gepland.

## <a name="service-tiers"></a>Servicelagen

Opties voor servicelagen in het vCore-model zijn Algemeen, Bedrijfskritiek en Hyperscale. De servicelaag definieert in het algemeen de opslagarchitectuur, ruimte- en I/O-limieten en opties voor bedrijfscontinuïteit met betrekking tot beschikbaarheid en herstel na noodherstel.

|-|**Algemeen doel**|**Bedrijfskritiek**|**Hyperscale**|
|---|---|---|---|
|Ideaal voor|De meeste zakelijke workloads. Biedt budgetgerichte, gebalanceerde en schaalbare reken- en opslagopties. |Biedt zakelijke toepassingen de hoogste tolerantie voor fouten met behulp van verschillende geïsoleerde replica's en biedt de hoogste I/O-prestaties per databasereplica.|De meeste zakelijke workloads met uiterst schaalbare opslag- en leesschaalvereisten.  Biedt een hogere tolerantie voor fouten door de configuratie van meer dan één geïsoleerde databasereplica toe te staan. |
|Storage|Maakt gebruik van externe opslag.<br/>**SQL Database inrichten:**<br/>5 GB – 4 TB<br/>**Serverloze computing:**<br/>5 GB - 3 TB<br/>**SQL Managed Instance:** 32 GB - 8 TB |Maakt gebruik van lokale SSD-opslag.<br/>**SQL Database inrichten:**<br/>5 GB – 4 TB<br/>**SQL Managed Instance:**<br/>32 GB - 4 TB |Flexibele automatische groei van opslag naar behoefte. Ondersteunt maximaal 100 TB aan opslag. Maakt gebruik van lokale SSD-opslag voor lokale buffergroepcache en lokale gegevensopslag. Maakt gebruik van externe opslag van Azure als laatste gegevensopslag voor de lange termijn. |
|IOPS en doorvoer (bij benadering)|**SQL Database:** zie resourcelimieten voor [individuele databases en](resource-limits-vcore-single-databases.md) [elastische pools.](resource-limits-vcore-elastic-pools.md)<br/>**SQL Managed Instance:** zie [Overzicht van Azure SQL Managed Instance resourcelimieten.](../managed-instance/resource-limits.md#service-tier-characteristics)|Zie resourcelimieten voor [individuele databases](resource-limits-vcore-single-databases.md) en [elastische pools.](resource-limits-vcore-elastic-pools.md)|Hyperscale is een architectuur met meerdere lagen met caching op meerdere niveaus. Effectieve IOPS en doorvoer zijn afhankelijk van de workload.|
|Beschikbaarheid|1 replica, geen replica's op leesschaal|3 replica's, 1 [replica met leesschaal](read-scale-out.md),<br/>zone-redundante hoge beschikbaarheid (HA)|1 replica voor lezen/schrijven, plus 0-4 [replica's op leesschaal](read-scale-out.md)|
|Back-ups|Geografisch redundante opslag met leestoegang [(RA-GRS),](../../storage/common/geo-redundant-design.md)1-35 dagen (standaard 7 dagen)|[RA-GRS,](../..//storage/common/geo-redundant-design.md)1-35 dagen (standaard 7 dagen)|Back-ups op basis van momentopnamen in externe Azure-opslag. Herstelt maakt gebruik van deze momentopnamen voor snel herstel. Back-ups worden onmiddellijk gemaakt en hebben geen invloed op de reken-I/O-prestaties. Herstelbewerkingen zijn snel en zijn geen gegevensbewerking met een grootte (minuten in plaats van uren of dagen).|
|In het geheugen|Niet ondersteund|Ondersteund|Niet ondersteund|
|||


### <a name="choosing-a-service-tier"></a>Het kiezen van een servicelaag

Zie de volgende artikelen voor informatie over het selecteren van een servicelaag voor uw specifieke workload:

- [Wanneer u de servicelaag Algemeen kiezen](service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [Wanneer u de servicelaag Bedrijfskritiek kiezen](service-tier-business-critical.md#when-to-choose-this-service-tier)
- [Wanneer kiest u de Hyperscale-servicelaag?](service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>Compute-lagen

Opties voor rekenlagen in het vCore-model omvatten de inrichtende en serverloze rekenlagen.


### <a name="provisioned-compute"></a>Inrichten van rekenkracht

De inrichtende rekenlaag biedt een specifieke hoeveelheid rekenresources die continu worden ingericht, onafhankelijk van de workloadactiviteit, en berekent de hoeveelheid rekenkracht die is ingericht tegen een vaste prijs per uur.


### <a name="serverless-compute"></a>Serverloze compute

De [serverloze rekenlaag](serverless-tier-overview.md) schaalt automatisch rekenbronnen op basis van de activiteit van de workload en berekent de hoeveelheid rekenkracht die per seconde wordt gebruikt.



## <a name="hardware-generations"></a>Hardware-generaties

Opties voor het genereren van hardware in het vCore-model zijn gen 4/5, M-serie, Fsv2-serie en DC-serie. Bij het genereren van hardware worden doorgaans de reken- en geheugenlimieten en andere kenmerken bepaald die van invloed zijn op de prestaties van de workload.

### <a name="gen4gen5"></a>Gen4/Gen5

- Gen4/Gen5-hardware biedt evenwichtige reken- en geheugenresources en is geschikt voor de meeste databaseworkloads die geen hogere geheugen-, hogere vCore- of snellere vereisten voor één vCore hebben, zoals geleverd door de Fsv2-serie of M-serie.

Zie Beschikbaarheid van Gen4/Gen5 voor regio's waar [Gen4/Gen5 beschikbaar is.](#gen4gen5-1)

### <a name="fsv2-series"></a>Fsv2-serie

- De Fsv2-serie is een voor rekenkracht geoptimaliseerde hardwareoptie die een lage CPU-latentie en hoge kloksnelheid biedt voor de meest CPU-veeleisende workloads.
- Afhankelijk van de workload kan de Fsv2-serie meer CPU-prestaties per vCore leveren dan Gen5 en de grootte van 72 vCores kan meer CPU-prestaties bieden tegen minder kosten dan 80 vCores op Gen5. 
- Fsv2 biedt minder geheugen en tempdb per vCore dan andere hardware, dus werkbelastingen die gevoelig zijn voor deze limieten kunnen in plaats daarvan de Gen5- of M-serie overwegen.  

De Fsv2-serie in wordt alleen ondersteund in de Algemeen laag. Zie Beschikbaarheid van Fsv2-serie voor regio's waar [de Fsv2-serie beschikbaar is.](#fsv2-series-1)

### <a name="m-series"></a>M-serie

- De M-serie is een hardwareoptie die is geoptimaliseerd voor geheugen voor workloads die meer geheugen en hogere rekenlimieten nodig hebben dan gen5.
- De M-serie biedt 29 GB per vCore en maximaal 128 vCores, waardoor de geheugenlimiet ten opzichte van Gen5 met 8x wordt verhoogd tot bijna 4 TB.

M-serie wordt alleen ondersteund in de Bedrijfskritiek laag en biedt geen ondersteuning voor zone-redundantie.  Zie Beschikbaarheid van de M-serie voor regio's waar [de M-serie beschikbaar is.](#m-series-1)

#### <a name="azure-offer-types-supported-by-m-series"></a>Typen Azure-aanbieding die worden ondersteund door de M-serie

Voor toegang tot de M-serie moet het abonnement een betaald aanbiedingstype zijn, inclusief Betalen per gebruik of Enterprise Agreement (EA).  Zie Huidige aanbiedingen zonder bestedingslimieten voor een volledige lijst met Azure-aanbiedingstypen die worden ondersteund door de [M-serie.](https://azure.microsoft.com/support/legal/offer-details)

<!--
To enable M-series hardware for a subscription and region, a support request must be opened. The subscription must be a paid offer type including Pay-As-You-Go or Enterprise Agreement (EA).  If the support request is approved, then the selection and provisioning experience of M-series follows the same pattern as for other hardware generations. For regions where M-series is available, see [M-series availability](#m-series).
-->

### <a name="dc-series"></a>DC-serie

> [!NOTE]
> DC-serie is momenteel beschikbaar als **openbare preview.**

- Hardware uit de DC-serie maakt gebruik van Intel-processors Software Guard Extensions (Intel SGX)-technologie.
- DC-serie is vereist voor [Always Encrypted beveiligde enclaves](/sql/relational-databases/security/encryption/always-encrypted-enclaves), die niet worden ondersteund met andere hardwareconfiguraties.
- Dc-serie is ontworpen voor workloads die gevoelige gegevens verwerken en confidential queryverwerkingsmogelijkheden vragen, die worden geleverd door Always Encrypted met beveiligde enclaves.
- Hardware uit de DC-serie biedt evenwichtige reken- en geheugenresources.

DC-serie wordt alleen ondersteund voor de inrichten rekenkracht (serverloos wordt niet ondersteund) en biedt geen ondersteuning voor zone redundantie. Zie Beschikbaarheid van DC-serie voor regio's waar [DC-serie beschikbaar is.](#dc-series-1)

#### <a name="azure-offer-types-supported-by-dc-series"></a>Typen Azure-aanbieding die worden ondersteund door DC-serie

Voor toegang tot dc-serie moet het abonnement een betaald aanbiedingstype zijn, waaronder Betalen per gebruik of Enterprise Agreement (EA).  Zie Huidige aanbiedingen zonder bestedingslimieten voor een volledige lijst met Azure-aanbiedingstypen die worden ondersteund door [DC-serie.](https://azure.microsoft.com/support/legal/offer-details)

### <a name="compute-and-memory-specifications"></a>Reken- en geheugenspecificaties


|Hardwaregeneratie  |Compute  |Geheugen  |
|:---------|:---------|:---------|
|Gen4     |- Intel® E5-2673 v3 (Haswell) 2,4 GHz-processors<br>- Maximaal 24 vCores inrichten (1 vCore = 1 fysieke kern)  |- 7 GB per vCore<br>- Maximaal 168 GB inrichten|
|Gen5     |**Inrichten van rekenkracht**<br>- Intel® E5-2673 v4 (Broadwell) 2,3 GHz, Intel® SP-8160 (Skylake) \* en Intel® 8272CL (Cascade Lake) 2,5 GHz-processors \*<br>- Maximaal 80 vCores inrichten (1 vCore = 1 hyper-thread)<br><br>**Serverloze compute**<br>- Intel® E5-2673 v4 (Broadwell) 2,3 GHz en Intel® SP-8160 (Skylake)* processors<br>- Automatisch omhoog schalen naar 40 vCores (1 vCore = 1 hyper-thread)|**Inrichten van rekenkracht**<br>- 5,1 GB per vCore<br>- Maximaal 408 GB inrichten<br><br>**Serverloze compute**<br>- Automatisch omhoog schalen tot 24 GB per vCore<br>- Automatisch omhoog schalen tot maximaal 120 GB|
|Fsv2-serie     |- Intel® 8168-processors (Skylake)<br>- Met een langdurige turbokloksnelheid van 3,4 GHz en een maximale turbokloksnelheid met één kern van 3,7 GHz.<br>- Maximaal 72 vCores inrichten (1 vCore = 1 hyper-thread)|- 1,9 GB per vCore<br>- Maximaal 136 GB inrichten|
|M-serie     |- Intel® E7-8890 v3 2,5 GHz en Intel® 8280 M 2,7 GHz (Cascade Lake)-processors<br>- Maximaal 128 vCores inrichten (1 vCore = 1 hyper-thread)|- 29 GB per vCore<br>- Maximaal 3,7 TB inrichten|
|DC-serie     | - Intel XEON E-2288G-processors<br>- Featuring Intel Software Guard Extension (Intel SGX))<br>- Maximaal 8 vCores inrichten (1 vCore = 1 fysieke kern) | 4,5 GB per vCore |

\* In de [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) dynamische beheerweergave wordt hardwaregeneratie voor databases met Intel® SP-8160-processors (Skylake) weergegeven als Gen6, terwijl hardwaregeneratie voor databases met Intel® 8272CL (Cascade Lake) wordt weergegeven als Gen7. Resourcelimieten voor alle Gen5-databases zijn hetzelfde, ongeacht het processortype (Broadwell, Skylake of Cascade Lake).

Zie Resourcelimieten voor individuele [databases (vCore)](resource-limits-vcore-single-databases.md)of Resourcelimieten voor elastische [pools (vCore)](resource-limits-vcore-elastic-pools.md)voor meer informatie over resourcelimieten.

### <a name="selecting-a-hardware-generation"></a>Een hardwaregeneratie selecteren

In de Azure Portal kunt u de hardwaregeneratie voor een database of pool in SQL Database selecteren op het moment van maken, of u kunt de hardwaregeneratie van een bestaande database of pool wijzigen.

**Een hardwaregeneratie selecteren bij het maken van een SQL Database of groep**

Zie Create [a SQL Database (Een SQL Database) voor gedetailleerde informatie.](single-database-create-quickstart.md)

Selecteer op **het** tabblad Basisinformatie de **koppeling Database configureren** in de sectie **Compute en** opslag en selecteer vervolgens de koppeling **Configuratie** wijzigen:

  ![database configureren](./media/service-tiers-vcore/configure-sql-database.png)

Selecteer de gewenste hardwaregeneratie:

  ![hardware selecteren](./media/service-tiers-vcore/select-hardware.png)


**De hardwaregeneratie van een bestaande groep SQL Database wijzigen**

Selecteer voor een database op de pagina Overzicht de koppeling **Prijscategorie:**

  ![hardware wijzigen](./media/service-tiers-vcore/change-hardware.png)

Selecteer voor een pool op de pagina Overzicht de optie **Configureren.**

Volg de stappen om de configuratie te wijzigen en selecteer de hardwaregeneratie zoals beschreven in de vorige stappen.

**Een hardwaregeneratie selecteren bij het maken van een SQL Managed Instance**

Zie Een SQL Managed Instance voor [gedetailleerde SQL Managed Instance.](../managed-instance/instance-create-quickstart.md)

Selecteer op **het tabblad** Basisinformatie de koppeling **Database configureren** in de sectie **Compute en** opslag en selecteer vervolgens gewenste hardwaregeneratie:

  ![configureren SQL Managed Instance](./media/service-tiers-vcore/configure-managed-instance.png)
  
**De hardwaregeneratie van een bestaande SQL Managed Instance**

# <a name="the-azure-portal"></a>[Azure Portal](#tab/azure-portal)

Selecteer op SQL Managed Instance pagina **Prijscategorie** de koppeling Prijscategorie onder de sectie Instellingen

![hardware SQL Managed Instance wijzigen](./media/service-tiers-vcore/change-managed-instance-hardware.png)

Op de pagina Prijscategorie kunt u het genereren van hardware wijzigen, zoals beschreven in de vorige stappen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Gebruik het volgende PowerShell-script:

```powershell-interactive
Set-AzSqlInstance -Name "managedinstance1" -ResourceGroupName "ResourceGroup01" -ComputeGeneration Gen5
```

Raadpleeg de opdracht [Set-AzSqlInstance voor meer](/powershell/module/az.sql/set-azsqlinstance) informatie.

# <a name="the-azure-cli"></a>[De Azure CLI](#tab/azure-cli)

Gebruik de volgende CLI-opdracht:

```azurecli-interactive
az sql mi update -g mygroup -n myinstance --family Gen5
```

Raadpleeg de opdracht [az sql mi update voor meer](/cli/azure/sql/mi#az_sql_mi_update) informatie.

---

### <a name="hardware-availability"></a>Hardwarebeschikbaarheid

#### <a name="gen4gen5"></a><a name="gen4gen5-1"></a> Gen4/Gen5

Gen4-hardware [wordt uitgefaseerd en](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/) is niet meer beschikbaar voor nieuwe implementaties. Alle nieuwe databases moeten worden geïmplementeerd op Gen5-hardware.

Gen5 is beschikbaar in alle openbare regio's wereldwijd.

#### <a name="fsv2-series"></a>Fsv2-serie

De Fsv2-serie is beschikbaar in de volgende regio's: Australië - centraal, Australië - centraal 2, Australië - oost, Australië - zuidoost, Brazilië - zuid, Canada - centraal, Azië - oost, VS - oost, Frankrijk - centraal, India - centraal, Korea - centraal, Korea - zuid, Europa - noord, Zuid-Afrika - noord, Azië - zuidoost, VK - zuid, VK - west, Europa - west, VS - west 2.


#### <a name="m-series"></a>M-serie

De M-serie is beschikbaar in de volgende regio's: VS - oost, Europa - noord, Europa - west, VS - west 2.
<!--
M-series may also have limited availability in additional regions. You can request a different region than listed here, but fulfillment in a different region may not be possible.

To enable M-series availability in a subscription, access must be requested by [filing a new support request](#create-a-support-request-to-enable-m-series).


##### Create a support request to enable M-series: 

1. Select **Help + support** in the portal.
2. Select **New support request**.

On the **Basics** page, provide the following:

1. For **Issue type**, select **Service and subscription limits (quotas)**.
2. For **Subscription** = select the subscription to enable M-series.
3. For **Quota type**, select **SQL database**.
4. Select **Next** to go to the **Details** page.

On the **Details** page, provide the following:

1. In the **PROBLEM DETAILS** section select the **Provide details** link. 
2. For **SQL Database quota type** select **M-series**.
3. For **Region**, select the region to enable M-series.
    For regions where M-series is available, see [M-series availability](#m-series).

Approved support requests are typically fulfilled within 5 business days.
-->

#### <a name="dc-series"></a>DC-serie

> [!NOTE]
> DC-serie is momenteel beschikbaar als **openbare preview.**

Dc-serie is beschikbaar in de volgende regio's: Canada - centraal, Canada - oost, VS - oost, Europa - noord, VK - zuid, Europa - west, VS - west.

Als u dc-serie in een momenteel niet-ondersteunde regio nodig [hebt,](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) dient u een ondersteuningsticket in volgens de instructies in Quotumverhogingen aanvragen voor Azure SQL Database [en SQL Managed Instance](quota-increase-request.md).

## <a name="next-steps"></a>Volgende stappen

Als u aan de slag wilt gaan, gaat u naar: 
- [Een SQL Database maken met behulp van de Azure Portal](single-database-create-quickstart.md)
- [Een SQL Managed Instance maken met behulp van de Azure Portal](../managed-instance/instance-create-quickstart.md)

Zie de pagina met prijzen voor [Azure SQL Database prijsinformatie.](https://azure.microsoft.com/pricing/details/sql-database/single/)

Zie voor meer informatie over de specifieke reken- en opslaggrootten die beschikbaar zijn in de servicelagen Algemeen gebruik en Bedrijfskritiek:

- [Resourcelimieten op basis van vCore voor Azure SQL Database](resource-limits-vcore-single-databases.md).
- [Resourcelimieten op basis van vCore voor Azure SQL Database](resource-limits-vcore-elastic-pools.md).
- [Resourcelimieten op basis van vCore voor Azure SQL Managed Instance](../managed-instance/resource-limits.md).
