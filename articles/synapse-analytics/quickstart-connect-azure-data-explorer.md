---
title: 'Quickstart: Azure Data Explorer verbinden met een Azure Synapse Analytics-werkruimte'
description: Verbind een Azure Data Explorer-cluster met een Azure Synapse Analytics-werkruimte met behulp van Apache Spark voor Azure Synapse Analytics.
services: synapse-analytics
author: manojraheja
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 10/07/2020
ms.author: maraheja
ms.reviewer: jrasnick
ms.openlocfilehash: ee9d137973bfa4eeb28bc6526437e76e781f3199
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92172270"
---
# <a name="connect-to-azure-data-explorer-using-apache-spark-for-azure-synapse-analytics"></a>Verbinding maken met Azure Data Explorer met behulp van Apache Spark voor Azure Synapse Analytics

In dit artikel wordt beschreven hoe u toegang krijgt tot Azure Data Explorer-databases vanuit Synapse Studio met Apache Spark voor Azure Synapse Analytics.

## <a name="prerequisites"></a>Vereisten

* [Maak een Azure Data Explorer-cluster en -database](/azure/data-explorer/create-cluster-database-portal).
* Gebruik een bestaande Azure Synapse Analytics-werkruimte of maak een nieuwe werkruimte door de stappen te volgen in [Quickstart: Een Azure Synapse-werkruimte maken](./quickstart-create-workspace.md).
* Gebruik een bestaande Synapse Apache Spark-pool of maak een nieuwe pool door de stappen te volgen in [Quickstart: Een Apache Spark-pool maken met behulp van de Azure-portal](./quickstart-create-apache-spark-pool-portal.md).
* [Maak een Azure AD-app (Azure Active Directory) door een Azure AD-toepassing in te richten.](/azure/data-explorer/kusto/management/access-control/how-to-provision-aad-app)
* Verleen uw Azure AD-app toegang tot uw database door de stappen te volgen in [Azure Data Explorer-databasemachtigingen beheren](/azure/data-explorer/manage-database-permissions).

## <a name="go-to-synapse-studio"></a>Ga naar Synapse Studio

Selecteer **Synapse Studio starten** vanuit een Azure Synapse-werkruimte. Selecteer op de startpagina van Synapse Studio de optie **Gegevens**. U wordt nu naar **Data Object Explorer** geleid.

## <a name="connect-an-azure-data-explorer-database-to-an-azure-synapse-workspace"></a>Een Azure Data Explorer-database verbinden met een Azure Synapse-werkruimte

Het verbinden van een Azure Data Explorer-database met een werkruimte wordt uitgevoerd via een gekoppelde service. Met een gekoppelde Azure Data Explorer-service kunt u gegevens bekijken en verkennen, lezen en schrijven vanuit Apache Spark voor Azure Synapse. U kunt ook integratietaken in een pijplijn uitvoeren.

Voer de volgende stappen uit vanuit Data Object Explorer om een rechtstreekse verbinding met een Azure Data Explorer-cluster te maken:

1. Selecteer het pictogram **+** bij de optie **Gegevens**.
1. Selecteer **Verbinding maken** om verbinding te maken met externe gegevens.
1. Selecteer **Azure Data Explorer (Kusto)** .
1. Selecteer **Doorgaan**.
1. Gebruik een beschrijvende naam voor de gekoppelde service. De naam wordt weergegeven in Data Object Explorer en wordt tijdens Azure Synapse-uitvoeringen gebruikt om verbinding te maken met de database.
1. Selecteer het Azure Data Explorer-cluster in uw abonnement of voer de URI in.
1. Voer de **id** en de **sleutel** van de service-principal in. Zorg ervoor dat deze service-principal toegang heeft tot de database voor leesbewerkingen en toegang tot de ingestor voor het opnemen van gegevens.
1. Voer de naam van de Azure Data Explorer-database in.
1. Selecteer **Verbinding testen** om te controleren of u over de juiste machtigingen beschikt.
1. Selecteer **Maken**.

    ![Schermopname van een nieuw gekoppelde service.](./media/quickstart-connect-azure-data-explorer/003-new-linked-service.png)

    > [!NOTE]
    > (Optioneel) **Verbinding testen** valideert geen schrijftoegang. Zorg ervoor dat uw service-principal-id schrijftoegang heeft tot de Azure Data Explorer-database.

1. Azure Data Explorer-clusters en -databases worden weergegeven op het tabblad **Gekoppeld** onder de sectie **Azure Data Explorer**.

    ![Schermopname van het zoeken naar clusters.](./media/quickstart-connect-azure-data-explorer/004-browse-clusters.png)

    > [!NOTE]
    > In de huidige release worden de databaseobjecten gevuld op basis van de machtigingen van uw Azure AD-account voor de Azure Data Explorer-databases. Wanneer u de Apache Spark-notebooks of -integratietaken uitvoert, wordt de referentie in de koppelingsservice gebruikt (bijvoorbeeld service-principal).

## <a name="quickly-interact-with-code-generated-actions"></a>Snel communiceren via met code gegenereerde acties

Wanneer u met de rechtermuisknop op een database of tabel klikt, wordt er een lijst met voorbeelden van Spark-notebooks weergegeven. Selecteer een optie om gegevens te lezen, schrijven of streamen naar Azure Data Explorer.

[![Schermopname van nieuwe voorbeeldnotitieblokken.](./media/quickstart-connect-azure-data-explorer/005-new-notebook.png)](./media/quickstart-connect-azure-data-explorer/005-new-notebook.png#lightbox)

Hier volgt een voorbeeld van het lezen van gegevens. Koppel het notebook aan uw Spark-pool en voer de cel uit.

[![Schermopname van het toevoegen van een nieuw notebook voor lezen.](./media/quickstart-connect-azure-data-explorer/006-read-data.png)](./media/quickstart-connect-azure-data-explorer/006-read-data.png#lightbox)

   > [!NOTE]
   > Bij de eerste uitvoering kan het langer dan drie minuten duren om de Spark-sessie te initiëren. Volgende uitvoeringen zullen veel sneller gaan.

## <a name="limitations"></a>Beperkingen

De Azure Data Explorer-connector wordt momenteel niet ondersteund in virtuele netwerken die door Azure Synapse worden beheerd.

## <a name="next-steps"></a>Volgende stappen

* [Voorbeeldcode met geavanceerde opties](https://github.com/Azure/azure-kusto-spark/blob/master/samples/src/main/python/SynapseSample.py)
* [Spark-connector voor Azure Data Explorer (Kusto)](https://github.com/Azure/azure-kusto-spark)