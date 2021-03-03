---
title: Azure Security Bench Mark v2-Asset Management
description: Asset Management van Azure Security Bench Mark v2
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 32b0a7e31fc0d595eacc2bf5257f41e4ce35566b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101735808"
---
# <a name="security-control-v2-asset-management"></a>Beveiligings controle v2: beheer van middelen

Asset Management bestrijkt de controles om de zicht baarheid van de beveiliging en het beheer van Azure-resources te garanderen. Dit omvat aanbevelingen voor machtigingen voor beveiligings personeel, beveiliging van de inventaris en het beheren van goed keuringen voor services en resources (inventaris, bijhouden en corrigeren).

Voor een overzicht van de toepasselijke ingebouwde Azure Policy raadpleegt u [de details van het ingebouwde initiatief Azure Security Bench Mark compliance compliantie: netwerk beveiliging](../../governance/policy/samples/azure-security-benchmark#asset-management)

## <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Zorg ervoor dat het beveiligingsteam inzicht heeft in risico's voor assets

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| AM-1 | 1,1, 1,2 | CM-8, PM-5 |

Zorg ervoor dat aan beveiligings teams machtigingen voor beveiligings lezers worden verleend in uw Azure-Tenant en-abonnementen zodat ze kunnen controleren op beveiligings Risico's met behulp van Azure Security Center.

Afhankelijk van de manier waarop de verantwoordelijkheden van het beveiligings team zijn gestructureerd, kan bewaking voor beveiligings Risico's de verantwoordelijkheid zijn van een centraal beveiligings team of een lokaal team. Om die reden moeten beveiligingsinzichten en -risico's altijd centraal worden verzameld in een organisatie. 

De machtiging Beveiligingslezer kan breed worden toegepast op een hele tenant (hoofdbeheergroep) of in het bereik van beheergroepen of specifieke abonnementen. 

Opmerking: Er zijn mogelijk extra machtigingen vereist om inzicht te krijgen in workloads en services.

- [Overzicht van rol Beveiligingslezer](../../role-based-access-control/built-in-roles.md#security-reader)

- [Overzicht van Azure-beheergroepen](../../governance/management-groups/overview.md)

**Verantwoordelijkheid**: Klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastructuur- en eindpuntbeveiliging](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Beheer van beveiligings naleving](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Controleer of het beveiligingsteam toegang heeft tot de asset-inventaris en metagegevens

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| AM-2 | 1,1, 1,2, 1,4, 1,5, 9,1, 12,1 | CM-8, PM-5 |

Zorg ervoor dat beveiligings teams toegang hebben tot een voortdurend bijgewerkte inventaris van assets in Azure. Beveiligingsteams hebben deze inventaris vaak nodig om de mogelijke blootstelling van hun organisatie aan toekomstige risico's te evalueren, en willen deze inventaris gebruiken als invoer waarmee ze de beveiliging constant kunnen verbeteren. 

Met de functie voor Azure Security Center Inventory en Azure resource Graph kunt u alle resources in uw abonnementen opvragen en detecteren, inclusief Azure-Services,-toepassingen en-netwerk bronnen.

Organiseer op logische wijze activa op basis van de taxonomie van uw organisatie met behulp van tags en andere meta gegevens in azure (naam, beschrijving en categorie).

- [Query's maken met Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md)

- [Azure Security Center Asset Inventory Management](../../security-center/asset-inventory.md)

- [Voor meer informatie over het labelen van assets raadpleegt u de hand leiding resource naamgeving en Tags toevoegen](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

**Verantwoordelijkheid**: Klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastructuur- en eindpuntbeveiliging](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Beheer van beveiligings naleving](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-3-use-only-approved-azure-services"></a>AM-3: Gebruik alleen goedgekeurde Azure-Services

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| AM-3 | 2,3, 2,4 | CM-7, CM-8 |

Gebruik Azure Policy om te controleren welke services gebruikers in uw omgeving kunnen inrichten en beperken. Gebruik Azure Resource Graph om resources binnen hun abonnementen op te vragen en te detecteren. U kunt Azure Monitor ook gebruiken om regels te maken voor het activeren van waarschuwingen wanneer een niet-goedgekeurde service wordt gedetecteerd.

- [Azure Policy configureren en beheren](../../governance/policy/tutorials/create-and-manage.md)

- [Een specifiek resource type weigeren met Azure Policy](../../governance/policy/samples/index.md)

- [Query's maken met Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md)

**Verantwoordelijkheid**: Klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Beheer van beveiligings naleving](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

- [Postuurbeheer](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

## <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Beveiliging van levenscyclusbeheer van assets garanderen

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| AM-4 | 2,3, 2,4, 2,5 | CM-7, CM-8, CM-10, CM-11 |

Een beveiligings beleid voor het beheer van de levens duur van asset-processen instellen of bijwerken voor mogelijk grote veranderingen. Het betreft hier bijvoorbeeld aanpassingen van: id-providers en toegang, gegevensgevoeligheid, netwerkconfiguratie en toewijzing van beheerdersbevoegdheden.

Verwijder Azure-resources wanneer deze ze niet meer nodig zijn.

- [Azure-resourcegroep en -resource verwijderen](../../azure-resource-manager/management/delete-resource-group.md)

**Verantwoordelijkheid**: Klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastructuur- en eindpuntbeveiliging](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Postuurbeheer](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Beheer van beveiligings naleving](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)

## <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: de mogelijkheid van gebruikers om te communiceren met Azure Resource Manager beperken

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| AM-5 | 2.9 | AC-3 |

Gebruik voorwaardelijke toegang van Azure AD om de interactie van gebruikers met Azure Resource Manager te beperken door ' blok toegang ' te configureren voor de app Microsoft Azure management.

- [Voorwaardelijke toegang configureren om de toegang tot Azure-bronnen beheer te blok keren](../../role-based-access-control/conditional-access-azure-management.md)

**Verantwoordelijkheid**: Klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Postuurbeheer](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Infrastructuur- en eindpuntbeveiliging](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

## <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6: alleen goedgekeurde toepassingen in reken bronnen gebruiken

| Azure-ID | CIS-besturings elementen v 7.1-ID ('s) | NIST SP 800-53 R4-ID ('s) |
|--|--|--|--|
| AM-6 | 2,6, 2,7 | AC-3, CM-7, CM-8, CM-10, CM-11 |

Zorg ervoor dat alleen geautoriseerde software wordt uitgevoerd en dat alle niet-geautoriseerde software wordt geblokkeerd voor het uitvoeren van Azure Virtual Machines.

Gebruik Azure Security Center (ASC) adaptieve toepassings besturings elementen voor het detecteren en genereren van een lijst met toegestane toepassingen. U kunt ook ASC-besturings elementen voor adaptieve toepassingen gebruiken om ervoor te zorgen dat alleen geautoriseerde software wordt uitgevoerd en alle niet-geautoriseerde software wordt geblokkeerd voor het uitvoeren van Azure Virtual Machines.

Gebruik Azure Automation Wijzigingen bijhouden en inventaris om het verzamelen van inventaris gegevens van uw Windows-en Linux-Vm's te automatiseren. De software naam, versie, uitgever en tijd van vernieuwen zijn beschikbaar via de Azure Portal. Als u de software-installatie datum en andere informatie wilt ophalen, schakelt u Diagnostische gegevens op gast niveau in en stuurt u de Windows-gebeurtenis logboeken naar Log Analytics werk ruimte.

Afhankelijk van het type scripts kunt u specifieke configuraties van het besturings systeem of bronnen van derden gebruiken om de mogelijkheid van gebruikers om scripts uit te voeren in azure Compute-resources te beperken.

U kunt ook een oplossing van derden gebruiken om niet-goedgekeurde software te detecteren en te identificeren.

- [Azure Security Center adaptieve toepassings besturings elementen gebruiken](../../security-center/security-center-adaptive-application.md)

- [Meer informatie over Azure Automation Wijzigingen bijhouden en inventaris](../../automation/change-tracking/overview.md)

- [De uitvoering van Power shell-scripts beheren in Windows-omgevingen](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6)

**Verantwoordelijkheid**: Klant

**Beveiligings belanghebbenden van klanten** ([meer informatie](/azure/cloud-adoption-framework/organize/cloud-security#security-functions)):

- [Infrastructuur- en eindpuntbeveiliging](/azure/cloud-adoption-framework/organize/cloud-security-infrastructure-endpoint)

- [Postuurbeheer](/azure/cloud-adoption-framework/organize/cloud-security-posture-management)

- [Beheer van beveiligings naleving](/azure/cloud-adoption-framework/organize/cloud-security-compliance-management)
