---
title: Ingebouwde beleidsdefinities voor Azure Security Center
description: Overzicht van de ingebouwde Azure Policy-beleidsdefinities voor Azure Security Center. Deze ingebouwde beleidsdefinities bieden algemene benaderingen voor het beheren van uw Azure-resources.
ms.date: 02/09/2021
ms.topic: reference
author: memildin
ms.author: memildin
ms.service: security-center
ms.custom: subject-policy-reference
ms.openlocfilehash: 3b5860cc4ada88e2e7c7813e3441db3ec89f31af
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101729914"
---
# <a name="azure-policy-built-in-definitions-for-azure-security-center"></a>Ingebouwde Azure Policy-definities voor Azure Security Center

Deze pagina bevat een index van [Azure Policy](../governance/policy/overview.md) ingebouwde beleids definities die betrekking hebben op Azure Security Center. De volgende groeperingen van beleids definities zijn beschikbaar:

- De groep [initiatieven](#azure-security-center-initiatives) bevat een lijst met de Azure Policy initiatief definities in de categorie ' Security Center '.
- In de [standaard-initiatief](#azure-security-center-initiatives) groep wordt een lijst weer gegeven met alle Azure Policy definities die deel uitmaken van het standaard initiatief van de Security Center, [Azure Security Bench Mark](../security/benchmarks/introduction.md). Deze micro soft-auteurde, algemeen gerespecteerde Bench Mark bouwt voort op besturings elementen uit het [Center voor Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) en het [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) met een focus op Cloud gerichte beveiliging.
- De [categorie](#azure-security-center-category) groep bevat alle Azure Policy definities in de categorie ' Security Center '.

Zie [werken met beveiligings beleid](./tutorial-security-policy.md)voor meer informatie over beveiligings beleidsregels. Zie [Ingebouwde Azure Policy-definities](../governance/policy/samples/built-in-policies.md) voor aanvullende ingebouwde modules voor Azure Policy voor andere services.

De naam van elke ingebouwde beleidsdefinitie linkt naar de beleidsdefinitie in de Azure-portal. Gebruik de koppeling in de kolom **Versie** om de bron te bekijken in de [Azure Policy GitHub-opslagplaats](https://github.com/Azure/azure-policy).

## <a name="azure-security-center-initiatives"></a>Azure Security Center initiatieven

Zie de volgende tabel voor meer informatie over de ingebouwde initiatieven die door Security Center worden bewaakt:

[!INCLUDE [azure-policy-reference-policyset-security-center](../../includes/policy/reference/bycat/policysets-security-center.md)]

## <a name="security-centers-default-initiative-azure-security-benchmark"></a>Het standaard initiatief van Security Center (Azure Security Bench Mark)

Zie de volgende tabel voor meer informatie over het ingebouwde beleid dat wordt bewaakt door Security Center:

[!INCLUDE [azure-policy-reference-init-asc](../../includes/policy/reference/custom/init-asc.md)]

## <a name="azure-security-center-category"></a>Azure Security Center categorie

[!INCLUDE [azure-policy-reference-category-securitycenter](../../includes/policy/reference/bycat/policies-security-center.md)]

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over Azure Policy definities voor beveiligings beleid in Security Center. Raadpleeg de volgende artikelen voor meer informatie.

- Bekijk de inbouwingen op de [Azure Policy GitHub-opslagplaats](https://github.com/Azure/azure-policy).
- Lees over de [structuur van Azure Policy-definities](../governance/policy/concepts/definition-structure.md).
- Lees [Informatie over de effecten van het beleid](../governance/policy/concepts/effects.md).
- [Azure Security Center plannings-en bedienings handleiding](./security-center-planning-and-operations-guide.md): informatie over het plannen en begrijpen van ontwerp overwegingen in azure Security Center.
- [Beveiligingsstatus controleren in Azure Security Center](./security-center-monitoring.md): meer informatie over het controleren van de status van uw Azure-resources.
- [Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center](./security-center-managing-and-responding-alerts.md): leer hoe u beveiligingswaarschuwingen kunt beheren en erop kunt reageren.
- [Partneroplossingen controleren met Azure Security Center](./security-center-partner-integration.md): leer hoe u de integriteitsstatus van uw partneroplossingen kunt controleren.
- [Azure Policy](../governance/policy/overview.md): meer informatie over het controleren en beheren van uw Azure-resources.
