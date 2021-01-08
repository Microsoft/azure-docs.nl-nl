---
title: Azure Functions uitvoer van Azure Stream Analytics
description: In dit artikel worden Azure functions als uitvoer voor Azure Stream Analytics beschreven.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: e5ea7a1abbbd6ab4be32955179227fbd539cf641
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019615"
---
# <a name="azure-functions-output-from-azure-stream-analytics"></a>Azure Functions uitvoer van Azure Stream Analytics

Azure Functions is een serverloze compute-service die u kunt gebruiken om code op aanvraag uit te voeren zonder expliciet een infra structuur in te richten of te beheren. Hiermee kunt u code implementeren die wordt geactiveerd door gebeurtenissen die optreden in azure of partner services. Dankzij deze mogelijkheid van Azure Functions om op Triggers te reageren, is het een natuurlijke uitvoer voor Azure Stream Analytics. Met deze uitvoer adapter kunnen gebruikers Stream Analytics verbinding maken met Azure Functions en een script of stukje code uitvoeren als reactie op verschillende gebeurtenissen.

Azure Functions uitvoer van Stream Analytics is niet beschikbaar in de regio's in azure China 21Vianet en Azure Duitsland (T-Systems International). Verbinding met Azure Functions in een virtueel netwerk (VNet) vanuit een Stream Analytics-taak die wordt uitgevoerd in een cluster met meerdere tenants wordt ook niet ondersteund.

Azure Stream Analytics roept Azure Functions aan via HTTP-triggers. De Azure Functions uitvoer adapter is beschikbaar met de volgende Configureer bare eigenschappen:

| Naam van eigenschap | Beschrijving |
| --- | --- |
| Functie-app |De naam van uw Azure Functions-app. |
| Functie |De naam van de functie in uw Azure Functions-app. |
| Sleutel |Als u een Azure-functie van een ander abonnement wilt gebruiken, kunt u dit doen door de sleutel voor toegang tot uw functie te geven. |
| Maximale Batch grootte |Een eigenschap waarmee u de maximale grootte kunt instellen voor elke uitvoer batch die wordt verzonden naar uw Azure-functie. De invoer eenheid bevindt zich in bytes. Deze waarde is standaard 262.144 bytes (256 KB). |
| Maximum aantal batches  |Een eigenschap waarmee u het maximum aantal gebeurtenissen in elke batch kunt opgeven dat naar Azure Functions wordt verzonden. De standaardwaarde is 100. |

Azure Stream Analytics verwacht HTTP-status 200 van de functie-app voor batches die zijn verwerkt.

Wanneer Azure Stream Analytics een uitzonde ring ontvangt van een 413 (' HTTP-aanvraag entiteit te groot ') van een Azure-functie, vermindert deze de grootte van de batches die worden verzonden naar Azure Functions. Gebruik in uw Azure-functie code Deze uitzonde ring om ervoor te zorgen dat Azure Stream Analytics geen grote aantal batches verzendt. Zorg er ook voor dat het maximum aantal batch-en grootte waarden die worden gebruikt in de functie consistent zijn met de waarden die zijn ingevoerd in de Stream Analytics Portal.

> [!NOTE]
> Tijdens een test verbinding stuurt Stream Analytics een lege batch naar Azure Functions om te testen als de verbinding tussen de twee werken. Zorg ervoor dat uw functions-app lege batch-aanvragen verwerkt om ervoor te zorgen dat de test verbinding verloopt.

In een situatie waarin er geen gebeurtenis overloop plaatsvindt in een tijd venster, wordt er ook geen uitvoer gegenereerd. Als gevolg hiervan wordt de functie **computeResult** niet aangeroepen. Dit gedrag is consistent met de ingebouwde statistische functies van het venster.

## <a name="partitioning"></a>Partitionering

De partitie sleutel is gebaseerd op de component PARTITION BY in de query. Het aantal schrijvers van de uitvoer volgt de invoer partities voor [volledig parallelle query's](stream-analytics-scale-jobs.md).

## <a name="output-batch-size"></a>Grootte van uitvoer batch

De standaard Batch grootte is 262.144 bytes (256 KB). Het aantal standaard gebeurtenissen per batch is 100. De Batch grootte kan worden geconfigureerd en kan worden verg root of verkleind in de Stream Analytics uitvoer opties.

## <a name="next-steps"></a>Volgende stappen

* [Snelstart: Een Stream Analytics-taak maken via Azure Portal](stream-analytics-quick-create-portal.md)
* [Quickstart: een Azure Stream Analytics-taak maken via de Azure CLI](quick-create-azure-cli.md)
* [Quickstart: Een Azure Stream Analytics-taak maken via een ARM-sjabloon](quick-create-azure-resource-manager.md)
* [Quickstart: Een Stream Analytics-taak maken met behulp van Azure PowerShell](stream-analytics-quick-create-powershell.md)
* [Quickstart: Een Azure Stream Analytics-taak maken met behulp van Visual Studio](stream-analytics-quick-create-vs.md)
* [Snelstartgids: een Azure Stream Analytics-taak maken in Visual Studio code](quick-create-visual-studio-code.md)