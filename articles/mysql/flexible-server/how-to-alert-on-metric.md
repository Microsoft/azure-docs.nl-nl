---
title: Metrische waarschuwingen configureren-Azure Portal-Azure Database for MySQL-flexibele server
description: In dit artikel wordt beschreven hoe u metrische waarschuwingen kunt configureren en benaderen voor Azure Database for MySQL flexibele server van de Azure Portal.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 9/21/2020
ms.openlocfilehash: be52bbe58f6c2ff6ee21703860e8d8e00ec30072
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110148"
---
# <a name="use-the-azure-portal-to-set-up-alerts-on-metrics-for-azure-database-for-mysql---flexible-server"></a>Gebruik de Azure Portal om waarschuwingen in te stellen voor de Azure Database for MySQL-flexibele server 

> [!IMPORTANT] 
> Azure Database for MySQL - Flexible Server is momenteel beschikbaar als openbare preview.

In dit artikel wordt beschreven hoe u Azure Database for MySQL waarschuwingen instelt met behulp van de Azure Portal. U kunt een waarschuwing ontvangen op basis van metrische bewakings gegevens voor uw Azure-Services.

De waarschuwing wordt geactiveerd wanneer de waarde van een opgegeven metriek een drempel overschrijdt die u toewijst. De waarschuwing wordt geactiveerd wanneer aan de voor waarde wordt voldaan en daarna wordt niet meer voldaan aan de voor waarde. Metrische waarschuwingen zijn stateful, dat wil zeggen dat ze alleen meldingen verzenden wanneer de status wordt gewijzigd.

U kunt een waarschuwing configureren om de volgende acties uit te voeren wanneer deze wordt geactiveerd:
* E-mail meldingen verzenden naar de service beheerder en mede beheerders
* E-mail verzenden naar extra e-mail berichten die u opgeeft.
* Een webhook aanroepen

U kunt informatie over waarschuwings regels configureren en ophalen met behulp van:
* [Azure-portal](../../azure-monitor/alerts/alerts-metric.md#create-with-azure-portal)
* [Azure-CLI](../../azure-monitor/alerts/alerts-metric.md#with-azure-cli)
* [Azure Monitor REST API](/rest/api/monitor/metricalerts)

## <a name="create-an-alert-rule-on-a-metric-from-the-azure-portal"></a>Een waarschuwingsregel maken op een metrische waarde vanuit Azure Portal
1. Selecteer in de [Azure Portal](https://portal.azure.com/)de Azure database for MySQL flexibele server die u wilt bewaken.
2. Selecteer in de sectie **bewaking** van de zijbalk **waarschuwingen**.
3. Selecteer **+ Nieuwe waarschuwingsregel**.
4. De pagina **Regel maken** wordt geopend. Vul de vereiste gegevens in:
5. Klik in de sectie **voor** waarde op **voor waarde selecteren**.
6. U ziet een lijst met ondersteunde signalen. Selecteer de metrische gegevens waarvoor u een waarschuwing wilt maken. Selecteer bijvoorbeeld ' opslag percentage '.
7. Er wordt een grafiek weer gegeven met de metrische gegevens voor de afgelopen zes uur. Gebruik de vervolg keuzelijst **grafiek periode** om de meer geschiedenis voor de metrische gegevens weer te geven.
8. Selecteer het type **drempel waarde** (bijvoorbeeld "Static" of "Dynamic"), **operator** (ex. ' Groter dan ') en **aggregatie type** (bijvoorbeeld gemiddelde). Hiermee wordt de logica bepaald die door de metrische waarschuwings regel wordt geëvalueerd.
    - Als u een **statische** drempel waarde gebruikt, gaat u door met het definiëren van een **drempelwaarde** (bijvoorbeeld 85 procent). De metrische grafiek kan helpen om te bepalen wat een redelijke drempel waarde kan hebben.
    - Als u een **dynamische** drempel waarde gebruikt, gaat u door met het definiëren van de **drempel gevoeligheid**. De metrische grafiek geeft de berekende drempel waarden weer op basis van recente gegevens. Meer [informatie over de drempel waarde voor het type en gevoeligheids opties voor dynamische waarden](../../azure-monitor/alerts/alerts-dynamic-thresholds.md).
9. Verfijn de voor waarde door het interval voor **aggregatie granulatie (periode)** aan te passen waarover gegevens punten worden gegroepeerd met behulp van de functie aggregatie type (ex. "30 minuten") en **frequentie** (ex "elke 15 minuten").
10. Klik op **Gereed**.
11. Voeg een actie groep toe. Een actie groep is een verzameling voor keuren voor meldingen die zijn gedefinieerd door de eigenaar van een Azure-abonnement. Kies in de sectie **actie groepen** de **optie actie groep selecteren** om een bestaande actie groep te selecteren die u aan de waarschuwings regel wilt koppelen.
12. U kunt ook een nieuwe actie groep maken om meldingen over de waarschuwing te ontvangen. Raadpleeg de [actie groep maken en beheren](../../azure-monitor/alerts/action-groups.md) voor meer informatie.
13. Als u een nieuwe actie groep wilt maken, kiest u **+ actie groep maken**. Vul het formulier actie groep maken in met een **abonnement**, **resource groep**, naam van de **actie groep** en **weergave naam**.
14. **Meldingen** voor actie groep configureren.
    
    Kies in **meldings type** de optie e-mail Azure Resource Manager rol om abonnements eigenaren, mede werkers en lezers te selecteren voor het ontvangen van meldingen. Kies de **Azure Resource Manager rol** voor het verzenden van het e-mail bericht.
    U kunt ook **e-mail/SMS-bericht/push/Voice** kiezen voor het verzenden van meldingen naar specifieke ontvangers.

    Geef een **naam** op voor het meldings type en selecteer **controleren + maken** wanneer dit is voltooid.

    <!--:::image type="content" source="./media/howto-alert-on-metric/10-action-group-type.png" alt-text="Action group":::-->
    
15. Vul de **Details van de waarschuwings regel** in, zoals de naam van de **waarschuwings regel**, **Beschrijving**, **waarschuwings regel opslaan in resource groep** en **Ernst**.

    <!--:::image type="content" source="./media/howto-alert-on-metric/11-name-description-severity.png" alt-text="Action group":::-->

16. Selecteer **waarschuwings regel maken** om de waarschuwing te maken.
    Binnen een paar minuten is de waarschuwing actief en worden triggers zoals eerder beschreven.
## <a name="manage-your-alerts"></a>De waarschuwingen beheren
Zodra u een waarschuwing hebt gemaakt, kunt u deze selecteren en de volgende acties uitvoeren:

* Een grafiek weer geven met de metrische drempel waarde en de werkelijke waarden van de vorige dag die relevant is voor deze waarschuwing.
* **Bewerk** of **Verwijder** de waarschuwings regel.
* **Schakel** de waarschuwing **in** of uit als u het ontvangen van meldingen tijdelijk wilt stoppen of hervatten.


## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het [instellen van een waarschuwing op metrische gegevens](../../azure-monitor/alerts/alerts-metric.md).
- Meer informatie over beschik bare [metrische gegevens in azure database for MySQL flexibele server](./concepts-monitoring.md).
- [Begrijpen hoe waarschuwingen voor metrische gegevens werken in Azure Monitor](../../azure-monitor/alerts/alerts-metric-overview.md)