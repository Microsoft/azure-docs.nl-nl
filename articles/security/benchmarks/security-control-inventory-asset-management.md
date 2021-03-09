---
title: Azure-beveiligings beheer-inventarisatie en Asset Management
description: Azure Security Control-inventaris en Asset Management
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 8a15517c7c34d39dfb1de237d19e84c40bf834ea
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102500101"
---
# <a name="security-control-inventory-and-asset-management"></a>Beveiligings beheer: inventarisatie en activa beheer

Aanbevelingen voor inventarisatie en Asset Management richten zich op het adresseren van problemen met betrekking tot het actief beheren (inventariseren, bijhouden en corrigeren) van alle Azure-resources, zodat alleen geautoriseerde resources toegang krijgen en niet-geautoriseerde en onbeheerde bronnen worden geïdentificeerd en verwijderd.

## <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatische Asset-detectie oplossing gebruiken

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 6.1 | 1,1, 1,2, 1,3, 1,4, 9,1, 12,1 | Klant |

Gebruik Azure resource Graph voor het opvragen/detecteren van alle resources (zoals compute, opslag, netwerk, poorten en protocollen enz.) binnen uw abonnement (en).  Zorg ervoor dat de juiste (Lees-) machtigingen voor uw Tenant en alle Azure-abonnementen en resources in uw abonnementen inventariseren.

Hoewel klassieke Azure-resources kunnen worden gedetecteerd via resource grafiek, is het raadzaam om Azure Resource Manager resources te maken en te gebruiken.

- [Query's maken met Azure resource Graph](../../governance/resource-graph/first-query-portal.md)

- [Uw Azure-abonnementen weer geven](/powershell/module/az.accounts/get-azsubscription)

- [Meer informatie over Azure RBAC](../../role-based-access-control/overview.md)

## <a name="62-maintain-asset-metadata"></a>6,2: meta gegevens van activa onderhouden

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 6,2 | 1.5 | Klant |

Pas Tags toe op Azure-resources die meta gegevens geven om ze logisch in een taxonomie te organiseren.

- [Tags maken en gebruiken](../../azure-resource-manager/management/tag-resources.md)

## <a name="63-delete-unauthorized-azure-resources"></a>6,3: niet-geautoriseerde Azure-resources verwijderen

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 6.3 | 1.6 | Klant |

Gebruik Tags, beheer groepen en afzonderlijke abonnementen, waar nodig, om assets te organiseren en bij te houden. Sluit de inventaris regel matig af en zorg ervoor dat niet-geautoriseerde resources tijdig worden verwijderd uit het abonnement.

- [Aanvullende Azure-abonnementen maken](../../cost-management-billing/manage/create-subscription.md)

- [Beheergroepen maken](../../governance/management-groups/create-management-group-portal.md)

- [Tags maken en gebruiken](../../azure-resource-manager/management/tag-resources.md)

## <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: een inventaris van goedgekeurde Azure-resources definiëren en onderhouden

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 6.4 | 2.1 | Klant |

Maak een inventaris van goedgekeurde Azure-resources en goedgekeurde software voor Compute-resources conform onze organisatorische behoeften.

## <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: monitor voor niet-goedgekeurde Azure-resources

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 6.5 | 2,3, 2,4 | Klant |

Gebruik Azure Policy om beperkingen toe te voegen voor het type resources dat kan worden gemaakt in uw abonnement (en).

Gebruik Azure resource Graph voor het opvragen/detecteren van resources binnen hun abonnement (en).  Zorg ervoor dat alle Azure-resources die aanwezig zijn in de omgeving, zijn goedgekeurd.

- [Azure Policy configureren en beheren](../../governance/policy/tutorials/create-and-manage.md)

- [Query's maken met Azure Graph](../../governance/resource-graph/first-query-portal.md)

## <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: monitor voor niet-goedgekeurde software toepassingen binnen reken resources

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 6.6 | 2,3, 2,4 | Klant |

Gebruik Azure virtual machine Inventory om het verzamelen van informatie over alle software op Virtual Machines te automatiseren. De software naam, versie, uitgever en tijd van vernieuwen zijn beschikbaar via de Azure Portal. Als u toegang wilt krijgen tot installatie datum en andere informatie, schakelt u Diagnostische gegevens op gast niveau in en brengt u de Windows-gebeurtenis Logboeken in een Log Analytics-werk ruimte.

- [Inventarisatie van virtuele Azure-machines inschakelen](../../automation/automation-tutorial-installed-software.md)

## <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: niet-goedgekeurde Azure-resources en software toepassingen verwijderen

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 6.7 | 2,5 | Klant |

Gebruik Azure Security Center File Integrity Monitoring (Wijzigingen bijhouden) en virtual machine Inventory om alle software te identificeren die op Virtual Machines is geïnstalleerd. U kunt uw eigen proces voor het verwijderen van onbevoegde software implementeren. U kunt ook een oplossing van derden gebruiken om niet-goedgekeurde software te identificeren.

- [Bestands integriteit controleren gebruiken](../../security-center/security-center-file-integrity-monitoring.md)

- [Meer informatie over Azure Wijzigingen bijhouden](../../automation/change-tracking/overview.md)

- [Inventarisatie van virtuele Azure-machines inschakelen](../../automation/automation-tutorial-installed-software.md)

## <a name="68-use-only-approved-applications"></a>6,8: alleen goedgekeurde toepassingen gebruiken

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 6.8 | 2,6 | Klant |

Gebruik Azure Security Center adaptieve toepassings controles om ervoor te zorgen dat alleen geautoriseerde software wordt uitgevoerd en alle niet-geautoriseerde software wordt geblokkeerd voor uitvoering op Azure Virtual Machines.

- [Azure Security Center adaptieve toepassings besturings elementen gebruiken](../../security-center/security-center-adaptive-application.md)

## <a name="69-use-only-approved-azure-services"></a>6,9: alleen goedgekeurde Azure-Services gebruiken

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 6.9 | 2,6 | Klant |

Gebruik Azure Policy om te beperken welke services u in uw omgeving kunt inrichten.

- [Azure Policy configureren en beheren](../../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resource type weigeren met Azure Policy](../../governance/policy/samples/index.md)

## <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: een inventaris van goedgekeurde software titels onderhouden

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 6.10 | 2.7 | Klant |

Gebruik Azure Security Center adaptieve toepassings besturings elementen om op te geven welke bestands typen een regel kan of niet van toepassing is op.

Implementeer een oplossing van derden als deze niet aan de vereiste voldoet.

- [Azure Security Center adaptieve toepassings besturings elementen gebruiken](../../security-center/security-center-adaptive-application.md)

## <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 6,11 | 2.9 | Klant |

Gebruik voorwaardelijke toegang van Azure om gebruikers de mogelijkheid te bieden om met Azure Resource Manager te communiceren door ' toegang blok keren ' te configureren voor de app Microsoft Azure-beheer.

- [Voorwaardelijke toegang configureren om de toegang tot Azure-bronnen beheer te blok keren](../../role-based-access-control/conditional-access-azure-management.md)

## <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: de mogelijkheid van gebruikers om scripts uit te voeren binnen reken bronnen beperken

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 6,12 | 2.9 | Klant |

Afhankelijk van het type scripts kunt u specifieke configuraties van het besturings systeem of bronnen van derden gebruiken om de mogelijkheid van gebruikers om scripts uit te voeren binnen Azure Compute-resources te beperken.  U kunt ook gebruikmaken van Azure Security Center adaptieve toepassings controles om ervoor te zorgen dat alleen geautoriseerde software wordt uitgevoerd en alle niet-geautoriseerde software wordt geblokkeerd voor uitvoering op Azure Virtual Machines.

- [De uitvoering van Power shell-scripts beheren in Windows-omgevingen](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

- [Azure Security Center adaptieve toepassings besturings elementen gebruiken](../../security-center/security-center-adaptive-application.md)

## <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: toepassingen met een hoog risico fysiek of logisch scheiden

| Azure-ID | CIS-Id's | Onder |
|--|--|--|
| 6,13 | 2.9 | Klant |

Software die vereist is voor bedrijfs activiteiten, maar die een groter risico kan opleveren voor de organisatie, moet worden geïsoleerd in een eigen virtuele machine en/of virtueel netwerk en voldoende beveiligd zijn met een Azure Firewall of een netwerk beveiligings groep.

- [Een virtueel netwerk maken](../../virtual-network/quick-create-portal.md)

- [Een NSG maken met een beveiligings configuratie](../../virtual-network/tutorial-filter-network-traffic.md)


## <a name="next-steps"></a>Volgende stappen

- Zie het volgende beveiligings beheer: [beveiligde configuratie](security-control-secure-configuration.md)