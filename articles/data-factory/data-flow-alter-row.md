---
title: Rij transformatie wijzigen in gegevens stroom toewijzen
description: Data base-doel bijwerken met behulp van de trans formatie Alter Row in data flow toewijzen
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/06/2020
ms.openlocfilehash: c3858756a0140481c0ab249e29c95f76c4b90da5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "82982646"
---
# <a name="alter-row-transformation-in-mapping-data-flow"></a>Rij transformatie wijzigen in gegevens stroom toewijzen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Gebruik de Alter Row trans formatie om INSERT-, Delete-, update-en upsert-beleid in te stellen op rijen. U kunt één-op-veel-voor waarden toevoegen als expressies. Deze voor waarden moeten in volg orde van prioriteit worden opgegeven, aangezien elke rij wordt gemarkeerd met het beleid dat overeenkomt met de eerste-overeenkomende expressie. Elk van deze voor waarden kan resulteren in een rij (of rijen) die wordt ingevoegd, bijgewerkt, verwijderd of upserted. Alter Row kan zowel DDL-& DML-acties op uw data base produceren.

![Rij-instellingen wijzigen](media/data-flow/alter-row1.png "Rij-instellingen wijzigen")

Alter Row-trans formaties worden alleen toegepast op Data Base-of CosmosDB-sinks in uw gegevens stroom. De acties die u toewijst aan rijen (invoegen, bijwerken, verwijderen, upsert) worden niet uitgevoerd tijdens foutopsporingssessie. Voer een activiteit gegevens stroom uitvoeren in een pijp lijn uit om het alter Row-beleid in uw database tabellen te bepalen.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vJYc]

## <a name="specify-a-default-row-policy"></a>Een standaard beleid voor rijen opgeven

Een alter Row trans formatie maken en een rij-beleid met een voor waarde van opgeven `true()` . Elke rij die niet overeenkomt met een van de eerder gedefinieerde expressies, wordt gemarkeerd voor het opgegeven Row-beleid. Standaard wordt elke rij die niet overeenkomt met een voorwaardelijke expressie gemarkeerd voor `Insert` .

![Alter Row Policy](media/data-flow/alter-row4.png "Alter Row Policy")

> [!NOTE]
> Als u alle rijen met één beleid wilt markeren, kunt u een voor waarde voor dat beleid maken en de voor waarde als opgeven `true()` .

## <a name="view-policies-in-data-preview"></a>Beleids regels weer geven in voor beeld van gegevens

Gebruik de [foutopsporingsmodus](concepts-data-flow-debug-mode.md) om de resultaten van de Alter Row-beleids regels weer te geven in het deel venster voor gegevens voorbeeld. Een voor beeld van een gegevens van een alter Row trans formatie produceert geen DDL-of DML-acties op uw doel.

![Alter Row policies](media/data-flow/alter-row3.png "Alter Row policies")

Elk Alter Row-beleid wordt vertegenwoordigd door een pictogram dat aangeeft of een insert-, update-, upsert-of verwijderde actie wordt uitgevoerd. In de bovenste koptekst ziet u hoeveel rijen worden beïnvloed door elk beleid in de preview-versie.

## <a name="allow-alter-row-policies-in-sink"></a>Alter Row Policies in Sink toestaan

De gegevens stroom moet worden geschreven naar een Data Base of Cosmos Sink om het beleid voor het wijzigen van de rij te kunnen gebruiken. Schakel op het tabblad **instellingen** in de sink in welke Alter Row-beleids regels zijn toegestaan voor die sink.

![Rij-Sink wijzigen](media/data-flow/alter-row2.png "Rij-Sink wijzigen")

Het standaard gedrag is alleen invoegen toestaan. Als u updates, upsert of verwijderen wilt toestaan, schakelt u het selectie vakje in de sink in die overeenkomt met deze voor waarde. Als updates, upsert of, worden verwijderd, zijn ingeschakeld, moet u opgeven welke sleutel kolommen in de Sink moeten worden gevonden.

> [!NOTE]
> Als uw invoeg-, update-of upsert het schema van de doel tabel in de Sink wijzigt, mislukt de gegevens stroom. Als u het doel schema in uw Data Base wilt wijzigen, kiest u **tabel opnieuw maken** als tabel actie. Hiermee wordt de tabel verwijderd en opnieuw gemaakt met de nieuwe schema definitie.

Voor de Sink-trans formatie is een enkele sleutel of een reeks sleutels vereist voor een unieke identificatie van de rij in de doel database. Voor SQL-sinks stelt u de sleutels in op het tabblad Instellingen voor sink. Stel voor CosmosDB de partitie sleutel in de instellingen in en stel het veld id van het CosmosDB-systeem in op uw Sink-toewijzing. Voor CosmosDB is het verplicht om de systeem kolom ' id ' op te Neem voor updates, upsert en verwijderingen.

## <a name="merges-and-upserts-with-azure-sql-database-and-synapse"></a>Samen voegen en upsert met Azure SQL Database en Synapse

ADF-gegevens stromen bieden ondersteuning voor samen voegingen van Azure SQL Database-en Synapse-database groep (Data Warehouse) met de optie upsert.

U kunt echter scenario's uitvoeren waarbij uw doel database schema gebruik maakt van de identiteits eigenschap van sleutel kolommen. Voor ADF moeten de sleutels worden geïdentificeerd die u gaat gebruiken om te voldoen aan de rijwaarden voor updates en upsert. Maar als voor de doel kolom de eigenschap identiteit is ingesteld en u het upsert-beleid gebruikt, kunt u in de doel database niet naar de kolom schrijven. Er kunnen ook fouten optreden wanneer u probeert te upserten op basis van de distributie kolom van een gedistribueerde tabel.

Hier volgen manieren om dit op te lossen:

1. Ga naar de instellingen voor Sink-trans formatie en stel ' Schrijf sleutel kolommen overs Laan ' in. Dit geeft aan dat de kolom die u hebt geselecteerd als de sleutel waarde voor uw toewijzing niet wordt geschreven.

2. Als deze sleutel kolom niet de kolom is die het probleem voor identiteits kolommen veroorzaakt, kunt u de volgende SQL-optie voor het verwerken van Sink-trans formatie gebruiken: ```SET IDENTITY_INSERT tbl_content ON``` . Schakel deze vervolgens uit met de SQL-eigenschap na de verwerking: ```SET IDENTITY_INSERT tbl_content OFF``` .

3. Voor zowel het identiteits-als het kolom distributie-object kunt u uw logica overschakelen van Upsert naar het gebruik van een afzonderlijke update voorwaarde en een afzonderlijke invoeg voorwaarde met behulp van een voorwaardelijke Splits-trans formatie. Op deze manier kunt u de toewijzing instellen op het pad van de update om de sleutel kolom toewijzing te negeren.

## <a name="data-flow-script"></a>Script voor gegevensstroom

### <a name="syntax"></a>Syntax

```
<incomingStream>
    alterRow(
           insertIf(<condition>?),
           updateIf(<condition>?),
           deleteIf(<condition>?),
           upsertIf(<condition>?),
        ) ~> <alterRowTransformationName>
```

### <a name="example"></a>Voorbeeld

Het onderstaande voor beeld is een alter rij Transformation `CleanData` met de naam die een inkomende stroom gebruikt `SpecifyUpsertConditions` en drie Alter Row-voor waarden maakt. In de vorige trans formatie wordt een kolom `alterRowCondition` met de naam berekend die bepaalt of een rij wordt ingevoegd, bijgewerkt of verwijderd in de-data base. Als de waarde van de kolom een teken reeks waarde heeft die overeenkomt met de regel Alter Row, wordt dat beleid toegewezen.

In de Data Factory UX ziet deze trans formatie er als volgt uit:

![Voor beeld van rijen wijzigen](media/data-flow/alter-row4.png "Voor beeld van rijen wijzigen")

Het gegevens stroom script voor deze trans formatie bevindt zich in het volgende fragment:

```
SpecifyUpsertConditions alterRow(insertIf(alterRowCondition == 'insert'),
    updateIf(alterRowCondition == 'update'),
    deleteIf(alterRowCondition == 'delete')) ~> AlterRow
```

## <a name="next-steps"></a>Volgende stappen

Na de Alter Row trans formatie wilt u mogelijk [uw gegevens in een doel gegevens archief opvangen](data-flow-sink.md).
