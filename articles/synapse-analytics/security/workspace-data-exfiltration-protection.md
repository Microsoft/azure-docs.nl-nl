---
title: Data exfiltration Protection voor Azure Synapse Analytics-werk ruimten
description: In dit artikel wordt de beveiliging van gegevens exfiltration in azure Synapse Analytics uitgelegd
author: NanditaV
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/01/2020
ms.author: NanditaV
ms.reviewer: jrasnick
ms.openlocfilehash: 71210cdcc2b3758a59a1b41816e6468556e94808
ms.sourcegitcommit: 84e3db454ad2bccf529dabba518558bd28e2a4e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96518257"
---
# <a name="data-exfiltration-protection-for-azure-synapse-analytics-workspaces"></a>Data exfiltration Protection voor Azure Synapse Analytics-werk ruimten
In dit artikel wordt de beveiliging van gegevens exfiltration in azure Synapse Analytics uitgelegd

## <a name="securing-data-egress-from-synapse-workspaces"></a>Gegevens uit Synapse-werk ruimten beveiligen
Azure Synapse Analytics-werk ruimten ondersteunen het inschakelen van gegevens exfiltration-beveiliging voor werk ruimten. Met exfiltration-beveiliging kunt u zich beschermen tegen kwaadwillende personen die toegang hebben tot uw Azure-resources en alomtegenwoordig geworden gevoelige gegevens naar locaties buiten het bereik van uw organisatie. Op het moment dat u de werk ruimte maakt, kunt u ervoor kiezen om de werk ruimte te configureren met een beheerd virtueel netwerk en extra beveiliging op basis van gegevens exfiltration. Wanneer een werk ruimte wordt gemaakt met een [beheerd virtueel netwerk](./synapse-workspace-managed-vnet.md), worden gegevens integratie en Spark-resources geïmplementeerd in het beheerde virtuele netwerk. De toegewezen SQL-groepen van de werk ruimte en de serverloze SQL-groepen hebben mogelijkheden voor meerdere tenants en moeten als zodanig bestaan buiten het beheerde virtuele netwerk. Voor werk ruimten met data exfiltration-beveiliging communiceert resources binnen het beheerde virtuele netwerk altijd via [beheerde persoonlijke eind punten](./synapse-workspace-managed-private-endpoints.md) en kunnen de Synapse SQL-resources alleen verbinding maken met geautoriseerde Azure-resources (doelen van goedgekeurde beheerde privé-eindpunt verbindingen vanuit de werk ruimte). 

>[!Note]
>U kunt de werkruimte configuratie voor beheerde virtuele netwerk-en gegevens exfiltration-beveiliging niet wijzigen nadat de werk ruimte is gemaakt.

## <a name="managing-synapse-workspace-data-egress-to-approved-targets"></a>Synapse-werkruimte gegevens die worden uitgaand beheren op goedgekeurde doelen
Nadat de werk ruimte is gemaakt terwijl de gegevens exfiltration zijn ingeschakeld, kunnen de eigen aren van de werkruimte resource de lijst met goedgekeurde Azure AD-tenants voor de werk ruimte beheren. Gebruikers met de [juiste machtigingen](./synapse-workspace-access-control-overview.md) voor de werk ruimte kunnen de Synapse Studio gebruiken om beheerde aanvragen voor het privé-eind punt te maken voor resources in de goedgekeurde Azure AD-tenants van de werk ruimte. Het beheerde privé-eind punt wordt gemaakt als de gebruiker probeert een persoonlijk eind punt verbinding te maken met een bron in een niet-goedgekeurde Tenant.

## <a name="sample-workspace-with-data-exfiltration-protection-enabled"></a>Voorbeeld werkruimte waarop data exfiltration Protection is ingeschakeld
Laat ons een voor beeld gebruiken om de beveiliging van gegevens exfiltration te illustreren voor Synapse-werk ruimten. Contoso heeft Azure-resources in Tenant A en Tenant B, en er is een nood zaak om deze bronnen veilig verbinding te laten maken. Er is een Synapse-werk ruimte gemaakt in Tenant A met Tenant B die is toegevoegd als een goedgekeurde Azure AD-Tenant. Het diagram toont particuliere endpoint-verbindingen met Azure Storage accounts in Tenant A en Tenant B die zijn goedgekeurd door de eigenaar van het opslag account. In het diagram wordt ook het geblokkeerde persoonlijke eind punt gemaakt weer gegeven. Het maken van dit persoonlijke eind punt is geblokkeerd omdat het is gericht op een Azure Storage account in de fabrikam Azure AD-Tenant. Dit is geen goedgekeurde Azure AD-Tenant voor de werk ruimte van contoso. 
:::image type="content" source="media/workspace-data-exfiltration-protection/workspace-data-exfiltration-protection-diagram.png" alt-text="Dit diagram toont hoe gegevens exfiltration-beveiliging wordt geïmplementeerd voor Synapse-werk ruimten":::

>[!IMPORTANT]
>Resources in andere tenants dan de Tenant van de werk ruimte mogen geen firewall regels blok keren, zodat de SQL-groepen er verbinding mee kunnen maken. Resources in het virtuele netwerk van de werk ruimte, zoals Spark-clusters, kunnen verbinding maken via beheerde persoonlijke koppelingen naar met firewalls beveiligde bronnen.
## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [maken van een werk ruimte waarvoor data exfiltration Protection is ingeschakeld](./how-to-create-a-workspace-with-data-exfiltration-protection.md)

Meer informatie over [Managed workspace Virtual Network](./synapse-workspace-managed-vnet.md)

Meer informatie over [beheerde privé-eindpunten](./synapse-workspace-managed-private-endpoints.md)

[Create Managed private endpoint to your data sources](./how-to-create-managed-private-endpoints.md) (Beheerde privé-eindpunten naar uw gegevensbronnen maken)
