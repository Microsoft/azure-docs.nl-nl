---
title: Ingebouwde beleidsdefinities voor Azure Security Center
description: Overzicht van de ingebouwde Azure Policy-beleidsdefinities voor Azure Security Center. Deze ingebouwde beleidsdefinities bieden algemene benaderingen voor het beheren van uw Azure-resources.
ms.date: 02/09/2021
ms.topic: reference
author: memildin
ms.author: memildin
ms.service: security-center
ms.custom: subject-policy-reference
ms.openlocfilehash: b80ceffe22c797d46b82ec33a47c726d68bf4cdb
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099825"
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

In dit artikel hebt u geleerd over Azure Policy definities voor beveiligings beleid in Security Center. Zie [Wat zijn beveiligings beleid, initiatieven en aanbevelingen?](security-policy-concept.md)voor meer informatie over initiatieven, beleids regels en hoe deze zich verhouden tot de aanbevelingen van Security Center.