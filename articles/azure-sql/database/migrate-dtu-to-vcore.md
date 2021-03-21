---
title: Migreren van DTU naar vCore
description: Migreer een data base in Azure SQL Database van het DTU-model naar het vCore-model. Migreren naar vCore is vergelijkbaar met het bijwerken of downgradeen tussen de Standard-en Premium-lagen.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
ms.custom: sqldbrb=1
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 02/09/2021
ms.openlocfilehash: 332a2273a377268a425619a0cdaa5f4780b46e73
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100361652"
---
# <a name="migrate-azure-sql-database-from-the-dtu-based-model-to-the-vcore-based-model"></a>Azure SQL Database migreren van het DTU-model naar het model op basis van vCore
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In dit artikel wordt beschreven hoe u uw data base in Azure SQL Database migreert van het [op DTU gebaseerde aankoop model](service-tiers-dtu.md) naar het [op vCore gebaseerde aankoop model](service-tiers-vcore.md). 

## <a name="migrate-a-database"></a>Een database migreren

Het migreren van een Data Base van het op DTU gebaseerde aankoop model naar het op vCore gebaseerde aankoop model is vergelijkbaar met schalen tussen service doelstellingen in de service lagen Basic, Standard en Premium, met een vergelijk bare [duur](single-database-scale.md#latency) en een [minimale downtime](scale-resources.md) aan het einde van het migratie proces. Een Data Base die is gemigreerd naar het op vCore gebaseerde aankoop model kan op hetzelfde moment op dezelfde manier worden gemigreerd naar het op DTU gebaseerde aankoop model, met uitzonde ring van data bases die zijn gemigreerd naar de [grootschalige](service-tier-hyperscale.md) -servicelaag. 

## <a name="choose-the-vcore-service-tier-and-service-objective"></a>De vCore van de service laag en de service doelstelling kiezen

Voor de meeste DTU om migratie scenario's te vCore, worden data bases en elastische Pools in de service lagen Basic en Standard toegewezen aan de [Algemeen](service-tier-general-purpose.md) servicelaag. Data bases en elastische Pools in de Premium-servicelaag worden toegewezen aan de [bedrijfskritiek](service-tier-business-critical.md) servicelaag. Afhankelijk van het toepassings scenario en de vereisten, kan de [grootschalige](service-tier-hyperscale.md) -service laag vaak worden gebruikt als het migratie doel voor afzonderlijke data bases in alle DTU-service lagen.

Als u de service doelstelling, of de reken grootte, wilt kiezen voor de gemigreerde data base in het vCore-model, kunt u een eenvoudige, maar een benaderende vuist regel gebruiken: elke 100 Dtu's in de lagen Basic of Standard vereist ten *minste* 1 vCore, en voor elke 125 dtu's in de Premium-laag is ten *minste* 1 vCore vereist. 

> [!TIP]
> Deze regel is bij benadering, omdat er geen rekening wordt gehouden met de hardware-generatie die wordt gebruikt voor de DTU-data base of elastische pool. 

In het DTU-model kan elke beschik bare [generatie van hardware](purchasing-models.md#hardware-generations-in-the-dtu-based-purchasing-model) worden gebruikt voor uw data base of elastische pool. Verder hebt u alleen indirect controle over het aantal vCores (logische Cpu's) door hogere of lagere DTU-of eDTU-waarden te kiezen. 

Met het vCore-model moeten klanten een expliciete keuze maken van zowel de hardware-generatie als het aantal vCores (logische Cpu's). Het DTU-model biedt deze keuzes niet, maar de generatie van de hardware en het aantal logische Cpu's dat wordt gebruikt voor elke Data Base en elastische pool, worden weer gegeven via dynamische beheer weergaven. Dit maakt het mogelijk om de overeenkomende vCore-service doelstelling nauw keuriger te bepalen. 

De volgende aanpak gebruikt deze informatie om een vCore-service doelstelling te bepalen met een vergelijk bare toewijzing van resources, om een vergelijkbaar prestatie niveau te verkrijgen na migratie naar het vCore-model.

### <a name="dtu-to-vcore-mapping"></a>Toewijzing van DTU naar vCore

Een T-SQL-query hieronder, wanneer deze wordt uitgevoerd in de context van een DTU-data base die moet worden gemigreerd, retourneert een matching (mogelijk Fractioneel) aantal vCores in elke hardware-generatie in het vCore-model. Klanten kunnen de vCore-service doelstelling kiezen die het meest overeenkomt met de bijbehorende DTU-data base of elastische pool, door dit getal af te ronden op het dichtstbijzijnde aantal vCores dat beschikbaar is voor [data bases](resource-limits-vcore-single-databases.md) en [elastische Pools](resource-limits-vcore-elastic-pools.md) in het vCore-model. 

Voor beelden van migratie scenario's die gebruikmaken van deze methode worden beschreven in de sectie [voor beeld](#dtu-to-vcore-migration-examples) .

Voer deze query uit in de context van de data base die moet worden gemigreerd, in plaats van in de- `master` Data Base. Wanneer u een elastische pool migreert, voert u de query uit in de context van elke data base in de groep.

```SQL
WITH dtu_vcore_map AS
(
SELECT rg.slo_name,
       DATABASEPROPERTYEX(DB_NAME(), 'Edition') AS dtu_service_tier,
       CASE WHEN rg.slo_name LIKE '%SQLG4%' THEN 'Gen4'
            WHEN rg.slo_name LIKE '%SQLGZ%' THEN 'Gen4'
            WHEN rg.slo_name LIKE '%SQLG5%' THEN 'Gen5'
            WHEN rg.slo_name LIKE '%SQLG6%' THEN 'Gen5'
            WHEN rg.slo_name LIKE '%SQLG7%' THEN 'Gen5'
       END AS dtu_hardware_gen,
       s.scheduler_count * CAST(rg.instance_cap_cpu/100. AS decimal(3,2)) AS dtu_logical_cpus,
       CAST((jo.process_memory_limit_mb / s.scheduler_count) / 1024. AS decimal(4,2)) AS dtu_memory_per_core_gb
FROM sys.dm_user_db_resource_governance AS rg
CROSS JOIN (SELECT COUNT(1) AS scheduler_count FROM sys.dm_os_schedulers WHERE status = 'VISIBLE ONLINE') AS s
CROSS JOIN sys.dm_os_job_object AS jo
WHERE dtu_limit > 0
      AND
      DB_NAME() <> 'master'
      AND
      rg.database_id = DB_ID()
)
SELECT dtu_logical_cpus,
       dtu_hardware_gen,
       dtu_memory_per_core_gb,
       dtu_service_tier,
       CASE WHEN dtu_service_tier = 'Basic' THEN 'General Purpose'
            WHEN dtu_service_tier = 'Standard' THEN 'General Purpose or Hyperscale'
            WHEN dtu_service_tier = 'Premium' THEN 'Business Critical or Hyperscale'
       END AS vcore_service_tier,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.7
       END AS Gen4_vcores,
       7 AS Gen4_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus * 1.7
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus
       END AS Gen5_vcores,
       5.05 AS Gen5_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.8
       END AS Fsv2_vcores,
       1.89 AS Fsv2_memory_per_core_gb,
       CASE WHEN dtu_hardware_gen = 'Gen4' THEN dtu_logical_cpus * 1.4
            WHEN dtu_hardware_gen = 'Gen5' THEN dtu_logical_cpus * 0.9
       END AS M_vcores,
       29.4 AS M_memory_per_core_gb
FROM dtu_vcore_map;
```

### <a name="additional-factors"></a>Aanvullende factoren

Naast het aantal vCores (logische Cpu's) en de generatie van de hardware, kunnen verschillende andere factoren van invloed zijn op de keuze van de vCore-service doelstelling:

- De toewijzing T-SQL-query komt overeen met de service doelstellingen voor DTU en vCore in termen van hun CPU-capaciteit, waardoor de resultaten nauw keuriger zijn voor CPU-gebonden workloads.
- Voor dezelfde hardware-generatie en hetzelfde aantal vCores worden de resource limieten voor IOPS en de doorvoer capaciteit van de vCore-data bases vaak hoger gebruikt dan voor DTU-data bases. Voor i/o-gebonden workloads is het mogelijk om het aantal vCores in het vCore-model te verlagen om hetzelfde prestatie niveau te krijgen. Resource limieten voor DTU-en vCore-data bases in absolute waarden worden weer gegeven in de weer gave [sys.dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) . Als u deze waarden vergelijkt tussen de DTU-data base die moet worden gemigreerd en een vCore-data base met een ongeveer overeenkomende service doelstelling, kunt u de vCore-service doelstelling nauw keuriger selecteren.
- De toewijzings query retourneert ook de hoeveelheid geheugen per kern voor de DTU-data base of elastische pool die moet worden gemigreerd, en voor elke generatie van de hardware in het vCore-model. Het is belang rijk om te zorgen dat het totale geheugen van de migratie naar vCore gelijk is aan of hoger is voor werk belastingen die een omvang rijke geheugen cache nodig hebben om voldoende prestaties te verkrijgen, of workloads die grote geheugen subsidies vereisen voor het verwerken van query's. Voor dergelijke werk belastingen, afhankelijk van de werkelijke prestaties, kan het nodig zijn om het aantal vCores te verg Roten om voldoende totaal geheugen te krijgen.
- Het [historische resource gebruik](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) van de DTU-data base moet worden overwogen bij het kiezen van de vCore-service doelstelling. DTU-data bases met consistente, in gebruik zijnde CPU-bronnen kunnen minder vCores nodig hebben dan het aantal dat door de toewijzings query wordt geretourneerd. Daarentegen kunnen DTU-data bases die voortdurend hoge CPU-gebruik veroorzaken, meer vCores vereisen dan het resultaat van de query.
- Als u data bases met periodieke of onvoorspelbare gebruiks patronen migreert, overweeg dan het gebruik van een [serverloze](serverless-tier-overview.md) Compute-laag. Houd er rekening mee dat het maximum aantal gelijktijdige werk rollen (aanvragen) in serverloze is 75% de limiet van de ingerichte Compute voor hetzelfde aantal vcores dat is geconfigureerd. De Maxi maal beschik bare hoeveelheid geheugen in serverloze is 3 GB keer het maximum aantal geconfigureerde vcores; zo is het maximale geheugen bijvoorbeeld 120 GB wanneer 40 maximum aantal vcores is geconfigureerd.   
- In het vCore-model kan de ondersteunde maximale database grootte variëren, afhankelijk van het genereren van hardware. Voor grote data bases controleert u de ondersteunde maximum groottes in het vCore-model voor [afzonderlijke data bases](resource-limits-vcore-single-databases.md) en [elastische Pools](resource-limits-vcore-elastic-pools.md).
- Voor elastische Pools hebben de [DTU](resource-limits-dtu-elastic-pools.md) -en [vCore](resource-limits-vcore-elastic-pools.md) -modellen verschillen in het Maxi maal ondersteunde aantal data bases per pool. Dit moet worden overwogen bij het migreren van elastische Pools met veel data bases.
- Sommige hardware-generaties zijn mogelijk niet in elke regio beschikbaar. Controleer de beschik baarheid onder [Hardware generaties](service-tiers-vcore.md#hardware-generations).

> [!IMPORTANT]
> De vCore-richt lijnen voor het DTU tot aanpassen van de doel database zijn bedoeld om u te helpen bij de eerste schatting van de service doelstelling van de beoogde data base.
>
> De optimale configuratie van de doel database is afhankelijk van de werk belasting. Daarom is het mogelijk dat de optimale prijs-prestatie verhouding na de migratie de flexibiliteit van het vCore-model nodig heeft om het aantal vCores, [de generatie](service-tiers-vcore.md#hardware-generations)van de hardware, de [service](service-tiers-vcore.md#service-tiers) en [Compute](service-tiers-vcore.md#compute-tiers) -lagen aan te passen, evenals de afstemming van andere database configuratie parameters, zoals de [maximale mate van parallelle](/sql/relational-databases/query-processing-architecture-guide#parallel-query-processing)uitvoering.
> 

### <a name="dtu-to-vcore-migration-examples"></a>Voor beelden van DTU naar vCore-migratie

> [!NOTE]
> De waarden in de onderstaande voor beelden zijn alleen ter illustratie. De werkelijke waarden die in de beschreven scenario's worden geretourneerd, kunnen afwijken.
> 

**Een standaard S9-data base migreren**

De toewijzings query retourneert het volgende resultaat (sommige kolommen die niet worden weer gegeven voor de boog):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|24,00|GEN5|5,40|16,800|7|24,000|5,05|

We zien dat de DTU-data base 24 logische Cpu's (vCores) heeft, met 5,4 GB geheugen per vCore en gebruikmaakt van GEN5-hardware. De directe overeenkomst met dat is een Algemeen 24 vCore-Data Base op GEN5-hardware, d.w.z. de service doelstelling van **GP_Gen5_24** vCore.

**Een standaard S0-data base migreren**

De toewijzings query retourneert het volgende resultaat (sommige kolommen die niet worden weer gegeven voor de boog):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|0,25|Gen4|0,42|0,250|7|0,425|5,05|

We zien dat de DTU-data base het equivalent van 0,25 logische Cpu's (vCores) heeft, met 0,42 GB aan geheugen per vCore en gebruikmaakt van Gen4-hardware. De kleinste vCore-service doelen in de Gen4-en GEN5-hardware generaties, **GP_Gen4_1** en **GP_Gen5_2**, bieden meer reken bronnen dan de standaard S0-data base, zodat er geen rechtstreekse overeenkomst kan worden gevonden. Aangezien Gen4-hardware [buiten gebruik](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/)wordt gesteld, is de optie **GP_Gen5_2** de voor keur. Als de werk belasting goed is afgestemd op de [serverloze](serverless-tier-overview.md) Compute-laag, dan is **GP_S_Gen5_1** een dichterbij komt.

**Een Premium P15-data base migreren**

De toewijzings query retourneert het volgende resultaat (sommige kolommen die niet worden weer gegeven voor de boog):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|42,00|GEN5|4,86|29,400|7|42,000|5,05|

We zien dat de DTU-data base 42 logische Cpu's (vCores) heeft, met 4,86 GB aan geheugen per vCore en gebruikmaakt van GEN5-hardware. Hoewel er geen vCore-service doelstelling is met 42 kernen, wordt het doel van de **BC_Gen5_40** -service zeer dicht bij de CPU-en geheugen capaciteit afgesloten en is het een goed resultaat.

**Een elastische groep Basic 200 eDTU migreren**

De toewijzings query retourneert het volgende resultaat (sommige kolommen die niet worden weer gegeven voor de boog):

|dtu_logical_cpus|dtu_hardware_gen|dtu_memory_per_core_gb|Gen4_vcores|Gen4_memory_per_core_gb|Gen5_vcores|Gen5_memory_per_core_gb|
|----------------|----------------|----------------------|-----------|-----------------------|-----------|-----------------------|
|4,00|GEN5|5,40|2,800|7|4,000|5,05|

We zien dat de elastische DTU-pool 4 logische Cpu's (vCores) heeft, met 5,4 GB geheugen per vCore en gebruikmaakt van GEN5-hardware. De rechtstreekse overeenkomst in het vCore-model is een **GP_Gen5_4** elastische pool. Deze service doelstelling ondersteunt echter Maxi maal 200 data bases per pool, terwijl de Basic 200 eDTU elastische pool ondersteuning biedt voor Maxi maal 500 data bases. Als de elastische pool moet worden gemigreerd meer dan 200 data bases, moet de overeenkomende vCore-service doelstelling **GP_Gen5_6** zijn, die ondersteuning biedt voor maxi maal 500 data bases.

## <a name="migrate-geo-replicated-databases"></a>Geo-gerepliceerde data bases migreren

Migreren van het DTU-model naar het op vCore gebaseerde aankoop model is vergelijkbaar met het bijwerken of verlagen van de geo-replicatie relaties tussen data bases in de Standard-en Premium-Service lagen. Tijdens de migratie hoeft u geo-replicatie niet te stoppen, maar moet u deze regels voor volgorde bepaling volgen:

- Wanneer u een upgrade uitvoert, moet u eerst de secundaire data base upgraden en vervolgens een upgrade uitvoeren van de primaire.
- Wanneer u een downgrade uitvoert, maakt u de volg orde ongedaan: u moet eerst de primaire data base opdowngradeen en vervolgens het secundaire database downgrade verlagen.

Wanneer u geo-replicatie tussen twee elastische Pools gebruikt, raden we u aan om één groep als primair en als de secundaire groep aan te wijzen. In dat geval moet u bij het migreren van elastische Pools dezelfde richt lijnen voor sequentiëren gebruiken. Als u echter elastische Pools hebt die zowel primaire als secundaire data bases bevatten, behandelt u de pool met het hogere gebruik als primair en volgt u de regels voor volgorde bepaling dienovereenkomstig.  

De volgende tabel bevat richt lijnen voor specifieke migratie scenario's:

|Huidige servicelaag|Doel service-laag|Type migratie|Gebruikersacties|
|---|---|---|---|
|Standard|Algemeen doel|Zij|Kan in een wille keurige volg orde worden gemigreerd, maar moeten de juiste vCore-grootte worden gegarandeerd zoals hierboven wordt beschreven|
|Premium|Bedrijfskritiek|Zij|Kan in een wille keurige volg orde worden gemigreerd, maar moeten de juiste vCore-grootte worden gegarandeerd zoals hierboven wordt beschreven|
|Standard|Bedrijfskritiek|Upgrade uitvoeren|Moet secundair eerst worden gemigreerd|
|Bedrijfskritiek|Standard|Downgrade|Moet primair eerst worden gemigreerd|
|Premium|Algemeen doel|Downgrade|Moet primair eerst worden gemigreerd|
|Algemeen doel|Premium|Upgrade uitvoeren|Moet secundair eerst worden gemigreerd|
|Bedrijfskritiek|Algemeen doel|Downgrade|Moet primair eerst worden gemigreerd|
|Algemeen doel|Bedrijfskritiek|Upgrade uitvoeren|Moet secundair eerst worden gemigreerd|
||||

## <a name="migrate-failover-groups"></a>Failover-groepen migreren

Migratie van failover-groepen met meerdere data bases vereist een afzonderlijke migratie van de primaire en secundaire data bases. Tijdens dat proces gelden dezelfde aandachtspunten en sequentiëren. Nadat de data bases zijn geconverteerd naar het op vCore gebaseerde aankoop model, blijft de failovergroep van kracht met dezelfde beleids instellingen.

### <a name="create-a-geo-replication-secondary-database"></a>Een secundaire data base met geo-replicatie maken

U kunt een secundaire data base met geo-replicatie (een geo-secundair) alleen maken door dezelfde servicelaag te gebruiken als u hebt gebruikt voor de primaire data base. Voor data bases met een hoge frequentie voor het genereren van het logboek, wordt aangeraden om de geo-secundair te maken met dezelfde reken grootte als de primaire.

Als u een geo-secundair maakt in de elastische pool voor één primaire data base, moet u ervoor zorgen dat de `maxVCore` instelling voor de pool overeenkomt met de reken grootte van de primaire data base. Als u een geo-secundair maakt voor een primaire groep in een andere elastische pool, raden we aan dat de groepen dezelfde `maxVCore` instellingen hebben.

## <a name="use-database-copy-to-migrate-from-dtu-to-vcore"></a>Database kopie gebruiken om van DTU naar vCore te migreren

U kunt elke Data Base met een op DTU gebaseerde reken grootte kopiëren naar een Data Base met een op vCore gebaseerde reken grootte zonder beperkingen of speciale sequentiëren, zolang de doel berekenings grootte de maximale database grootte van de bron database ondersteunt. De kopie van de data base maakt een moment opname van de gegevens vanaf de begin tijd van de Kopieer bewerking en synchroniseert geen gegevens tussen de bron en het doel.

## <a name="next-steps"></a>Volgende stappen

- Zie [SQL database resource limieten op basis van vCore voor afzonderlijke data](resource-limits-vcore-single-databases.md)bases voor de specifieke berekenings grootte en beschik bare opslag grootte voor afzonderlijke data bases.
- Zie [SQL database op vCore gebaseerde resource limieten voor elastische Pools](resource-limits-vcore-elastic-pools.md)voor de specifieke berekenings grootten en opties voor opslag grootte die beschikbaar zijn voor elastische Pools.