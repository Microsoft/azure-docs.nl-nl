---
title: Opnemen in Azure Data Lake Storage Gen2
description: Meer informatie over het opnemen van gegevens in Azure Data Lake Storage Gen2 in Azure Synapse Analytics
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: e3024fe1a8fe524a1deddef23a67d86a600b9394
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567601"
---
# <a name="ingest-data-into-azure-data-lake-storage-gen2"></a>Gegevens opnemen in Azure Data Lake Storage Gen2 

In dit artikel leert u hoe u gegevens van de ene locatie naar de andere kunt opnemen in een Azure Data Lake Gen 2-opslagaccount (Azure Data Lake Gen 2) met behulp van Azure Synapse Analytics.

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement:** als u geen Azure-abonnement hebt, maakt u een gratis [Azure-account](https://azure.microsoft.com/free/) voordat u begint.
* **Azure Storage:** u gebruikt Azure Data Lake Gen 2 als brongegevensopslag.  Als u geen opslagaccount hebt, zie Een opslagaccount [maken Azure Storage stappen](../../storage/common/storage-account-create.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) om er een te maken.

## <a name="create-linked-services"></a>Gekoppelde services maken

In Azure Synapse Analytics definieert u de verbindingsgegevens voor andere services in een gekoppelde service. In deze sectie voegt u een Azure Synapse Analytics Azure Data Lake Gen 2 toe als gekoppelde services.

1. Open de Azure Synapse Analytics UX en ga naar het **tabblad** Beheren.
1. Selecteer **onder Externe verbindingen** de optie **Gekoppelde services.**
1. Selecteer **Nieuw** om een gekoppelde service toe te voegen.
1. Selecteer de Azure Data Lake Storage Gen2 in de lijst en selecteer **Doorgaan.**
1. Voer uw verificatiereferenties in. Accountsleutel, service-principal en beheerde identiteit worden momenteel ondersteunde verificatietypen. Selecteer Verbinding testen om te controleren of uw referenties juist zijn. 
1. Selecteer **Maken** nadat dit is voltooid.

## <a name="create-pipeline"></a>Pijplijn maken

Een pijplijn bevat de logische stroom voor het uitvoeren van een reeks activiteiten. In deze sectie maakt u een pijplijn met een kopieeractiviteit die gegevens uit Azure Data Lake Gen 2 ops nam in een toegewezen SQL-pool.

1. Ga naar het **tabblad Orchestrate.** Selecteer op het pluspictogram naast de koptekst van de pijplijnen en selecteer **Pijplijn**.
1. Sleep **gegevens kopiëren naar het**  pijplijn-canvas onder Verplaatsen en transformeren in het deelvenster Activiteiten.
1. Selecteer de kopieeractiviteit en ga naar het **tabblad** Bron. Selecteer **Nieuw om** een nieuwe bronset te maken.
1. Selecteer Azure Data Lake Storage Gen2 als uw gegevensopslag en selecteer Doorgaan.
1. Selecteer DelimitedText als uw indeling en selecteer Doorgaan.
1. Selecteer in het deelvenster Eigenschappen instellen de gekoppelde ADLS-service die u hebt gemaakt. Geef het bestandspad van uw brongegevens op en geef op of de eerste rij een header bevat. U kunt het schema importeren uit het bestandsopslag of een voorbeeldbestand. Als u klaar bent, klikt u op OK.
1. Ga naar het **tabblad Sink.** Selecteer **Nieuw om** een nieuwe sink-gegevensset te maken.
1. Selecteer Azure Data Lake Storage gen2 als uw gegevensopslag en selecteer Doorgaan.
1. Selecteer DelimitedText als uw indeling en selecteer Doorgaan.
1. Selecteer in het deelvenster Eigenschappen instellen de gekoppelde ADLS-service die u hebt gemaakt. Geef het pad op van de map waar u gegevens wilt schrijven. Als u klaar bent, klikt u op OK.

## <a name="debug-and-publish-pipeline"></a>Fouten opsporen in pijplijn en pijplijn publiceren

Wanneer u klaar bent met het configureren van de pijplijn, kunt u deze uitvoeren om fouten op te sporten voordat u uw artefacten publiceert en te controleren of alles klopt.

1. Selecteer **Fouten opsporen** om fouten op te sporen in de pijplijn. De status van de pijplijnuitvoering wordt weergegeven op het tabblad **Uitvoer** onder in het venster. 
1. Zodra de pijplijn kan worden uitgevoerd, selecteert u Alles publiceren in **de bovenste werkbalk.** Met deze actie publiceert u entiteiten (gegevenssets en pijplijnen) die u hebt gemaakt in de Synapse Analytics-service.
1. Wacht tot u het bericht **Gepubliceerd** ziet. Als u meldingen wilt bekijken, selecteert u de knop met de bel in de rechterbovenhoek. 


## <a name="trigger-and-monitor-the-pipeline"></a>De pijplijn activeren en controleren

In deze stap activeert u handmatig de pijplijn die in de vorige stap is gepubliceerd. 

1. Selecteer op de werkbalk de optie **Trigger toevoegen** en selecteer vervolgens **Nu activeren**. Selecteer op **de pagina Pijplijn** uitvoeren de optie **Voltooien.**  
1. Ga naar het tabblad **Controle** in de zijbalk aan de linkerkant. U ziet een pijplijn die wordt geactiveerd door een handmatige trigger. U kunt via de links in de kolom **Acties** details van de activiteiten bekijken en de pijplijn opnieuw uitvoeren.
1. Selecteer de link **Uitvoeringen van activiteit weergeven** in de kolom **Acties** om de activiteituitvoeringen te zien die zijn gekoppeld aan de pijplijnuitvoering. Omdat er in dit voorbeeld slechts één activiteit is, ziet u slechts één vermelding in de lijst. Selecteer de link **Details** (pictogram van een bril) in de kolom **Acties** om details over de kopieerbewerking te zien. Selecteer **Pijplijn runs** bovenaan om terug te gaan naar de weergave Pipeline Runs. Selecteer **Vernieuwen** om de weergave te vernieuwen.
1. Controleer of uw gegevens correct zijn geschreven in de toegewezen SQL-pool.


## <a name="next-steps"></a>Volgende stappen

Zie het artikel Gegevens opnemen in een toegewezen SQL-pool Azure Synapse Analytics informatie over gegevensintegratie voor een specifieke [SQL-pool.](data-integration-sql-pool.md)