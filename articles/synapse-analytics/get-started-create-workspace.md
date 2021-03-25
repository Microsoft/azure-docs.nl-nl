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
ms.openlocfilehash: 695ad1463bb207e9dd61d7b0147adbe15b44df37
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105108057"
---
# <a name="creating-a-synapse-workspace"></a>Een Synapse-werkruimte maken

In deze zelfstudie leert u hoe u een Synapse-werkruimte, een toegewezen SQL-pool en een serverloze Apache Spark-pool maakt. 

## <a name="prerequisites"></a>Vereisten

Om alle stappen van deze zelfstudie te kunnen voltooien, moet u toegang hebben tot een resourcegroep waarvoor de rol **Eigenaar** aan u is toegewezen. Maak de Synapse-werkruimte in deze resourcegroep.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Maak een Synapse-werkruimte in Azure Portal

### <a name="start-the-process"></a>Het proces starten
1. Open de [Azure Portal](https://portal.azure.com), typ **Synapse** in de zoek balk zonder ENTER.
1. Selecteer in de zoekresultaten onder **Services** de optie **Azure Synapse Analytics**.
1. Selecteer **Toevoegen** om een werkruimte te maken.

## <a name="basics-tab--project-details"></a>Tabblad basis beginselen > project Details
Vul de volgende velden in:
    1. **Abonnement** : Kies een abonnement.
    1. **Resource groep** : gebruik een resource groep.
    1. **Resource groep** : laat dit veld leeg.


## <a name="basics-tab--workspace-details"></a>Tabblad basis informatie > werkruimte Details
Vul de volgende velden in: 1. **Werkruimte naam** : Kies een wereld wijd unieke naam. In deze zelfstudie gebruiken we **myworkspace**.
1. **Regio** : Kies een wille keurige regio.
1. **Data Lake Storage gen 2 selecteren** i. Klik op de knop voor **van abonnement**.
1. Klik op **account naam** op **Nieuw** en geef de naam van het nieuwe opslag account **contosolake** of soortgelijk op als deze naam uniek moet zijn.
1. Op **naam van bestands systeem**, klikt u op **nieuwe maken** en de naam **gebruikers** benoemen. Hiermee maakt u een opslag container die **gebruikers** wordt genoemd. In de werkruimte wordt dit opslagaccount gebruikt als het primaire opslagaccount voor logboeken voor Spark-tabellen en Spark-toepassingen.
1. Schakel het selectie vakje ' de rol van de BLOB voor het koppelen van gegevens in het Data Lake Storage Gen2-account toewijzen ' in. 

## <a name="completing-the-process"></a>Het proces volt ooien
Selecteer **Beoordelen en maken** > **Maken**. Uw werkruimte is binnen een paar minuten klaar.

> [!NOTE]
> Als u werkruimtefuncties wilt inschakelen vanuit een bestaande toegewezen SQL-pool (voorheen SQL DW), raadpleegt u [Een werkruimte inschakelen voor uw toegewezen SQL-pool (voorheen SQL DW)](./sql-data-warehouse/workspace-connected-create.md).


## <a name="open-synapse-studio"></a>Synapse Studio openen

Wanneer uw Azure Synapse-werkruimte is gemaakt, kunt u Synapse Studio op twee manieren openen:

* Open uw Synapse-werk ruimte in de [Azure Portal](https://portal.azure.com), Selecteer in het gedeelte **overzicht** van de werk ruimte Synapse de optie **openen** in het vak open Synapse Studio.
* Ga naar `https://web.azuresynapse.net` en meld u aan bij uw werkruimte.











## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Analyseren met behulp van een serverloze SQL-groep](get-started-analyze-sql-on-demand.md)
