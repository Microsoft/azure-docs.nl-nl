---
title: Resources voor elastische pools schalen
description: Op deze pagina wordt het schalen van resources voor elastische pools in Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein
ms.date: 04/09/2021
ms.openlocfilehash: 1125ea0c6c625ece010b2acb416ad223e37aea84
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787121"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Resources voor elastische pool schalen in Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In dit artikel wordt beschreven hoe u de reken- en opslagbronnen schaalt die beschikbaar zijn voor elastische pools en pooldatabases in Azure SQL Database.

## <a name="change-compute-resources-vcores-or-dtus"></a>Rekenbronnen wijzigen (vCores of DTUs)

Nadat u in eerste instantie het aantal vCores of eDTUs hebt kiest, kunt u een elastische pool dynamisch omhoog of omlaag schalen op basis van de werkelijke ervaring met behulp van het volgende:

* [Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#overview-sql-database)
* [Azure-portal](elastic-pool-manage.md#azure-portal)
* [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool)
* [Azure-CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update)
* [REST API](/rest/api/sql/elasticpools/update)


### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Gevolgen van het wijzigen van de servicelaag of het opnieuw schalen van de rekenkracht

Het wijzigen van de servicelaag of de rekengrootte van een elastische pool volgt een vergelijkbaar patroon als voor individuele databases en omvat hoofdzakelijk de service die de volgende stappen moet uitvoeren:

1. Een nieuw rekenine-exemplaar maken voor de elastische pool  

    Er wordt een nieuw rekenmodel voor de elastische pool gemaakt met de aangevraagde servicelaag en rekenkracht. Voor sommige combinaties van wijzigingen in de servicelaag en de rekengrootte moet een replica van elke database worden gemaakt in de nieuwe rekenexemplaar. Hierbij worden gegevens gekopieerd en kan dit een grote invloed hebben op de algehele latentie. De databases blijven tijdens deze stap online en verbindingen worden nog steeds omgeleid naar de databases in de oorspronkelijke reken-instantie.

2. Switch routing of connections to new compute instance (Routering van verbindingen naar nieuwe reken-instantie overschakelen)

    Bestaande verbindingen met de databases in de oorspronkelijke reken-instantie worden uitgevallen. Nieuwe verbindingen worden tot stand gebracht met de databases in de nieuwe reken-instantie. Voor sommige combinaties van wijzigingen in de servicelaag en de rekengrootte worden databasebestanden losgekoppeld en opnieuw vastgemaakt tijdens de switch.  Hoe dan ook, de switch kan leiden tot een korte serviceonderbreking wanneer databases over het algemeen minder dan 30 seconden en vaak slechts een paar seconden niet beschikbaar zijn. Als er langlopende transacties worden uitgevoerd wanneer verbindingen worden afgebroken, kan de duur van deze stap langer duren om afgebroken transacties te herstellen. [Accelerated Database Recovery](../accelerated-database-recovery.md) kunt de impact van het afbreken van langlopende transacties verminderen.

> [!IMPORTANT]
> Er gaan geen gegevens verloren tijdens een stap in de werkstroom.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Latentie bij het wijzigen van de servicelaag of het opnieuw schalen van de rekenkracht

De geschatte latentie voor het wijzigen van de servicelaag, het schalen van de rekengrootte van een individuele database of elastische pool, het verplaatsen van een database in/uit een elastische pool of het verplaatsen van een database tussen elastische pools wordt als volgt geparameteriseerd:

|Servicelaag|Eenvoudige individuele database,</br>Standard (S0-S1)|Eenvoudige elastische pool,</br>Standard (S2-S12), </br>Algemeen database of elastische pool maken|Premium of Bedrijfskritiek individuele database of elastische pool|Hyperscale
|:---|:---|:---|:---|:---|
|**Eenvoudige individuele database, </br> Standard (S0-S1)**|&bull;&nbsp;Constante tijdlatentie onafhankelijk van gebruikte ruimte</br>&bull;&nbsp;Normaal gesproken minder dan 5 minuten|&bull;&nbsp;Latentie in verhouding tot databaseruimte die wordt gebruikt als gevolg van het kopiëren van gegevens</br>&bull;&nbsp;Doorgaans minder dan 1 minuut per GEBRUIKTE GB ruimte|&bull;&nbsp;Latentie in verhouding tot databaseruimte die wordt gebruikt als gevolg van het kopiëren van gegevens</br>&bull;&nbsp;Doorgaans minder dan 1 minuut per GEBRUIKTE GB ruimte|&bull;&nbsp;Latentie in verhouding tot databaseruimte die wordt gebruikt als gevolg van het kopiëren van gegevens</br>&bull;&nbsp;Doorgaans minder dan 1 minuut per GEBRUIKTE GB ruimte|
|**Basic elastische pool, </br> Standard (S2-S12), </br> Algemeen individuele database of elastische pool**|&bull;&nbsp;Latentie in verhouding tot databaseruimte die wordt gebruikt als gevolg van het kopiëren van gegevens</br>&bull;&nbsp;Doorgaans minder dan 1 minuut per GEBRUIKTE GB ruimte|&bull;&nbsp;Voor individuele databases is de constante tijdlatentie onafhankelijk van de gebruikte ruimte</br>&bull;&nbsp;Normaal gesproken minder dan 5 minuten voor individuele databases</br>&bull;&nbsp;Voor elastische pools, in verhouding tot het aantal databases|&bull;&nbsp;Latentie in verhouding tot databaseruimte die wordt gebruikt als gevolg van het kopiëren van gegevens</br>&bull;&nbsp;Doorgaans minder dan 1 minuut per GEBRUIKTE GB ruimte|&bull;&nbsp;Latentie in verhouding tot databaseruimte die wordt gebruikt als gevolg van het kopiëren van gegevens</br>&bull;&nbsp;Doorgaans minder dan 1 minuut per GEBRUIKTE GB ruimte|
|**Premium of Bedrijfskritiek individuele database of elastische pool**|&bull;&nbsp;Latentie in verhouding tot databaseruimte die wordt gebruikt als gevolg van het kopiëren van gegevens</br>&bull;&nbsp;Doorgaans minder dan 1 minuut per GEBRUIKTE GB ruimte|&bull;&nbsp;Latentie in verhouding tot databaseruimte die wordt gebruikt als gevolg van het kopiëren van gegevens</br>&bull;&nbsp;Doorgaans minder dan 1 minuut per GEBRUIKTE GB ruimte|&bull;&nbsp;Latentie in verhouding tot databaseruimte die wordt gebruikt als gevolg van het kopiëren van gegevens</br>&bull;&nbsp;Doorgaans minder dan 1 minuut per GEBRUIKTE GB ruimte|&bull;&nbsp;Latentie in verhouding tot databaseruimte die wordt gebruikt als gevolg van het kopiëren van gegevens</br>&bull;&nbsp;Doorgaans minder dan 1 minuut per GEBRUIKTE GB ruimte|
|**Hyperscale**|N.v.t.|N.v.t.|N.v.t.|&bull;&nbsp;Constante tijdlatentie onafhankelijk van gebruikte ruimte</br>&bull;&nbsp;Doorgaans minder dan 2 minuten|

> [!NOTE]
>
> - In het geval van het wijzigen van de servicelaag of het opnieuw schalen van de rekenkracht voor een elastische pool, moet de optelsom van de ruimte die wordt gebruikt voor alle databases in de pool worden gebruikt om de schatting te berekenen.
> - In het geval van het verplaatsen van een database naar/van een elastische pool heeft alleen de ruimte die door de database wordt gebruikt invloed op de latentie, niet op de ruimte die wordt gebruikt door de elastische pool.
> - Voor Standard- en Algemeen elastische pools is de latentie van het verplaatsen van een database in/uit een elastische pool of tussen elastische pools evenredig met de grootte van de database als de elastische pool gebruik maakt van[PfS-opslag](../../storage/files/storage-files-introduction.md)(Premium File Share). Als u wilt bepalen of een groep PFS-opslag gebruikt, voert u de volgende query uit in de context van een database in de pool. Als de waarde in de kolom AccountType of `PremiumFileStorage` `PremiumFileStorage-ZRS` is, gebruikt de groep PFS-opslag.

```sql
SELECT s.file_id,
       s.type_desc,
       s.name,
       FILEPROPERTYEX(s.name, 'AccountType') AS AccountType
FROM sys.database_files AS s
WHERE s.type_desc IN ('ROWS', 'LOG');
```

> [!TIP]
> Als u actieve bewerkingen wilt bewaken, gaat u naar: Bewerkingen beheren met behulp van [de SQL REST API,](/rest/api/sql/operations/list)Bewerkingen beheren met [CLI,](/cli/azure/sql/db/op)Bewerkingen bewaken met [T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) en deze twee PowerShell-opdrachten: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) en [Stop-AzSqlDatabaseActivity.](/powershell/module/az.sql/stop-azsqldatabaseactivity)

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Aanvullende overwegingen bij het wijzigen van de servicelaag of het opnieuw schalen van de rekenkracht

- Wanneer u de grootte van vCores of eDTU's voor een elastische pool in downsize brengt, moet de gebruikte ruimte voor de pool kleiner zijn dan de maximaal toegestane grootte van de doelservicelaag en pool-eDTUs.
- Bij het opnieuw schalen van eDTUs voor een elastische pool zijn extra opslagkosten van toepassing als (1) de maximale opslaggrootte van de groep wordt ondersteund door de doelgroep en (2) de maximale opslaggrootte groter is dan de inbegrepen opslaggrootte van de doelgroep. Als bijvoorbeeld een Standard-groep van 100 eDTU met een maximale grootte van 100 GB wordt ge downsized naar een Standard-groep van 50 eDTU, zijn er extra opslagkosten van toepassing, omdat de doelgroep een maximale grootte van 100 GB ondersteunt en de inbegrepen opslag slechts 50 GB is. De extra opslagruimte is dus 100 GB – 50 GB = 50 GB. Zie prijzen voor meer informatie over [SQL Database opslag.](https://azure.microsoft.com/pricing/details/sql-database/) Als de werkelijke hoeveelheid gebruikte ruimte kleiner is dan de inbegrepen opslagruimte, kunnen deze extra kosten worden vermeden door de maximale grootte van de database te verkleinen tot de inbegrepen hoeveelheid.

### <a name="billing-during-rescaling"></a>Facturering tijdens het schalen

U wordt gefactureerd voor elk uur dat een database bestaat met de hoogste servicelaag en rekenkracht die tijdens dat uur is toegepast, ongeacht het gebruik of of de database minder dan een uur actief was. Als u bijvoorbeeld een individuele database maakt en deze vijf minuten later verwijdert, worden er kosten voor één databaseuur op uw factuur weergegeven.

## <a name="change-elastic-pool-storage-size"></a>Opslaggrootte elastische pool wijzigen

> [!IMPORTANT]
> Onder bepaalde omstandigheden moet u mogelijk een database verkleinen om ongebruikte ruimte vrij te maken. Zie Bestandsruimte beheren [in Azure SQL Database.](file-space-manage.md)

### <a name="vcore-based-purchasing-model"></a>Aankoopmodel op basis van vCore

- Opslag kan worden ingericht tot de maximale groottelimiet:

  - Voor opslag in de servicelagen Standard of Algemeen gebruik vergroot of verkleint u de grootte in stappen van 10 GB
  - Voor opslag in de premium- of bedrijfskritieke servicelagen vergroot of verkleint u de grootte in stappen van 250 GB
- Opslag voor een elastische pool kan worden ingericht door de maximale grootte te vergroten of te verlagen.
- De opslagprijs voor een elastische pool is de opslagbedrag vermenigvuldigd met de opslageenheidprijs van de servicelaag. Zie prijzen voor meer informatie over de [prijs van SQL Database opslag.](https://azure.microsoft.com/pricing/details/sql-database/)

> [!IMPORTANT]
> Onder bepaalde omstandigheden moet u mogelijk een database verkleinen om ongebruikte ruimte vrij te maken. Zie Bestandsruimte beheren [in Azure SQL Database.](file-space-manage.md)

### <a name="dtu-based-purchasing-model"></a>Aankoopmodel op basis van DTU

- De eDTU-prijs voor een elastische pool bevat een bepaalde hoeveelheid opslag zonder extra kosten. Extra opslag buiten het inbegrepen bedrag kan worden ingericht tegen extra kosten tot de maximale groottelimiet in stappen van 250 GB tot 1 TB en vervolgens in stappen van 256 GB boven 1 TB. Zie Resources limits for elastic pools using the [DTU purchasing model (Resourceslimieten](resource-limits-dtu-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes) voor elastische pools met behulp van het DTU-aankoopmodel) of Resourcelimieten voor elastische pools met behulp van het [vCore-aankoopmodel](resource-limits-vcore-elastic-pools.md)voor inbegrepen opslagbedragen en maximale groottelimieten.
- Extra opslag voor een elastische pool kan worden ingericht door de maximale grootte te vergroten met behulp van [de Azure Portal,](elastic-pool-manage.md#azure-portal) [PowerShell,](/powershell/module/az.sql/Get-AzSqlElasticPool) [de Azure CLI](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update)of de [REST API](/rest/api/sql/elasticpools/update).
- De prijs van extra opslag voor een elastische pool is de extra opslagruimte vermenigvuldigd met de extra opslageenheidprijs van de servicelaag. Zie prijzen voor meer informatie over de [prijs van SQL Database opslag.](https://azure.microsoft.com/pricing/details/sql-database/)

> [!IMPORTANT]
> Onder bepaalde omstandigheden moet u mogelijk een database verkleinen om ongebruikte ruimte vrij te maken. Zie Bestandsruimte beheren [in Azure SQL Database.](file-space-manage.md)

## <a name="next-steps"></a>Volgende stappen

Zie resourcelimieten op [basis SQL Database vCore -](resource-limits-vcore-elastic-pools.md) elastische pools en SQL Database op DTU gebaseerde resourcelimieten - elastische pools voor algemene [resourcelimieten.](resource-limits-dtu-elastic-pools.md)