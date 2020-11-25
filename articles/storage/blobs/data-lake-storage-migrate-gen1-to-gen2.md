---
title: Azure Data Lake Storage migreren van gen1 naar Gen2
description: Migreer Azure Data Lake Storage van gen1 naar Gen2, dat is gebaseerd op Azure Blob-opslag en biedt een reeks mogelijkheden die zijn toegewezen aan big data Analytics.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 03/11/2020
ms.service: storage
ms.reviewer: rukmani-msft
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: aeb2c58504d1f058a3b887e02a7b7406c09db5b6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95913144"
---
# <a name="migrate-azure-data-lake-storage-from-gen1-to-gen2"></a>Azure Data Lake Storage migreren van gen1 naar Gen2

U kunt uw gegevens, werk belastingen en toepassingen migreren van Data Lake Storage Gen1 naar Data Lake Storage Gen2.

Azure Data Lake Storage Gen2 is gebaseerd op [Azure Blob-opslag](storage-blobs-introduction.md) en biedt een reeks mogelijkheden die zijn toegewezen aan Big Data Analytics. [Data Lake Storage Gen2](https://azure.microsoft.com/services/storage/data-lake-storage/) combineert functies van [Azure data Lake Storage gen1](../../data-lake-store/index.yml), zoals bestandssysteem semantiek, Directory en beveiliging op bestands niveau en schaal met goedkope, gelaagde opslag, hoge Beschik baarheid/herstel na nood gevallen vanuit [Azure Blob-opslag](storage-blobs-introduction.md).

> [!NOTE]
> Voor een eenvoudiger lees gemak gebruikt dit artikel de term *gen1* om naar Azure data Lake Storage gen1 te verwijzen en de term *Gen2* om naar Azure data Lake Storage Gen2 te verwijzen.

## <a name="recommended-approach"></a>Aanbevolen benadering

Als u wilt migreren naar Gen2, raden we u aan de volgende benadering uit te voeren.

: heavy_check_mark: stap 1: gereedheid evalueren

: heavy_check_mark: stap 2: migratie voorbereiden

: heavy_check_mark: stap 3: gegevens en werk belastingen van toepassingen migreren

: heavy_check_mark: stap 4: Cutover van gen1 naar Gen2

> [!NOTE]
> Gen1 en Gen2 zijn verschillende services, er is geen in-place upgrade ervaring, wat opzettelijke migratie nodig is. 

### <a name="step-1-assess-readiness"></a>Stap 1: gereedheid beoordelen

1. Meer informatie over de [Data Lake Storage Gen2 aanbieding](https://azure.microsoft.com/services/storage/data-lake-storage/); het gaat om voor delen, kosten en algemene architectuur. 

2. [Vergelijk de mogelijkheden](#gen1-gen2-feature-comparison) van gen1 met die van Gen2. 

3. Bekijk een lijst met [bekende problemen](data-lake-storage-known-issues.md) om eventuele hiaten in de functionaliteit te beoordelen.

4. Gen2 biedt ondersteuning voor Blob Storage-functies, zoals [Diagnostische logboek registratie](../common/storage-analytics-logging.md), [toegangs lagen](storage-blob-storage-tiers.md)en beleid voor het [levenscyclus beheer van Blob-opslag](storage-lifecycle-management-concepts.md). Als u geïnteresseerd bent in het gebruik van een van deze functies, raadpleegt u het [huidige ondersteunings niveau](./data-lake-storage-supported-blob-storage-features.md).

5. Bekijk de huidige status van [Azure ecosysteem ondersteuning](./data-lake-storage-multi-protocol-access.md) om ervoor te zorgen dat Gen2 alle services ondersteunt waarvan uw oplossingen afhankelijk zijn.

### <a name="step-2-prepare-to-migrate"></a>Stap 2: migratie voorbereiden

1. Identificeer de gegevens sets die u wilt migreren.

   Volg deze mogelijkheid om gegevens sets op te schonen die u niet meer gebruikt. Tenzij u van plan bent om al uw gegevens in één keer te migreren, moet u deze tijd volgen om logische gegevens groepen te identificeren die u in fasen kunt migreren.
   
2. Bepaal wat het effect is van een migratie op uw bedrijf.

   U kunt bijvoorbeeld overwegen of u elke downtime wilt veroorloven terwijl de migratie plaatsvindt. Deze overwegingen kunnen u helpen bij het identificeren van een geschikt migratie patroon en het kiezen van de meest geschikte hulpprogram ma's.

3. Een migratie plan maken. 

   Deze [migratie patronen](#migration-patterns)worden aangeraden. U kunt een van deze patronen kiezen, samen combi neren of een aangepaste eigen patroon ontwerpen.

### <a name="step-3-migrate-data-workloads-and-applications"></a>Stap 3: gegevens, werk belastingen en toepassingen migreren

Migreer gegevens, werk belastingen en toepassingen met behulp van het gewenste patroon. We raden u aan scenario's incrementeel te valideren.

1. [Maak een opslag account](../common/storage-account-create.md) en schakel de functie voor hiërarchische naam ruimte in. 

2. Uw gegevens migreren. 

3. Configureer [Services in uw workloads](./data-lake-storage-supported-azure-services.md) zodat deze verwijzen naar uw Gen2-eind punt. 
   
4. Toepassingen bijwerken voor het gebruik van Gen2-Api's. Raadpleeg de hand leidingen voor [.net](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [python](data-lake-storage-directory-file-acl-python.md), [Java script](data-lake-storage-directory-file-acl-javascript.md) en [rest](/rest/api/storageservices/data-lake-storage-gen2). 
   
5. Scripts bijwerken om Data Lake Storage Gen2 [Power shell-cmdlets](data-lake-storage-directory-file-acl-powershell.md)en [Azure cli-opdrachten](data-lake-storage-directory-file-acl-cli.md)te gebruiken.
   
6. Zoek naar URI-verwijzingen die de teken reeks `adl://` in code bestanden bevatten, of in Databricks-notebooks, Apache Hive HQL-bestanden of een ander bestand dat wordt gebruikt als onderdeel van uw werk belastingen. Vervang deze verwijzingen door de met [Gen2 geformatteerde URI](data-lake-storage-introduction-abfs-uri.md) van uw nieuwe opslag account. Bijvoorbeeld: de gen1-URI: wordt `adl://mydatalakestore.azuredatalakestore.net/mydirectory/myfile` mogelijk `abfss://myfilesystem@mydatalakestore.dfs.core.windows.net/mydirectory/myfile` . 

7. Configureer de beveiliging van uw account voor [Azure-functies](../common/storage-auth-aad-rbac-portal.md), [beveiliging op bestands-en mapniveau](data-lake-storage-access-control.md)en [Azure Storage firewalls en virtuele netwerken](../common/storage-network-security.md).

### <a name="step-4-cutover-from-gen1-to-gen2"></a>Stap 4: Cutover van gen1 naar Gen2

Wanneer u zeker weet dat uw toepassingen en workloads stabiel zijn op Gen2, kunt u Gen2 gaan gebruiken om te voldoen aan uw bedrijfs scenario's. Schakel alle resterende pijp lijnen uit die worden uitgevoerd op gen1 en maak uw gen1-account buiten gebruik. 

<a id="gen1-gen2-feature-comparison"></a>

## <a name="gen1-vs-gen2-capabilities"></a>Mogelijkheden van gen1 tegenover Gen2

Deze tabel vergelijkt de mogelijkheden van gen1 tot die van Gen2.

|Gebied |Gen1   |Gen2 |
|---|---|---|
|Gegevens organisatie|[Hiërarchische naamruimte](data-lake-storage-namespace.md)<br>Ondersteuning voor bestanden en mappen|[Hiërarchische naamruimte](data-lake-storage-namespace.md)<br>Ondersteuning voor containers, bestanden en mappen |
|Geografische redundantie| [LRS](../common/storage-redundancy.md#locally-redundant-storage)| [LRS](../common/storage-redundancy.md#locally-redundant-storage), [ZRS](../common/storage-redundancy.md#zone-redundant-storage), [GRS](../common/storage-redundancy.md#geo-redundant-storage), [Ra-GRS](../common/storage-redundancy.md#read-access-to-data-in-the-secondary-region) |
|Verificatie|[Door AAD beheerde identiteit](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Service-principals](../../active-directory/develop/app-objects-and-service-principals.md)|[Door AAD beheerde identiteit](../../active-directory/managed-identities-azure-resources/overview.md)<br>[Service-principals](../../active-directory/develop/app-objects-and-service-principals.md)<br>[Gedeelde toegangs sleutel](/rest/api/storageservices/authorize-with-shared-key)|
|Autorisatie|Beheer- [Azure RBAC](../../role-based-access-control/overview.md)<br>Gegevens: [acl's](data-lake-storage-access-control.md)|Beheer – [Azure RBAC](../../role-based-access-control/overview.md)<br>Gegevens-  [acl's](data-lake-storage-access-control.md), [Azure RBAC](../../role-based-access-control/overview.md) |
|Versleuteling – Data-at-rest|Server zijde: met door [micro soft beheerde](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) of door de [klant beheerde](../common/customer-managed-keys-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) sleutels|Server zijde: met door [micro soft beheerde](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) of door de [klant beheerde](../common/customer-managed-keys-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) sleutels|
|VNET-ondersteuning|[VNET-integratie](../../data-lake-store/data-lake-store-network-security.md)|[Service-eind punten](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [persoonlijke eind punten](../common/storage-private-endpoints.md)|
|Ontwikkelaars ervaring|[Rest](../../data-lake-store/data-lake-store-data-operations-rest-api.md), [.net](../../data-lake-store/data-lake-store-data-operations-net-sdk.md), [Java](../../data-lake-store/data-lake-store-get-started-java-sdk.md), [python](../../data-lake-store/data-lake-store-data-operations-python.md), [Power shell](../../data-lake-store/data-lake-store-get-started-powershell.md), [Azure cli](../../data-lake-store/data-lake-store-get-started-cli-2.0.md)|Algemeen beschikbaar- [rest](/rest/api/storageservices/data-lake-storage-gen2), [.net](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [python](data-lake-storage-directory-file-acl-python.md)<br>Open bare preview- [Java script](data-lake-storage-directory-file-acl-javascript.md), [Power shell](data-lake-storage-directory-file-acl-powershell.md), [Azure cli](data-lake-storage-directory-file-acl-cli.md)|
|Resourcelogboeken|Klassieke logboeken<br>[Azure Monitor geïntegreerd](../../data-lake-store/data-lake-store-diagnostic-logs.md)|[Klassieke logboeken](../common/storage-analytics-logging.md) -algemeen beschikbaar<br>Integratie van Azure monitor – tijd lijn TBD|
|Ecosysteem|[HDInsight (3,6)](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md), [Azure Databricks (3,1 en hoger)](https://docs.databricks.com/data/data-sources/azure/azure-datalake.html), [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md), [ADF](../../data-factory/load-azure-data-lake-store.md)|[HDInsight (3,6, 4,0)](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md), [Azure Databricks (5,1 en hoger)](/azure/databricks/data/data-sources/azure/azure-datalake-gen2), [Azure Synapse Analytics](../../azure-sql/database/vnet-service-endpoint-rule-overview.md), [ADF](../../data-factory/load-azure-data-lake-storage-gen2.md)|

<a id="migration-patterns"></a>

## <a name="gen1-to-gen2-patterns"></a>Gen1 naar Gen2-patronen

Kies een migratie patroon en wijzig dit patroon naar wens.

|||
|---|---|
|**Lift en SHIFT**|Het eenvoudigste patroon. Ideaal als uw gegevens pijplijnen downtime kunnen veroorloven.|
|**Incrementele kopie**|Net als bij *liften en verschuiving*, maar met minder downtime. Ideaal voor grote hoeveel heden gegevens die meer moeten worden gekopieerd.|
|**Dubbele pijp lijn**|Ideaal voor pijp lijnen die geen downtime kunnen bieden.|
|**Bidirectionele synchronisatie**|Net als bij *dubbele pijp lijn*, maar met een meer gefaseerde benadering die geschikt is voor complexere pijp lijnen.|

Laten we eens kijken naar elk patroon.
 
### <a name="lift-and-shift-pattern"></a>Patroon voor lift en verschuiving

Dit is het eenvoudigste patroon. 

1. Alle schrijf bewerkingen naar gen1 stoppen.

2. Verplaats gegevens van gen1 naar Gen2. We raden u aan [Azure Data Factory](../../data-factory/connector-azure-data-lake-storage.md). Acl's kopiëren met de gegevens.

3. Het opnemen van bewerkingen en workloads naar Gen2.

4. Gen1 uit bedrijf nemen.

Bekijk onze voorbeeld code voor het lift-en Shift-patroon in ons voor beeld van lift-en [SHIFT-migratie](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Lift%20and%20Shift/README.md).

> [!div class="mx-imgBorder"]
> ![patroon voor lift en verschuiving](./media/data-lake-storage-migrate-gen1-to-gen2/lift-and-shift.png)

#### <a name="considerations-for-using-the-lift-and-shift-pattern"></a>Overwegingen voor het gebruik van het lift-en Shift-patroon

: heavy_check_mark: Cutover van gen1 naar Gen2 voor alle werk belastingen tegelijk.

: heavy_check_mark: verwachte uitval tijd tijdens de migratie en de cutover periode.

: heavy_check_mark: ideaal voor pijp lijnen die downtime kunnen veroorloven en alle apps tegelijk kunnen worden bijgewerkt.

### <a name="incremental-copy-pattern"></a>Patroon voor incrementeel kopiëren

1. Begin met het verplaatsen van gegevens van gen1 naar Gen2. We raden u aan [Azure Data Factory](../../data-factory/connector-azure-data-lake-storage.md). Acl's kopiëren met de gegevens.

2. Incrementeel nieuwe gegevens kopiëren vanuit gen1.

3. Nadat alle gegevens zijn gekopieerd, stopt u alle schrijf bewerkingen naar gen1 en wijst u werk belastingen toe aan Gen2.

4. Gen1 uit bedrijf nemen.

Bekijk onze voorbeeld code voor het incrementele Kopieer patroon in het voor [beeld van een incrementele kopie migratie](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Incremental/README.md).


> [!div class="mx-imgBorder"]
> ![Patroon voor incrementeel kopiëren](./media/data-lake-storage-migrate-gen1-to-gen2/incremental-copy.png)

#### <a name="considerations-for-using-the-incremental-copy-pattern"></a>Overwegingen voor het gebruik van het patroon voor incrementeel kopiëren:

: heavy_check_mark: Cutover van gen1 naar Gen2 voor alle werk belastingen tegelijk.

: heavy_check_mark: er wordt alleen downtime verwacht tijdens de cutover-periode.

: heavy_check_mark: ideaal voor pijp lijnen waarbij alle apps tegelijk zijn bijgewerkt, maar er meer tijd nodig is voor het kopiëren van de gegevens.

### <a name="dual-pipeline-pattern"></a>Patroon met dubbele pijp lijn

1. Verplaats gegevens van gen1 naar Gen2. We raden u aan [Azure Data Factory](../../data-factory/connector-azure-data-lake-storage.md). Acl's kopiëren met de gegevens.

2. Nieuwe gegevens opnemen in zowel gen1 als Gen2.

3. Werk belastingen op Gen2.

4. Alle schrijf bewerkingen naar gen1 stoppen en vervolgens gen1 uit bedrijf nemen.

Bekijk onze voorbeeld code voor het dubbele pijplijn patroon in ons voor beeld van een [dubbele pijp lijn migratie](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Dual%20pipeline/README.md).

> [!div class="mx-imgBorder"]
> ![Patroon met dubbele pijp lijn](./media/data-lake-storage-migrate-gen1-to-gen2/dual-pipeline.png)

#### <a name="considerations-for-using-the-dual-pipeline-pattern"></a>Overwegingen voor het gebruik van het patroon met dubbele pijp lijnen:

: heavy_check_mark: gen1-en Gen2-pijp lijnen worden naast elkaar uitgevoerd.

: heavy_check_mark: ondersteunt geen uitval tijd.

: heavy_check_mark: ideaal in situaties waarin uw workloads en toepassingen geen downtime kunnen veroorloven en u kunt opnemen in beide opslag accounts.

### <a name="bi-directional-sync-pattern"></a>Patroon voor bidirectionele synchronisatie

1. Bidirectionele replicatie tussen gen1 en Gen2 instellen. We raden [WanDisco](https://docs.wandisco.com/bigdata/wdfusion/adls/)aan. Het biedt een herstel functie voor bestaande gegevens.

3. Wanneer alle verplaatsingen zijn voltooid, stopt u alle schrijf bewerkingen naar gen1 en schakelt u bidirectionele replicatie uit.

4. Gen1 uit bedrijf nemen.

Bekijk onze voorbeeld code voor het bidirectionele synchronisatie patroon in het voor [beeld van een bidirectionele synchronisatie migratie](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Bi-directional/README.md).

> [!div class="mx-imgBorder"]
> ![Bidirectioneel patroon](./media/data-lake-storage-migrate-gen1-to-gen2/bidirectional-sync.png)

#### <a name="considerations-for-using-the-bi-directional-sync-pattern"></a>Overwegingen voor het gebruik van het bidirectionele synchronisatie patroon:

: heavy_check_mark: ideaal voor complexe scenario's die betrekking hebben op een groot aantal pijp lijnen en afhankelijkheden waarbij een gefaseerde benadering meer indruk kan maken.  

: heavy_check_mark: migratie is hoog, maar biedt wel ondersteuning naast elkaar voor gen1 en Gen2.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de verschillende onderdelen van het instellen van de beveiliging van een opslag account. Zie [Azure Storage Security Guide (Engelstalig](./security-recommendations.md)).
- Optimaliseer de prestaties van uw Data Lake Store. Zie [Azure data Lake Storage Gen2 optimaliseren voor prestaties](data-lake-storage-performance-tuning-guidance.md)
- Bekijk de aanbevolen procedures voor het beheren van uw Data Lake Store. Bekijk [Aanbevolen procedures voor het gebruik van Azure data Lake Storage Gen2](data-lake-storage-best-practices.md)