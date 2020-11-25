---
title: 'Cheatsheet: Azure Synapse Analytics (preview van werkruimten)'
description: Naslaggids waarmee de gebruiker Azure Synapse Analytics leert kennen
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 04/15/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: a592b1b160edef1ed1f41478187d989d087e9617
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844979"
---
# <a name="azure-synapse-analytics-cheat-sheet"></a>Cheatsheet voor Azure Synapse Analytics

[!INCLUDE [preview](includes/note-preview.md)]

Het cheatsheet voor Azure Synapse Analytics bevat een overzicht van de basisconcepten en belangrijke opdrachten van de service. Dit artikel is nuttig voor zowel nieuwe gebruikers als mensen die meer over belangrijke Azure Synapse-onderwerpen willen weten.

## <a name="basics"></a>Basisbeginselen

Een **Synapse-werkruimte** is een beveiligbare samenwerkingsgrens voor het uitvoeren van zakelijke cloudanalyses in Azure. Een werkruimte wordt geïmplementeerd in een specifieke regio en heeft een gekoppeld ADLS Gen2-account en -bestandssysteem (voor het opslaan van tijdelijke gegevens). Een werkruimte bevindt zich onder een resourcegroep.

Met een werkruimte kunt u analyses uitvoeren met SQL en Apache Spark. Resources die beschikbaar zijn voor SQL en Spark Analytics, zijn georganiseerd in SQL- en Spark-**pools**. 

Een werkruimte kan verschillende **Gekoppelde services** bevatten. Dit zijn in principe verbindingsreeksen waarmee de verbindingsgegevens worden gedefinieerd die nodig zijn om de werkruimte aan externe resources te koppelen.

## <a name="synapse-sql-terminology"></a>Synapse SQL-terminologie

**Synapse SQL** is de mogelijkheid om op T-SQL gebaseerde analyses uit te voeren in een Synapse-werkruimte. Synapse SQL heeft twee verbruiksmodellen: toegewezen en serverloos.  Gebruik **toegewezen SQL-pools** voor het toegewezen model. Een werkruimte kan elk gewenst aantal pools bevatten. Gebruik de **serverloze SQL-pools** als u het serverloze model wilt gebruiken. Elke werkruimte heeft een van deze pools.

* **SQL Request** - Bewerking zoals een queryuitvoering via een toegewezen SQL-pool of serverloze SQL-pool.
* **SQL-script** - Een set met SQL-opdrachten die zijn opgeslagen in een bestand. Een SQL-script kan een of meer SQL-instructies bevatten. Het kan worden gebruikt om SQL-aanvragen uit te voeren via een toegewezen SQL-pool of een serverloze SQL-pool.

## <a name="apache-spark-for-synapse-terminology"></a>Apache Spark for Synapse terminologie

Maak en gebruik **serverloze Apache Spark-pools** in uw Synapse-werkruimte als u Spark-analyses wilt gebruiken.


* **Apache Spark for Synapse** - Spark-runtime die wordt gebruikt in een serverloze Spark-pool. De huidige versie die wordt ondersteund is Spark 2.4 met python 3.6.1, scala 2.11.12, .NET-ondersteuning voor Apache Spark 0.5 en Delta Lake 0.3.  
* **Apache Spark pool** - 0-to-N in Spark ingerichte resources en de bijbehorende databases kunnen in een werkruimte worden geïmplementeerd. Een Spark-pool kan automatisch worden onderbroken, hervat en geschaald.  
* **Spark-toepassing** - Het bestaat uit een stuurprogrammaproces en een reeks uitvoeringsprocedures. Een Spark-toepassing wordt uitgevoerd in een serverloze Spark-pool.            
* **Spark-sessies** - Uniform invoerpunt van een Spark-toepassing. Een sessie biedt een manier om te communiceren met de verschillende functies van Spark en met een kleiner aantal constructs. Als u een notebook wilt uitvoeren, moet u een sessie maken. Een sessie kan worden geconfigureerd om te worden uitgevoerd op een specifiek aantal uitvoerders van een specifiek formaat. De standaardconfiguratie voor een notebook-sessie is om op twee middelgrote uitvoerders te worden uitgevoerd.
* **Notebook** -  Interactieve en reactieve interface voor gegevenswetenschap en engineering die ondersteuning biedt voor Scala, PySpark, C# en SparkSQL.
* **Spark-taakdefinitie** - Interface voor het verzenden van een Spark-taak met een assembly-JAR die de code en de bijbehorende afhankelijkheden bevat.

## <a name="pipelines-terminology"></a>Terminologie van pijplijnen
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

