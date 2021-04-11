---
title: Azure Advisor voor PostgreSQL
description: Meer informatie over Azure Advisor aanbevelingen voor PostgreSQL.
author: alau-ms
ms.author: alau
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 9d736fe77e41b80abaf76d13e918b7d51bde09c0
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107250990"
---
# <a name="azure-advisor-for-postgresql"></a>Azure Advisor voor PostgreSQL
Meer informatie over hoe Azure Advisor op Azure Database for PostgreSQL wordt toegepast en antwoorden krijgt op veelgestelde vragen.
## <a name="what-is-azure-advisor-for-postgresql"></a>Wat is Azure Advisor voor PostgreSQL?
Het Azure Advisor systeem gebruikt telemetrie om aanbevelingen voor prestaties en betrouw baarheid te geven voor uw PostgreSQL-data base. Aanbevelingen voor Advisor worden gesplitst over onze PostgreSQL-database aanbiedingen:
* Azure Database for PostgreSQL - één server
* Azure Database for PostgreSQL - Flexible Server
* Azure Database for PostgreSQL - Hyperscale (Citus)

Enkele aanbevelingen zijn gebruikelijk voor meerdere product aanbiedingen, terwijl andere aanbevelingen zijn gebaseerd op productspecifieke optimalisaties.
## <a name="where-can-i-view-my-recommendations"></a>Waar kan ik mijn aanbevelingen bekijken?
Aanbevelingen zijn beschikbaar op de **overzichts** navigatie zijbalk in het Azure Portal. Een voor beeld wordt weer gegeven als banner melding en details kunnen worden weer gegeven in het gedeelte **meldingen** dat zich net onder de grafiek van resource gebruik bevindt.

:::image type="content" source="./media/concepts-azure-advisor-recommendations/advisor-example.png" alt-text="Scherm opname van de Azure Portal een Azure Advisor aanbeveling weer gegeven.":::

## <a name="recommendation-types"></a>Aanbevelings typen
Azure Database for PostgreSQL prioriteit van de volgende typen aanbevelingen:
* **Prestaties**: om de snelheid van uw postgresql-server te verbeteren. Dit geldt ook voor CPU-gebruik, geheugen druk, groepsgewijze verbindingen, schijf gebruik en productspecifieke server parameters. Zie aanbevelingen voor Advisor- [prestaties](../advisor/advisor-performance-recommendations.md)voor meer informatie.
* **Betrouw baarheid**: om de continuïteit van uw bedrijfs kritieke data bases te garanderen en te verbeteren. Dit omvat opslag limieten, verbindings limieten en aanbevelingen voor het distribueren van grootschalige-gegevens. Zie aanbevelingen voor de [betrouw baarheid van Advisor](../advisor/advisor-high-availability-recommendations.md)voor meer informatie.
* **Kosten**: om uw totale Azure-uitgaven te optimaliseren en te verlagen. Dit omvat aanbevelingen voor het aanpassen van de server. Zie aanbevelingen voor Advisor- [kosten](../advisor/advisor-cost-recommendations.md)voor meer informatie.

## <a name="understanding-your-recommendations"></a>Meer informatie over uw aanbevelingen
* **Dagelijks schema**: voor Azure postgresql-data bases controleren we de server-telemetrie en geven ze aanbevelingen volgens een dagelijks schema. Als u een wijziging aanbrengt in de server configuratie, blijven bestaande aanbevelingen zichtbaar totdat de telemetrie op de volgende dag opnieuw wordt onderzocht. 
* **Prestatie geschiedenis**: enkele aanbevelingen zijn gebaseerd op de prestatie geschiedenis. Deze aanbevelingen worden pas weer gegeven nadat een Server 7 dagen met dezelfde configuratie is uitgevoerd. Hierdoor kunnen we patronen van intensief gebruik (bijvoorbeeld een hoge CPU-activiteit of een hoog verbindings volume) detecteren gedurende een langere periode. Als u een nieuwe server inricht of een nieuwe vCore-configuratie wijzigt, worden deze aanbevelingen tijdelijk onderbroken. Dit voor komt dat verouderde telemetrie aanbevelingen voor het activeren van een nieuw opnieuw geconfigureerde server. Dit betekent echter ook dat aanbevelingen op basis van de prestatie geschiedenis mogelijk niet direct worden geïdentificeerd.

## <a name="next-steps"></a>Volgende stappen
Zie [Azure Advisor Overview](../advisor/advisor-overview.md)voor meer informatie.
