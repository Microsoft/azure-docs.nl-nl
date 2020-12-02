---
title: Terminologie - Azure Synapse Analytics (preview-versie van werkruimten)
description: Naslaggids waarmee de gebruiker Azure Synapse Analytics leert kennen
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 11/18/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: c58ee46a608ccdcbb01a082ee278d9e0f8a07f6e
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96030676"
---
# <a name="azure-synapse-analytics-terminology"></a>Azure Synapse Analytics-terminologie

[!INCLUDE [preview](includes/note-preview.md)]

Dit document behandelt de basisconcepten van Azure Synapse Analytics.

## <a name="basics"></a>Basisbeginselen

Een **Synapse-werkruimte** is een beveiligbare samenwerkingsgrens voor het uitvoeren van zakelijke cloudanalyses in Azure. Een werkruimte wordt geïmplementeerd in een specifieke regio en heeft een gekoppeld ADLS Gen2-account en -bestandssysteem (voor het opslaan van tijdelijke gegevens). Een werkruimte bevindt zich onder een resourcegroep.

Met een werkruimte kunt u analyses uitvoeren met SQL en Apache Spark. Resources die beschikbaar zijn voor SQL en Spark Analytics, zijn georganiseerd in SQL- en Spark-**pools**. 

## <a name="linked-services"></a>Gekoppelde services

Een werkruimte kan verschillende **Gekoppelde services** bevatten. Dit zijn in principe verbindingsreeksen waarmee de verbindingsgegevens worden gedefinieerd die nodig zijn om de werkruimte aan externe resources te koppelen.

## <a name="synapse-sql"></a>Synapse SQL

**Synapse SQL** is de mogelijkheid om op T-SQL gebaseerde analyses uit te voeren in een Synapse-werkruimte. Synapse SQL heeft twee verbruiksmodellen: toegewezen en serverloos.  Gebruik **toegewezen SQL-pools** voor het toegewezen model. Een werkruimte kan elk gewenst aantal pools bevatten. Gebruik de **serverloze SQL-pools** als u het serverloze model wilt gebruiken. Elke werkruimte heeft een van deze pools.

* **SQL Request** - Bewerking zoals een queryuitvoering via een toegewezen SQL-pool of serverloze SQL-pool.
* **SQL-script** - Een set met SQL-opdrachten die zijn opgeslagen in een bestand. Een SQL-script kan een of meer SQL-instructies bevatten. Het kan worden gebruikt om SQL-aanvragen uit te voeren via een toegewezen SQL-pool of een serverloze SQL-pool.

## <a name="apache-spark-for-synapse"></a>Apache Spark for Synapse

Maak en gebruik **serverloze Apache Spark-pools** in uw Synapse-werkruimte als u Spark-analyses wilt gebruiken. Wanneer u een Spark-pool begint te gebruiken, maakt de werkruimte een **Spark-sessie** voor de resources van die sessie. 

Binnen Synapse zijn er twee manieren om Spark te gebruiken:
* **Spark-notebooks** voor het uitvoeren van data science en data engineering met behulp van Scala, PySpark, C# en SparkSQL
* **Spark-taakdefinities** voor het uitvoeren van Spark-batchtaken met behulp van JAR-bestanden.

Versieondersteuning:
* Spark 2.4
* Python 3.6.1
* Scala 2.11.12
* .NET voor Apache Spark 1.0
* Delta Lake 0.3.  

## <a name="pipelines"></a>Pipelines

* **Gegevensintegratie** - Biedt de mogelijkheid om gegevens op te nemen in verschillende bronnen en activiteiten te organiseren die worden uitgevoerd binnen of buiten een werkruimte.
* **Gegevensstroom** - Biedt een volledig visuele ervaring zonder codering om big data-transformatie uit te voeren. Alle optimalisatie- en uitvoeringstaken worden op serverloze wijze afgehandeld.
* **Pijplijn** - Een logische groep activiteiten die samen een taak uitvoeren.
* **Activiteit** - Staat voor acties die moeten worden uitgevoerd op gegevens, zoals het kopiëren van gegevens of het uitvoeren van een Notebook of een SQL-script.
* **Trigger** - Voert een pijplijn uit. Deze kan handmatig of automatisch worden uitgevoerd (planning, tumblingvenster of op gebeurtenis gebaseerd)
* **Integratie van gegevensset** - Een weergave van gegevens met een naam die simpelweg verwijst naar de gegevens die in een activiteit moeten worden gebruikt als invoer en uitvoer. Deze hoort bij een gekoppelde service.

## <a name="next-steps"></a>Volgende stappen

* [Aan de slag met Azure Synapse Analytics](get-started.md)
* [Een werkruimte maken](quickstart-create-workspace.md)
* [Serverloze SQL-pools gebruiken](quickstart-sql-on-demand.md)

