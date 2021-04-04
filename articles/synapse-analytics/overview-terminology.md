---
title: Terminologie - Azure Synapse Analytics
description: Naslaggids waarmee de gebruiker Azure Synapse Analytics leert kennen
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 11/18/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: 828f37030ae567cacbaad25849b7ba24c561c20c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98132763"
---
# <a name="azure-synapse-analytics-terminology"></a>Azure Synapse Analytics-terminologie

Dit document behandelt de basisconcepten van Azure Synapse Analytics.

## <a name="basics"></a>Basisbeginselen

Een **Synapse-werkruimte** is een beveiligbare samenwerkingsgrens voor het uitvoeren van zakelijke cloudanalyses in Azure. Een werkruimte wordt geïmplementeerd in een specifieke regio en heeft een gekoppeld ADLS Gen2-account en -bestandssysteem (voor het opslaan van tijdelijke gegevens). Een werkruimte bevindt zich onder een resourcegroep.

Met een werkruimte kunt u analyses uitvoeren met SQL en Apache Spark. Resources die beschikbaar zijn voor SQL en Spark Analytics, zijn georganiseerd in SQL- en Spark-**pools**. 

## <a name="linked-services"></a>Gekoppelde services

Een werkruimte kan verschillende **Gekoppelde services** bevatten. Dit zijn in principe verbindingsreeksen waarmee de verbindingsgegevens worden gedefinieerd die nodig zijn om de werkruimte aan externe resources te koppelen.

## <a name="synapse-sql"></a>Synapse SQL

**Synapse SQL** is de mogelijkheid om op T-SQL gebaseerde analyses uit te voeren in een Synapse-werkruimte. Synapse SQL heeft twee verbruiksmodellen: toegewezen en serverloos.  Gebruik **toegewezen SQL-pools** voor het toegewezen model. Een werkruimte kan elk gewenst aantal pools bevatten. Gebruik de **serverloze SQL-pools** als u het serverloze model wilt gebruiken. Elke werkruimte heeft een van deze pools.

In Synapse Studio kunt u met SQL-pools werken door **SQL-scripts** te maken en uit te voeren.

## <a name="apache-spark-for-synapse"></a>Apache Spark for Synapse

Maak en gebruik **serverloze Apache Spark-pools** in uw Synapse-werkruimte als u Spark-analyses wilt gebruiken. Wanneer u een Spark-pool gaat gebruiken, maakt de werkruimte een **Spark-sessie** om de resources te verwerken die aan die sessie zijn gekoppeld. 

Binnen Synapse zijn er twee manieren om Spark te gebruiken:
* **Spark-notebooks** voor het uitvoeren van data science en data engineering met behulp van Scala, PySpark, C# en SparkSQL
* **Spark-taakdefinities** voor het uitvoeren van Spark-batchtaken met behulp van JAR-bestanden.

## <a name="pipelines"></a>Pipelines

Azure Synapse maakt gebruik van pijplijnen om gegevensintegratie te bieden. U kunt hiermee gegevens verplaatsen tussen services en activiteiten organiseren.

* Een **pijplijn** is een logische groep activiteiten die samen een taak uitvoeren.
* **Activiteiten** zijn acties binnen een pijplijn die moeten worden uitgevoerd op gegevens, zoals het kopiëren van gegevens of het uitvoeren van een notebook of een SQL-script.
* **Gegevensstromen** zijn een specifiek soort activiteit om zonder programmeerervaring gegevenstransformatie uit te voeren waarbij achter de schermen gebruik wordt gemaakt van Synapse Spark.
* **Trigger** - Voert een pijplijn uit. Deze kan handmatig of automatisch worden uitgevoerd (planning, tumblingvenster of op gebeurtenis gebaseerd)
* **Integratie van gegevensset** - Een weergave van gegevens met een naam die simpelweg verwijst naar de gegevens die in een activiteit moeten worden gebruikt als invoer en uitvoer. Deze hoort bij een gekoppelde service.

## <a name="next-steps"></a>Volgende stappen

* [Aan de slag met Azure Synapse Analytics](get-started.md)
* [Een werkruimte maken](quickstart-create-workspace.md)
* [Serverloze SQL-pools gebruiken](quickstart-sql-on-demand.md)

