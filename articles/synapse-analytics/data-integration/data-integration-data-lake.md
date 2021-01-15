---
title: Opnemen in Azure Data Lake Storage Gen2
description: Meer informatie over het opnemen van gegevens in Azure Data Lake Storage Gen2 in azure Synapse Analytics
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: fbc4f11b450a645002daedc800d4fed74ed37a3d
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98219569"
---
# <a name="ingest-data-into-azure-data-lake-storage-gen2"></a>Gegevens opnemen in Azure Data Lake Storage Gen2 

In dit artikel leert u hoe u gegevens opneemt van de ene locatie naar de andere in een opslag account van Azure Data Lake gen 2 (Azure Data Lake gen 2) met behulp van Azure Synapse Analytics.

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement**: als u nog geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.
* **Azure Storage account**: u Azure data Lake gen 2 gebruiken als een *brongegevens* opslag. Als u geen opslag account hebt, raadpleegt u [een Azure Storage-account maken](../../storage/common/storage-account-create.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) om er een te maken.

## <a name="create-linked-services"></a>Gekoppelde services maken

In Azure Synapse Analytics definieert u de verbindingsgegevens voor andere services in een gekoppelde service. In deze sectie voegt u Azure Synapse Analytics en Azure Data Lake gen 2 toe als gekoppelde services.

1. Open Azure Synapse Analytics UX en ga naar het tabblad **beheren** .
1. Onder **externe verbindingen** selecteert u **gekoppelde services**.
1. Selecteer **Nieuw** om een gekoppelde service toe te voegen.
1. Selecteer de tegel Azure Data Lake Storage Gen2 in de lijst en selecteer **door gaan**.
1. Voer uw verificatie referenties in. Account sleutel, Service-Principal en beheerde identiteit worden momenteel ondersteunde verificatie typen. Selecteer verbinding testen om te controleren of uw referenties correct zijn. 
1. Selecteer **Maken** nadat dit is voltooid.

## <a name="create-pipeline"></a>Pijplijn maken

Een pijplijn bevat de logische stroom voor het uitvoeren van een reeks activiteiten. In deze sectie maakt u een pijp lijn met een Kopieer activiteit waarmee gegevens van Azure Data Lake gen 2 worden opgenomen in een toegewezen SQL-groep.

1. Ga naar het tabblad **Orchestration** . Selecteer op het plus-pictogram naast de kop pijp lijnen en selecteer **pijp lijn**.
1. Sleep onder **verplaatsen en transformeren** in het deel venster activiteiten de gegevens naar het pijp lijn papier **kopiëren** .
1. Selecteer op de Kopieer activiteit en ga naar het tabblad **bron** . Selecteer **Nieuw** om een nieuwe bron-gegevensset te maken.
1. Selecteer Azure Data Lake Storage Gen2 als uw gegevens archief en selecteer door gaan.
1. Selecteer DelimitedText als uw indeling en selecteer door gaan.
1. Selecteer in het deel venster set Properties de ADLS-gekoppelde service die u hebt gemaakt. Geef het bestandspad van de bron gegevens op en geef op of de eerste rij een koptekst heeft. U kunt het schema importeren uit het bestands archief of een voorbeeld bestand. Als u klaar bent, klikt u op OK.
1. Ga naar het tabblad **sink** . Selecteer **Nieuw** om een nieuwe Sink-gegevensset te maken.
1. Selecteer Azure Data Lake Storage Gen2 als uw gegevens archief en selecteer door gaan.
1. Selecteer DelimitedText als uw indeling en selecteer door gaan.
1. Selecteer in het deel venster set Properties de ADLS-gekoppelde service die u hebt gemaakt. Geef het pad op van de map waarnaar u gegevens wilt schrijven. Als u klaar bent, klikt u op OK.

## <a name="debug-and-publish-pipeline"></a>Fouten opsporen en pijp lijn publiceren

Wanneer u klaar bent met het configureren van de pijplijn, kunt u deze uitvoeren om fouten op te sporten voordat u uw artefacten publiceert en te controleren of alles klopt.

1. Selecteer **Fouten opsporen** om fouten op te sporen in de pijplijn. De status van de pijplijnuitvoering wordt weergegeven op het tabblad **Uitvoer** onder in het venster. 
1. Zodra de pijp lijn kan worden uitgevoerd, selecteert u in de bovenste werk balk **Alles publiceren**. Met deze actie publiceert u entiteiten (gegevenssets en pijplijnen) die u hebt gemaakt in de Synapse Analytics-service.
1. Wacht tot u het bericht **Gepubliceerd** ziet. Als u meldingen wilt bekijken, selecteert u de knop met de bel in de rechterbovenhoek. 


## <a name="trigger-and-monitor-the-pipeline"></a>De pijplijn activeren en controleren

In deze stap moet u de pijp lijn die u in de vorige stap hebt gepubliceerd hand matig activeren. 

1. Selecteer op de werkbalk de optie **Trigger toevoegen** en selecteer vervolgens **Nu activeren**. Selecteer op de pagina **pijplijn uitvoering** de optie **volt ooien**.  
1. Ga naar het tabblad **Controle** in de zijbalk aan de linkerkant. U ziet een pijplijn die wordt geactiveerd door een handmatige trigger. U kunt via de links in de kolom **Acties** details van de activiteiten bekijken en de pijplijn opnieuw uitvoeren.
1. Selecteer de link **Uitvoeringen van activiteit weergeven** in de kolom **Acties** om de activiteituitvoeringen te zien die zijn gekoppeld aan de pijplijnuitvoering. Omdat er in dit voorbeeld slechts één activiteit is, ziet u slechts één vermelding in de lijst. Selecteer de link **Details** (pictogram van een bril) in de kolom **Acties** om details over de kopieerbewerking te zien. Selecteer de **pijp lijn wordt** aan de bovenkant uitgevoerd om terug te gaan naar de weer gave pijplijn uitvoeringen. Selecteer **Vernieuwen** om de weergave te vernieuwen.
1. Controleer of uw gegevens correct zijn geschreven in de toegewezen SQL-pool.


## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over gegevens integratie voor Azure Synapse Analytics het [opnemen van gegevens in een specifiek artikel over een SQL-groep](data-integration-sql-pool.md) .