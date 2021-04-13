---
title: De schaal van de resources voor één database aanpassen
description: In dit artikel wordt beschreven hoe u de berekenings-en opslag resources schaalt die beschikbaar zijn voor één data base in Azure SQL Database.
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
ms.openlocfilehash: 47686f457e2579ca8a643de6671c886effefa6f1
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313518"
---
# <a name="scale-single-database-resources-in-azure-sql-database"></a>Resources van een individuele database schalen in Azure SQL Database

In dit artikel wordt beschreven hoe u de berekenings-en opslag resources schaalt die beschikbaar zijn voor een Azure SQL Database in de ingerichte Compute-laag. De [Compute-laag zonder server](serverless-tier-overview.md) biedt ook reken capaciteit automatisch schalen en facturen per seconde voor gebruikte reken kracht.

Nadat u het aantal vCores of Dtu's hebt gekozen, kunt u een enkele data base dynamisch omhoog of omlaag schalen op basis van de werkelijke ervaring met:

* [Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#overview-sql-database)
* [Azure-portal](single-database-manage.md#the-azure-portal)
* [PowerShell](/powershell/module/az.sql/set-azsqldatabase)
* [Azure-CLI](/cli/azure/sql/db#az-sql-db-update)
* [REST API](/rest/api/sql/databases/update)


De volgende video toont het dynamisch wijzigen van de servicelaag en de berekenings grootte om beschik bare Dtu's voor één Data Base te verg Roten.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Azure-SQL-Database-dynamically-scale-up-or-scale-down/player]

> [!IMPORTANT]
> In sommige gevallen moet u mogelijk een Data Base verkleinen om ongebruikte ruimte te claimen. Zie [Bestands ruimte beheren in Azure SQL database](file-space-manage.md)voor meer informatie.

## <a name="impact"></a>Impact

Het wijzigen van de servicelaag of reken grootte van voornamelijk omvat de service die de volgende stappen uitvoert:

1. Maak een nieuw reken exemplaar voor de-data base. 

    Er wordt een nieuw reken exemplaar gemaakt met de aangevraagde servicelaag en de berekenings grootte. Voor sommige combi Naties van de service tier en de grootte van de berekening moet er een replica van de Data Base worden gemaakt in het nieuwe reken exemplaar, waarbij gegevens worden gekopieerd en de algehele latentie kan worden beïnvloed. De data base blijft tijdens deze stap online en er worden verbindingen met de data base in het oorspronkelijke Compute-exemplaar in rekening gebracht.

2. Routeren van verbindingen naar een nieuw reken exemplaar overschakelen.

    Bestaande verbindingen met de data base in het oorspronkelijke Compute-exemplaar worden verwijderd. Nieuwe verbindingen worden tot stand gebracht in de data base in het nieuwe reken exemplaar. Voor sommige combi Naties van service tier-en Compute-wijzigingen worden database bestanden losgekoppeld en opnieuw gekoppeld tijdens de switch.  De switch kan echter een korte onderbreking van de service tot gevolg hebben wanneer de data base minder dan 30 seconden lang niet beschikbaar is, en vaak slechts enkele seconden. Als er langlopende trans acties worden uitgevoerd wanneer verbindingen worden verbroken, kan de duur van deze stap langer duren om afgebroken trans acties te herstellen. [Versneld database herstel](../accelerated-database-recovery.md) kan het effect verminderen van het afbreken van langlopende trans acties.

> [!IMPORTANT]
> Er gaan geen gegevens verloren tijdens elke stap in de werk stroom. Zorg ervoor dat u enkele [pogings logica](troubleshoot-common-connectivity-issues.md) hebt geïmplementeerd in de toepassingen en onderdelen die Azure SQL database gebruiken terwijl de servicelaag wordt gewijzigd.

## <a name="latency"></a>Latentie

De geschatte latentie voor het wijzigen van de servicelaag, het schalen van de reken grootte van één data base of elastische pool, het verplaatsen van een data base in/uit een elastische pool of het verplaatsen van een Data Base tussen elastische Pools is als volgt:

|Servicelaag|Basis, afzonderlijke Data Base,</br>Standaard (S0-S1)|Algemene elastische pool,</br>Standard (S2-S12), </br>Algemeen afzonderlijke data base of elastische pool|Premium of Bedrijfskritiek afzonderlijke data base of elastische pool|Hyperscale
|:---|:---|:---|:---|:---|
|**Basis enkele data base, </br> standaard (S0-S1)**|&bull;&nbsp;Constante tijd latentie, onafhankelijk van gebruikte ruimte</br>&bull;&nbsp;Normaal gesp roken minder dan 5 minuten|&bull;&nbsp;Latentie in verhouding tot de database ruimte die wordt gebruikt door het kopiëren van gegevens</br>&bull;&nbsp;Normaal gesp roken, minder dan 1 minuut per GB gebruikte ruimte|&bull;&nbsp;Latentie in verhouding tot de database ruimte die wordt gebruikt door het kopiëren van gegevens</br>&bull;&nbsp;Normaal gesp roken, minder dan 1 minuut per GB gebruikte ruimte|&bull;&nbsp;Latentie in verhouding tot de database ruimte die wordt gebruikt door het kopiëren van gegevens</br>&bull;&nbsp;Normaal gesp roken, minder dan 1 minuut per GB gebruikte ruimte|
|**Basis elastische pool, </br> standaard (S2-S12), </br> algemeen afzonderlijke data base of elastische pool**|&bull;&nbsp;Latentie in verhouding tot de database ruimte die wordt gebruikt door het kopiëren van gegevens</br>&bull;&nbsp;Normaal gesp roken, minder dan 1 minuut per GB gebruikte ruimte|&bull;&nbsp;Voor individuele data bases geldt een constante tijd latentie, onafhankelijk van de gebruikte ruimte</br>&bull;&nbsp;Meestal minder dan 5 minuten voor afzonderlijke data bases</br>&bull;&nbsp;Voor elastische Pools, evenredig met het aantal data bases|&bull;&nbsp;Latentie in verhouding tot de database ruimte die wordt gebruikt door het kopiëren van gegevens</br>&bull;&nbsp;Normaal gesp roken, minder dan 1 minuut per GB gebruikte ruimte|&bull;&nbsp;Latentie in verhouding tot de database ruimte die wordt gebruikt door het kopiëren van gegevens</br>&bull;&nbsp;Normaal gesp roken, minder dan 1 minuut per GB gebruikte ruimte|
|**Premium of Bedrijfskritiek afzonderlijke data base of elastische pool**|&bull;&nbsp;Latentie in verhouding tot de database ruimte die wordt gebruikt door het kopiëren van gegevens</br>&bull;&nbsp;Normaal gesp roken, minder dan 1 minuut per GB gebruikte ruimte|&bull;&nbsp;Latentie in verhouding tot de database ruimte die wordt gebruikt door het kopiëren van gegevens</br>&bull;&nbsp;Normaal gesp roken, minder dan 1 minuut per GB gebruikte ruimte|&bull;&nbsp;Latentie in verhouding tot de database ruimte die wordt gebruikt door het kopiëren van gegevens</br>&bull;&nbsp;Normaal gesp roken, minder dan 1 minuut per GB gebruikte ruimte|&bull;&nbsp;Latentie in verhouding tot de database ruimte die wordt gebruikt door het kopiëren van gegevens</br>&bull;&nbsp;Normaal gesp roken, minder dan 1 minuut per GB gebruikte ruimte|
|**Hyperscale**|N.v.t.|N.v.t.|N.v.t.|&bull;&nbsp;Constante tijd latentie, onafhankelijk van gebruikte ruimte</br>&bull;&nbsp;Normaal gesp roken minder dan 2 minuten|

> [!NOTE]
> Voor standaard (S2-S12) en Algemeen-data bases geldt dat latentie voor het verplaatsen van een data base in/uit een elastische pool of tussen elastische Pools evenredig is met de grootte van de Data Base als de data base gebruikmaakt van[PFS](../../storage/files/storage-files-introduction.md)-opslag (Premium file share).
>
> Voer de volgende query uit in de context van de data base om te bepalen of een Data Base PFS-opslag gebruikt. Als de waarde in de kolom account type is `PremiumFileStorage` of `PremiumFileStorage-ZRS` , gebruikt de data base PFS-opslag.

```sql
SELECT s.file_id,
       s.type_desc,
       s.name,
       FILEPROPERTYEX(s.name, 'AccountType') AS AccountType
FROM sys.database_files AS s
WHERE s.type_desc IN ('ROWS', 'LOG');
```

> [!NOTE]
> De zone redundante eigenschap blijft standaard hetzelfde wanneer u van de Bedrijfskritiek naar de Algemeen laag wilt schalen. Latentie voor deze downgrade wanneer zone redundantie is ingeschakeld en latentie voor het overschakelen naar zone redundantie voor de Algemeen laag evenredig is met de grootte van de data base.

> [!TIP]
> Zie voor het controleren van bewerkingen in uitvoering: [bewerkingen beheren met behulp van de SQL rest API](/rest/api/sql/operations/list), [bewerkingen beheren met CLI](/cli/azure/sql/db/op), [bewerkingen bewaken met T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) en deze twee Power shell-opdrachten: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) en [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

## <a name="cancelling-changes"></a>Wijzigingen annuleren

Een wijzigings-of COMPUTE-bewerking voor het opnieuw schalen van een service tier kan worden geannuleerd.

### <a name="the-azure-portal"></a>Azure Portal

Ga op de Blade database overzicht naar **meldingen** en klik op de tegel die aangeeft dat er een doorlopende bewerking is:

![Doorlopende bewerking](./media/single-database-scale/ongoing-operations.png)

Klik vervolgens op de knop met het label **annulering van deze bewerking**.

![Doorlopende bewerking annuleren](./media/single-database-scale/cancel-ongoing-operation.png)

### <a name="powershell"></a>PowerShell

Stel vanuit een Power shell-opdracht prompt de `$resourceGroupName` , `$serverName` en in `$databaseName` en voer vervolgens de volgende opdracht uit:

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

- Als u een upgrade uitvoert naar een hogere servicelaag of reken grootte, wordt de maximale grootte van de data base niet verhoogd, tenzij u expliciet een grotere grootte (maxSize) opgeeft.
- Om een Data Base te downgradeen, moet de gebruikte data base kleiner zijn dan de Maxi maal toegestane grootte van de doellaag en de grootte van de berekening.
- Bij downgrade van **Premium** naar de laag **standaard** geldt een extra opslag kosten als beide (1) de maximale grootte van de Data Base worden ondersteund in de doel berekenings grootte, en (2) de maximale grootte de inbegrepen opslag hoeveelheid van de doel berekenings grootte overschrijdt. Als een P1-data base met een maximale grootte van 500 GB bijvoorbeeld verkleind is aan S3, geldt er een extra opslag kosten, aangezien S3 een maximum grootte van 1 TB ondersteunt en de inbegrepen opslag hoeveelheid alleen 250 GB is. De extra opslag ruimte is dus 500 GB – 250 GB = 250 GB. Zie voor prijzen voor extra opslag [Azure SQL database prijzen](https://azure.microsoft.com/pricing/details/sql-database/). Als de daad werkelijke hoeveelheid gebruikte ruimte kleiner is dan het totale aantal opslag, kunnen deze extra kosten worden vermeden door de maximale grootte van de data base te verlagen tot het opgenomen bedrag.
- Bij het bijwerken van een Data Base waarvoor [geo-replicatie](active-geo-replication-configure-portal.md) is ingeschakeld, werkt u de secundaire data bases bij naar de gewenste servicelaag en de berekenings grootte vóór de upgrade van de primaire data base (algemene richt lijnen voor de beste prestaties). Wanneer u een upgrade uitvoert naar een andere editie, is het een vereiste dat de secundaire data base eerst wordt geüpgraded.
- Bij het downgradeen van een Data Base met [geo-replicatie](active-geo-replication-configure-portal.md) is ingeschakeld, downgradet u de primaire data bases naar de gewenste servicelaag en reken grootte voordat u de secundaire data base downgradet (algemene richt lijnen voor de beste prestaties). Bij downgrade naar een andere editie is het een vereiste dat de primaire data base het eerst downgradet.
- De mogelijkheden om de service te herstellen verschillen voor de verschillende servicelagen. Als u downgradet naar de **basis** -laag, hebt u een lagere Bewaar periode voor back-ups. Zie [Azure SQL database back-ups](automated-backups-overview.md).
- De nieuwe eigenschappen voor de Data Base worden pas toegepast als de wijzigingen zijn voltooid.
- Wanneer het kopiëren van gegevens vereist is voor het schalen van een Data Base (Zie [latentie](#latency)) bij het wijzigen van de servicelaag, kan het hoge bron gebruik voor de schaal bewerking langer duren. Met [versneld database herstel (ADR)](/sql/relational-databases/accelerated-database-recovery-concepts)is het terugdraaien van langlopende trans acties geen aanzienlijke vertragings bron, maar met het hoge gelijktijdige gebruik van bronnen kunnen minder compute-, opslag-en netwerk bandbreedte bronnen worden gebruikt voor schalen, met name voor kleinere reken grootten.

## <a name="billing"></a>Billing

Er worden kosten in rekening gebracht voor elk uur dat een data base bestaat met de hoogste service laag + reken grootte die tijdens dat uur is toegepast, ongeacht het gebruik of het feit dat de data base minder dan een uur actief was. Als u bijvoorbeeld een enkele data base maakt en deze vijf minuten later verwijdert, wordt de factuur voor één data base uur weer gegeven.

## <a name="change-storage-size"></a>Opslag grootte wijzigen

### <a name="vcore-based-purchasing-model"></a>Aankoopmodel op basis van vCore

- Opslag kan worden ingericht tot de maximale grootte van de gegevens opslag met een veelvoud van 1 GB. De minimale Configureer bare gegevens opslag is 1 GB. Zie documentatie pagina's voor resource limieten voor [resource limieten voor afzonderlijke data bases met behulp van het vCore-aankoop model](resource-limits-vcore-single-databases.md) en [resource limieten voor afzonderlijke data bases met behulp van het DTU-aankoop model](resource-limits-dtu-single-databases.md)voor de maximale grootte limieten voor gegevens opslag in elke service doelstelling.
- Gegevens opslag voor één data base kan worden ingericht door de maximale grootte te verhogen of te verlagen met behulp van de [Azure Portal](https://portal.azure.com), [Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#examples-1), [Power shell](/powershell/module/az.sql/set-azsqldatabase), [Azure cli](/cli/azure/sql/db#az-sql-db-update)of [rest API](/rest/api/sql/databases/update). Als de waarde voor de maximale grootte in bytes is opgegeven, moet deze een meervoud van 1 GB (1073741824 bytes) zijn.
- De hoeveelheid gegevens die kan worden opgeslagen in de gegevens bestanden van een Data Base wordt beperkt door de geconfigureerde maximale grootte van de gegevens opslag. Naast die opslag wordt met Azure SQL Database automatisch 30% meer opslag toegewezen voor het transactie logboek.
- Azure SQL Database wijst 32 GB per vCore automatisch toe aan de `tempdb` Data Base. `tempdb` bevindt zich in de lokale SSD-opslag in alle service lagen.
- De prijs van opslag voor één data base of een elastische pool is de som van de hoeveelheid gegevens opslag en de opslag van transactie logboeken vermenigvuldigd met de prijs van de opslag eenheid van de servicelaag. De kosten van `tempdb` zijn inbegrepen in de prijs. Zie voor meer informatie over de opslag prijs [Azure SQL database prijzen](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> In sommige gevallen moet u mogelijk een Data Base verkleinen om ongebruikte ruimte te claimen. Zie [Bestands ruimte beheren in Azure SQL database](file-space-manage.md)voor meer informatie.

### <a name="dtu-based-purchasing-model"></a>Op DTU gebaseerd inkoop model

- De DTU-prijs voor één Data Base bevat een bepaalde hoeveelheid opslag zonder extra kosten. Extra opslag buiten de inbegrepen hoeveelheid kan worden ingericht voor extra kosten tot de maximale grootte in stappen van 250 GB tot 1 TB en vervolgens in stappen van 256 GB tot meer dan 1 TB. Zie [Single Data Base: opslag grootten en reken grootten](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes)voor inbegrepen opslag bedragen en maximale grootte limieten.
- Extra opslag ruimte voor één data base kan worden ingericht door de maximale grootte te verhogen met behulp van de Azure Portal, [Transact-SQL](/sql/t-sql/statements/alter-database-transact-sql#examples-1), [Power shell](/powershell/module/az.sql/set-azsqldatabase), de [Azure cli](/cli/azure/sql/db#az-sql-db-update)of de [rest API](/rest/api/sql/databases/update).
- De prijs van extra opslag voor één data base is de extra opslag hoeveelheid vermenigvuldigd met de extra eenheids prijs voor opslag van de servicelaag. Zie [Azure SQL database prijzen](https://azure.microsoft.com/pricing/details/sql-database/)voor meer informatie over de prijs van extra opslag.

> [!IMPORTANT]
> In sommige gevallen moet u mogelijk een Data Base verkleinen om ongebruikte ruimte te claimen. Zie [Bestands ruimte beheren in Azure SQL database](file-space-manage.md)voor meer informatie.

### <a name="geo-replicated-database"></a>Geo-gerepliceerde data base

Als u de grootte van de data base van een gerepliceerde secundaire Data Base wilt wijzigen, wijzigt u de grootte van de primaire data base. Deze wijziging wordt dan ook gerepliceerd en geïmplementeerd op de secundaire data base.

## <a name="p11-and-p15-constraints-when-max-size-greater-than-1-tb"></a>P11-en P15-beperkingen bij een maximale grootte van meer dan 1 TB

Meer dan 1 TB aan opslag ruimte in de Premium-laag is momenteel beschikbaar in alle regio's behalve: China-oost, China-noord, Duitsland-centraal en Duitsland-noordoost. In deze regio’s is de maximale opslagruimte in de Premium-laag beperkt tot 1 TB. De volgende overwegingen en beperkingen zijn van toepassing op P11-en P15-data bases met een maximale grootte van meer dan 1 TB:

- Als de maximale grootte voor een P11-of P15-data base ooit is ingesteld op een waarde die groter is dan 1 TB, dan kan deze alleen worden teruggezet of gekopieerd naar een P11-of P15-data base.  Vervolgens kan de data base opnieuw worden geschaald naar een andere reken grootte, op voor waarde dat de hoeveelheid toegewezen ruimte op het moment van de bewerking voor opnieuw schalen niet groter is dan de maximale grootte limieten van de nieuwe reken grootte.
- Voor scenario's met actieve geo-replicatie:
  - Instellen van een geo-replicatie relatie: als de primaire data base P11 of P15 is, moeten de secundaire (s) ook P11 of P15 zijn. Een lagere reken grootte wordt afgewezen als secundaire data bases, omdat ze niet meer dan 1 TB kunnen ondersteunen.
  - Een upgrade uitvoeren van de primaire data base in een geo-replicatie relatie: als u de maximum grootte wijzigt in meer dan 1 TB op een primaire data base, wordt dezelfde wijziging geactiveerd voor de secundaire data base. Beide upgrades moeten worden uitgevoerd om de wijziging van kracht te laten worden. Er gelden beperkingen voor regio's voor de optie meer dan 1 TB. Als het secundaire deel uitmaakt van een regio die niet meer dan 1 TB ondersteunt, wordt de primaire niet geüpgraded.
- Het is niet mogelijk om de import/export-service te gebruiken voor het laden van P11/P15-data bases met meer dan 1 TB. Gebruik SqlPackage.exe om gegevens te [importeren](database-import.md) en [exporteren](database-export.md) .

## <a name="next-steps"></a>Volgende stappen

Zie [Azure SQL database resource limieten op basis van vCore-afzonderlijke data bases](resource-limits-vcore-single-databases.md) en [Azure SQL database DTU-gebaseerde resource limieten-afzonderlijke data bases](resource-limits-dtu-single-databases.md)voor algemene resource limieten.
