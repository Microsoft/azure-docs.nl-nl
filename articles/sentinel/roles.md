---
title: Machtigingen in azure Sentinel | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe Azure Sentinel gebruikmaakt van toegangs beheer op basis van rollen om machtigingen toe te wijzen aan gebruikers en de toegestane acties voor elke rol te identificeren.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/11/2021
ms.author: yelevin
ms.openlocfilehash: b64adbb63efaa4ce4781474f732bc9509d51029e
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310322"
---
# <a name="permissions-in-azure-sentinel"></a>Machtigingen in Azure Sentinel

Azure Sentinel maakt gebruik [van Azure op rollen gebaseerd toegangs beheer (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) om [ingebouwde rollen](../role-based-access-control/built-in-roles.md) te bieden die kunnen worden toegewezen aan gebruikers, groepen en services in Azure.

Gebruik Azure RBAC om rollen binnen uw beveiligings team te maken en toe te wijzen om de juiste toegang tot Azure Sentinel te verlenen. De verschillende rollen bieden een nauw keurige controle over wat gebruikers van Azure Sentinel kunnen zien en doen. Azure-rollen kunnen rechtstreeks worden toegewezen in de Azure Sentinel-werk ruimte (Zie de opmerking hieronder) of in een abonnement of resource groep waartoe de werk ruimte behoort, die door Azure Sentinel wordt overgenomen.

## <a name="roles-for-working-in-azure-sentinel"></a>Rollen voor het werken in azure-Sentinel

### <a name="azure-sentinel-specific-roles"></a>Specifieke Azure Sentinel-rollen

**Alle ingebouwde Azure Sentinel-rollen verlenen Lees toegang tot de gegevens in uw Azure Sentinel-werk ruimte.**

- De [Azure Sentinel-lezer](../role-based-access-control/built-in-roles.md#azure-sentinel-reader) kan gegevens, incidenten, werkmappen en andere Azure Sentinel-resources bekijken.

- De [Azure Sentinel-beantwoorder](../role-based-access-control/built-in-roles.md#azure-sentinel-responder) kan, naast het bovenstaande, ook incidenten beheren (toewijzen, verwijderen, enzovoort)

- De [Azure Sentinel-inzender](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor) kan, naast het bovenstaande, werkmappen, analyseregels en overige Azure Sentinel-resources maken en bewerken.

- [Azure Sentinel Automation-bijdrager](../role-based-access-control/built-in-roles.md#azure-sentinel-contributor) maakt Azure Sentinel toe om playbooks toe te voegen aan Automation-regels. Het is niet bedoeld voor gebruikers accounts.

> [!NOTE]
>
> - Voor de beste resultaten moeten deze rollen worden toegewezen aan de **resource groep** die de Azure Sentinel-werk ruimte bevat. Op deze manier worden de rollen toegepast op alle resources die zijn geïmplementeerd ter ondersteuning van Azure Sentinel, omdat deze resources ook in dezelfde resource groep moeten worden geplaatst.
>
> - Een andere mogelijkheid is om de rollen rechtstreeks toe te wijzen aan de Azure Sentinel- **werk ruimte** zelf. Als u dit doet, moet u ook dezelfde rollen toewijzen aan de resource van de SecurityInsights- **oplossing** in die werk ruimte. Mogelijk moet u ze ook toewijzen aan andere resources, en moet u voortdurend roltoewijzingen voor resources beheren.

### <a name="additional-roles-and-permissions"></a>Aanvullende rollen en machtigingen

Gebruikers met bepaalde taak vereisten moeten mogelijk aanvullende rollen of specifieke machtigingen toewijzen om hun taken uit te voeren.

- **Werken met playbooks om reacties op bedreigingen te automatiseren**

    Azure Sentinel maakt gebruik van **playbooks** voor automatische reactie op bedreigingen. Playbooks zijn gebaseerd op **Azure Logic apps** en zijn een afzonderlijke Azure-resource. U kunt het beste aan specifieke leden van uw beveiligings team toewijzen om Logic Apps te gebruiken voor via-bewerkingen (Security Orchestration, Automation en Response). U kunt de rol [Inzender voor logische apps](../role-based-access-control/built-in-roles.md#logic-app-contributor) gebruiken om expliciete machtigingen toe te wijzen voor het gebruik van playbooks.

- **Gegevens bronnen verbinden met Azure Sentinel**

    Als een gebruiker **gegevensconnectors** wilt toevoegen, moet u de schrijf machtigingen van de gebruiker toewijzen aan de Azure Sentinel-werk ruimte. Let ook op de vereiste extra machtigingen voor elke connector, zoals vermeld op de relevante connector pagina.

- **Gast gebruikers die incidenten toewijzen**

    Als een gast gebruiker incidenten moet kunnen toewijzen, moet aan de gebruiker naast de rol van Azure Sentinel responder ook de rol van [Directory Reader](../active-directory/roles/permissions-reference.md#directory-readers)worden toegewezen. Houd er rekening mee dat deze rol *geen* Azure-rol, maar een **Azure Active Directory** rol, en dat normale (niet-gast) gebruikers standaard deze rol kunnen toewijzen.

- **Werkmappen maken en verwijderen**

    Als u een Azure Sentinel-werkmap wilt maken en verwijderen, moet de gebruiker ook worden toegewezen met de Azure Monitor rol van [bewakings bijdrager](../role-based-access-control/built-in-roles.md#monitoring-contributor). Deze rol is niet nodig voor het *gebruik* van werkmappen, maar alleen voor het maken en verwijderen van.

### <a name="other-roles-you-might-see-assigned"></a>Andere functies die u kunt zien, zijn toegewezen

Bij het toewijzen van Azure Sentinel-specifieke Azure-rollen hebt u mogelijk een ander Azure-en Log Analytics Azure-rollen die mogelijk aan gebruikers zijn toegewezen voor andere doel einden. Houd er rekening mee dat deze rollen een bredere set machtigingen verlenen die toegang hebben tot uw Azure Sentinel-werk ruimte en andere resources:

- **Azure-rollen:** [eigenaar](../role-based-access-control/built-in-roles.md#owner), [bijdrager](../role-based-access-control/built-in-roles.md#contributor)en [lezer](../role-based-access-control/built-in-roles.md#reader). Azure-rollen verlenen toegang tot alle Azure-resources, waaronder Log Analytics-werk ruimten en Azure-Sentinel-resources.

- **Log Analytics rollen:** [Log Analytics Inzender](../role-based-access-control/built-in-roles.md#log-analytics-contributor) en [log Analytics lezer](../role-based-access-control/built-in-roles.md#log-analytics-reader). Log Analytics rollen verlenen toegang tot uw Log Analytics-werk ruimten.

Bijvoorbeeld, een gebruiker aan wie de rol van **Azure Sentinel Reader** is toegewezen, maar niet de rol **Azure Sentinel contributor** , kan nog steeds items in azure Sentinel bewerken als de rol **Inzender** op Azure-niveau is toegewezen. Als u daarom machtigingen voor een gebruiker alleen in azure Sentinel wilt verlenen, moet u de vorige machtigingen van deze gebruiker zorgvuldig verwijderen en ervoor zorgen dat u geen toegang hebt tot een andere bron.

## <a name="azure-sentinel-roles-and-allowed-actions"></a>Azure Sentinel-rollen en toegestane acties

De volgende tabel bevat een overzicht van de Azure Sentinel-rollen en de toegestane acties in azure Sentinel.

| Rol | Playbooks maken en uitvoeren| Analytische regels en andere Azure-Sentinel-resources maken en bewerken [*](#workbooks) | Incidenten beheren (sluiten, toewijzen, enz.) | Gegevens, incidenten, werkmappen en andere Azure Sentinel-bronnen weer geven |
|---|---|---|---|---|
| Azure Sentinel Reader | -- | -- | -- | &#10003; |
| Azure Sentinel Responder | -- | -- | &#10003; | &#10003; |
| Azure Sentinel Contributor | -- | &#10003; | &#10003; | &#10003; |
| Inzender voor Azure Sentinel contributor + Logic apps | &#10003; | &#10003; | &#10003; | &#10003; |
| | | | | |

<a name=workbooks></a>* Voor het maken en verwijderen van werkmappen is de rol extra [bewakings bijdrage](../role-based-access-control/built-in-roles.md#monitoring-contributor) vereist. Zie [aanvullende rollen en machtigingen](#additional-roles-and-permissions)voor meer informatie.
## <a name="custom-roles-and-advanced-azure-rbac"></a>Aangepaste rollen en geavanceerde Azure RBAC

- **Aangepaste rollen**. Naast of in plaats van met behulp van ingebouwde rollen van Azure, kunt u aangepaste Azure-rollen maken voor Azure Sentinel. Aangepaste Azure-rollen voor Azure Sentinel worden op dezelfde manier gemaakt als bij het maken van andere [aangepaste rollen van Azure](../role-based-access-control/custom-roles-rest.md#create-a-custom-role), op basis van [specifieke machtigingen voor Azure Sentinel](../role-based-access-control/resource-provider-operations.md#microsoftsecurityinsights) en tot [Azure log Analytics-resources](../role-based-access-control/resource-provider-operations.md#microsoftoperationalinsights).

- **Log Analytics RBAC**. U kunt de Log Analytics geavanceerde op rollen gebaseerd toegangs beheer van Azure gebruiken voor de gegevens in uw Azure Sentinel-werk ruimte. Dit omvat zowel Azure RBAC als op gegevens type gebaseerd Azure RBAC. Zie voor meer informatie:

    - [Logboek gegevens en-werk ruimten in Azure Monitor beheren](../azure-monitor/logs/manage-access.md#manage-access-using-workspace-permissions)

    - [Resource-context RBAC voor Azure Sentinel](resource-context-rbac.md)
    - [RBAC op tabelniveau](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043)

    Resource-context en RBAC op tabel niveau zijn twee methoden om toegang te bieden tot specifieke gegevens in uw Azure Sentinel-werk ruimte zonder toegang te verlenen tot de volledige Azure-Sentinel-ervaring.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u kunt werken met rollen voor Azure Sentinel-gebruikers en wat elke rol gebruikers kan doen.

Zoek blog berichten over de beveiliging en naleving van Azure op de [Azure-Sentinel-blog](https://aka.ms/azuresentinelblog).
