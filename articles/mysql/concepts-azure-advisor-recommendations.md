---
title: Azure Advisor voor MySQL
description: Meer informatie over Azure Advisor aanbevelingen voor MySQL.
author: alau-ms
ms.author: alau
ms.service: mysql
ms.topic: conceptual
ms.date: 04/08/2021
ms.openlocfilehash: 4b3d750c930defbfcb7db6d4d67210e001e3ce8b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315455"
---
# <a name="azure-advisor-for-mysql"></a>Azure Advisor voor MySQL
Meer informatie over hoe Azure Advisor op Azure Database for MySQL wordt toegepast en antwoorden krijgt op veelgestelde vragen.
## <a name="what-is-azure-advisor-for-mysql"></a>Wat is Azure Advisor voor MySQL?
Het Azure Advisor systeem gebruikt telemetrie om aanbevelingen voor prestaties en betrouw baarheid voor uw MySQL-data base uit te geven. Aanbevelingen voor de Advisor-Data Base zijn onderverdeeld in de volgende aanbiedingen voor MySQL:
* Azure Database for MySQL - Single Server
* Azure Database for MySQL-flexibele server

Enkele aanbevelingen zijn gebruikelijk voor meerdere product aanbiedingen, terwijl andere aanbevelingen zijn gebaseerd op productspecifieke optimalisaties.
## <a name="where-can-i-view-my-recommendations"></a>Waar kan ik mijn aanbevelingen bekijken?
Aanbevelingen zijn beschikbaar op de **overzichts** navigatie zijbalk in het Azure Portal. Een voor beeld wordt weer gegeven als banner melding en details kunnen worden weer gegeven in het gedeelte **meldingen** dat zich net onder de grafiek van resource gebruik bevindt.

:::image type="content" source="./media/concepts-azure-advisor-recommendations/advisor-example.png" alt-text="Scherm opname van de Azure Portal een Azure Advisor aanbeveling weer gegeven.":::

## <a name="recommendation-types"></a>Aanbevelings typen
Azure Database for MySQL prioriteit van de volgende typen aanbevelingen:
* **Prestaties**: om de snelheid van de mysql-server te verbeteren. Dit geldt ook voor CPU-gebruik, geheugen druk, groepsgewijze verbindingen, schijf gebruik en productspecifieke server parameters. Zie aanbevelingen voor Advisor- [prestaties](../advisor/advisor-performance-recommendations.md)voor meer informatie.
* **Betrouw baarheid**: om de continuïteit van uw bedrijfs kritieke data bases te garanderen en te verbeteren. Dit omvat de aanbevelingen voor opslag limieten en verbindings limieten. Zie aanbevelingen voor de [betrouw baarheid van Advisor](../advisor/advisor-high-availability-recommendations.md)voor meer informatie.
* **Kosten**: om uw totale Azure-uitgaven te optimaliseren en te verlagen. Dit omvat aanbevelingen voor het aanpassen van de server. Zie aanbevelingen voor Advisor- [kosten](../advisor/advisor-cost-recommendations.md)voor meer informatie.

## <a name="understanding-your-recommendations"></a>Meer informatie over uw aanbevelingen
* **Dagelijks schema**: voor Azure MySQL-data bases wordt de server-telemetrie gecontroleerd en worden aanbevelingen op een dagelijks schema verleend. Als u een wijziging aanbrengt in de server configuratie, blijven bestaande aanbevelingen zichtbaar totdat de telemetrie op de volgende dag opnieuw wordt onderzocht. 
* **Prestatie geschiedenis**: enkele aanbevelingen zijn gebaseerd op de prestatie geschiedenis. Deze aanbevelingen worden pas weer gegeven nadat een Server 7 dagen met dezelfde configuratie is uitgevoerd. Hierdoor kunnen we patronen van intensief gebruik (bijvoorbeeld een hoge CPU-activiteit of een hoog verbindings volume) detecteren gedurende een langere periode. Als u een nieuwe server inricht of een nieuwe vCore-configuratie wijzigt, worden deze aanbevelingen tijdelijk onderbroken. Dit voor komt dat verouderde telemetrie aanbevelingen voor het activeren van een nieuw opnieuw geconfigureerde server. Dit betekent echter ook dat aanbevelingen op basis van de prestatie geschiedenis mogelijk niet direct worden geïdentificeerd.

## <a name="next-steps"></a>Volgende stappen
Zie [Azure Advisor Overview](../advisor/advisor-overview.md)voor meer informatie.
