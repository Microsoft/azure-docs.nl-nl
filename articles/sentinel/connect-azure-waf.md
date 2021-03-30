---
title: Azure Web Application firewall-gegevens (WAF) verbinden met Azure Sentinel
description: Meer informatie over het verbinden van Azure WAF-gegevens met Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: bfa2eca4-abdc-49ce-b11a-0ee229770cdd
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 05/07/2020
ms.author: yelevin
ms.openlocfilehash: c554f3582e67622a5a1739c9e410328c902d491b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94655898"
---
# <a name="connect-data-from-azure-web-application-firewall-waf"></a>Gegevens verbinden vanuit Azure Web Application firewall (WAF)

Webtoepassingen zijn in toenemende mate het doel van aanvallen die gebruikmaken van algemeen bekende beveiligingsproblemen. Azure Web Application firewall (WAF) biedt gecentraliseerde beveiliging van uw webtoepassingen tegen veelvoorkomende aanvallen en bedreigingen, zoals code injectie en cross-site scripting. Azure WAF kan worden geïmplementeerd op de [Azure-toepassing gateway](../web-application-firewall/ag/ag-overview.md) service, de [Azure front-deur](../web-application-firewall/afds/afds-overview.md) service en via een [Azure Content Delivery Network (CDN)](../web-application-firewall/cdn/cdn-overview.md) WAF-beleid (dit is momenteel beschikbaar in de open bare preview).
U kunt Azure WAF-logboeken verbinden met Azure Sentinel, zodat u de logboek gegevens in een werkmap kan bekijken, kunt gebruiken om aangepaste waarschuwingen te maken en deze op te nemen om uw onderzoek te verbeteren.

## <a name="prerequisites"></a>Vereisten

- U moet lees-en schrijf machtigingen hebben voor de Azure Sentinel-werk ruimte.

## <a name="connect-to-azure-waf"></a>Verbinding maken met Azure WAF

### <a name="instructions-tab"></a>Tabblad instructies

1. Selecteer in het navigatie menu van de Azure-Sentinel **Data connectors**.

1. Selecteer **Azure Web Application firewall (WAF)** in de galerie met gegevens connectors en selecteer vervolgens **connector pagina openen** in het voorbeeld venster.

1. Selecteer de koppeling voor het type WAF-resource waarvan u de logboeken die u wilt verbinden wilt **openen: open Application Gateway resource >**, open de WAF van de **front-deur resource >** of open het beleid voor het **openen van Content Delivery Network (CDN) >** , en klik in het scherm van de resource lijst op een WAF-resource in de lijst.

    1. Selecteer in het navigatie menu van de resource WAF **Diagnostische instellingen**.

    1. Selecteer **+ Diagnostische instelling toevoegen** onder aan de lijst.

    1. Typ in het scherm **Diagnostische instellingen** een naam in het veld **naam Diagnostische instellingen** .

    1. Klik op het selectie vakje **verzenden naar log Analytics** . Er worden twee nieuwe velden weer gegeven. Kies het relevante **abonnement** en de **log Analytics werk ruimte** (waar Azure Sentinel zich bevindt).

    1. Schakel de selectie vakjes in van de regel typen waarvan u de logboeken wilt opnemen. Onze aanbevelingen voor elk resource type:

        | Resource | Logboeken om te selecteren voor opname |
        |----------|------------------------------|
        | Application Gateway | ApplicationGatewayAccessLog<br>ApplicationGatewayFirewallLog |
        | Front Door          | FrontdoorAccessLog<br>FrontdoorWebApplicationFirewallLog |
        | CDN WAF-beleid      | WebApplicationFirewallLogs |
        |

    1. Selecteer **Opslaan**.

### <a name="next-steps-tab"></a>Tabblad volgende stappen

- Zie de aanbevolen werkmappen, query voorbeelden en analyse regel sjablonen die zijn gebundeld met de **azure Web Application firewall** Data Connector, om inzicht te krijgen in uw Azure WAF-logboek gegevens.

- Als u een query wilt uitvoeren voor Azure WAF-gegevens in **Logboeken**, typt u **AzureDiagnostics** in het query venster.

> [!NOTE]
>
> Met deze specifieke gegevens connector wordt de verbindings status indicator (een kleur balk in de galerie met gegevens connectors en verbindings pictogrammen naast de namen van gegevens typen) alleen weer gegeven als *verbonden* (groen) als de gegevens op een bepaald moment in de afgelopen twee weken zijn opgenomen. Als er twee weken zijn geslaagd zonder opname van gegevens, wordt de connector weer gegeven als niet-verbonden. Op het moment dat er meer gegevens worden opgehaald, wordt de status *verbonden* geretourneerd.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Azure WAF-Logboeken kunt verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).