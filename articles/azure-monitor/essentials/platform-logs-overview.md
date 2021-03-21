---
title: Overzicht van Azure-platform logboeken | Microsoft Docs
description: Overzicht van Logboeken in Azure Monitor die uitgebreide, frequente gegevens bieden over de werking van een Azure-resource.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: bwren
ms.openlocfilehash: 3300b0587cbb6c2193b56c9152af0cb11ea51936
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033296"
---
# <a name="overview-of-azure-platform-logs"></a>Overzicht van Azure-platformlogboeken
Platform logboeken bieden gedetailleerde informatie over diagnostische gegevens en controle voor Azure-resources en het Azure-platform waarvan ze afhankelijk zijn. Ze worden automatisch gegenereerd, maar u moet bepaalde platform logboeken configureren om te worden doorgestuurd naar een of meer doelen die moeten worden bewaard. Dit artikel bevat een overzicht van de platform logboeken, inclusief welke informatie ze bieden en hoe u deze kunt configureren voor het verzamelen en analyseren van gegevens.

## <a name="types-of-platform-logs"></a>Typen platform logboeken
De volgende tabel bevat de specifieke platform logboeken die beschikbaar zijn op verschillende lagen van Azure.

| Logboek | Laag | Beschrijving |
|:---|:---|:---|
| [Resourcelogboeken](./resource-logs.md) | Azure-bronnen | Verstrek inzicht in de bewerkingen die zijn uitgevoerd in een Azure-resource (het *gegevens vlak*), bijvoorbeeld om een geheim te verkrijgen van een Key Vault of een aanvraag naar een Data Base te maken. De inhoud van bron Logboeken is afhankelijk van de Azure-service en het resource type.<br><br>*In de bron logboeken werden eerder Diagnostische logboeken genoemd.*  |
| [Activiteitenlogboek](../essentials/activity-log.md) | Azure-abonnement | Biedt inzicht in de bewerkingen op elke Azure-resource in het abonnement van buiten (*het beheer vlak*) naast updates voor service Health gebeurtenissen. Gebruik het activiteiten logboek om te bepalen _wat_, _wie_ en _Wanneer_ voor schrijf bewerkingen (put, post, Delete) die zijn uitgevoerd op de resources in uw abonnement. Er is één activiteiten logboek voor elk Azure-abonnement. |
| [Azure Active Directory-logboeken](../../active-directory/reports-monitoring/overview-reports.md) | Azure-tenant |  Bevat de geschiedenis van de aanmeldings activiteit en de audittrail van wijzigingen die zijn aangebracht in de Azure Active Directory voor een bepaalde Tenant.   |

> [!NOTE]
> Het Azure-activiteiten logboek is voornamelijk bedoeld voor activiteiten die zich in Azure Resource Manager voordoen. Er worden geen resources bijgehouden met het klassieke/RDFE-model. Sommige klassieke resource typen hebben een proxy resource provider in Azure Resource Manager (bijvoorbeeld micro soft. ClassicCompute). Als u met een klassiek resource type communiceert via Azure Resource Manager met behulp van deze proxy resource providers, worden de bewerkingen weer gegeven in het activiteiten logboek. Als u communiceert met een klassiek resource type buiten de Azure Resource Manager-proxy's, worden uw acties alleen vastgelegd in het bewerkings logboek. Het bewerkings logboek kan worden doorzocht in een afzonderlijk gedeelte van de portal.

![Overzicht van platformlogboeken](media/platform-logs-overview/logs-overview.png)




## <a name="viewing-platform-logs"></a>Platform logboeken weer geven
Er zijn verschillende opties voor het weer geven en analyseren van de verschillende logboeken van het Azure-platform.

- Bekijk het activiteiten logboek in de Azure Portal en open gebeurtenissen vanuit Power shell en CLI. Zie [het activiteiten logboek weer geven](../essentials/activity-log.md#view-the-activity-log) voor meer informatie. 
- Azure Active Directory beveiligings-en activiteiten rapporten weer geven in de Azure Portal. Zie [Wat zijn Azure Active Directory-rapporten?](../../active-directory/reports-monitoring/overview-reports.md)  voor meer informatie.
- Resource logboeken worden automatisch gegenereerd door ondersteunde Azure-resources, maar ze kunnen niet worden weer gegeven, tenzij u ze naar een [bestemming](#destinations)verzendt. 

## <a name="destinations"></a>Bestemmingen
U kunt platform logboeken verzenden naar een of meer van de doelen in de volgende tabel, afhankelijk van uw bewakings vereisten. Configureer doelen voor platform logboeken door [een diagnostische instelling te maken](../essentials/diagnostic-settings.md).

| Doel | Beschrijving |
|:---|:---|
| Log Analytics-werkruimte | Analyseer de logboeken van al uw Azure-resources samen en profiteer van alle functies die beschikbaar zijn voor [Azure monitor logboeken](../logs/data-platform-logs.md) , inclusief [logboek query's](../logs/log-query-overview.md) en [logboek waarschuwingen](../alerts/alerts-log.md). De resultaten van een logboek query vastmaken aan een Azure-dash board of opnemen in een werkmap als onderdeel van een interactief rapport. |  |
| Event Hub | Verzend gegevens van het platform logboek buiten Azure, bijvoorbeeld naar een SIEM of aangepast telemetrie-platform van derden.
| Azure Storage | Archiveer de logboeken voor controle of back-up. |

- Zie voor meer informatie over het maken van een diagnostische instelling voor activiteiten logboek of bron logboeken [Diagnostische instellingen maken om platform logboeken en metrische gegevens naar verschillende bestemmingen te verzenden](../essentials/diagnostic-settings.md). 
- Raadpleeg de volgende artikelen voor meer informatie over het maken van een diagnostische instelling voor Azure Active Directory Logboeken.
  - [Azure AD-logboeken integreren met Azure Monitor-logboeken](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)
  - [Zelfstudie: Azure Active Directory-logboeken streamen naar een Azure Event Hub](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)
  - [Zelfstudie: Azure AD-logboeken archiveren in een Azure-opslagaccount](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)



## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het activiteiten logboek lezen](../essentials/activity-log.md)
* [Meer informatie over resource logboeken](./resource-logs.md)