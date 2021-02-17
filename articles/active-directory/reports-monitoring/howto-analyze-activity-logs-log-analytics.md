---
title: Activiteiten logboeken analyseren met Azure Monitor-logboeken | Microsoft Docs
description: Meer informatie over het analyseren van Azure Active Directory activiteiten logboeken met behulp van Azure Monitor-logboeken
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4535ae65-8591-41ba-9a7d-b7f00c574426
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 905261058c2de0afae18cbc5572c64962bef8834
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100580025"
---
# <a name="analyze-azure-ad-activity-logs-with-azure-monitor-logs"></a>Azure AD-activiteiten logboeken analyseren met Azure Monitor-logboeken

Nadat u [Azure AD-activiteiten Logboeken hebt geïntegreerd met Azure monitor-logboeken](howto-integrate-activity-logs-with-log-analytics.md), kunt u de kracht van Azure monitor-Logboeken gebruiken om inzicht te krijgen in uw omgeving. U kunt ook de [log Analytics-weer gaven voor Azure AD-activiteiten logboeken](howto-install-use-log-analytics-views.md) installeren om toegang te krijgen tot vooraf gemaakte rapporten rond controle en aanmeldings gebeurtenissen in uw omgeving.

In dit artikel leert u hoe u de Azure AD-activiteiten Logboeken in uw Log Analytics-werk ruimte kunt analyseren. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Vereisten 

Als u wilt volgen, hebt u het volgende nodig:

* Een Log Analytics-werk ruimte in uw Azure-abonnement. Leer [een Log Analytics-werkruimte maken](../../azure-monitor/logs/quick-create-workspace.md).
* Voer eerst de stappen uit om [de Azure AD-activiteiten logboeken te routeren naar uw log Analytics-werk ruimte](howto-integrate-activity-logs-with-log-analytics.md).
*  [Toegang](../../azure-monitor/logs/manage-access.md#manage-access-using-workspace-permissions) tot de log Analytics-werk ruimte
* De volgende rollen in Azure Active Directory (als u Log Analytics via Azure Active Directory Portal opent)
    - Beveiligingsbeheerder
    - Beveiligingslezer
    - Rapportlezer
    - Globale beheerder
    
## <a name="navigate-to-the-log-analytics-workspace"></a>Ga naar de werk ruimte Log Analytics

1. Meld u aan bij [Azure Portal](https://portal.azure.com). 

2. Selecteer **Azure Active Directory** en selecteer vervolgens **Logboeken** in het gedeelte **bewaking** om uw log Analytics-werk ruimte te openen. De werk ruimte wordt geopend met een standaard query.

    ![Standaard query](./media/howto-analyze-activity-logs-log-analytics/defaultquery.png)


## <a name="view-the-schema-for-azure-ad-activity-logs"></a>Het schema voor Azure AD-activiteiten logboeken weer geven

De logboeken worden gepusht naar de **audit logs bevat** -en **SigninLogs** -tabellen in de werk ruimte. Het schema voor deze tabellen weer geven:

1. Selecteer in de standaard query weergave in de vorige sectie **schema** en vouw de werk ruimte uit. 

2. Vouw de sectie **logboek beheer** uit en vouw vervolgens **audit logs bevat** of **SigninLogs** uit om het logboek schema weer te geven.
    ![Audit logboeken ](./media/howto-analyze-activity-logs-log-analytics/auditlogschema.png) ![ aanmeldings logboeken](./media/howto-analyze-activity-logs-log-analytics/signinlogschema.png)

## <a name="query-the-azure-ad-activity-logs"></a>Query's uitvoeren op de activiteiten logboeken van Azure AD

Nu u de logboeken in uw werk ruimte hebt, kunt u nu query's uitvoeren. Als u bijvoorbeeld de belangrijkste toepassingen wilt ophalen die in de afgelopen week worden gebruikt, vervangt u de standaard query door het volgende en selecteert u **uitvoeren**

```
SigninLogs 
| where CreatedDateTime >= ago(7d)
| summarize signInCount = count() by AppDisplayName 
| sort by signInCount desc 
```

Als u de bovenste controle gebeurtenissen in de afgelopen week wilt ophalen, gebruikt u de volgende query:

```
AuditLogs 
| where TimeGenerated >= ago(7d)
| summarize auditCount = count() by OperationName 
| sort by auditCount desc 
```
## <a name="alert-on-azure-ad-activity-log-data"></a>Waarschuwing voor Azure AD-activiteiten logboek gegevens

U kunt ook waarschuwingen instellen voor uw query. Als u bijvoorbeeld een waarschuwing wilt configureren wanneer er meer dan 10 toepassingen in de afgelopen week zijn gebruikt:

1. Selecteer in de werk ruimte de optie **waarschuwing instellen** om de pagina **regel maken** te openen.

    ![Waarschuwing instellen](./media/howto-analyze-activity-logs-log-analytics/setalert.png)

2. Selecteer de standaard **waarschuwings criteria** die in de waarschuwing zijn gemaakt en werk de **drempel** waarde in de standaard waarde in op 10.

    ![Waarschuwings criteria](./media/howto-analyze-activity-logs-log-analytics/alertcriteria.png)

3. Voer een naam en beschrijving in voor de waarschuwing en kies het Ernst niveau. In ons voor beeld kan het worden ingesteld op **informatief**.

4. Selecteer de **actie groep** die wordt gewaarschuwd wanneer het signaal optreedt. U kunt ervoor kiezen om uw team op de hoogte te stellen van een e-mail of SMS-bericht of u zou de actie kunnen automatiseren met webhooks, Azure functions of Logic apps. Meer informatie over [het maken en beheren van waarschuwings groepen vindt u in de Azure Portal](../../azure-monitor/alerts/action-groups.md).

5. Zodra u de waarschuwing hebt geconfigureerd, selecteert u **waarschuwing maken** om deze in te scha kelen. 

## <a name="use-pre-built-workbooks-for-azure-ad-activity-logs"></a>Vooraf gemaakte werkmappen gebruiken voor Azure AD-activiteiten logboeken

De werkmappen bieden verschillende rapporten die betrekking hebben op algemene scenario's met betrekking tot het controleren, aanmelden en inrichten van gebeurtenissen. U kunt ook een waarschuwing ontvangen voor de gegevens in de rapporten, met behulp van de stappen die in de vorige sectie zijn beschreven.

* **Inrichtings analyse**: deze [werkmap](../app-provisioning/application-provisioning-log-analytics.md) bevat rapporten met betrekking tot het controleren van inrichtings activiteiten, zoals het aantal nieuwe gebruikers dat is ingericht en het inrichten van fouten, het aantal gebruikers dat is bijgewerkt en de mislukte update en het aantal gebruikers dat de inrichting heeft en de bijbehorende fouten.    
* **Gebeurtenissen voor aanmeldingen**: deze werkmap bevat de meest relevante rapporten met betrekking tot het controleren van de aanmeldings activiteit, zoals aanmeldingen per toepassing, gebruiker, apparaat en een samen vatting van het aantal aanmeldingen in de loop van de tijd.
* **Inzichten** op basis van voorwaardelijke toegang: met de Insights- [werkmap](../conditional-access/howto-conditional-access-insights-reporting.md) voor voorwaardelijke toegang kunt u de gevolgen van het beleid voor voorwaardelijke toegang in uw organisatie in de loop van de tijd begrijpen. 

## <a name="next-steps"></a>Volgende stappen

* [Aan de slag met query's in Azure Monitor-logboeken](../../azure-monitor/logs/get-started-queries.md)
* [Waarschuwings groepen maken en beheren in de Azure Portal](../../azure-monitor/alerts/action-groups.md)
* [De log Analytics-weer gaven voor Azure Active Directory installeren en gebruiken](howto-install-use-log-analytics-views.md)