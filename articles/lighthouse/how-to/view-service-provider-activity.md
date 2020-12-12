---
title: Activiteit van serviceprovider bekijken
description: Klanten kunnen vastgelegde activiteiten weer geven om acties te bekijken die door service providers worden uitgevoerd via het beheer van gedelegeerde resources van Azure.
ms.date: 12/11/2020
ms.topic: how-to
ms.openlocfilehash: dcf177cc41dac846d096607445ff4c3d433620ca
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97356375"
---
# <a name="view-service-provider-activity"></a>Activiteit van serviceprovider bekijken

Klanten met gedelegeerde abonnementen voor [Azure Lighthouse](../overview.md) kunnen [Azure-activiteiten logboek gegevens weer geven](../../azure-monitor/platform/platform-logs-overview.md) om alle uitgevoerde acties weer te geven. Dit biedt klanten volledige zicht baarheid van de activiteiten die service providers uitvoeren via [Azure delegated resource management](../concepts/azure-delegated-resource-management.md), samen met bewerkingen die worden uitgevoerd door gebruikers binnen de eigen Azure Active Directory (Azure AD)-Tenant van de klant.

> [!TIP]
> We bieden ook Azure Policy ingebouwde beleids definities om de [overdracht te beperken tot specifieke beheer tenants](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/AllowCertainManagingTenantIds_Deny.json) en om [de overdracht van scopes naar een beheer Tenant te controleren](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/Lighthouse_Delegations_Audit.json). Zie [audit delegaties in uw omgeving](view-manage-service-providers.md#audit-delegations-in-your-environment)voor meer informatie.

## <a name="view-activity-log-data"></a>Gegevens van activiteitenlogboek weergeven

U kunt [het activiteiten logboek bekijken](../../azure-monitor/platform/activity-log.md#view-the-activity-log) via het menu **Monitor** in de Azure Portal. Als u de resultaten wilt beperken tot een specifiek abonnement, gebruikt u de filters om een specifiek abonnement te selecteren. U kunt [activiteiten logboek gebeurtenissen ook programmatisch weer geven en ophalen](../../azure-monitor/platform/activity-log.md#view-the-activity-log) .

> [!NOTE]
> Gebruikers in de Tenant van een service provider kunnen de resultaten van het activiteiten logboek weer geven voor een gedelegeerd abonnement in een Tenant van de klant als ze de rol van [lezer](../../role-based-access-control/built-in-roles.md#reader) hebben gekregen (of een andere ingebouwde rol die lezers toegang bevat) wanneer dat abonnement is onboardd naar Azure Lighthouse.

In het activiteiten logboek ziet u de naam van de bewerking en de bijbehorende status, samen met de datum en tijd waarop het is uitgevoerd. In de **gebeurtenis gestart door** kolom ziet u welke gebruiker de bewerking heeft uitgevoerd, of het een gebruiker in de Tenant van een service provider is die zich bevindt via Azure Lighthouse of een gebruiker in de eigen Tenant van de klant. Houd er rekening mee dat de naam van de gebruiker wordt weer gegeven in plaats van de Tenant of de rol die aan de gebruiker is toegewezen voor dat abonnement.

Geregistreerde activiteiten is in de afgelopen 90 dagen beschikbaar in de Azure Portal. Zie [Azure-activiteiten Logboeken in log Analytics werk ruimte verzamelen en analyseren](../../azure-monitor/platform/activity-log.md)voor meer informatie over hoe u deze gegevens langer dan 90 dagen opslaat.

> [!NOTE]
> Gebruikers van de service provider worden weer gegeven in het activiteiten logboek, maar deze gebruikers en hun roltoewijzingen worden niet weer gegeven in **Access Control (IAM)** of bij het ophalen van roltoewijzings gegevens via api's.

## <a name="set-alerts-for-critical-operations"></a>Waarschuwingen instellen voor kritieke bewerkingen

We raden u aan [activiteiten logboek waarschuwingen](../../azure-monitor/platform/activity-log-alerts.md)te maken om te zorgen dat de service providers (of gebruikers in uw eigen Tenant) op de hoogte blijven van kritieke bewerkingen. U kunt bijvoorbeeld alle beheer acties voor een abonnement bijhouden of een melding ontvangen wanneer een virtuele machine in een bepaalde resource groep wordt verwijderd. Wanneer u waarschuwingen maakt, bevatten ze acties die worden uitgevoerd door gebruikers in de eigen Tenant van de klant, evenals bij het beheer van tenants.

Zie [waarschuwingen voor activiteiten logboeken maken en beheren](../../azure-monitor/platform/alerts-activity-log.md)voor meer informatie.

## <a name="create-log-queries"></a>Logboek query's maken

U kunt query's maken om uw vastgelegde activiteiten te analyseren of te focussen op specifieke items. Bijvoorbeeld: een audit vereist dat u rapporteert over alle acties op beheer niveau die zijn uitgevoerd op een abonnement. U kunt een query maken om alleen op deze acties te filteren en de resultaten te sorteren op gebruiker, datum of een andere waarde.

Zie [overzicht van logboek query's in azure monitor](../../azure-monitor/log-query/log-query-overview.md)voor meer informatie.

## <a name="view-user-activity-across-domains"></a>Gebruikers activiteit in meerdere domeinen weer geven

U kunt activiteiten van afzonderlijke gebruikers in meerdere domeinen weer geven met behulp van de voorbeeld werkmap [activiteiten logboeken per domein](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain) .

Resultaten kunnen worden gefilterd op domein naam. U kunt ook extra filters toep assen, zoals categorie, niveau of resource groep.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure monitor](../../azure-monitor/index.yml).
- Meer informatie over het [weer geven en beheren van aanbiedingen voor service providers](view-manage-service-providers.md) in de Azure Portal.
