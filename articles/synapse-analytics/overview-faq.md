---
title: Veelgestelde vragen - Azure Synapse Analytics
description: Veelgestelde vragen voor Azure Synapse Analytics
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: overview
ms.date: 10/25/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: a7ee4e205851a751f7a50ac0ddadfb4e4c7eb81a
ms.sourcegitcommit: 08458f722d77b273fbb6b24a0a7476a5ac8b22e0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98247400"
---
# <a name="azure-synapse-analytics-frequently-asked-questions"></a>Veelgestelde vragen over Azure Synapse Analytics

In deze handleiding vindt u de veelgestelde vragen over Azure Synapse Analytics.

## <a name="general"></a>Algemeen

### <a name="q-how-can-i-use-rbac-roles-to-secure-my-workspace"></a>V: Hoe kan ik RBAC-rollen gebruiken voor het beveiligen van mijn werkruimte?

A: Azure Synapse introduceert een aantal rollen en bereiken waarmee ze kunnen worden toegewezen, waardoor de beveiliging van uw werkruimte wordt vereenvoudigd.

Synapse RBAC-rollen:
* Synapse-beheerder
* Synapse SQL-beheerder
* Synapse Spark-beheerder
* Synapse-inzender (preview-versie)
* Synapse-artefactuitgever (preview-versie)
* Synapse-artefactgebruiker (preview-versie)
* Synapse-compute-operator (preview-versie)
* Synapse-referentiegebruiker (preview-versie)

Als u uw Synapse-werkruimte wilt beveiligen, wijst u de RBAC-rollen toe aan deze RBAC-bereiken:
* Workspaces
* Spark-pools
* Integration Runtimes
* Gekoppelde services
* Referenties

Daarnaast beschikt u met toegewezen SQL-pools over alle beveiligingsfuncties die u kent en waardeert.

### <a name="q-how-do-i-control-cont-dedicated-sql-pools-serverless-sql-pools-and-serverless-spark-pools"></a>V: Hoe kan ik toegewezen SQL-groepen, serverloze SQL-groepen en serverloze Spark-pools beheren?

A: Om te beginnen werkt Azure Synapse samen met de ingebouwde kostenanalyse en kostenwaarschuwingen die beschikbaar zijn op het niveau van het Azure-abonnement.

- Toegewezen SQL-pools: u hebt rechtstreeks inzicht in de kosten en controle over de kosten, omdat u de grootte van toegewezen SQL-pools maakt en specificeert. U kunt verder zelf bepalen welke gebruikers toegewezen SQL-pools kunnen maken of schalen met Azure RBAC-rollen.

- Serverloze SQL-pools: u hebt controle-en kostenbeheerinstellingen waarmee u op een dagelijks, wekelijks en maandelijks niveau uitgaven kunt limiteren. Zie [Kostenbeheer voor serverloze SQL-pool](./sql/data-processed.md) voor meer informatie. 

- Serverloze Spark-pools: u kunt bepalen wie Spark-pools kan maken met Synapse RBAC-rollen.  

### <a name="q-will-synapse-workspace-support-folder-organization-of-objects-and-granularity-at-ga"></a>V: Biedt een Synapse-werkruimte de mogelijkheid om objecten in mappen te organiseren en granulatie bij algemene beschikbaarheid?

A: Synapse-werkruimten bieden ondersteuning voor door de gebruiker gedefinieerde mappen.

### <a name="q-can-i-link-more-than-one-power-bi-workspace-to-a-single-azure-synapse-workspace"></a>V: Kan ik meer dan één Power BI-werkruimte koppelen aan een enkele Azure Synapse-werkruimte?
    
A: Op dit moment kunt u slechts één Power BI-werkruimte koppelen aan een Azure Synapse-werkruimte. 

### <a name="q-is-synapse-link-to-cosmos-db-ga"></a>V: Is Synapse Link voor Cosmos DB algemeen beschikbaar?

A: Synapse Link voor Apache Spark is algemeen beschikbaar. Synapse Link voor een serverloze SQL-pool bevindt zich in openbare preview.

### <a name="q-does-azure-synapse-workspace-support-cicd"></a>V: Biedt een Azure Synapse-werkruimte ondersteuning voor CI/CD? 

A: Ja. Alle pijplijnartefacten, notebooks, SQL-scripts en Spark-taakdefinities bevinden zich in Git. Alle pooldefinities worden als ARM-sjablonen opgeslagen in Git. Toegewezen SQL-poolobjecten (schema's, tabellen, weergaven, enzovoort) worden beheerd met databaseprojecten met CI/CD-ondersteuning.

## <a name="pipelines"></a>Pipelines

### <a name="q-how-do-i-ensure-i-know-what-credential-is-being-used-to-run-a-pipeline"></a>V: Hoe kan ik weten welke referenties worden gebruikt om een pijplijn uit te voeren? 

A: Elke activiteit in een Synapse-pijplijn wordt uitgevoerd met behulp van de referenties die zijn opgegeven in de gekoppelde service.

### <a name="q-are-ssis-irs-supported-in-synapse-integrate"></a>V: Wordt SSIS IR (SQL Server Integration Services) ondersteund in Synapse Integrate?

A: Momenteel niet. 

### <a name="q-how-do-i-migrate-existing-pipelines-from-azure-data-factory-to-an-azure-synapse-workspace"></a>V: Hoe kan ik bestaande pijplijnen van Azure Data Factory naar een Azure Synapse-werkruimte migreren?

A: Op dit moment moet u uw Azure Data Factory-pijplijnen en gerelateerde artefacten handmatig opnieuw maken door de JSON uit de oorspronkelijke pijplijn te exporteren en deze te importeren in uw Synapse-werkruimte.

## <a name="apache-spark"></a>Apache Spark

### <a name="q-what-is-the-difference-between-apache-spark-for-synapse-and-apache-spark"></a>V: Wat is het verschil tussen Apache Spark voor Synapse en Apache Spark?

A: Apache Spark voor Synapse is Apache Spark met extra ondersteuning voor integraties met andere services (AAD, AzureML, enzovoort) en extra bibliotheken (mssparktuils, Hummingbird) en vooraf afgestemde prestatieconfiguraties.

Elke workload die momenteel wordt uitgevoerd op Apache Spark, wordt zonder wijziging uitgevoerd op Apache Spark voor Azure Synapse. 

### <a name="q-what-versions-of-spark-are-available"></a>V: Welke versies van Spark zijn er beschikbaar?

A: Azure Synapse Apache Spark biedt volledige ondersteuning voor Spark 2.4. Raadpleeg [Ondersteuning voor Apache Spark-versies](./spark/apache-spark-version-support.md) voor een volledige lijst met kernonderdelen en de versie die momenteel wordt ondersteund.

### <a name="q-is-there-an-equivalent-of-dbutils-in-azure-synapse-spark"></a>V: Is er een equivalent van DButils in Azure Synapse Spark?

A: Ja, Azure Synapse Apache Spark biedt de **mssparkutils**-bibliotheek. Zie [Inleiding tot Microsoft Spark-hulpprogramma's](./spark/microsoft-spark-utilities.md) voor volledige documentatie over het hulpprogramma.

### <a name="q-how-do-i-set-session-parameters-in-apache-spark"></a>V: Hoe kan ik sessieparameters in Apache Spark instellen?

A: Als u sessieparameters wilt instellen, gebruikt u %%configure magic available. De sessie moet opnieuw worden gestart om de parameters toe te passen. 

### <a name="q-how-do-i-set-cluster-level-parameters-in-a-serverless-spark-pool"></a>V: Hoe kan ik parameters op clusterniveau in een serverloze Spark-pool instellen?

A: Als u parameters op clusterniveau wilt instellen, kunt u een spark.conf-bestand opgeven voor de Spark-pool. In deze pool worden vervolgens de parameters uit het configuratiebestand gebruikt. 

### <a name="q-can-i-run-a-multi-user-spark-cluster-in-azure-synapse-analytics"></a>V: Kan ik een Spark-cluster met meerdere gebruikers uitvoeren in Azure Synapse Analytics?
 
A: Azure Synapse biedt speciaal ontwikkelde engines voor specifieke gebruikstoepassingen. Apache Spark voor Synapse is ontworpen als taakservice en niet als clustermodel. Er zijn twee scenario's waarin mensen om een clustermodel met meerdere gebruikers vragen.

**Scenario 1: veel gebruikers die toegang hebben tot een cluster om gegevens te verwerken voor BI-doeleinden.**

De eenvoudigste manier om deze taak uit te voeren, is om de gegevens in Spark te zetten en vervolgens te profiteren van de verwerkingsmogelijkheden van Synapse SQL zodat ze Power BI kunnen verbinden met die gegevenssets.

**Scenario 2: meerdere ontwikkelaars op één cluster om geld te besparen.**
 
Voor dit scenario moet u elke ontwikkelaar een serverloze Spark-pool bieden die is ingesteld op het gebruik van een klein aantal Spark-resources. Aangezien serverloze Spark-Pools niets kosten totdat ze actief worden gebruikt, worden de kosten geminimaliseerd wanneer er meerdere ontwikkelaars zijn. De pools delen metagegevens (Spark-tabellen) zodat ze eenvoudig met elkaar kunnen samenwerken.

### <a name="q-how-do-i-include-manage-and-install-libraries"></a>V: Hoe kan ik bibliotheken toevoegen, beheren en installeren?

A:  U kunt externe pakketten installeren via een requirements.txt-bestand tijdens het maken van de Spark-pool, vanuit de Synapse-werkruimte of vanuit Azure Portal. Zie [Bibliotheken beheren voor Apache Spark in Azure Synapse Analytics](./spark/apache-spark-azure-portal-add-libraries.md).

## <a name="dedicated-sql-pools"></a>Toegewezen SQL-pools

### <a name="q-what-are-the-functional-differences-between-dedicated-sql-pools-and-serverless-pools"></a>V: Wat zijn de functionele verschillen tussen toegewezen SQL-pools en serverloze pools?

A: U vindt een volledige lijst met verschillen in [Verschillen in T-SQL-functies in Synapse SQL](./sql/overview-features.md).

### <a name="q-now-that-azure-synapse-is-ga-how-do-i-move-my-dedicated-sql-pools-that-were-previously-standalone-into-azure-synapse"></a>V: Nu Azure Synapse algemeen beschikbaar is, hoe verplaats ik mijn toegewezen SQL-pools die voorheen zelfstandig waren in Azure Synapse? 

A: Er is geen "verplaatsing" of "migratie". U kunt ervoor kiezen om nieuwe werkruimtefuncties in te schakelen voor uw bestaande pools. Als u dit doet, zijn er geen wijzigingen die fouten veroorzaken en kunt u nieuwe functies gebruiken, zoals Synapse Studio, Spark en serverloze SQL-pools.

### <a name="q-what-is-the-default-deployment-of-dedicated-sql-pools-now"></a>V: Wat is nu de standaardimplementatie van toegewezen SQL-pools? 

A: Standaard worden alle nieuwe toegewezen SQL-pools geïmplementeerd in een werkruimte. U kunt echter nog steeds een speciale SQL-pool (voorheen SQL DW) in een zelfstandige vorm maken. 

## <a name="next-steps"></a>Volgende stappen

* [Aan de slag met Azure Synapse Analytics](get-started.md)
* [Een werkruimte maken](quickstart-create-workspace.md)
* [Serverloze SQL-pools gebruiken](quickstart-sql-on-demand.md)
