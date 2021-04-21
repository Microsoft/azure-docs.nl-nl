---
title: De schaal van de resources voor één database aanpassen
description: In dit artikel wordt beschreven hoe u de reken- en opslagbronnen kunt schalen die beschikbaar zijn voor één database in Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1, references_regions
ms.devlang: ''
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 04/09/2021
ms.openlocfilehash: ae1b3cc41d709c28ba517d672eb98cb60a837a8d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779071"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Resources van een individuele database schalen in Azure SQL Database

In dit artikel wordt beschreven hoe u de reken- en opslagbronnen die beschikbaar zijn voor een Azure SQL Database in de inrichtende rekenlaag kunt schalen. De serverloze [rekenlaag](serverless-tier-overview.md) biedt ook automatische rekenkracht en facturen per seconde voor het gebruikte rekenmodel.

Nadat u in eerste instantie het aantal vCores of DTUs hebt kiest, kunt u één database dynamisch omhoog of omlaag schalen op basis van de werkelijke ervaring met behulp van:

* [Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#overview-sql-database)
* [Azure-portal](single-database-manage.md#the-azure-portal)
* [PowerShell](/powershell/module/az.sql/set-azsqldatabase)
* [Azure-CLI](/cli/azure/sql/db#az_sql_db_update)
* [REST API](/rest/api/sql/databases/update)


In de volgende video ziet u hoe u de servicelaag en rekenkracht dynamisch kunt wijzigen om de beschikbare DTUs voor één database te vergroten.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]

> [!IMPORTANT]
> Onder bepaalde omstandigheden moet u mogelijk een database verkleinen om ongebruikte ruimte vrij te maken. Zie Bestandsruimte beheren in Azure SQL Database voor [meer informatie.](file-space-manage.md)

## <a name="impact"></a>Impact

Bij het wijzigen van de servicelaag of rekengrootte moet de service de volgende stappen uitvoeren:

1. Maak een nieuw reken-exemplaar voor de database. 

    Er wordt een nieuw rekenmodel gemaakt met de aangevraagde servicelaag en rekenkracht. Voor sommige combinaties van wijzigingen in de servicelaag en rekengrootte moet een replica van de database worden gemaakt in het nieuwe rekenexemplaar. Dit omvat het kopiëren van gegevens en kan een grote invloed hebben op de algehele latentie. Hoe dan ook, de database blijft online tijdens deze stap en verbindingen worden nog steeds omgeleid naar de database in de oorspronkelijke reken-instantie.

2. Schakel de routering van verbindingen naar een nieuw reken-exemplaar over.

    Bestaande verbindingen met de database in het oorspronkelijke reken exemplaar worden verwijderd. Nieuwe verbindingen worden tot stand gebracht met de database in de nieuwe reken-instantie. Voor sommige combinaties van wijzigingen in de servicelaag en de rekengrootte worden databasebestanden losgekoppeld en opnieuw vastgemaakt tijdens de switch.  Hoe dan ook, de switch kan leiden tot een korte serviceonderbreking wanneer de database doorgaans minder dan 30 seconden en vaak slechts een paar seconden niet beschikbaar is. Als er langlopende transacties worden uitgevoerd wanneer verbindingen worden afgebroken, kan de duur van deze stap langer duren om afgebroken transacties te herstellen. [Accelerated Database Recovery](../accelerated-database-recovery.md) kunt de impact van het afbreken van langlopende transacties verminderen.

> [!IMPORTANT]
> Er gaan geen gegevens verloren tijdens een stap in de werkstroom. Zorg ervoor dat u logica voor opnieuw proberen hebt geïmplementeerd [in](troubleshoot-common-connectivity-issues.md) de toepassingen en onderdelen die gebruikmaken van Azure SQL Database terwijl de servicelaag wordt gewijzigd.

## <a name="latency"></a>Latentie

De geschatte latentie voor het wijzigen van de servicelaag, het schalen van de rekengrootte van een individuele database of elastische pool, het verplaatsen van een database in/uit een elastische pool of het verplaatsen van een database tussen elastische pools wordt als volgt geparameteriseerd:

|Servicelaag|Eenvoudige individuele database,</br>Standard (S0-S1)|Eenvoudige elastische pool,</br>Standard (S2-S12), </br>Algemeen database of elastische pool|Premium of Bedrijfskritiek individuele database of elastische pool|Hyperscale
|:---|:---|:---|:---|:---|
|**Eenvoudige individuele database, </br> Standard (S0-S1)**|&bull;&nbsp;Constante tijdlatentie onafhankelijk van gebruikte ruimte</br>&bull;&nbsp;Doorgaans minder dan 5 minuten|&bull;&nbsp;Latentie in verhouding tot databaseruimte die wordt gebruikt als gevolg van het kopiëren van gegevens</br>&bull;&nbsp;Doorgaans minder dan 1 minuut per GEBRUIKTE GB ruimte|&bull;&nbsp;Latentie in verhouding tot databaseruimte die wordt gebruikt als gevolg van het kopiëren van gegevens</br>&bull;&nbsp;Doorgaans minder dan 1 minuut per GEBRUIKTE GB ruimte|&bull;&nbsp;Latentie in verhouding tot databaseruimte die wordt gebruikt als gevolg van het kopiëren van gegevens</br>&bull;&nbsp;Doorgaans minder dan 1 minuut per GEBRUIKTE GB ruimte|
|**Basic elastische pool, </br> Standard (S2-S12), </br> Algemeen individuele database of elastische pool**|&bull;&nbsp;Latentie in verhouding tot databaseruimte die wordt gebruikt als gevolg van het kopiëren van gegevens</br>&bull;&nbsp;Doorgaans minder dan 1 minuut per GEBRUIKTE GB ruimte|&bull;&nbsp;Voor individuele databases is constante tijdlatentie onafhankelijk van de gebruikte ruimte</br>&bull;&nbsp;Normaal gesproken minder dan 5 minuten voor individuele databases</br>&bull;&nbsp;Voor elastische pools, evenredig met het aantal databases|&bull;&nbsp;Latentie in verhouding tot databaseruimte die wordt gebruikt als gevolg van het kopiëren van gegevens</br>&bull;&nbsp;Doorgaans minder dan 1 minuut per GEBRUIKTE GB ruimte|&bull;&nbsp;Latentie in verhouding tot databaseruimte die wordt gebruikt als gevolg van het kopiëren van gegevens</br>&bull;&nbsp;Doorgaans minder dan 1 minuut per GEBRUIKTE GB ruimte|
|**Premium of Bedrijfskritiek individuele database of elastische pool**|&bull;&nbsp;Latentie in verhouding tot databaseruimte die wordt gebruikt als gevolg van het kopiëren van gegevens</br>&bull;&nbsp;Doorgaans minder dan 1 minuut per GEBRUIKTE GB ruimte|&bull;&nbsp;Latentie in verhouding tot databaseruimte die wordt gebruikt als gevolg van het kopiëren van gegevens</br>&bull;&nbsp;Doorgaans minder dan 1 minuut per GEBRUIKTE GB ruimte|&bull;&nbsp;Latentie in verhouding tot databaseruimte die wordt gebruikt als gevolg van het kopiëren van gegevens</br>&bull;&nbsp;Doorgaans minder dan 1 minuut per GEBRUIKTE GB ruimte|&bull;&nbsp;Latentie in verhouding tot databaseruimte die wordt gebruikt als gevolg van het kopiëren van gegevens</br>&bull;&nbsp;Doorgaans minder dan 1 minuut per GEBRUIKTE GB ruimte|
|**Hyperscale**|N.v.t.|N.v.t.|N.v.t.|&bull;&nbsp;Constante tijdlatentie onafhankelijk van gebruikte ruimte</br>&bull;&nbsp;Doorgaans minder dan 2 minuten|

> [!NOTE]
> Daarnaast is voor Standard-databases (S2-S12) en Algemeen-databases de latentie voor het verplaatsen van een database in/uit een elastische pool of tussen elastische pools evenredig met de databasegrootte als de database gebruik maakt van[PFS-opslag](../../storage/files/storage-files-introduction.md)(Premium File Share).
>
> Als u wilt bepalen of een database PFS-opslag gebruikt, voert u de volgende query uit in de context van de database. Als de waarde in de kolom AccountType of is, gebruikt `PremiumFileStorage` `PremiumFileStorage-ZRS` de database PFS-opslag.

```sql
SELECT s.file_id,
       s.type_desc,
       s.name,
       FILEPROPERTYEX(s.name, 'AccountType') AS AccountType
FROM sys.database_files AS s
WHERE s.type_desc IN ('ROWS', 'LOG');
```

> [!NOTE]
> De zone-redundante eigenschap blijft standaard hetzelfde bij het schalen van de Bedrijfskritiek naar Algemeen laag. Latentie voor deze downgrade wanneer zone-redundantie is ingeschakeld en de latentie voor het overschakelen naar zone-redundantie voor de Algemeen-laag evenredig is met de databasegrootte.

> [!TIP]
> Als u actieve bewerkingen wilt bewaken, gaat u naar: Bewerkingen beheren met behulp van [de SQL REST API,](/rest/api/sql/operations/list)Bewerkingen beheren met [CLI,](/cli/azure/sql/db/op)Bewerkingen bewaken met [T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) en deze twee PowerShell-opdrachten: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) en [Stop-AzSqlDatabaseActivity.](/powershell/module/az.sql/stop-azsqldatabaseactivity)

## <a name="cancelling-changes"></a>Wijzigingen annuleren

Een wijziging in de servicelaag of herschalen van rekenkracht kan worden geannuleerd.

### <a name="the-azure-portal"></a>Azure Portal

Navigeer op de **overzichtsblade** van de database naar Meldingen en klik op de tegel die aangeeft dat er een bewerking wordt uitgevoerd:

![Lopende bewerking](./media/single-database-scale/ongoing-operations.png)

Klik vervolgens op de knop met het label **Deze bewerking annuleren.**

![Lopende bewerking annuleren](./media/single-database-scale/cancel-ongoing-operation.png)

### <a name="powershell"></a>PowerShell

Stel vanaf een PowerShell-opdrachtprompt `$resourceGroupName` de , en in en voer de volgende opdracht `$serverName` `$databaseName` uit:

```azurecli
$operationName = (az sql db op list --resource-group $resourceGroupName --server $serverName --database $databaseName --query "[?state=='InProgress'].name" --out tsv)
if (-not [string]::IsNullOrEmpty($operationName)) {
    (az sql db op cancel --resource-group $resourceGroupName --server $serverName --database $databaseName --name $operationName)
        "Operation " + $operationName + " has been canceled"
}
else {
    "No service tier change or compute rescaling operation found"
}
```

## <a name="additional-considerations"></a>Aanvullende overwegingen

- Als u een upgrade naar een hogere servicelaag of rekenkracht wilt uitvoeren, wordt de maximale grootte van de database niet groter, tenzij u expliciet een grotere grootte (maxsize) opgeeft.
- Als u een database wilt downgraden, moet de gebruikte databaseruimte kleiner zijn dan de maximaal toegestane grootte van de doelservicelaag en rekenkracht.
- Bij het downgraden van **Premium** naar de **Standard-laag** gelden extra opslagkosten als beide (1) de maximale grootte van de database worden ondersteund in de doelrekengrootte en (2) de maximale grootte groter is dan de inbegrepen opslaggrootte van de doelrekengrootte. Als een P1-database met een maximale grootte van 500 GB bijvoorbeeld wordt ge downseld naar S3, zijn er extra opslagkosten van toepassing omdat S3 een maximale grootte van 1 TB ondersteunt en de inbegrepen opslag slechts 250 GB is. De extra opslagruimte is dus 500 GB – 250 GB = 250 GB. Zie Prijzen voor Azure SQL Database prijzen voor [meer informatie.](https://azure.microsoft.com/pricing/details/sql-database/) Als de werkelijke hoeveelheid ruimte kleiner is dan de inbegrepen opslagruimte, kunnen deze extra kosten worden vermeden door de maximale grootte van de database te verkleinen tot de inbegrepen hoeveelheid.
- Wanneer u een database bijwerkt met [geo-replicatie](active-geo-replication-configure-portal.md) ingeschakeld, moet u de secundaire databases upgraden naar de gewenste servicelaag en rekenkracht voordat u de primaire database bijwerkt (algemene richtlijnen voor de beste prestaties). Bij een upgrade naar een andere editie is het een vereiste dat de secundaire database eerst wordt bijgewerkt.
- Wanneer u een database downgradet met [geo-replicatie](active-geo-replication-configure-portal.md) ingeschakeld, downgradet u de primaire databases naar de gewenste servicelaag en rekenkracht voordat u de secundaire database downgradet (algemene richtlijnen voor de beste prestaties). Wanneer u downgradet naar een andere editie, is het een vereiste dat de primaire database eerst wordt gedowngraded.
- De mogelijkheden om de service te herstellen verschillen voor de verschillende servicelagen. Als u downgradet naar  de basic-laag, is er een lagere bewaarperiode voor back-ups. Zie Azure SQL Database Backups ( [Back-ups).](automated-backups-overview.md)
- De nieuwe eigenschappen voor de database worden pas toegepast wanneer de wijzigingen zijn voltooid.
- Wanneer het kopiëren van gegevens vereist is om een database te schalen (zie [Latentie](#latency)) bij het wijzigen van de servicelaag, kan een hoog resourcegebruik dat gelijktijdig met de schaalbewerking wordt uitgevoerd, leiden tot langere schaaltijden. Met [Accelerated Database Recovery (ADR)](/sql/relational-databases/accelerated-database-recovery-concepts)is het terugdraaien van langlopende transacties geen belangrijke bron van vertraging, maar kan een hoog gelijktijdig resourcegebruik leiden tot minder reken-, opslag- en netwerkbandbreedteresources voor schalen, met name bij kleinere rekenkracht.

## <a name="billing"></a>Billing

U wordt gefactureerd voor elk uur dat een database bestaat met de hoogste servicelaag en rekenkracht die in dat uur zijn toegepast, ongeacht het gebruik of of de database minder dan een uur actief was. Als u bijvoorbeeld een individuele database maakt en deze vijf minuten later verwijdert, worden er kosten voor één databaseuur op uw factuur weergegeven.

## <a name="change-storage-size"></a>Opslaggrootte wijzigen

### <a name="vcore-based-purchasing-model"></a>Aankoopmodel op basis van vCore

- Opslag kan worden ingericht tot de maximale grootte van de gegevensopslag met stappen van 1 GB. De minimaal configureerbare gegevensopslag is 1 GB. Zie de pagina's met documentatie over resourcelimieten voor individuele databases met behulp van het [vCore-aankoopmodel](resource-limits-vcore-single-databases.md) en Resourcelimieten voor individuele databases met behulp van het DTU-aankoopmodel voor maximale grootte van gegevensopslag in elke [servicedoelstelling.](resource-limits-dtu-single-databases.md)
- Gegevensopslag voor één database kan worden ingericht door de maximale grootte te vergroten of te verlagen met behulp van [de Azure Portal,](https://portal.azure.com) [Transact-SQL,](/sql/t-sql/statements/alter-database-transact-sql#examples-1) [PowerShell,](/powershell/module/az.sql/set-azsqldatabase) [Azure CLI](/cli/azure/sql/db#az_sql_db_update) [of REST API](/rest/api/sql/databases/update). Als de maximale groottewaarde is opgegeven in bytes, moet deze een veelvoud van 1 GB (1073741824 bytes) zijn.
- De hoeveelheid gegevens die kan worden opgeslagen in de gegevensbestanden van een database, wordt beperkt door de maximale grootte van de geconfigureerde gegevensopslag. Naast deze opslag wijst Azure SQL Database automatisch 30% meer opslagruimte toe die moet worden gebruikt voor het transactielogboek.
- Azure SQL Database wijst automatisch 32 GB per vCore toe voor de `tempdb` database. `tempdb` bevindt zich op de lokale SSD-opslag in alle servicelagen.
- De opslagprijs voor één database of elastische pool is de som van de opslagbedragen voor gegevensopslag en transactielogboek, vermenigvuldigd met de opslageenheidprijs van de servicelaag. De kosten van `tempdb` zijn opgenomen in de prijs. Zie prijzen voor Azure SQL Database voor [meer informatie over de opslagprijs.](https://azure.microsoft.com/pricing/details/sql-database/)

> [!IMPORTANT]
> Onder bepaalde omstandigheden moet u mogelijk een database verkleinen om ongebruikte ruimte vrij te maken. Zie Bestandsruimte beheren in Azure SQL Database voor [meer informatie.](file-space-manage.md)

### <a name="dtu-based-purchasing-model"></a>Aankoopmodel op basis van DTU

- De DTU-prijs voor een individuele database omvat een bepaalde hoeveelheid opslag, zonder extra kosten. Extra opslag buiten het inbegrepen bedrag kan worden ingericht voor extra kosten tot de maximale groottelimiet in stappen van 250 GB tot 1 TB en vervolgens in stappen van 256 GB boven 1 TB. Zie Individuele database: opslaggrootten en rekengrootten voor inbegrepen opslagbedragen en maximale [groottelimieten.](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes)
- Extra opslag voor één database kan worden ingericht door de maximale grootte te vergroten met behulp van de Azure Portal, [Transact-SQL,](/sql/t-sql/statements/alter-database-transact-sql#examples-1) [PowerShell,](/powershell/module/az.sql/set-azsqldatabase) [de Azure CLI](/cli/azure/sql/db#az_sql_db_update)of de [REST API](/rest/api/sql/databases/update).
- De prijs van extra opslag voor een individuele database is de extra opslagruimte vermenigvuldigd met de extra opslageenheidprijs van de servicelaag. Zie prijzen voor meer informatie over de prijs [van Azure SQL Database opslag.](https://azure.microsoft.com/pricing/details/sql-database/)

> [!IMPORTANT]
> Onder bepaalde omstandigheden moet u mogelijk een database verkleinen om ongebruikte ruimte vrij te maken. Zie Bestandsruimte beheren in Azure SQL Database voor [meer informatie.](file-space-manage.md)

### <a name="geo-replicated-database"></a>Geo-gerepliceerde database

Als u de databasegrootte van een gerepliceerde secundaire database wilt wijzigen, wijzigt u de grootte van de primaire database. Deze wijziging wordt vervolgens ook gerepliceerd en geïmplementeerd in de secundaire database.

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>P11- en P15-beperkingen wanneer maximale grootte groter is dan 1 TB

Meer dan 1 TB aan opslag in de Premium-laag is momenteel beschikbaar in alle regio's, behalve: China - oost, China - noord, Duitsland - centraal en Duitsland - noordoost. In deze regio’s is de maximale opslagruimte in de Premium-laag beperkt tot 1 TB. De volgende overwegingen en beperkingen zijn van toepassing op P11- en P15-databases met een maximale grootte van meer dan 1 TB:

- Als de maximale grootte voor een P11- of P15-database ooit is ingesteld op een waarde van meer dan 1 TB, kan deze dan alleen worden hersteld of gekopieerd naar een P11- of P15-database.  De database kan vervolgens opnieuw worden geschaald naar een andere rekenkracht, mits de hoeveelheid ruimte die is toegewezen op het moment van de herschaalbewerking de maximale groottelimieten van de nieuwe rekenkracht niet overschrijdt.
- Voor actieve geo-replicatiescenario's:
  - Een geo-replicatierelatie instellen: als de primaire database P11 of P15 is, moeten de secundaire database(s) ook P11 of P15 zijn. Een lagere rekenkracht wordt geweigerd als secondaries omdat ze niet meer dan 1 TB kunnen ondersteunen.
  - Een upgrade uitvoeren van de primaire database in een geo-replicatierelatie: Als u de maximale grootte wijzigt in meer dan 1 TB op een primaire database, wordt dezelfde wijziging in de secundaire database activeert. Beide upgrades moeten zijn geslaagd om de wijziging op de primaire van kracht te laten worden. Regiobeperkingen voor de optie van meer dan 1 TB zijn van toepassing. Als de secundaire zich in een regio met geen ondersteuning voor meer dan 1 TB, wordt de primaire niet bijgewerkt.
- Het gebruik van de Import/Export-service voor het laden van P11/P15-databases met meer dan 1 TB wordt niet ondersteund. Gebruik SqlPackage.exe om [gegevens te importeren](database-import.md) [en te](database-export.md) exporteren.

## <a name="next-steps"></a>Volgende stappen

Zie resourcelimieten op basis [Azure SQL Database vCore -](resource-limits-vcore-single-databases.md) individuele databases en Azure SQL Database op DTU gebaseerde resourcelimieten - individuele databases voor algemene [resourcelimieten.](resource-limits-dtu-single-databases.md)
