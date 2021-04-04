---
title: Geïntegreerde oplossingen bouwen
description: Hulp middelen en partners voor oplossingen die zijn geïntegreerd met een toegewezen SQL-groep (voorheen SQL DW) in azure Synapse Analytics.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 3e55ef054d5c305937f88d6ec5b2b4453cac6792
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98117755"
---
# <a name="integrate-other-services-with-a-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Integreer andere services met een toegewezen SQL-groep (voorheen SQL DW) in azure Synapse Analytics.

Met de functie voor exclusieve SQL-groep (voorheen SQL DW) in azure Synapse Analytics kunnen gebruikers integreren met veel van de andere services in Azure. Een toegewezen SQL-groep kan gebruikmaken van verschillende extra services, waaronder:

* Power BI
* Azure Data Factory
* Azure Machine Learning
* Azure Stream Analytics

Raadpleeg het artikel [Integration partners](sql-data-warehouse-partner-data-integration.md)voor meer informatie over integratie Services in Azure.

## <a name="power-bi"></a>Power BI

Met Power BI-integratie kunt u de reken kracht van een Data Warehouse combi neren met de dynamische rapportage en visualisatie van Power BI. De Power BI-integratie bevat momenteel:

* **Direct Connect**: een geavanceerdere verbinding met logische pushdown voor een Data Warehouse dat is ingericht met een exclusieve SQL-groep (voorheen SQL DW). Pushdown biedt snellere analyse op grotere schaal.
* **Open in Power bi**: met de knop openen in Power bi worden instantie gegevens door gegeven aan Power BI voor een vereenvoudigde manier om verbinding te maken.

Zie [integreren met Power bi](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)of de [Power bi documentatie](https://powerbi.microsoft.com/blog/exploring-azure-sql-data-warehouse-with-power-bi/)voor meer informatie.

## <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory biedt gebruikers een beheerd platform voor het maken van complexe uitpakken en laad pijplijnen. De exclusieve integratie van een SQL-groep (voorheen SQL DW) met Azure Data Factory omvat:

* **Opgeslagen procedures**: de uitvoering van opgeslagen procedures organiseren.
* **Kopiëren**: gebruik ADF om gegevens te verplaatsen naar een toegewezen SQL-groep (voorheen SQL DW). Met deze bewerking kan het standaard mechanisme voor gegevens verplaatsing van ADF of poly base onder de kaften worden gebruikt.

Zie [integreren met Azure Data Factory](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)voor meer informatie.

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning is een volledig beheerde analyse service waarmee u ingewikkelde modellen kunt maken met behulp van een groot aantal voorspellende hulpprogram ma's. De toegewezen SQL-groep (voorheen SQL DW) wordt ondersteund als bron en doel voor deze modellen en heeft de volgende functionaliteit:

* **Gegevens lezen:** Modellen op schaal schalen met behulp van T-SQL op basis van een toegewezen SQL-groep (voorheen SQL DW).
* **Gegevens schrijven:** Wijzigingen van een model door voeren naar een toegewezen SQL-groep (voorheen SQL DW).

Zie [integreren met Azure machine learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)voor meer informatie.

## <a name="azure-stream-analytics"></a>Azure Stream Analytics

Azure Stream Analytics is een complexe, volledig beheerde infra structuur voor het verwerken en gebruiken van gebeurtenis gegevens die zijn gegenereerd vanuit Azure Event hub.  Dankzij de integratie met de toegewezen SQL-groep (voorheen SQL DW) kunnen streaminggegevens worden verwerkt en opgeslagen naast relationele gegevens, waardoor er dieper, geavanceerde analyses mogelijk zijn.  

* **Taak uitvoer:** Uitvoer van Stream Analytics-taken rechtstreeks naar een toegewezen SQL-groep (voorheen SQL DW) verzenden.

Zie [integreren met Azure stream Analytics](sql-data-warehouse-integrate-azure-stream-analytics.md)voor meer informatie.