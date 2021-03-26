---
title: Stappen in een blauwdrukimplementatie
description: Meer informatie over de beveiligings-en artefact stappen die door de Azure blauw drukken-services worden uitgevoerd tijdens het maken van een blauw druk-toewijzing.
ms.date: 01/27/2021
ms.topic: conceptual
ms.openlocfilehash: 473f8c7957994401ea6000ecc0d8023a89f8c349
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105560145"
---
# <a name="stages-of-a-blueprint-deployment"></a>Stappen in een blauwdrukimplementatie

Wanneer een blauw druk wordt geïmplementeerd, wordt er een reeks acties uitgevoerd door de service Azure-blauw drukken om de resources te implementeren die zijn gedefinieerd op de blauw druk. In dit artikel vindt u informatie over wat elke stap omvat.

De implementatie van blauw druk wordt geactiveerd door een blauw druk aan een abonnement toe te wijzen of [een bestaande toewijzing](../how-to/update-existing-assignments.md)bij te werken. Tijdens de implementatie neemt Azure blauw drukken de volgende stappen op hoog niveau:

> [!div class="checklist"]
> - Eigendoms rechten verleend aan Azure-blauw drukken
> - Het toewijzings object blauw druk wordt gemaakt
> - Optioneel: Azure-blauw drukken maakt door het **systeem toegewezen** beheerde identiteit
> - De beheerde identiteit implementeert blauw druk artefacten
> - De Azure blauw drukken-service en door **het systeem toegewezen** beheerde identiteits rechten worden ingetrokken

## <a name="azure-blueprints-granted-owner-rights"></a>Eigendoms rechten verleend aan Azure-blauw drukken

De service-principal van Azure Blauwsen krijgt eigendoms rechten voor het toegewezen abonnement of de abonnementen wanneer een door het [systeem toegewezen beheerde](../../../active-directory/managed-identities-azure-resources/overview.md) identiteit wordt gebruikt. Met de verleende rol kunnen Azure-blauw drukken de door het **systeem toegewezen** beheerde identiteit maken en later intrekken. Als u een door de **gebruiker toegewezen** beheerde identiteit gebruikt, wordt de service-principal van Azure blauw niet ontvangen en heeft deze geen eigenaars rechten voor het abonnement.

De rechten worden automatisch verleend als de toewijzing wordt uitgevoerd via de portal. Als de toewijzing echter wordt uitgevoerd via de REST API, moet het verlenen van de rechten worden uitgevoerd met een afzonderlijke API-aanroep. De Azure-Schema's AppId is `f71766dc-90d9-4b7d-bd9d-4499c4331c3f` , maar de Service-Principal is afhankelijk van de Tenant. Gebruik [Azure Active Directory Graph API](/graph/migrate-azure-ad-graph-planning-checklist) en rest endpoint [servicePrincipals](/graph/api/resources/serviceprincipal) om de Service-Principal op te halen. Ken vervolgens het Azure-blauw drukken toe aan de rol van _eigenaar_ via de [Portal](../../../role-based-access-control/role-assignments-portal.md), de [Azure cli](../../../role-based-access-control/role-assignments-cli.md), [Azure PowerShell](../../../role-based-access-control/role-assignments-powershell.md), [rest API](../../../role-based-access-control/role-assignments-rest.md)of een [Azure Resource Manager sjabloon](../../../role-based-access-control/role-assignments-template.md).

De service Azure-blauw drukken biedt geen rechtstreekse implementatie van de resources.

## <a name="the-blueprint-assignment-object-is-created"></a>Het toewijzings object blauw druk wordt gemaakt

Een gebruiker, groep of Service-Principal wijst een blauw druk toe aan een abonnement. Het toewijzings object bestaat op het abonnements niveau waar de blauw druk is toegewezen. Resources die zijn gemaakt door de implementatie, worden niet uitgevoerd in de context van de implementatie-entiteit.

Tijdens het maken van de blauw druk-toewijzing wordt het type [beheerde identiteit](../../../active-directory/managed-identities-azure-resources/overview.md) geselecteerd. De standaard waarde is een door het **systeem toegewezen** beheerde identiteit. Een door de **gebruiker toegewezen** beheerde identiteit kan worden gekozen. Wanneer u een door de **gebruiker toegewezen** beheerde identiteit gebruikt, moet u deze definiëren en machtigingen verlenen voordat de blauw druk-toewijzing wordt gemaakt. Beide ingebouwde rollen van de operator [eigenaar](../../../role-based-access-control/built-in-roles.md#owner) en [blauw](../../../role-based-access-control/built-in-roles.md#blueprint-operator) drukken hebben de benodigde `blueprintAssignment/write` machtiging om een toewijzing te maken die gebruikmaakt van een door de **gebruiker toegewezen** beheerde identiteit.

## <a name="optional---azure-blueprints-creates-system-assigned-managed-identity"></a>Optioneel: Azure-blauw drukken maakt door het systeem toegewezen beheerde identiteit

Wanneer door het [systeem toegewezen beheerde identiteit](../../../active-directory/managed-identities-azure-resources/overview.md) wordt geselecteerd tijdens de toewijzing, wordt de identiteit door Azure blauw drukken gemaakt en wordt de beheerde identiteit verleend aan de rol van [eigenaar](../../../role-based-access-control/built-in-roles.md#owner) . Als een [bestaande toewijzing wordt bijgewerkt](../how-to/update-existing-assignments.md), gebruikt Azure-blauw drukken de eerder gemaakte beheerde identiteit.

De beheerde identiteit van de blauw druk-toewijzing wordt gebruikt om de resources die zijn gedefinieerd op de blauw druk te implementeren of opnieuw te implementeren. Dit ontwerp voor komt dat toewijzingen per ongeluk met elkaar conflicteren.
Dit ontwerp biedt ook ondersteuning voor de functie voor het [vergren delen van resources](./resource-locking.md) door de beveiliging van elke geïmplementeerde resource van de blauw druk te beheren.

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>De beheerde identiteit implementeert blauw druk artefacten

De beheerde identiteit activeert vervolgens de Resource Manager-implementaties van de artefacten in de blauw druk in de gedefinieerde [volg orde voor sequentiëren](./sequencing-order.md). De volg orde kan worden aangepast om ervoor te zorgen dat artefacten die afhankelijk zijn van andere artefacten in de juiste volg orde worden geïmplementeerd.

Een toegangs fout van een implementatie is vaak het resultaat van het toegangs niveau dat aan de beheerde identiteit wordt verleend. De service Azure-blauw drukken beheert de beveiligings levenscyclus van de door het **systeem toegewezen** beheerde identiteit. De gebruiker is echter verantwoordelijk voor het beheer van de rechten en levens cyclus van een door de **gebruiker toegewezen** beheerde identiteit.

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>De service blauw drukken en door het systeem toegewezen beheerde identiteits rechten worden ingetrokken

Zodra de implementaties zijn voltooid, trekt Azure-blauw drukken de rechten van de door het **systeem toegewezen** beheerde identiteit uit het abonnement. Vervolgens trekt de Azure-service voor blauw drukken de rechten van het abonnement. Rechten verwijderen voor komt dat Azure-blauw drukken een permanente eigenaar van een abonnement wordt.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over hoe u [statische en dynamische parameters](./parameters.md) gebruikt.
- Meer informatie over hoe u de [blauwdrukvolgorde](./sequencing-order.md) aanpast.
- Meer informatie over hoe u gebruikmaakt van [resourcevergrendeling in blauwdrukken](./resource-locking.md).
- Meer informatie over hoe u [bestaande toewijzingen bijwerkt](../how-to/update-existing-assignments.md).
- Problemen oplossen tijdens de toewijzing van een blauwdruk met [algemene probleemoplossing](../troubleshoot/general.md).