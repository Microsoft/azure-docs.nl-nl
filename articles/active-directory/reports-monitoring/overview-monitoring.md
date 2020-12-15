---
title: Wat is bewaking van Azure Active Directory? | Microsoft Docs
description: Biedt een algemeen overzicht van Azure Active Directory-bewaking.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: e2b3d8ce-708a-46e4-b474-123792f35526
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 427cf2614f81a086dcb174db06cd636df4876c7e
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96778492"
---
# <a name="what-is-azure-active-directory-monitoring"></a>Wat is bewaking van Azure Active Directory?

Met bewaking van Azure Active Directory (Azure AD), kunt u nu uw Azure AD-activiteitenlogboeken naar verschillende eindpunten routeren. U kunt deze vervolgens behouden voor later gebruik of integreren met SIEM-hulpprogramma's (Security Information and Event Management) van derden om meer inzicht in uw omgeving te verkrijgen.

Op dit moment kunt u de logboeken routeren naar:

- Een Azure Storage-account.
- Een Azure Event Hub, zodat u deze kunt integreren met uw Splunk en Sumologic-exemplaren.
- Azure Log Analytics-werkruimte, waarin u de gegevens kunt analyseren en een dashboard en waarschuwingen voor specifieke gebeurtenissen kunt maken

**Vereiste rol**: Hoofdbeheerder

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="licensing-and-prerequisites-for-azure-ad-reporting-and-monitoring"></a>Licenties en vereisten voor Azure AD-rapportage en-bewaking

U hebt een premium-licentie van Azure AD nodig om toegang te krijgen tot de logboeken voor Azure AD-aanmelding.

Gedetailleerde informatie over functies en licenties vindt u in de [prijsgids voor Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

Als u Azure AD-bewaking en-rapportage wilt implementeren, hebt u een gebruiker nodig met de rol van globale beheerder of beveiligingsbeheerder voor de Azure AD-tenant.

Afhankelijk van de uiteindelijke bestemming van uw logboekgegevens, hebt u een van de volgende opties nodig:

* Een Azure-opslagaccount waarop u ListKeys-machtigingen hebt. We raden u aan om een algemeen opslagaccount te gebruiken en geen Blob Storage-account. Zie de [Prijscalculator voor Azure Storage](https://azure.microsoft.com/pricing/calculator/?service=storage) voor prijsinformatie over opslag.

* Een Azure Event Hubs-naamruimte om te integreren met SIEM-oplossingen van derden.

* Een Azure Log Analytics-werkruimte om logboeken naar Azure Monitor-logboeken te verzenden.

## <a name="diagnostic-settings-configuration"></a>Configuratie voor diagnostische instellingen

Om bewakingsinstellingen voor Azure AD-activiteitenlogboeken te configureren, meldt u zich aan bij de [Azure-portal](https://portal.azure.com) en selecteert u **Azure Active Directory**. Vervolgens kunt u de pagina voor het configureren van de diagnostische instellingen op twee manieren openen:

* Selecteer **Diagnostische instellingen** in de sectie **Bewaking**.

    ![Diagnostische instellingen](./media/overview-monitoring/diagnostic-settings.png)
    
* Selecteer **Auditlogboeken** of **Aanmeldingen** en selecteer vervolgens **Exportinstellingen**. 

    ![Exportinstellingen](./media/overview-monitoring/export-settings.png)


## <a name="route-logs-to-storage-account"></a>Logboeken naar opslagaccount doorsturen

Door logboeken te routeren naar een Azure Storage-account, kunt u deze langer bewaren dan de standaardbewaartermijn die wordt beschreven in ons [bewaarbeleid](reference-reports-data-retention.md). Ontdek hoe u [gegevens routeert naar uw opslagaccount](quickstart-azure-monitor-route-logs-to-storage-account.md).

## <a name="stream-logs-to-event-hub"></a>Logboeken naar Event Hub streamen

Het routeren van logboeken naar een Azure Event Hub biedt u de mogelijkheid deze te integreren met SIEM-hulpprogramma's van derden, zoals Sumologic en Splunk. Zo kunt u gegevens van Azure Active Directory-activiteitenlogboeken combineren met andere gegevens die worden beheerd door uw SIEM en meer inzicht krijgen in uw omgeving. Ontdek hoe u [logboeken streamt naar een Event Hub](tutorial-azure-monitor-stream-logs-to-event-hub.md).

## <a name="send-logs-to-azure-monitor-logs"></a>Logboeken verzenden naar logboeken van Azure Monitor

[Logboeken van Azure Monitor](../../azure-monitor/log-query/log-query-overview.md) vormen een oplossing waarin bewakingsgegevens uit verschillende bronnen worden samengevoegd. Met de bijbehorende querytaal en analyse-engine krijgt u meer inzicht in de werking van uw toepassingen en resources. Door Azure AD-activiteitenlogboeken naar logboeken van Azure Monitor te verzenden, kunt u verzamelde gegevens snel ophalen en controleren en eventuele waarschuwingen bekijken. Lees meer over [het verzenden van gegevens naar de logboeken van Azure Monitor](howto-integrate-activity-logs-with-log-analytics.md).

U kunt ook de vooraf gemaakte weergaven voor Azure AD-activiteitenlogboeken installeren om veelvoorkomende scenario's met betrekking tot aanmeldingen en controlegebeurtenissen te bewaken. Ontdek hoe u [Log Analytics-weergaven voor activiteitenlogboeken van Azure AD installeert en gebruikt](howto-install-use-log-analytics-views.md).

## <a name="next-steps"></a>Volgende stappen

* [Activiteitenlogboeken in Azure Monitor](concept-activity-logs-azure-monitor.md)
* [Logboeken naar Event Hub streamen](tutorial-azure-monitor-stream-logs-to-event-hub.md)
* [Logboeken verzenden naar logboeken van Azure Monitor](howto-integrate-activity-logs-with-log-analytics.md)
