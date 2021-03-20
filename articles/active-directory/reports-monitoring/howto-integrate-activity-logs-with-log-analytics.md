---
title: Azure Active Directory logboeken streamen naar Azure Monitor-logboeken | Microsoft Docs
description: Meer informatie over het integreren van Azure Active Directory-logboeken met Azure Monitor-logboeken
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
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
ms.openlocfilehash: 0f328ed44252f7fb314552d6d05df9806f59d972
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100591126"
---
# <a name="integrate-azure-ad-logs-with-azure-monitor-logs"></a>Azure AD-logboeken integreren met Azure Monitor-logboeken

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Met Azure Monitor-Logboeken kunt u gegevens opvragen om bepaalde gebeurtenissen te zoeken, trends te analyseren en de correlatie tussen verschillende gegevens bronnen uit te voeren. Met de integratie van Azure AD-activiteiten Logboeken in Azure Monitor-Logboeken kunt u nu taken uitvoeren zoals:

 * Uw aanmeldings logboeken voor Azure AD vergelijken op basis van de beveiligings logboeken die zijn gepubliceerd door Azure Security Center

 * Los de prestatie knelpunten op de aanmeldings pagina van uw toepassing op door de prestatie gegevens van de toepassing te correleren van Azure-toepassing Insights.  

In de volgende video van een Ignite-sessie ziet u de voor delen van het gebruik van Azure Monitor logboeken voor Azure AD-Logboeken in praktische gebruikers scenario's.

> [!VIDEO https://www.youtube.com/embed/MP5IaCTwkQg?start=1894]

In dit artikel leert u hoe u Azure Active Directory (Azure AD)-logboeken integreert met Azure Monitor.

## <a name="supported-reports"></a>Ondersteunde rapporten

U kunt Logboeken voor controle activiteiten en logboeken voor de registratie van activiteiten naar Azure Monitor logboeken voor verdere analyse routeren. 

* **Auditlogboeken**: het [activiteitenrapport voor auditlogboeken](concept-audit-logs.md) biedt u toegang tot de geschiedenis van elke taak die in uw tenant is uitgevoerd.
* **Aanmeldingslogboeken**: met het [activiteitenrapport voor aanmeldingen](concept-sign-ins.md) kunt u bepalen wie de taken heeft uitgevoerd die in het auditlogboek zijn gerapporteerd.
* **Inrichtings logboeken**: met de [inrichtings logboeken](../app-provisioning/application-provisioning-log-analytics.md)kunt u controleren welke gebruikers zijn gemaakt, bijgewerkt en verwijderd in al uw toepassingen van derden. 

> [!NOTE]
> Auditlogboeken en aanmeldingslogboeken met betrekking tot B2C worden momenteel niet ondersteund.
>

## <a name="prerequisites"></a>Vereisten 

U hebt het volgende nodig om deze functie te gebruiken:

* Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, kunt u zich registreren voor een [gratis proefversie](https://azure.microsoft.com/free/).
* Een Azure AD-tenant.
* Een gebruiker die een *globale beheerder* of *beveiligingsbeheerder* voor de Azure-tenant is.
* Een Log Analytics-werk ruimte in uw Azure-abonnement. Leer [een Log Analytics-werkruimte maken](../../azure-monitor/logs/quick-create-workspace.md).

## <a name="licensing-requirements"></a>Licentievereisten

Voor het gebruik van deze functie is een Azure AD Premium P1-of P2-licentie vereist. Zie [Algemeen beschikbare functies van de edities Gratis, Basic en Premium vergelijken](https://azure.microsoft.com/pricing/details/active-directory/) als u een licentie zoekt die bij uw vereisten past.

## <a name="send-logs-to-azure-monitor"></a>Logboeken naar Azure Monitor verzenden

1. Meld u aan bij [Azure Portal](https://portal.azure.com). 

2. Selecteer   >  Diagnostische **instellingen Azure Active Directory diagnostische**  ->  **instelling toevoegen**. U kunt ook **export instellingen** selecteren op de pagina **controle logboeken** of **aanmeldingen** om naar de configuratie pagina Diagnostische instellingen te gaan.  
    
3. Schakel in het menu **Diagnostische instellingen** het selectie vakje **verzenden naar log Analytics werk ruimte** in en selecteer vervolgens **configureren**.

4. Selecteer de Log Analytics werk ruimte waarnaar u de logboeken wilt verzenden of maak een nieuwe werk ruimte in het dialoog venster dat wordt gegeven.  

5. Voer een van de volgende bewerkingen uit, of beide:
    * Als u audit logboeken wilt verzenden naar de Log Analytics-werk ruimte, schakelt u het selectie vakje **audit logs bevat** in. 
    * Als u aanmeldings logboeken naar de Log Analytics-werk ruimte wilt verzenden, schakelt u het selectie vakje **SignInLogs** in.

6. Selecteer **Opslaan** om de instelling op te slaan.

    ![Diagnostische instellingen](./media/howto-integrate-activity-logs-with-log-analytics/Configure.png)

7. Controleer na ongeveer 15 minuten of gebeurtenissen worden gestreamd naar uw Log Analytics-werk ruimte.

## <a name="next-steps"></a>Volgende stappen

* [Azure AD-activiteiten logboeken analyseren met Azure Monitor-logboeken](howto-analyze-activity-logs-log-analytics.md)
* [De log Analytics-weer gaven voor Azure Active Directory installeren en gebruiken](howto-install-use-log-analytics-views.md)