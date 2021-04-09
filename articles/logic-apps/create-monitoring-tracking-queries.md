---
title: Query's voor Logic apps in Azure Monitor logboeken weer geven en maken
description: Query's weer geven en maken in Azure Monitor logboeken voor Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 5de795811923c965a1473f8d91e3fd654aeef266
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100574546"
---
# <a name="view-and-create-queries-for-monitoring-and-tracking-in-azure-monitor-logs-for-azure-logic-apps"></a>Query's weer geven en maken voor het controleren en bijhouden van Azure Monitor logboeken voor Azure Logic Apps

U kunt de onderliggende query's weer geven die de resultaten genereren uit [Azure monitor logboeken](../azure-monitor/logs/log-query-overview.md) en query's maken waarmee de resultaten worden gefilterd op basis van uw specifieke criteria. U kunt bijvoorbeeld berichten vinden op basis van een specifiek uitwisselings controle nummer. Query's gebruiken de [Kusto-query taal](/azure/data-explorer/kusto/query/), die u kunt bewerken als u andere resultaten wilt weer geven. Zie [Azure monitor-logboek query's](/azure/data-explorer/kusto/query/)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

* Een Log Analytics-werkruimte. Als u geen Log Analytics-werk ruimte hebt, leert u [hoe u een log Analytics-werk ruimte maakt](../azure-monitor/logs/quick-create-workspace.md).

* Een logische app die is ingesteld met Azure Monitor logboek registratie en verzendt deze gegevens naar een Log Analytics-werk ruimte. Meer informatie [over het instellen van Azure monitor-logboeken voor uw logische app](../logic-apps/monitor-logic-apps.md).

* Als u een integratie account gebruikt, moet u ervoor zorgen dat u het account hebt ingesteld met Azure Monitor logboek registratie om die informatie naar een Log Analytics-werk ruimte te verzenden. Meer informatie over het [instellen van Azure monitor logboek registratie voor uw integratie account](../logic-apps/monitor-b2b-messages-log-analytics.md).

## <a name="view-queries-behind-results"></a>Query's achter resultaten weer geven

Voer de volgende stappen uit om de query te bekijken of te bewerken waarmee de resultaten worden gegenereerd in uw werk ruimte-samen vatting:

1. Selecteer op de pagina resultaten de optie **alles weer geven**.

   ![Alle resultaten weer geven](./media/create-monitoring-tracking-queries/logic-app-see-all.png)

   De pagina Logboeken wordt geopend en toont de query achter de vorige resultaten pagina.

   ![Pagina logboeken-query weergave](./media/create-monitoring-tracking-queries/view-query-behind-results.png)

1. Op de pagina **Logboeken** kunt u de volgende opties selecteren:

   * Als u de query resultaten als een tabel wilt weer geven, selecteert u in de query-editor de optie **tabel**.

   * Als u de query wilt wijzigen, werkt u de query reeks bij en selecteert u **uitvoeren** om de resultaten in de tabel weer te geven.

## <a name="create-your-own-query"></a>Uw eigen query maken

Als u resultaten wilt zoeken of filteren op basis van specifieke eigenschappen of waarden, kunt u uw eigen query maken door te beginnen met een lege query of door een bestaande query te gebruiken. Zie [aan de slag met logboek query's in azure monitor](../azure-monitor/logs/get-started-queries.md)voor meer informatie.

1. Zoek in het [Azure Portal](https://portal.azure.com)uw log Analytics-werk ruimte en selecteer deze.

1. Selecteer in het menu werk ruimte onder **Algemeen** de optie **Logboeken**.

1. Beginnen met een lege query of beschik bare bestaande query's.

   * Als u wilt controleren of er bestaande query's beschikbaar zijn, selecteert u op de werk balk van de query de geschiedenis query **query's**  >  , waarin query's van eerdere query-uitvoeringen worden weer gegeven, of selecteer **query Verkenner**, waarin vooraf samengestelde query's worden weer gegeven.

     De Logische apps B2B oplossing biedt bijvoorbeeld deze vooraf opgebouwde query's:

     ![Beginnen met vooraf gemaakte query's voor de oplossing Logische apps B2B](./media/create-monitoring-tracking-queries/b2b-prebuilt-queries.png)

   * Als u een lege query wilt starten, typt u in de query-editor de [Kusto-query taal](/azure/data-explorer/kusto/query/) voor uw query.

     ![Beginnen met lege query](./media/create-monitoring-tracking-queries/create-query-from-blank.png)

## <a name="next-steps"></a>Volgende stappen

* [Volgschema’s voor AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Volgschema’s voor X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Aangepaste tracking schema's](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)