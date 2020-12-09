---
title: Trans formatie sinken bij toewijzing van gegevens stroom
description: Meer informatie over het configureren van een Sink-trans formatie in het toewijzen van gegevens stroom.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/08/2020
ms.openlocfilehash: 242249e3ab7fbedf9f19f3cb9a49fc4a8359f4ae
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929341"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>Trans formatie sinken bij toewijzing van gegevens stroom

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Nadat u klaar bent met het transformeren van uw gegevens, schrijft u deze naar een doel archief met behulp van de Sink-trans formatie. Elke gegevens stroom vereist ten minste één sink-trans formatie, maar u kunt indien nodig naar zoveel sinks schrijven om uw transformatie stroom te volt ooien. Als u naar extra sinks wilt schrijven, maakt u nieuwe streams via nieuwe vertakkingen en voorwaardelijke splitsingen.

Elke Sink-trans formatie is gekoppeld aan precies één Azure Data Factory DataSet-object of gekoppelde service. De Sink-trans formatie bepaalt de vorm en locatie van de gegevens waarnaar u wilt schrijven.

## <a name="inline-datasets"></a>Inline gegevens sets

Wanneer u een Sink-trans formatie maakt, moet u kiezen of uw Sink-informatie is gedefinieerd binnen een DataSet-object of binnen de Sink-trans formatie. De meeste indelingen zijn alleen beschikbaar in één van beide. Zie het juiste connector document voor meer informatie over het gebruik van een specifieke connector.

Wanneer een indeling wordt ondersteund voor zowel inline als een object DataSet, zijn er voor delen van beide. DataSet-objecten zijn herbruikbare entiteiten die kunnen worden gebruikt in andere gegevens stromen en activiteiten zoals kopiëren. Deze herbruikbare entiteiten zijn vooral nuttig wanneer u een geharder schema gebruikt. Gegevens sets zijn niet gebaseerd op Spark. Af en toe moet u mogelijk bepaalde instellingen of schema projectie in de Sink-trans formatie overschrijven.

Inline gegevens sets worden aanbevolen bij het gebruik van flexibele schema's, eenmalige Sink-instanties of sinks met para meters. Als uw Sink intensief is para meters, kunt u met inline-gegevens sets geen dummy-object maken. Inline gegevens sets zijn gebaseerd op Spark en hun eigenschappen zijn standaard voor de gegevens stroom.

Als u een inline gegevensset wilt gebruiken, selecteert u de gewenste indeling in de selectie van het **sink-type** . In plaats van een Sink-gegevensset te selecteren, selecteert u de gekoppelde service waarmee u verbinding wilt maken.

![Scherm opname waarin inline wordt weer gegeven.](media/data-flow/inline-selector.png "Scherm opname waarin inline wordt weer gegeven.")

##  <a name="supported-sink-types"></a><a name="supported-sinks"></a> Ondersteunde Sink-typen

Toewijzing van gegevens stroom volgt de aanpak van extractie, laden en transformeren (ELT) en werkt met *faserings* gegevens sets die allemaal in azure zijn. Op dit moment kunnen de volgende gegevens sets worden gebruikt in een bron transformatie.

| Connector | Indeling | Gegevensset/inline |
| --------- | ------ | -------------- |
| [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties) <br>[Tekst met scheidings tekens](format-delimited-text.md#mapping-data-flow-properties) <br>[Delta](format-delta.md) <br>[JSON](format-json.md#mapping-data-flow-properties) <br/>[ORC](format-orc.md#mapping-data-flow-properties)<br>[Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br>✓/- <br>-/✓ <br>✓/- <br>✓/✓<br>✓/- |
| [Azure Cosmos DB (SQL API)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties) <br>[Tekst met scheidings tekens](format-delimited-text.md#mapping-data-flow-properties) <br>[JSON](format-json.md#mapping-data-flow-properties) <br/>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br>✓/- <br>✓/- <br>✓/✓<br>✓/- |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties) <br/>[Common Data Model](format-common-data-model.md#sink-properties)<br>[Tekst met scheidings tekens](format-delimited-text.md#mapping-data-flow-properties) <br>[Delta](format-delta.md) <br>[JSON](format-json.md#mapping-data-flow-properties) <br/>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br>-/✓ <br>✓/- <br>-/✓ <br>✓/-<br>✓/✓ <br>✓/- |
| [Azure Database for PostgreSQL](connector-azure-database-for-postgresql.md) |  | ✓/✓ |
| [Azure SQL Database](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Azure SQL Managed instance (preview-versie)](connector-azure-sql-managed-instance.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |

De instellingen die specifiek zijn voor deze connectors bevinden zich op het tabblad **instellingen** . Voor beelden van gegevens-en gegevensstroom scripts in deze instellingen vindt u in de documentatie van de connector.

Azure Data Factory heeft toegang tot meer dan [90 systeem eigen connectors](connector-overview.md). Als u gegevens wilt schrijven naar deze andere bronnen uit uw gegevens stroom, gebruikt u de Kopieer activiteit om die gegevens uit een ondersteunde sink te laden.

## <a name="sink-settings"></a>Sink-instellingen

Nadat u een Sink hebt toegevoegd, configureert u via het tabblad **sink** . Hier kunt u de gegevensset voor uw sinks selecteren of maken. Ontwikkelings waarden voor gegevensset-para meters kunnen worden geconfigureerd in [instellingen voor fout opsporing](concepts-data-flow-debug-mode.md). (De foutopsporingsmodus moet zijn ingeschakeld.)

In de volgende video wordt een aantal verschillende Sink-opties voor door tekst gescheiden bestands typen uitgelegd.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4tf7T]

![Scherm opname van de Sink-instellingen.](media/data-flow/sink-settings.png "Scherm opname van de Sink-instellingen.")

**Schema-drift**: [schema-drift](concepts-data-flow-schema-drift.md) is de mogelijkheid van Data Factory om flexibele schema's in uw gegevens stromen systeem eigen te verwerken zonder dat er expliciet kolom wijzigingen hoeven te worden gedefinieerd. Schakel **schema-drift toestaan** in voor het schrijven van aanvullende kolommen boven op wat is gedefinieerd in het sink data-schema.

**Schema valideren**: als validate schema is geselecteerd, mislukt de gegevens stroom als een van de kolommen van het binnenkomende bron schema niet wordt gevonden in de bron projectie of als de gegevens typen niet overeenkomen. Gebruik deze instelling om af te dwingen dat de bron gegevens voldoen aan het contract van uw gedefinieerde projectie. Het is handig in database bron scenario's om te signaleren dat kolom namen of typen zijn gewijzigd.

## <a name="cache-sink"></a>Cache-Sink

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4HKt1]

Een *cache-Sink* is wanneer een gegevens stroom gegevens schrijft naar de Spark-cache in plaats van een gegevens archief. Bij het toewijzen van gegevens stromen kunt u binnen dezelfde stroom meerdere keren naar deze gegevens verwijzen met behulp van een *cache zoekopdracht*. Dit is handig als u wilt verwijzen naar gegevens als onderdeel van een expressie, maar niet expliciet de kolommen wilt samen voegen. Veelvoorkomende voor beelden waarbij een cache-Sink kan helpen bij het opzoeken van een maximum waarde in een gegevens archief en het vinden van overeenkomende fout codes naar een Data Base met fout berichten. 

Als u naar een cache-Sink wilt schrijven, voegt u een Sink-trans formatie toe en selecteert u **cache** als Sink-type. In tegens telling tot andere Sink-typen hoeft u geen gegevensset of gekoppelde service te selecteren omdat u niet naar een externe Store schrijft. 

![Cache-Sink selecteren](media/data-flow/select-cache-sink.png "Cache-Sink selecteren")

In de Sink-instellingen kunt u eventueel de sleutel kolommen van de cache-Sink opgeven. Deze worden gebruikt als overeenkomende voor waarden bij het gebruik `lookup()` van de functie in een cache zoekopdracht. Als u sleutel kolommen opgeeft, kunt u de functie niet gebruiken `outputs()` in een zoek opdracht in de cache. Zie [in cache opgeslagen lookups](concepts-data-flow-expression-builder.md#cached-lookup)voor meer informatie over de zoek syntaxis in de cache.

![Filter sleutel kolommen voor cache](media/data-flow/cache-sink-key-columns.png "Filter sleutel kolommen voor cache")

Als er bijvoorbeeld een kolom met één sleutel van `column1` in een cache-sink wordt aangeroepen `cacheExample` , wordt door de aanroep `cacheExample#lookup()` één para meter opgegeven voor de rij in de cache-sink die moet worden gevonden. De functie voert een enkele complexe kolom uit met subkolomen voor elke toegewezen kolom.

> [!NOTE]
> Een cache-Sink moet zich in een volledig onafhankelijke gegevens stroom bevinden op basis van een trans formatie die verwijst naar de cache met behulp van een zoek opdracht in de Een cache-Sink moet ook de eerste Sink hebben geschreven. 

## <a name="field-mapping"></a>Veldtoewijzing

Net als bij een Selecteer trans formatie kunt u op het tabblad **toewijzing** van de Sink bepalen welke binnenkomende kolommen worden geschreven. Standaard worden alle invoer kolommen, inclusief gedrijfde kolommen, toegewezen. Dit gedrag wordt *automapping* genoemd.

Wanneer u automatisch toewijzen uitschakelt, kunt u vaste toewijzingen op basis van een kolom of op basis van een regel toevoegen. Met toewijzing op basis van een regel kunt u expressies met patroon matching schrijven. Met vaste toewijzing worden logische en fysieke kolom namen toegewezen. Zie voor meer informatie over op regels gebaseerde toewijzing [kolom patronen in gegevens stroom toewijzen](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink).

## <a name="custom-sink-ordering"></a>Aangepaste Sink-ordening

Standaard worden gegevens in een niet-deterministische volg orde naar meerdere sinks geschreven. De uitvoerings engine schrijft gegevens parallel als de transformatie logica is voltooid en de Sink-bestelling kan elke uitvoering variëren. Als u een exacte Sink-ordening wilt opgeven, schakelt u **aangepaste Sink-ordening** in op het tabblad **Algemeen** van de gegevens stroom. Als deze functie is ingeschakeld, worden de sinks opeenvolgend geschreven in een oplopende volg orde.

![Scherm afbeelding waarin de aangepaste Sink-ordening wordt weer gegeven.](media/data-flow/custom-sink-ordering.png "Scherm afbeelding waarin de aangepaste Sink-ordening wordt weer gegeven.")

> [!NOTE]
> Wanneer u [lookups in de cache](./concepts-data-flow-expression-builder.md#cached-lookup)gebruikt, moet u ervoor zorgen dat de sinks in de cache zijn ingesteld op 1, het laagste (of het eerste) in de volg orde.

![Aangepaste Sink-ordening](media/data-flow/cache-2.png "Aangepaste Sink-ordening")

### <a name="sink-groups"></a>Sink-groepen

U kunt sinks groeperen door hetzelfde Volg nummer toe te passen voor een reeks Sinks. ADF behandelt die sinks als groepen die parallel kunnen worden uitgevoerd. Opties voor parallelle uitvoering worden weer gegeven in de activiteit pijplijn gegevensstroom.

## <a name="error-row-handling"></a>Verwerking van foutrijen

Bij het schrijven naar data bases kunnen bepaalde gegevens rijen mislukken vanwege beperkingen die zijn ingesteld door de bestemming. Standaard mislukt een uitvoering van de gegevens stroom bij de eerste fout die wordt uitgevoerd. In bepaalde connectors kunt u ervoor kiezen om **door te gaan** met een fout waarmee uw gegevens stroom kan worden voltooid, zelfs als afzonderlijke rijen fouten bevatten. Deze functie is momenteel alleen beschikbaar in Azure SQL Database. Zie voor meer informatie [fout rijen verwerken in Azure SQL DB](connector-azure-sql-database.md#error-row-handling).

Hieronder vindt u een video zelfstudie over het gebruik van database fout rijen automatisch in uw Sink-trans formatie.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4IWne]

## <a name="data-preview-in-sink"></a>Voor beeld van gegevens in Sink

Wanneer u een voor beeld van een gegevens ophaalt op een cluster voor fout opsporing, worden er geen gegevens naar uw Sink geschreven. Een moment opname van de gegevens die worden weer gegeven, maar er wordt niets naar uw bestemming geschreven. Als u het schrijven van gegevens naar uw Sink wilt testen, voert u een pijplijn fout opsporing uit vanaf het pijp lijn papier.

## <a name="next-steps"></a>Volgende stappen

Nu u de gegevens stroom hebt gemaakt, voegt u een [gegevens stroom activiteit toe aan uw pijp lijn](concepts-data-flow-overview.md).