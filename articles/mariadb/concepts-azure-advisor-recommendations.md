---
title: Azure Advisor for MariaDB
description: Meer informatie Azure Advisor aanbevelingen voor MariaDB.
author: alau-ms
ms.author: alau
ms.service: mariadb
ms.topic: conceptual
ms.date: 04/12/2021
ms.openlocfilehash: 60a0a5763085de38dbfd4dabcb65dc24b299b29a
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368615"
---
# <a name="azure-advisor-for-mariadb"></a>Azure Advisor for MariaDB
Meer informatie over hoe Azure Advisor wordt toegepast op Azure Database for MariaDB en antwoorden op veelvoorkomende vragen.
## <a name="what-is-azure-advisor-for-mariadb"></a>Wat is Azure Advisor for MariaDB?
Het Azure Advisor gebruikt telemetrie om prestatie- en betrouwbaarheidsaanbevelingen uit te geven voor uw MariaDB-database. 

Sommige aanbevelingen zijn gebruikelijk voor meerdere productaanbiedingen, terwijl andere aanbevelingen zijn gebaseerd op productspecifieke optimalisaties.
## <a name="where-can-i-view-my-recommendations"></a>Waar kan ik mijn aanbevelingen bekijken?
Aanbevelingen zijn beschikbaar via de zijbalk **Overzicht** in de Azure Portal. Een voorbeeld wordt weergegeven als een bannermelding en details kunnen worden weergegeven in de sectie **Meldingen,** net onder de grafieken voor resourcegebruik.

:::image type="content" source="./media/concepts-azure-advisor-recommendations/advisor-example.png" alt-text="Schermopname van de Azure Portal met een Azure Advisor aanbeveling.":::

## <a name="recommendation-types"></a>Aanbevelingstypen
Azure Database for MariaDB de volgende typen aanbevelingen prioriteren:
* **Prestaties:** om de snelheid van uw MariaDB-server te verbeteren. Dit omvat CPU-gebruik, geheugendruk, schijfgebruik en productspecifieke serverparameters. Zie Advisor Performance [recommendations (Aanbevelingen voor Advisor-prestaties) voor meer informatie.](../advisor/advisor-performance-recommendations.md)
* **Betrouwbaarheid:** om de continuïteit van uw bedrijfskritieke databases te garanderen en te verbeteren. Dit omvat aanbevelingen voor opslaglimiet en verbindingslimiet. Zie Advisor-aanbevelingen voor betrouwbaarheid [voor meer informatie.](../advisor/advisor-high-availability-recommendations.md)
* **Kosten:** om uw totale Azure-uitgaven te optimaliseren en te verminderen. Dit omvat aanbevelingen voor het juiste formaat van de server. Zie Advisor [Cost recommendations (Aanbevelingen voor Advisor-kosten) voor meer informatie.](../advisor/advisor-cost-recommendations.md)

## <a name="understanding-your-recommendations"></a>Inzicht in uw aanbevelingen
* **Dagelijks schema:** voor Azure MariaDB-databases controleren we de telemetrie van de server en doen we aanbevelingen volgens een dagelijks schema. Als u de serverconfiguratie wijzigt, blijven bestaande aanbevelingen zichtbaar totdat de telemetrie op de volgende dag opnieuw wordt onderzocht. 
* **Prestatiegeschiedenis:** sommige van onze aanbevelingen zijn gebaseerd op de prestatiegeschiedenis. Deze aanbevelingen worden alleen weergegeven nadat een server al zeven dagen met dezelfde configuratie werkt. Hierdoor kunnen we patronen van intensief gebruik (bijvoorbeeld hoge CPU-activiteit of een hoog verbindingsvolume) detecteren gedurende een langdurige periode. Als u een nieuwe server inrichten of een nieuwe vCore-configuratie wijzigt, worden deze aanbevelingen tijdelijk onderbroken. Dit voorkomt dat verouderde telemetrie aanbevelingen activeert op een nieuw geconfigureerde server. Dit betekent echter ook dat aanbevelingen op basis van prestatiegeschiedenis niet onmiddellijk kunnen worden geïdentificeerd.

## <a name="next-steps"></a>Volgende stappen
Zie overzicht Azure Advisor [voor meer informatie.](../advisor/advisor-overview.md)
