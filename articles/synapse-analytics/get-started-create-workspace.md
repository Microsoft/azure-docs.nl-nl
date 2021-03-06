---
title: 'Quickstart: Aan de slag: een Synapse-werkruimte maken'
description: In deze zelfstudie leert u hoe u een Synapse-werkruimte, een toegewezen SQL-pool en een serverloze Apache Spark-pool maakt.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 03/17/2021
ms.openlocfilehash: 4b7251be220c012ca51970863ac2eed55d46d711
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751144"
---
# <a name="creating-a-synapse-workspace"></a>Een Synapse-werkruimte maken

In deze zelfstudie leert u hoe u een Synapse-werkruimte, een toegewezen SQL-pool en een serverloze Apache Spark-pool maakt. 

## <a name="prerequisites"></a>Vereisten

Om alle stappen van deze zelfstudie te kunnen voltooien, moet u toegang hebben tot een resourcegroep waarvoor de rol **Eigenaar** aan u is toegewezen. Maak de Synapse-werkruimte in deze resourcegroep.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Maak een Synapse-werkruimte in Azure Portal

### <a name="start-the-process"></a>Het proces starten
1. Open de [Azure Portal](https://portal.azure.com), voer in de zoekbalk **Synapse** in zonder op Enter te raken.
1. Selecteer in de zoekresultaten onder **Services** de optie **Azure Synapse Analytics**.
1. Selecteer **Toevoegen** om een werkruimte te maken.

## <a name="basics-tab--project-details"></a>Tabblad Basisbeginselen > Projectdetails
Vul de volgende velden in:

1. **Abonnement:** kies een abonnement.
1. **Resourcegroep:** gebruik een resourcegroep.
1. **Beheerde resourcegroep:** laat dit leeg.

## <a name="basics-tab--workspace-details"></a>Tabblad Basisbeginselen > details van werkruimte
Vul de volgende velden in:

1. **Werkruimtenaam:** kies een wereldwijd unieke naam. In deze zelfstudie gebruiken we **myworkspace**.
1. **Regio:** kies de regio waar u uw clienttoepassingen/-services (bijvoorbeeld Azure VM, Power BI, Azure Analysis Service) en opslag die gegevens bevatten (bijvoorbeeld Azure Data Lake-opslag, Azure Cosmos DB analytische opslag).

> [!NOTE]
> Een werkruimte die zich niet op dezelfde locatie als de clienttoepassingen of opslag heeft, kan de hoofdoorzaak van veel prestatieproblemen zijn. Als uw gegevens of de clients in meerdere regio's worden geplaatst, kunt u afzonderlijke werkruimten in verschillende regio's maken die zijn samen met uw gegevens en clients.

Selecteer **onder Data Lake Storage Gen 2:**

1. Selecteer **bij Accountnaam** de optie **Nieuwe maken** en noem het nieuwe opslagaccount **contosolake** of vergelijkbaar met de naam moet uniek zijn.
1. Selecteer **op Bestandssysteemnaam** de optie **Nieuwe maken** en noem deze **gebruikers**. Hiermee maakt u een opslagcontainer met de **naam users**. In de werkruimte wordt dit opslagaccount gebruikt als het primaire opslagaccount voor logboeken voor Spark-tabellen en Spark-toepassingen.
1. Vink het selectievakje 'Assign me the Storage Blob Data Contributor role on the Data Lake Storage Gen2 account' aan. 

## <a name="completing-the-process"></a>Het proces voltooien
Selecteer **Beoordelen en maken** > **Maken**. Uw werkruimte is binnen een paar minuten klaar.

> [!NOTE]
> Als u werkruimtefuncties wilt inschakelen vanuit een bestaande toegewezen SQL-pool (voorheen SQL DW), raadpleegt u [Een werkruimte inschakelen voor uw toegewezen SQL-pool (voorheen SQL DW)](./sql-data-warehouse/workspace-connected-create.md).


## <a name="open-synapse-studio"></a>Synapse Studio openen

Wanneer uw Azure Synapse-werkruimte is gemaakt, kunt u Synapse Studio op twee manieren openen:

* Open uw Synapse-werkruimte in [Azure Portal](https://portal.azure.com),  selecteer in de sectie Overzicht van de Synapse-werkruimte de optie **Openen** in het vak Synapse Studio openen.
* Ga naar `https://web.azuresynapse.net` en meld u aan bij uw werkruimte.

## <a name="place-sample-data-into-the-primary-storage-account"></a>Voorbeeldgegevens in het primaire opslagaccount plaatsen
We gaan een kleine gegevensset van 100.000 rijen met NYX Taxi Cab-gegevens gebruiken voor veel voorbeelden in deze aan de slag-handleiding. We beginnen met het plaatsen ervan in het primaire opslagaccount dat u voor de werkruimte hebt gemaakt.

* Download dit bestand naar uw computer: https://azuresynapsestorage.blob.core.windows.net/sampledata/NYCTaxiSmall/NYCTripSmall.parquet 
* Navigeer Synapse Studio naar de Data Hub. 
* Selecteer **Gekoppeld.**
* Onder de **Azure Data Lake Storage Gen2** ziet u een item met een naam zoals **myworkspace ( Primary - contosolake )**.
* Selecteer de container met de **naam users (Primary)**.
* Selecteer **Uploaden** en selecteer het `NYCTripSmall.parquet` bestand dat u hebt gedownload.

Als het Parquet-bestand is geüpload, is het beschikbaar via twee equivalente URI's:
* `https://contosolake.dfs.core.windows.net/users/NYCTripSmall.parquet` 
* `abfss://users@contosolake.dfs.core.windows.net/NYCTripSmall.parquet`

In de voorbeelden die volgen op deze zelfstudie, moet u **contosolake** in de gebruikersinterface vervangen door de naam van het primaire opslagaccount dat u voor uw werkruimte hebt geselecteerd.



## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Analyseren met behulp van een serverloze SQL-pool](get-started-analyze-sql-on-demand.md)
