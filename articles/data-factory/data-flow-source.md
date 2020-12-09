---
title: Bron transformatie in gegevens stroom toewijzen
description: Meer informatie over het instellen van een bron transformatie in het toewijzen van gegevens stroom.
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/08/2020
ms.openlocfilehash: d375632ad02f9ec7cacf1708ac81c4f257916609
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929239"
---
# <a name="source-transformation-in-mapping-data-flow"></a>Bron transformatie in gegevens stroom toewijzen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Met een bron transformatie configureert u de gegevens bron voor de gegevens stroom. Wanneer u gegevens stromen ontwerpt, is de eerste stap altijd het configureren van een bron transformatie. Als u een bron wilt toevoegen, selecteert u het vak **bron toevoegen** in het canvas voor de gegevens stroom.

Elke gegevens stroom vereist ten minste één bron transformatie, maar u kunt zoveel bronnen toevoegen als u nodig hebt om uw gegevens transformaties te volt ooien. U kunt deze bronnen samen voegen met een koppelings-, zoek-of een Union-trans formatie.

Elke bron transformatie is gekoppeld aan precies één gegevensset of gekoppelde service. De gegevensset definieert de vorm en locatie van de gegevens waarnaar u wilt schrijven of waaruit u wilt lezen. Als u een gegevensset op basis van een bestand gebruikt, kunt u Joker tekens en bestands lijsten in uw bron gebruiken om met meer dan één bestand tegelijk te werken.

## <a name="inline-datasets"></a>Inline gegevens sets

De eerste beslissing die u maakt bij het maken van een bron transformatie is of uw bron gegevens worden gedefinieerd binnen een DataSet-object of binnen de bron transformatie. De meeste indelingen zijn alleen beschikbaar in één van beide. Zie het juiste connector document voor meer informatie over het gebruik van een specifieke connector.

Wanneer een indeling wordt ondersteund voor zowel inline als een object DataSet, zijn er voor delen van beide. DataSet-objecten zijn herbruikbare entiteiten die kunnen worden gebruikt in andere gegevens stromen en activiteiten zoals kopiëren. Deze herbruikbare entiteiten zijn vooral nuttig wanneer u een geharder schema gebruikt. Gegevens sets zijn niet gebaseerd op Spark. Af en toe moet u mogelijk bepaalde instellingen of schema projectie in de bron transformatie overschrijven.

Inline gegevens sets worden aanbevolen bij het gebruik van flexibele schema's, eenmalige bron instanties of bronnen met para meters. Als uw bron sterk is para meters, kunt u met inline-gegevens sets geen dummy-object maken. Inline gegevens sets zijn gebaseerd op Spark en hun eigenschappen zijn standaard voor de gegevens stroom.

Als u een inline gegevensset wilt gebruiken, selecteert u de gewenste indeling in de **bron type** kiezer. In plaats van een bron gegevensset te selecteren, selecteert u de gekoppelde service waarmee u verbinding wilt maken.

![Scherm opname waarin inline wordt weer gegeven.](media/data-flow/inline-selector.png "Scherm opname waarin inline wordt weer gegeven.")

##  <a name="supported-source-types"></a><a name="supported-sources"></a> Ondersteunde bron typen

Toewijzing van gegevens stroom volgt de aanpak van extractie, laden en transformeren (ELT) en werkt met *faserings* gegevens sets die allemaal in azure zijn. Op dit moment kunnen de volgende gegevens sets worden gebruikt in een bron transformatie.

| Connector | Indeling | Gegevensset/inline |
| --------- | ------ | -------------- |
| [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Tekst met scheidings tekens](format-delimited-text.md#mapping-data-flow-properties)<br>[Delta](format-delta.md)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties) <br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/-<br>✓/-<br>-/✓<br>✓/✓<br/>✓/-<br>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Cosmos DB (SQL API)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Tekst met scheidings tekens](format-delimited-text.md#mapping-data-flow-properties)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties)<br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/-<br>✓/-<br>✓/✓<br/>✓/-<br>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[Common Data Model](format-common-data-model.md#source-properties)<br>[Tekst met scheidings tekens](format-delimited-text.md#mapping-data-flow-properties)<br>[Delta](format-delta.md)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties)<br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/-<br/>-/✓<br>✓/-<br>-/✓<br>✓/✓<br>✓/-<br/>✓/✓<br/>✓/-<br>✓/✓ |
| [Azure Database for PostgreSQL](connector-azure-database-for-postgresql.md) |  | ✓/✓ |
| [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Azure SQL Managed instance (preview-versie)](connector-azure-sql-managed-instance.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Hive](connector-hive.md#mapping-data-flow-properties) | | -/✓ |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |

De instellingen die specifiek zijn voor deze connectors bevinden zich op het tabblad **bron opties** . Voor beelden van gegevens-en gegevensstroom scripts in deze instellingen vindt u in de documentatie van de connector.

Azure Data Factory heeft toegang tot meer dan [90 systeem eigen connectors](connector-overview.md). Als u gegevens uit deze andere bronnen in uw gegevens stroom wilt toevoegen, gebruikt u de Kopieer activiteit om die gegevens te laden in een van de ondersteunde staging-gebieden.

## <a name="source-settings"></a>Broninstellingen

Nadat u een bron hebt toegevoegd, configureert u via het tabblad **bron instellingen** . Hier kunt u de gegevensset voor uw bron punten kiezen of maken. U kunt ook schema-en bemonsterings opties voor uw gegevens selecteren.

Ontwikkelings waarden voor gegevensset-para meters kunnen worden geconfigureerd in [instellingen voor fout opsporing](concepts-data-flow-debug-mode.md). (De foutopsporingsmodus moet zijn ingeschakeld.)

![Scherm opname van het tabblad Bron instellingen.](media/data-flow/source1.png "Scherm opname van het tabblad Bron instellingen.")

**Naam van de uitvoer stroom**: de naam van de bron transformatie.

**Bron type**: Kies of u een inline-gegevensset of een bestaand DataSet-object wilt gebruiken.

**Verbinding testen**: test of de Spark-service van de gegevens stroom verbinding kan maken met de gekoppelde service die in de bron-gegevensset wordt gebruikt. De foutopsporingsmodus moet zijn ingeschakeld om deze functie in te scha kelen.

**Schema-drift**: [schema-drift](concepts-data-flow-schema-drift.md) is de mogelijkheid van Data Factory om flexibele schema's in uw gegevens stromen systeem eigen te verwerken zonder dat er expliciet kolom wijzigingen hoeven te worden gedefinieerd.

* Schakel het selectie vakje **schema-drift toestaan** in als de bron kolommen vaak worden gewijzigd. Met deze instelling kunnen alle binnenkomende bron velden door de trans formaties worden getransporteerd naar de sink.

* Als u overlopende **kolom typen** selecteert, wordt Data Factory het detecteren en definiëren van gegevens typen voor elke nieuwe kolom die wordt gedetecteerd. Als deze functie is uitgeschakeld, zijn alle gedrijfde kolommen van het type teken reeks.

**Schema valideren:** Als **validatie schema** is geselecteerd, kan de gegevens stroom niet worden uitgevoerd als de binnenkomende bron gegevens niet overeenkomen met het gedefinieerde schema van de gegevensset.

**Aantal regels overs Laan**: het veld **aantal regel items overs Laan** geeft aan hoeveel regels aan het begin van de gegevensset moeten worden genegeerd.

**Sampling**: Schakel **steek proeven** in om het aantal rijen van de bron te beperken. Gebruik deze instelling wanneer u gegevens uit uw bron test of van uw source gebruikt voor fout opsporing.

Als u wilt valideren of uw bron juist is geconfigureerd, schakelt u de foutopsporingsmodus in en haalt u een voor beeld van de gegevens op. Zie [debug mode (foutopsporingsmodus](concepts-data-flow-debug-mode.md)) voor meer informatie.

> [!NOTE]
> Wanneer de foutopsporingsmodus is ingeschakeld, wordt de sampling-instelling in de bron tijdens de preview-versie overschreven door de configuratie van het aantal rijen in de instellingen voor fout opsporing.

## <a name="source-options"></a>Bron opties

Het tabblad **bron opties** bevat instellingen die specifiek zijn voor de gekozen connector en notatie. Zie de relevante [connector documentatie](#supported-sources)voor meer informatie en voor beelden.

## <a name="projection"></a>Projectie

Net als schema's in gegevens sets definieert de projectie in een bron de gegevens kolommen, typen en indelingen van de bron gegevens. Voor de meeste typen gegevens sets, zoals SQL en Parquet, wordt de projectie van een bron vastgesteld op basis van het schema dat is gedefinieerd in een gegevensset. Als uw bron bestanden niet sterk worden getypt (bijvoorbeeld platte. CSV-bestanden in plaats van Parquet-bestanden), kunt u de gegevens typen voor elk veld in de bron transformatie definiëren.

![Scherm afbeelding met instellingen op het tabblad projectie.](media/data-flow/source3.png "Scherm afbeelding met instellingen op het tabblad projectie.")

Als uw tekst bestand geen gedefinieerd schema heeft, selecteert u **gegevens type detecteren** zodat Data Factory de gegevens typen worden gesampled en afgeleid. Selecteer **standaard indeling instellen** op automatische detectie van de standaard gegevens indelingen.

Als u het **schema opnieuw instelt** , wordt de projectie ingesteld op wat is gedefinieerd in de gegevensset waarnaar wordt verwezen.

U kunt de kolom gegevens typen in een downstream afgeleide kolom transformatie wijzigen. Gebruik een SELECT trans formatie om de kolom namen te wijzigen.

### <a name="import-schema"></a>Schema importeren

Selecteer de knop **schema importeren** op het tabblad **projectie** om een actieve debug-cluster te gebruiken om een schema projectie te maken. Deze is beschikbaar in elk bron type. Als u het schema hier importeert, wordt de projectie die in de gegevensset is gedefinieerd, overschreven. Het object dataset wordt niet gewijzigd.

Het importeren van het schema is handig in gegevens sets zoals Avro en Azure Cosmos DB die complexe gegevens structuren ondersteunen waarvoor geen schema definities zijn vereist in de gegevensset. Voor inline gegevens sets is het importeren van schema de enige manier om te verwijzen naar kolom meta gegevens zonder schema-drift.

## <a name="optimize-the-source-transformation"></a>De bron transformatie optimaliseren

Op het tabblad **Optimize** kunnen partitie gegevens worden bewerkt tijdens elke transformatie stap. In de meeste gevallen is het **gebruik van huidige partitionering** geoptimaliseerd voor de ideale partitie structuur voor een bron.

Als u leest van een Azure SQL Database bron, worden de gegevens waarschijnlijk het snelst gelezen door aangepaste **bron** partities. Data Factory worden grote query's gelezen door verbinding met uw data base parallel te maken. Deze partitionering van de bron kan worden uitgevoerd op een kolom of met behulp van een query.

![Scherm opname van de instellingen van de bron partitie.](media/data-flow/sourcepart3.png "Scherm opname van de instellingen van de bron partitie.")

Zie het [tabblad optimaliseren](concepts-data-flow-overview.md#optimize)voor meer informatie over optimalisatie binnen de toewijzing van gegevens stromen.

## <a name="next-steps"></a>Volgende stappen

Begin met het bouwen van uw gegevens stroom met een [afgeleide kolom transformatie](data-flow-derived-column.md) en een [geselecteerde trans formatie](data-flow-select.md).
