---
title: Ingebouwde beleidsdefinities voor Azure DDoS Protection Standard
description: Een Azure Policy ingebouwde beleidsdefinities voor Azure DDoS Protection Standard. Deze ingebouwde beleidsdefinities bieden algemene benaderingen voor het beheren van uw Azure-resources.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2021
ms.author: yitoh
ms.custom: subject-policy-reference
ms.topic: include
ms.openlocfilehash: 86fa383fe2456eabe0cce142bd4a9c665fb95a2a
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505697"
---
# <a name="azure-policy-built-in-definitions-for-azure-ddos-protection-standard"></a>Azure Policy ingebouwde definities voor Azure DDoS Protection Standard

Deze pagina is een index van [Azure Policy](../governance/policy/overview.md) ingebouwde beleidsdefinities voor Azure DDoS Protection Standard. Zie [Ingebouwde Azure Policy-definities](../governance/policy/samples/built-in-policies.md) voor aanvullende ingebouwde modules voor Azure Policy voor andere services.

De naam van elke ingebouwde beleidsdefinitie linkt naar de beleidsdefinitie in de Azure-portal. Gebruik de koppeling in de kolom **Versie** om de bron te bekijken in de [Azure Policy GitHub-opslagplaats](https://github.com/Azure/azure-policy).

## <a name="azure-ddos-protection-standard"></a>Azure DDoS Protection Standard

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Virtuele netwerken moeten worden beveiligd met Azure DDoS Protection Standard](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F94de2ad3-e0c1-4caf-ad78-5d47bbc83d3d)|Bescherm uw virtuele netwerken tegen volumetrische en protocolaanvallen met Azure DDoS Protection Standard. U vindt meer informatie op [https://aka.ms/ddosprotectiondocs](https://aka.ms/ddosprotectiondocs).|Wijzigen, Controleren, Uitgeschakeld|[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkDdosStandard_Audit.json)|
|[Voor openbare IP-adressen moeten resourcelogboeken zijn ingeschakeld voor Azure DDoS Protection Standard](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F752154a7-1e0f-45c6-a880-ac75a7e4f648)|Schakel resourcelogboeken voor openbare IP-adressen in diagnostische instellingen in om te streamen naar een Log Analytics-werkruimte. Krijg gedetailleerde zichtbaarheid in aanvalsverkeer en acties die worden ondernomen om DDoS-aanvallen te beperken via meldingen, rapporten en stroomlogboeken.|AuditIfNotExists, DeployIfNotExists, Disabled|[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/PublicIpDdosLogging_Audit.json)|


## <a name="next-steps"></a>Volgende stappen

- Bekijk de inbouwingen op de [Azure Policy GitHub-opslagplaats](https://github.com/Azure/azure-policy).
- Lees over de [structuur van Azure Policy-definities](../governance/policy/concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../governance/policy/concepts/effects.md).
