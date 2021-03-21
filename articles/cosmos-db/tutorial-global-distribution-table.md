---
title: Zelfstudie voor het instellen van wereldwijde distributie met Azure Cosmos DB met behulp van de Table-API
description: Meer informatie over het functioneren van wereldwijde distributie in Azure Cosmos DB Table-API-accounts en het configureren van de lijst met regio's van voorkeur
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 01/30/2020
ms.reviewer: sngun
ms.openlocfilehash: f06fbe482327d01917f648ccdec34baa15c46e80
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93073907"
---
# <a name="set-up-azure-cosmos-db-global-distribution-using-the-table-api"></a>Wereldwijde distributie met Azure Cosmos DB instellen met behulp van de Table-API
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Dit artikel behandelt de volgende taken: 

> [!div class="checklist"]
> * Wereldwijde distributie configureren met behulp van Azure Portal
> * Globale distributie configureren met behulp van de [Table-API](table-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-table-api"></a>Verbinding maken met een voorkeursregio met behulp van de Table-API

Als u wilt profiteren van de [wereldwijde distributie](distribute-data-globally.md), moeten door clienttoepassingen de huidige locaties worden opgegeven waar de toepassing wordt uitgevoerd. Dit doet u door de eigenschap `CosmosExecutorConfiguration.CurrentRegion` in te stellen. De eigenschap `CurrentRegion` moet één locatie bevatten. Elk clientexemplaar kan een eigen regio opgeven voor leesbewerkingen met lage latentie. De regio krijgt de naam op basis van de [weergavenamen](/previous-versions/azure/reference/gg441293(v=azure.100)), bijvoorbeeld US - west. 

De Azure Cosmos DB Table-API SDK kiest automatisch het beste eindpunt om mee te communiceren op basis van de accountconfiguratie en de huidige regionale beschikbaarheid. De regio die het dichtst in de buurt is krijgt de prioriteit, zodat clients over de beste latentie beschikken. Nadat u de huidige eigenschap, `CurrentRegion`, hebt ingesteld, worden lees- en schrijfaanvragen als volgt omgeleid:

* **Leesaanvragen:** Alle leesaanvragen worden verzonden naar de geconfigureerde eigenschap `CurrentRegion`. Op basis van nabijheid selecteert de SDK automatisch een geo-gerepliceerde regio om op terug te kunnen vallen, zodat hoge beschikbaarheid wordt geboden.

* **Schrijfaanvragen:** De SDK verzendt alle schrijfaanvragen automatisch naar de huidige schrijfregio. In een account met multiregio-schrijfaanvragen verzorgt de huidige regio ook de schrijfaanvragen. Op basis van nabijheid selecteert de SDK automatisch een geo-gerepliceerde regio om op terug te kunnen vallen, zodat hoge beschikbaarheid wordt geboden.

Als u eigenschap `CurrentRegion` niet opgeeft, gebruikt de SDK de huidige schrijfregio voor alle bewerkingen.

Bijvoorbeeld als een Azure Cosmos-account zich in de regio's US - west en US - oost bevindt. Als US - west de schrijfregio is en de toepassing zich in US - oost bevindt. Als de eigenschap CurrentRegion niet goed is geconfigureerd, worden alle lees- en schrijfaanvragen altijd doorgestuurd naar de regio US - west. Als de eigenschap CurrentRegion is geconfigureerd, worden alle leesaanvragen vanuit de regio US - oost afgehandeld.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende gedaan:

> [!div class="checklist"]
> * Wereldwijde distributie configureren met behulp van Azure Portal
> * Globale distributie met Azure Cosmos DB instellen met behulp van de Table-API’s