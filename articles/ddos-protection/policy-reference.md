---
title: Ingebouwde beleids definities voor Azure DDoS Protection Standard
description: Een lijst met Azure Policy ingebouwde beleids definities voor Azure DDoS Protection Standard. Deze ingebouwde beleidsdefinities bieden algemene benaderingen voor het beheren van uw Azure-resources.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/07/2021
ms.author: yitoh
ms.custom: subject-policy-reference
ms.topic: include
ms.openlocfilehash: 6a0496740fbd82090ba7199ca3e064f5bd2bf7c5
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108195"
---
# <a name="azure-policy-built-in-definitions-for-azure-ddos-protection-standard"></a>Azure Policy ingebouwde definities voor Azure DDoS Protection Standard

Deze pagina bevat een index van [Azure Policy](../governance/policy/overview.md) ingebouwde beleids definities voor Azure DDoS Protection Standard. Zie [Ingebouwde Azure Policy-definities](../governance/policy/samples/built-in-policies.md) voor aanvullende ingebouwde modules voor Azure Policy voor andere services.

De naam van elke ingebouwde beleidsdefinitie linkt naar de beleidsdefinitie in de Azure-portal. Gebruik de koppeling in de kolom **Versie** om de bron te bekijken in de [Azure Policy GitHub-opslagplaats](https://github.com/Azure/azure-policy).

## <a name="azure-ddos-protection-standard"></a>Azure DDoS Protection Standard

|Naam<br /><sub>(Azure-portal)</sub> |Beschrijving |Gevolg(en) |Versie<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Virtuele netwerken moeten worden beveiligd door Azure DDoS Protection Standard](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F94de2ad3-e0c1-4caf-ad78-5d47bbc83d3d)|Bescherm uw virtuele netwerken tegen maat-en protocol aanvallen met Azure DDoS Protection Standard. U vindt meer informatie op [https://aka.ms/ddosprotectiondocs](https://aka.ms/ddosprotectiondocs).|Wijzigen, controleren, uitgeschakeld|[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkDdosStandard_Audit.json)|
|[Voor open bare IP-adressen moeten resource logboeken zijn ingeschakeld voor Azure DDoS Protection Standard](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F752154a7-1e0f-45c6-a880-ac75a7e4f648)|Schakel bron logboeken voor open bare IP-adressen in Diagnostische instellingen in om naar een Log Analytics-werk ruimte te streamen. Krijg gedetailleerde zicht baarheid in het aanvals verkeer en de acties die worden uitgevoerd om DDoS-aanvallen te beperken via meldingen, rapporten en stroom Logboeken.|AuditIfNotExists, DeployIfNotExists, uitgeschakeld|[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Monitoring/PublicIpDdosLogging_Audit.json)|


## <a name="next-steps"></a>Volgende stappen

- Bekijk de inbouwingen op de [Azure Policy GitHub-opslagplaats](https://github.com/Azure/azure-policy).
- Lees over de [structuur van Azure Policy-definities](../governance/policy/concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../governance/policy/concepts/effects.md).