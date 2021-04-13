---
title: Resources voor elastische pools schalen
description: Op deze pagina wordt de schaal van resources voor elastische Pools in Azure SQL Database beschreven.
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
ms.openlocfilehash: 3d935332854816ae62dea8e30f08bee2b92a4eab
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107302978"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Elastische pool-resources in Azure SQL Database schalen
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In dit artikel wordt beschreven hoe u de berekenings-en opslag resources die beschikbaar zijn voor elastische Pools en gepoolde data bases in Azure SQL Database kunt schalen.

## <a name="change-compute-resources-vcores-or-dtus"></a>Reken bronnen wijzigen (vCores of Dtu's)

Nadat u het aantal vCores of Edtu's hebt gekozen, kunt u een elastische pool dynamisch omhoog of omlaag schalen op basis van de werkelijke ervaring met behulp van:

* [Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#overview-sql-database)
* [Azure-portal](elastic-pool-manage.md#azure-portal)
* [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool)
* [Azure-CLI](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)
* [REST API](/rest/api/sql/elasticpools/update)


### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Gevolgen van het wijzigen van de servicelaag of het opnieuw schalen van de reken grootte

Het wijzigen van de servicelaag of de reken grootte van een elastische pool volgt een vergelijkbaar patroon als voor afzonderlijke data bases en omvat voornamelijk de service die de volgende stappen uitvoert:

1. Een nieuw reken exemplaar maken voor de elastische pool  

    Er wordt een nieuw reken exemplaar voor de elastische pool gemaakt met de aangevraagde servicelaag en de berekenings grootte. Voor sommige combi Naties van de service tier en de grootte van de berekening moet er een replica van elke Data Base worden gemaakt in het nieuwe reken exemplaar, waarbij het kopiëren van gegevens is vereist en de algehele latentie kan beïnvloeden. Ongeacht dat de data bases tijdens deze stap online blijven en dat er verbindingen worden uitgevoerd naar de data bases in het oorspronkelijke reken exemplaar.

2. Route ring van verbindingen naar een nieuw reken exemplaar overschakelen

    Bestaande verbindingen met de data bases in het oorspronkelijke Compute-exemplaar worden verwijderd. Er worden nieuwe verbindingen tot stand gebracht met de data bases in het nieuwe reken exemplaar. Voor sommige combi Naties van service tier-en Compute-wijzigingen worden database bestanden losgekoppeld en opnieuw gekoppeld tijdens de switch.  De switch kan echter een korte service onderbreking veroorzaken wanneer data bases in de meeste gevallen minder dan 30 seconden niet beschikbaar zijn en vaak slechts enkele seconden. Als er langlopende trans acties worden uitgevoerd wanneer de verbindingen worden verbroken, kan de duur van deze stap langer duren om afgebroken trans acties te herstellen. [Versneld database herstel](../accelerated-database-recovery.md) kan het effect verminderen van het afbreken van langlopende trans acties.

> [!IMPORTANT]
> Er gaan geen gegevens verloren tijdens elke stap in de werk stroom.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Latentie van het wijzigen van de servicelaag of het opnieuw schalen van de reken grootte

De geschatte latentie voor het wijzigen van de servicelaag, het schalen van de reken grootte van één data base of elastische pool, het verplaatsen van een data base in/uit een elastische pool of het verplaatsen van een Data Base tussen elastische Pools is als volgt:

|Servicelaag|Basis, afzonderlijke Data Base,</br>Standaard (S0-S1)|Algemene elastische pool,</br>Standard (S2-S12), </br>Algemeen afzonderlijke data base of elastische pool|Premium of Bedrijfskritiek afzonderlijke data base of elastische pool|Hyperscale
|:---|:---|:---|:---|:---|
|**Basis enkele data base, </br> standaard (S0-S1)**|&bull;&nbsp;Constante tijd latentie, onafhankelijk van gebruikte ruimte</br>&bull;&nbsp;Normaal gesp roken minder dan 5 minuten|&bull;&nbsp;Latentie in verhouding tot de database ruimte die wordt gebruikt door het kopiëren van gegevens</br>&bull;&nbsp;Normaal gesp roken, minder dan 1 minuut per GB gebruikte ruimte|&bull;&nbsp;Latentie in verhouding tot de database ruimte die wordt gebruikt door het kopiëren van gegevens</br>&bull;&nbsp;Normaal gesp roken, minder dan 1 minuut per GB gebruikte ruimte|&bull;&nbsp;Latentie in verhouding tot de database ruimte die wordt gebruikt door het kopiëren van gegevens</br>&bull;&nbsp;Normaal gesp roken, minder dan 1 minuut per GB gebruikte ruimte|
|**Basis elastische pool, </br> standaard (S2-S12), </br> algemeen afzonderlijke data base of elastische pool**|&bull;&nbsp;Latentie in verhouding tot de database ruimte die wordt gebruikt door het kopiëren van gegevens</br>&bull;&nbsp;Normaal gesp roken, minder dan 1 minuut per GB gebruikte ruimte|&bull;&nbsp;Voor individuele data bases geldt een constante tijd latentie, onafhankelijk van de gebruikte ruimte</br>&bull;&nbsp;Meestal minder dan 5 minuten voor afzonderlijke data bases</br>&bull;&nbsp;Voor elastische Pools, evenredig met het aantal data bases|&bull;&nbsp;Latentie in verhouding tot de database ruimte die wordt gebruikt door het kopiëren van gegevens</br>&bull;&nbsp;Normaal gesp roken, minder dan 1 minuut per GB gebruikte ruimte|&bull;&nbsp;Latentie in verhouding tot de database ruimte die wordt gebruikt door het kopiëren van gegevens</br>&bull;&nbsp;Normaal gesp roken, minder dan 1 minuut per GB gebruikte ruimte|
|**Premium of Bedrijfskritiek afzonderlijke data base of elastische pool**|&bull;&nbsp;Latentie in verhouding tot de database ruimte die wordt gebruikt door het kopiëren van gegevens</br>&bull;&nbsp;Normaal gesp roken, minder dan 1 minuut per GB gebruikte ruimte|&bull;&nbsp;Latentie in verhouding tot de database ruimte die wordt gebruikt door het kopiëren van gegevens</br>&bull;&nbsp;Normaal gesp roken, minder dan 1 minuut per GB gebruikte ruimte|&bull;&nbsp;Latentie in verhouding tot de database ruimte die wordt gebruikt door het kopiëren van gegevens</br>&bull;&nbsp;Normaal gesp roken, minder dan 1 minuut per GB gebruikte ruimte|&bull;&nbsp;Latentie in verhouding tot de database ruimte die wordt gebruikt door het kopiëren van gegevens</br>&bull;&nbsp;Normaal gesp roken, minder dan 1 minuut per GB gebruikte ruimte|
|**Hyperscale**|N.v.t.|N.v.t.|N.v.t.|&bull;&nbsp;Constante tijd latentie, onafhankelijk van gebruikte ruimte</br>&bull;&nbsp;Normaal gesp roken minder dan 2 minuten|

> [!NOTE]
>
> - In het geval van het wijzigen van de servicelaag of het opnieuw schalen van de computer voor een elastische pool, moet de som van de ruimte die wordt gebruikt voor alle data bases in de pool worden gebruikt voor het berekenen van de schatting.
> - In het geval van het verplaatsen van een Data Base naar/van een elastische pool, is alleen de ruimte die wordt gebruikt door de data base van invloed op de latentie, niet de ruimte die wordt gebruikt door de elastische pool.
> - Voor standaard-en Algemeen elastische Pools wordt een latentie van het verplaatsen van een data base in/uit een elastische pool of tussen elastische Pools evenredig met de grootte van de Data Base als de elastische pool gebruikmaakt van[PFS](../../storage/files/storage-files-introduction.md)-opslag (Premium file share). Voer de volgende query uit in de context van een data base in de groep om te bepalen of een pool gebruikmaakt van PFS-opslag. Als de waarde in de kolom account type is `PremiumFileStorage` of `PremiumFileStorage-ZRS` , gebruikt de groep PFS-opslag.

```sql
SELECT s.file_id,
       s.type_desc,
       s.name,
       FILEPROPERTYEX(s.name, 'AccountType') AS AccountType
FROM sys.database_files AS s
WHERE s.type_desc IN ('ROWS', 'LOG');
```

> [!TIP]
> Zie voor het controleren van bewerkingen in uitvoering: [bewerkingen beheren met behulp van de SQL rest API](/rest/api/sql/operations/list), [bewerkingen beheren met CLI](/cli/azure/sql/db/op), [bewerkingen bewaken met T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) en deze twee Power shell-opdrachten: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) en [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Aanvullende overwegingen bij het wijzigen van de servicelaag of het opnieuw schalen van de reken grootte

- Wanneer Overweeg vCores of Edtu's voor een elastische pool wordt gebruikt, moet de gebruikte ruimte van de groep kleiner zijn dan de Maxi maal toegestane grootte van de doel-servicelaag en de groeps-Edtu's.
- Bij het opnieuw schalen van Edtu's voor een elastische pool, geldt een extra opslag kosten als (1) de maximale grootte van de opslag ruimte van de groep wordt ondersteund door de doel groep en (2) de maximale grootte van de opslag ruimte de opgenomen hoeveelheid opslag ruimte van de doel groep overschrijdt. Als bijvoorbeeld een 100 eDTU Standard-pool met een maximale grootte van 100 GB is verkleind aan een standaard groep van 50 eDTU, worden er extra opslag kosten in rekening kunnen worden genomen omdat de doel groep een maximale grootte van 100 GB ondersteunt en de inbegrepen opslag hoeveelheid alleen 50 GB is. De extra opslag ruimte is dus 100 GB – 50 GB = 50 GB. Zie voor prijzen voor extra opslag [SQL database prijzen](https://azure.microsoft.com/pricing/details/sql-database/). Als de daad werkelijke hoeveelheid gebruikte ruimte kleiner is dan het totale aantal opslag, kunnen deze extra kosten worden vermeden door de maximale grootte van de data base te verlagen tot het opgenomen bedrag.

### <a name="billing-during-rescaling"></a>Facturering tijdens opnieuw schalen

Er worden kosten in rekening gebracht voor elk uur dat een data base bestaat met de hoogste service laag + reken grootte die tijdens dat uur is toegepast, ongeacht het gebruik of het feit dat de data base minder dan een uur actief was. Als u bijvoorbeeld een enkele data base maakt en deze vijf minuten later verwijdert, wordt de factuur voor één data base uur weer gegeven.

## <a name="change-elastic-pool-storage-size"></a>Opslag grootte van elastische pool wijzigen

> [!IMPORTANT]
> In sommige gevallen moet u mogelijk een Data Base verkleinen om ongebruikte ruimte te claimen. Zie [Bestands ruimte beheren in Azure SQL database](file-space-manage.md)voor meer informatie.

### <a name="vcore-based-purchasing-model"></a>Aankoopmodel op basis van vCore

- Opslag kan worden ingericht tot de maximale grootte:

  - Voor opslag in de service lagen standaard of algemeen, verg root of verkleint u de grootte in stappen van 10 GB
  - Voor opslag in de essentiële of bedrijfskritische service lagen verg root of verkleint u de grootte in stappen van 250 GB
- Opslag voor een elastische pool kan worden ingericht door de maximale grootte te verhogen of te verlagen.
- De prijs van opslag voor een elastische pool is de opslag hoeveelheid vermenigvuldigd met de prijs voor de opslag eenheid van de servicelaag. Zie [SQL database prijzen](https://azure.microsoft.com/pricing/details/sql-database/)voor meer informatie over de prijs van extra opslag.

> [!IMPORTANT]
> In sommige gevallen moet u mogelijk een Data Base verkleinen om ongebruikte ruimte te claimen. Zie [Bestands ruimte beheren in Azure SQL database](file-space-manage.md)voor meer informatie.

### <a name="dtu-based-purchasing-model"></a>Op DTU gebaseerd inkoop model

- De eDTU-prijs voor een elastische pool bevat een bepaalde hoeveelheid opslag zonder extra kosten. Extra opslag buiten de inbegrepen hoeveelheid kan worden ingericht voor extra kosten tot de maximale grootte in stappen van 250 GB tot 1 TB en vervolgens in stappen van 256 GB tot meer dan 1 TB. Zie [bronnen limieten voor elastische Pools met behulp van het DTU-aankoop model](resource-limits-dtu-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes) of de [resource limieten voor elastische Pools met behulp van het vCore-inkoop model](resource-limits-vcore-elastic-pools.md)voor meer informatie over de hoeveelheid opslag en de maximale grootte.
- Extra opslag voor een elastische pool kan worden ingericht door de maximale grootte te verhogen met behulp van de [Azure Portal](elastic-pool-manage.md#azure-portal), [Power shell](/powershell/module/az.sql/Get-AzSqlElasticPool), de [Azure cli](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)of de [rest API](/rest/api/sql/elasticpools/update).
- De prijs van extra opslag voor een elastische pool is de extra opslag hoeveelheid vermenigvuldigd met de extra eenheids prijs voor opslag van de servicelaag. Zie [SQL database prijzen](https://azure.microsoft.com/pricing/details/sql-database/)voor meer informatie over de prijs van extra opslag.

> [!IMPORTANT]
> In sommige gevallen moet u mogelijk een Data Base verkleinen om ongebruikte ruimte te claimen. Zie [Bestands ruimte beheren in Azure SQL database](file-space-manage.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie [SQL database op vCore gebaseerde resource](resource-limits-vcore-elastic-pools.md) limieten voor algemene resource limieten: elastische pools en [SQL database op DTU gebaseerde resource limieten-elastische Pools](resource-limits-dtu-elastic-pools.md).