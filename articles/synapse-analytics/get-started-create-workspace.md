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
ms.date: 11/21/2020
ms.openlocfilehash: 949992418cb19c5c69ed8da92e9c2f0fc5f2f791
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445418"
---
# <a name="creating-a-synapse-workspace"></a>Een Synapse-werkruimte maken

In deze zelfstudie leert u hoe u een Synapse-werkruimte, een toegewezen SQL-pool en een serverloze Apache Spark-pool maakt. 

## <a name="prerequisites"></a>Vereisten

Om alle stappen van deze zelfstudie te kunnen voltooien, moet u toegang hebben tot een resourcegroep waarvoor de rol **Eigenaar** aan u is toegewezen. Maak de Synapse-werkruimte in deze resourcegroep.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Maak een Synapse-werkruimte in Azure Portal

1. Open de [Azure-portal](https://portal.azure.com) en zoek bovenin naar **Synapse**.
1. Selecteer in de zoekresultaten onder **Services** de optie **Azure Synapse Analytics**.
1. Selecteer **Toevoegen** om een werkruimte te maken.
1. Voer in **Basics** uw gewenste **Abonnement**, **Resourcegroep** en **Regio** in, en kies vervolgens een werkruimtenaam. In deze zelfstudie gebruiken we **myworkspace**.
1. Navigeer naar **Data Lake Storage Gen 2 selecteren**. 
1. Klik op **Nieuwe maken** en voer de naam **contosolake** in.
1. Klik op **Bestandssysteem** en voer de naam **gebruikers** in. Hiermee maakt u een container met de naam **gebruikers**
1. In de werkruimte wordt dit opslagaccount gebruikt als het primaire opslagaccount voor logboeken voor Spark-tabellen en Spark-toepassingen.
1. Selecteer **Beoordelen en maken** > **Maken**. Uw werkruimte is binnen een paar minuten klaar.

> [!NOTE]
> Als u werkruimtefuncties wilt inschakelen vanuit een bestaande toegewezen SQL-pool (voorheen SQL DW), raadpleegt u [Een werkruimte inschakelen voor uw toegewezen SQL-pool (voorheen SQL DW)](./sql-data-warehouse/workspace-connected-create.md).


## <a name="open-synapse-studio"></a>Synapse Studio openen

Wanneer uw Azure Synapse-werkruimte is gemaakt, kunt u Synapse Studio op twee manieren openen:

* Open de Synapse-werkruimte in de [Azure-portal](https://portal.azure.com). Selecteer bovenaan de sectie **Overzicht** de optie **Synapse Studio starten**.
* Ga naar `https://web.azuresynapse.net` en meld u aan bij uw werkruimte.

## <a name="create-a-dedicated-sql-pool"></a>Een toegewezen SQL-pool maken

1. Selecteer in Synapse Studio in het linkerdeelvenster de optie **Beheren** > **SQL-pools**.
1. Selecteer **Nieuw**
1. Selecteer **SQLPOOL1** voor **Naam van SQL-pool**
1. Kies **DW100C** voor **Prestatieniveau**
1. Selecteer **Beoordelen en maken** > **Maken**. Uw toegewezen SQL-pool is binnen een paar minuten klaar. Uw toegewezen SQL-pool is gekoppeld aan een toegewezen SQL-pooldatabase die ook **SQLPOOL1** heet.

Een toegewezen SQL-pool verbruikt factureerbare resources zolang deze worden uitgevoerd. U kunt de pool later onderbreken om de kosten te verlagen.

> [!NOTE] 
> Bij het maken van een nieuwe toegewezen SQL-pool (voorheen SQL DW) in uw werkruimte, wordt de pagina voor het inrichten van de toegewezen SQL-pool geopend. Het inrichten vindt plaats op de logische SQL-server.

## <a name="create-a-serverless-apache-spark-pool"></a>Een serverloze Apache Spark-pool maken

1. Selecteer in Synapse Studio in het linkerdeelvenster **Beheren** > **Apache Spark-pools**.
1. Selecteer **Nieuw** 
1. Voer **Spark1** in voor **Naam van Apache Spark-pool**.
1. Voer **Klein** in voor **Grootte van knooppunt**.
1. Stel voor **Aantal knooppunten** het minimum in op 3 en het maximum op 3
1. Selecteer **Beoordelen en maken** > **Maken**. Uw Apache Spark-pool is binnen een paar seconden gereed.

De Spark-pool informeert Azure Synapse hoeveel Apache Spark-resources moeten worden gebruikt. U betaalt alleen voor de resources die u gebruikt. Wanneer u de pool niet meer actief gebruikt, treedt er automatisch een time-out op in de resources en worden ze gerecycled.

## <a name="the-built-in-serverless-sql-pool"></a>De ingebouwde serverloze SQL-pool

Elke werkruimte wordt geleverd met een vooraf gemaakte serverloze SQL-pool met de naam **Ingebouwd**. Deze pool kan niet worden verwijderd. Met serverloze SQL-pools kunt u SQL gebruiken zonder dat er capaciteit hoeft te worden gereserveerd in toegewezen SQL-pools. In tegenstelling tot de toegewezen SQL-pools is facturering voor een serverloze SQL-pool gebaseerd op de hoeveelheid gegevens die zijn gescand om de query uit te voeren en niet op de hoeveelheid capaciteit die aan de pool wordt toegewezen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Analyseren met een toegewezen SQL-pool](get-started-analyze-sql-pool.md)
