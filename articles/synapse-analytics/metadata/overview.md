---
title: Gedeeld metagegevensmodel
description: Met Azure Synapse Analytics kunnen de verschillende rekenengines voor de werkruimte databases en tabellen delen tussen de serverloze Apache Spark-pools, de serverloze SQL-pool en toegewezen SQL-pools.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.openlocfilehash: b10b6f011fa7daee4094f0cc7b819d36127fedcd
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96460348"
---
# <a name="azure-synapse-analytics-shared-metadata"></a>Gedeelde Azure Synapse Analytics-metagegevens

Met Azure Synapse Analytics kunnen de verschillende rekenengines voor de werkruimte databases en tabellen delen tussen de serverloze Apache Spark-pools en serverloze SQL-pools.

Het delen ondersteunt het zogenaamde moderne datawarehousepatroon en biedt de werkruimte-SQL-engines toegang tot databases en tabellen die zijn gemaakt met Spark. Ook kunnen de SQL-engines hun eigen objecten maken die niet worden gedeeld met de andere engines.

## <a name="support-the-modern-data-warehouse"></a>Het moderne datawarehouse ondersteunen

Het gedeelde metagegevensmodel ondersteunt het moderne datawarehousepatroon op de volgende manier:

1. Gegevens van de Data Lake worden op efficiënte wijze voorbereid en gestructureerd met Spark door de voorbereide gegevens op te slaan in (mogelijk gepartitioneerde) door Parquet ondersteunde tabellen die zijn opgenomen in mogelijk verschillende databases.

2. De door Spark gemaakte databases en alle tabellen worden zichtbaar in een Azure Synapse-werkruimte met Spark-poolexemplaren en kunnen worden gebruikt vanuit elke Spark-taak. Deze mogelijkheid is onderhevig aan [machtigingen](#security-model-at-a-glance), aangezien alle Spark-pools in een werkruimte dezelfde onderliggende catalogusmeta-opslagplaats delen.

3. De door Apache Spark gemaakte databases en de door Parquet ondersteunde tabellen worden zichtbaar in de werkruimte-serverloze SQL-pool. [Databases](database.md) worden automatisch gemaakt in de metagegevens van de serverloze SQL-pool en de [externe en beheerde tabellen](table.md) die zijn gemaakt door een Apache Spark-taak, worden toegankelijk gemaakt als externe tabellen in de metagegevens van de serverloze SQL-pool in het `dbo`-schema van de bijbehorende database. 

<!--[INSERT PICTURE]-->

<!--__Figure 1 -__ Supporting the Modern Data Warehouse Pattern with shared metadata-->

Objectsynchronisatie vindt asynchroon plaats. Er is een lichte vertraging van enkele seconden tot objecten worden weergegeven in de SQL-context. Zodra ze worden weergegeven, kunnen er query's op worden uitgevoerd. Ze kunnen echter niet worden bijgewerkt of gewijzigd door de SQL-engines die er toegang toe hebben.

## <a name="shared-metadata-objects"></a>Gedeelde metagegevensobjecten

Met Spark kunt u databases, externe tabellen, beheerde tabellen en weergaven maken. Aangezien voor Spark-weergaven een Spark-engine nodig is voor het verwerken van de definiërende Spark SQL-instructie en deze niet kan worden verwerkt door een SQL-engine, worden alleen databases en de bijbehorende externe en beheerde tabellen die gebruikmaken van de Parquet-opslagindeling gedeeld met de werkruimte-SQL-engine. Spark-weergaven worden alleen gedeeld tussen exemplaren van de Spark-pool.

## <a name="security-model-at-a-glance"></a>Beveiligingsmodel in één oogopslag

De Spark-databases en -tabellen en hun gesynchroniseerde weergaven in de SQL-engines worden beveiligd op het onderliggende opslagniveau. Wanneer een query wordt uitgevoerd op de tabel waarvoor de inzender van de query gebruiksrechten heeft, wordt de beveiligingsprincipal van de query-inzender doorgegeven aan de onderliggende bestanden. Machtigingen worden gecontroleerd op het niveau van het bestandssysteem.

Zie [Gedeelde database van Azure Synapse Analytics](database.md) voor meer informatie.

## <a name="change-maintenance"></a>Onderhoud wijzigen

Als een metagegevensobject wordt verwijderd of gewijzigd met Spark, worden de wijzigingen opgehaald en doorgegeven aan de serverloze SQL-pool. Synchronisatie is asynchroon en wijzigingen worden na enige vertraging weergegeven in de SQL-engine.

## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over gedeelde Azure Synapse Analytics-metagegevensdatabases](database.md)
- [Meer informatie over gedeelde Azure Synapse Analytics-metagegevenstabellen](table.md)

