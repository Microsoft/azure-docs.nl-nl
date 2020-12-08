---
title: Wat is Azure Synapse Analytics?
description: Een overzicht van Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 10/28/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: c315dae7e5f02f112dfdfbec02e1ebaaa5e48a9f
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445786"
---
# <a name="what-is-azure-synapse-analytics"></a>Wat is Azure Synapse Analytics?

Zakelijke analyses moeten op grote schaal worden uitgevoerd op elk soort gegevens, ongeacht of het gaat om onbewerkte, precieze of zeer samengestelde gegevens. Hiervoor moeten ondernemingen meestal technologieën voor big data en datawarehousing samenvoegen in complexe gegevenspijplijnen, die werken met de gegevens in relationele opslag en data lakes. Dit soort oplossingen is moeilijk te compileren, te onderhouden en te beveiligen. De complexiteit ervan zorgt voor vertraging in de levering van het inzicht dat ondernemingen nodig hebben.

**Azure Synapse** is een geïntegreerde analyse-service die sneller inzicht biedt in alle datawarehouses en systemen voor big data. Azure Synapse combineert het beste van de **SQL**-technologieën die worden gebruikt in zakelijke datawarehousing, **Spark**-technologieën die worden gebruikt voor big data, en **Pipelines** voor gegevensintegratie en ETL/ELT. **Synapse Studio** biedt een uniforme ervaring voor beheer, bewaking, codering en beveiliging. Synapse bevat een diepgaande integratie met andere Azure-services, zoals **Power BI**, **CosmosDB** en **AzureML**.

## <a name="key-features--benefits"></a>Belangrijkste functies en voordelen

### <a name="industry-leading-sql"></a>Toonaangevende SQL

* **Synapse SQL** is een gedistribueerd querysysteem waarmee ondernemingen scenario's voor datawarehousing en gegevensvirtualisatie kunnen implementeren, met behulp van bekende T-SQL-standaardversies. Ook worden de mogelijkheden van SQL uitgebreid om scenario's wat betreft streaming- en machine learning aan te pakken.

* Synapse SQL biedt zowel **serverloze** als **toegewezen** resourcemodellen, waarmee u de opties voor verbruik en facturering kunt aanpassen aan uw behoeften. Voor voorspelbare prestaties en kosten kunt u toegewezen SQL-pools maken om verwerkingskracht te reserveren voor gegevens die zijn opgeslagen in SQL-tabellen. Voor niet-geplande of bursty werkbelastingen gebruikt u het altijd beschikbare, serverloze SQL-eindpunt.
* Gebruik ingebouwde **streamingmogelijkheden** om gegevens van gegevensbronnen in de cloud in SQL-tabellen te plaatsen
* Integreer AI met SQL door **machine learning**-modellen te gebruiken om gegevens een score te geven met behulp van de [functie T-SQL PREDICT](https://docs.microsoft.com/sql/t-sql/queries/predict-transact-sql?view=azure-sqldw-latest)

### <a name="industry-standard-apache-spark"></a>Toonaangevende Apache Spark

**Apache Spark voor Azure Synapse** integreert diep en naadloos Apache Spark, de meest populaire open source big data-engine die wordt gebruikt voor gegevensvoorbereiding, data engineering, ETL en machine learning.

* ML-modellen met SparkML-algoritmen en AzureML-integratie voor Apache Spark 2.4 met ingebouwde ondersteuning voor Linux Foundation Delta Lake.
* Vereenvoudigd resourcemodel waarmee u zich geen zorgen hoeft te maken over het beheer van clusters.
* Snel Spark opstarten en agressief automatisch schaal aanpassen.
* Ingebouwde ondersteuning voor .NET voor Spark, waarmee u uw C#-expertise en de bestaande .NET-code in een Spark-toepassing opnieuw kunt gebruiken.

### <a name="interop-of-sql-and-apache-spark-on-your-data-lake"></a>Interop van SQL en Apache Spark op uw Data Lake

Azure Synapse verwijdert de traditionele technologische barrières tussen het gebruik van SQL en Spark. U kunt naadloos combineren en vergelijken op basis van uw behoeften en expertise.

* Met een gedeeld metagegevenssysteem dat compatibel is met de Hive, kunnen tabellen die zijn gedefinieerd op bestanden in de data lake naadloos worden gebruikt door Spark of Hive.
* SQL en Spark kunnen Parquet-, CSV-, TSV- en JSON-bestanden die zijn opgeslagen in de data lake rechtstreeks verkennen en analyseren.
* Snel schaalbare belasting en verwijderen van belasting voor gegevens die worden verzonden tussen SQL-en Spark-databases

### <a name="built-in-data-integration-via-pipelines"></a>Ingebouwde gegevensintegratie via pijplijnen

Azure Synapse is ingebouwd met dezelfde gegevensintegratie-engine en ervaring als Azure Data Factory, zodat u uitgebreide ETL-pijplijnen op schaal kunt maken zonder Azure Synapse Analytics te verlaten.

* Gegevens opnemen uit meer dan 90 gegevensbronnen
* Code-Free ETL met gegevensstroomactiviteiten
* Notebooks, Spark-taken, opgeslagen procedures, SQL-scripts en meer indelen

### <a name="unified-management-monitoring-and-security"></a>Uniform beheer, bewaking en beveiliging

Azure Synapse biedt ondernemingen één manier om analyse-resources te beheren, gebruik en activiteiten te bewaken en beveiliging af te dwingen.

* Gebruikers toewijzen aan Rol om de toegang tot analytics-resources te vereenvoudigen
* Nauwkeurig toegangsbeheer voor gegevens en code
* Eén dashboard voor het bewaken van resources, gebruik en gebruikers in SQL en Spark

### <a name="synapse-studio"></a>Synapse Studio

**Synapse Studio** is de websysteem-eigen ervaring die alles verbindt de data engineers, zodat ze op één locatie elke taak kunnen uitvoeren die ze nodig hebben om een volledige oplossing te bouwen.

* Bouw een end-to-end-analyse-oplossing op één locatie: opnemen, verkennen, voorbereiden, organiseren, visualiseren
* Toonaangevende productiviteit voor data engineers die SQL-of Spark-code schrijven: ontwerpen, foutopsporing en prestaties optimaliseren
* Integreren met zakelijke CI/CD-processen

## <a name="engage-with-the-synapse-engineering-team"></a>Contact met het technische team van Synapse

- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-synapse): Voor ontwikkelingsvragen.
- [Microsoft Q&A-vragenpagina](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html): Voor technische vragen.

## <a name="next-steps"></a>Volgende stappen

* [Aan de slag met Azure Synapse Analytics](get-started.md)
* [Een werkruimte maken](quickstart-create-workspace.md)
* [Serverloze SQL-pools gebruiken](quickstart-sql-on-demand.md)
