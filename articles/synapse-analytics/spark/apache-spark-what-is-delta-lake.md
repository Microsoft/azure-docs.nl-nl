---
title: Wat is Delta Lake
description: Overzicht van Delta Lake en hoe het werkt als onderdeel van Azure Synapse Analytics
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 23a269fe9498295a3ff325a261c6539f5e1e31aa
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101676217"
---
# <a name="what-is-delta-lake"></a>Wat is Delta Lake

Azure Synapse Analytics is compatibel met Linux Foundation Delta Lake. Delta Lake is een open-source opslagbus die trans acties voor zuren (atomiciteit, consistentie, isolatie en duurzaamheid) tot Apache Spark en big data werk belastingen brengt.

De huidige versie van Delta Lake die is opgenomen in azure Synapse heeft taal ondersteuning voor scala, PySpark en .NET. Onder aan de pagina staan koppelingen naar meer gedetailleerde voor beelden en documentatie.

## <a name="key-features"></a>Belangrijke functies

| Functie | Beschrijving |
| --- | --- |
| **ZURE trans acties** | Data-meren worden meestal gevuld via meerdere processen en pijp lijnen, waarvan sommige gegevens gelijktijdig met lees bewerkingen worden geschreven. Vóór Delta Lake en het toevoegen van trans acties moesten gegevens technici een hand matig fout gevoelig proces door lopen om de gegevens integriteit te garanderen. Delta Lake brengt bekende ACID-trans acties naar Data meren. Het biedt serialiseerbaar, het krach tigste niveau van het isolatie niveau. Meer informatie over [Delta Lake: het uitpakken van het transactie logboek](https://databricks.com/blog/2019/08/21/diving-into-delta-lake-unpacking-the-transaction-log.html).|
| **Schaal bare afhandeling van meta gegevens** | In big data kan zelfs de meta gegevens zelf ' big data ' zijn. Delta Lake behandelt meta gegevens zoals gegevens en maakt gebruik van de gedistribueerde verwerkings kracht van Spark om alle meta gegevens te verwerken. Als gevolg hiervan kan Delta Lake PETA byte tabellen met miljarden partities en bestanden op een gemakkelijke manier verwerken. |
| **Tijdige reis (versie beheer van gegevens)** | De mogelijkheid om een wijziging ongedaan te maken of terug te gaan naar een eerdere versie is een van de belangrijkste functies van trans acties. Delta Lake biedt moment opnamen van gegevens waarmee u kunt terugkeren naar eerdere versies van gegevens voor audits, terugdraai acties of het reproduceren van experimenten. Meer informatie over het [introduceren van Delta Lake time reis voor grootschalige data meren](https://databricks.com/blog/2019/02/04/introducing-delta-time-travel-for-large-scale-data-lakes.html). |
| **Indeling openen** | Apache Parquet is de basislijn notatie voor Delta Lake, zodat u de efficiënte compressie-en coderings schema's kunt gebruiken die standaard worden gebruikt voor de indeling. |
| **Unified batch-en streaming-bron en Sink** | Een tabel in Delta Lake is zowel een batch tabel als een streaming-bron en Sink. Gegevens opname, batch historische backfill en interactieve query's worden allemaal alleen uit het vak opgenomen. |
| **Schema afdwingen** | Bij het afdwingen van het schema kunt u ervoor zorgen dat de gegevens typen juist zijn en dat de vereiste kolommen aanwezig zijn, waardoor het voor komen dat beschadigde gegevens geen gegevens consistentie veroorzaken. Zie voor meer informatie gaat u [naar Delta Lake: schema Afdwinging & evolutie](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **Schema ontwikkeling** | Met Delta Lake kunt u wijzigingen aanbrengen in een tabel schema dat automatisch kan worden toegepast, zonder dat u migratie-DDL hoeft te schrijven. Zie voor meer informatie gaat u [naar Delta Lake: schema Afdwinging & evolutie](https://databricks.com/blog/2019/09/24/diving-into-delta-lake-schema-enforcement-evolution.html) |
| **Controle geschiedenis** | In het transactie logboek van Delta Lake worden gegevens over elke wijziging aangebracht in gegevens die een volledige controle spoor van de wijzigingen leveren. |
| **Updates en verwijderingen** | Delta Lake ondersteunt scala/Java/python-en SQL-Api's voor verschillende functies. Ondersteuning voor samen Voeg-, update-en verwijderings bewerkingen helpt u te voldoen aan de nalevings vereisten. Zie [de release van Delta Lake 0.6.1 aankondigen](https://delta.io/news/delta-lake-0-6-1-released/)voor meer informatie en  [aankondiging van de Delta Lake 0,7-release](https://delta.io/news/delta-lake-0-7-0-released/) en [eenvoudige, betrouw bare Upsert en verwijderingen op Delta Lake-tabellen met behulp van python-api's](https://databricks.com/blog/2019/10/03/simple-reliable-upserts-and-deletes-on-delta-lake-tables-using-python-apis.html), die code fragmenten bevatten voor het samen voegen, bijwerken en verwijderen van DML-opdrachten. |
| **100% compatibel met Apache Spark-API** | Ontwikkel aars kunnen Delta Lake met hun bestaande gegevens pijplijnen gebruiken met minimale wijziging omdat het volledig compatibel is met bestaande Spark-implementaties. |

Zie de [pagina met documentatie voor Delta Lake](https://docs.delta.io/latest/delta-intro.html) voor de volledige documentatie

Zie [Delta Lake-project](https://github.com/delta-io/delta) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Documentatie voor .NET voor Apache Spark](/dotnet/spark)
- [Azure Synapse Analytics](../index.yml)