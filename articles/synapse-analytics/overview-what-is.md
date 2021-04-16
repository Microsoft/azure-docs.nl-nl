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
ms.openlocfilehash: 652f98659f96b36e3185432e50d9d36dc569bd43
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537941"
---
# <a name="what-is-azure-synapse-analytics"></a>Wat is Azure Synapse Analytics?

**Azure Synapse** is een analyseservice voor ondernemingen die sneller inzicht krijgt in datawarehouses en big data systemen. Azure Synapse combineert het beste van de **SQL**-technologieën die in zakelijke datawarehousing worden gebruikt, **Spark**-technologieën die voor big data worden gebruikt en **Pijplijnen** voor gegevensintegratie en ETL/ELT, en diepe integratie met andere Azure-services, zoals **Power BI**, **CosmosDB** en **AzureML**.

![Diagram van Azure Synapse Analytics architectuur.](./media/overview-what-is/synapse-architecture.png)

## <a name="industry-leading-sql"></a>Toonaangevende SQL

**Synapse SQL** is een gedistribueerd querysysteem voor T-SQL dat scenario's voor datawarehousing en gegevensvirtualisatie mogelijk maakt en T-SQL uitbreidt om scenario's voor streaming en machine learning aanpakken.

* Synapse SQL biedt **zowel serverloze** als **toegewezen** resourcemodellen. Voor voorspelbare prestaties en kosten kunt u toegewezen SQL-pools maken om verwerkingskracht te reserveren voor gegevens die zijn opgeslagen in SQL-tabellen. Voor niet-geplande of bursty werkbelastingen gebruikt u het altijd beschikbare, serverloze SQL-eindpunt.
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

* Tabellen die zijn gedefinieerd voor bestanden in data lake worden naadloos gebruikt door Spark of Hive.
* SQL en Spark kunnen Parquet-, CSV-, TSV- en JSON-bestanden die zijn opgeslagen in de data lake rechtstreeks verkennen en analyseren.
* Snel, schaalbaar gegevens laden tussen SQL- en Spark-databases

## <a name="built-in-data-integration"></a>Ingebouwde gegevensintegratie

Azure Synapse bevat dezelfde engine voor gegevensintegratie en ervaringen als Azure Data Factory, zodat u uitgebreide ETL-pijplijnen op schaal kunt maken zonder dat u Azure Synapse Analytics.

* Gegevens opnemen uit meer dan 90 gegevensbronnen
* Code-Free ETL met gegevensstroomactiviteiten
* Notebooks, Spark-taken, opgeslagen procedures, SQL-scripts en meer ins delen

## <a name="unified-experience"></a>Uniforme ervaring 

**Synapse Studio** biedt ondernemingen één manier om oplossingen te bouwen, te onderhouden en te beveiligen in één gebruikerservaring

* Belangrijke taken uitvoeren: opnemen, verkennen, voorbereiden, orkestreren, visualiseren
* Resources, gebruik en gebruikers bewaken in SQL en Spark
* Op rollen gebaseerd toegangsbeheer gebruiken om de toegang tot analysebronnen te vereenvoudigen
* SQL- of Spark-code schrijven en integreren met ZAKELIJKE CI/CD-processen

## <a name="engage-with-the-synapse-community"></a>Contact maken met de Synapse-community

- [Microsoft Q&A:](/answers/topics/azure-synapse-analytics.html)stel technische vragen.
- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse): Voor ontwikkelingsvragen.

## <a name="next-steps"></a>Volgende stappen

* [Aan de slag met Azure Synapse Analytics](get-started.md)
* [Een werkruimte maken](quickstart-create-workspace.md)
* [Serverloze SQL-pools gebruiken](quickstart-sql-on-demand.md)
