---
title: Wat is Azure Synapse Analytics?
description: Een overzicht van Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 03/24/2021
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 78b31cb32e3df9b0d8e198d8c2122e492e609283
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625807"
---
# <a name="what-is-azure-synapse-analytics"></a>Wat is Azure Synapse Analytics?

**Azure Synapse** is een Enter prise Analytics-service die tijd voor inzicht in data warehouses en Big data systemen versnelt. Azure Synapse combineert het beste van de **SQL**-technologieën die in zakelijke datawarehousing worden gebruikt, **Spark**-technologieën die voor big data worden gebruikt en **Pijplijnen** voor gegevensintegratie en ETL/ELT, en diepe integratie met andere Azure-services, zoals **Power BI**, **CosmosDB** en **AzureML**.

![Diagram van Azure Synapse Analytics-architectuur.](./media/overview-what-is/synapse-architecture.png)

## <a name="industry-leading-sql"></a>Toonaangevende SQL

**Synapse SQL** is een gedistribueerd query systeem voor T-SQL waarmee data warehouse-en data Virtualization-Scenario's en T-SQL kunnen worden geadresseerd en machine learning scenario's worden uitgebreid.

* Synapse SQL biedt zowel **serverloze** als **toegewezen** resource modellen. Voor voorspelbare prestaties en kosten kunt u toegewezen SQL-pools maken om verwerkingskracht te reserveren voor gegevens die zijn opgeslagen in SQL-tabellen. Voor niet-geplande of bursty werkbelastingen gebruikt u het altijd beschikbare, serverloze SQL-eindpunt.
* Gebruik ingebouwde **streamingmogelijkheden** om gegevens van gegevensbronnen in de cloud in SQL-tabellen te plaatsen
* Integreer AI met SQL door **machine learning**-modellen te gebruiken om gegevens een score te geven met behulp van de [functie T-SQL PREDICT](/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest&preserve-view=true)

## <a name="industry-standard-apache-spark"></a>Toonaangevende Apache Spark

**Apache Spark voor Azure Synapse** integreert diep en naadloos Apache Spark, de meest populaire open source big data-engine die wordt gebruikt voor gegevensvoorbereiding, data engineering, ETL en machine learning.

* ML-modellen met SparkML-algoritmen en AzureML-integratie voor Apache Spark 2.4 met ingebouwde ondersteuning voor Linux Foundation Delta Lake.
* Vereenvoudigd resourcemodel waarmee u zich geen zorgen hoeft te maken over het beheer van clusters.
* Snel Spark opstarten en agressief automatisch schaal aanpassen.
* Ingebouwde ondersteuning voor .NET voor Spark, waarmee u uw C#-expertise en de bestaande .NET-code in een Spark-toepassing opnieuw kunt gebruiken.

## <a name="working-with-your-data-lake"></a>Werken met uw Data Lake

Azure Synapse verwijdert de traditionele technologische barrières tussen het gebruik van SQL en Spark. U kunt naadloos combineren en vergelijken op basis van uw behoeften en expertise.

* Met een gedeeld metagegevenssysteem dat compatibel is met de Hive, kunnen tabellen die zijn gedefinieerd op bestanden in de data lake naadloos worden gebruikt door Spark of Hive.
* SQL en Spark kunnen Parquet-, CSV-, TSV- en JSON-bestanden die zijn opgeslagen in de data lake rechtstreeks verkennen en analyseren.
* Snel schaalbare belasting en verwijderen van belasting voor gegevens die worden verzonden tussen SQL-en Spark-databases

## <a name="built-in-data-integration"></a>Ingebouwde gegevens integratie

Azure Synapse bevat dezelfde gegevens integratie-engine en ervaringen als Azure Data Factory, zodat u uitgebreide ETL-pijp lijnen kunt maken zonder Azure Synapse Analytics te verlaten.

* Gegevens opnemen uit meer dan 90 gegevensbronnen
* Code-Free ETL met gegevensstroomactiviteiten
* Notebooks, Spark-taken, opgeslagen procedures, SQL-scripts en meer indelen

## <a name="unified-management-monitoring-and-security"></a>Uniform beheer, bewaking en beveiliging

Azure Synapse biedt ondernemingen één manier om analyse-resources te beheren, gebruik en activiteiten te bewaken en beveiliging af te dwingen.

* Gebruikers toewijzen aan Rol om de toegang tot analytics-resources te vereenvoudigen
* Nauwkeurig toegangsbeheer voor gegevens en code
* Eén dashboard voor het bewaken van resources, gebruik en gebruikers in SQL en Spark

## <a name="unified-experience"></a>Uniforme ervaring

**Synapse Studio** is de gebruikers ervaring die alles samen met gegevens technici verbindt. Hiermee kunnen ze elke taak uitvoeren die ze nodig hebben om een volledige analyse oplossing te bouwen.

* Belangrijkste gegevens engineer op één locatie: opnemen, verkennen, voorbereiden, organiseren, visualiseren
* Toonaangevende productiviteit voor het schrijven van SQL-of Spark-code: ontwerpen, fout opsporing en optimalisatie van prestaties
* Integreren met Enter prise CI/CD-proces

## <a name="engage-with-the-synapse-engineering-team"></a>Contact met het technische team van Synapse

- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse): Voor ontwikkelingsvragen.
- [Microsoft Q&A-vragenpagina](/answers/topics/azure-synapse-analytics.html): Voor technische vragen.

## <a name="next-steps"></a>Volgende stappen

* [Aan de slag met Azure Synapse Analytics](get-started.md)
* [Een werkruimte maken](quickstart-create-workspace.md)
* [Serverloze SQL-pools gebruiken](quickstart-sql-on-demand.md)
