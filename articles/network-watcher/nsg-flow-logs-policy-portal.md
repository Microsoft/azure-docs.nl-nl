---
title: 'Quick Start: NSG-stroom logboeken implementeren en beheren met Azure Policy'
titleSuffix: Azure Network Watcher
description: In dit artikel wordt uitgelegd hoe u het ingebouwde beleid gebruikt voor het beheren van de implementatie van NSG-stroom logboeken
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 8da1130809c1802f4db963f4b4b000a848e9abaa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98011098"
---
# <a name="quickstart-deploy-and-manage-nsg-flow-logs-using-azure-policy"></a>Quick Start: NSG-stroom logboeken implementeren en beheren met behulp van Azure Policy 

## <a name="overview"></a>Overzicht
Met Azure Policy kunt u organisatiestandaarden afdwingen en compliance op schaal beoordelen. Veelvoorkomende use-cases voor Azure Policy zijn onder andere het implementeren van governance voor consistentie van resources, naleving van de regelgeving, beveiliging, kosten en beheer. In dit artikel gebruiken we twee ingebouwde beleids regels die beschikbaar zijn voor NSG-stroom Logboeken om de installatie van uw stroom logboeken te beheren. Het eerste beleid markeert alle Nsg's zonder dat er stroom logboeken zijn ingeschakeld. Het tweede beleid implementeert automatisch stroom logboeken voor Nsg's zonder dat er stroom logboeken zijn ingeschakeld. 

Als u voor de eerste keer een Azure-beleid maakt, kunt u het volgende lezen: 
- [Overzicht van Azure-beleid](../governance/policy/overview.md) 
- [Zelf studie voor het maken van beleid](../governance/policy/assign-policy-portal.md#create-a-policy-assignment).


## <a name="locate-the-policies"></a>Het beleid zoeken
1. Ga naar de Azure Portal – [Portal.Azure.com](https://portal.azure.com) 

Ga naar Azure Policy pagina door te zoeken naar beleid op de start pagina van het hoogste zoek balk ![ beleid](./media/network-watcher-builtin-policy/1_policy-search.png)

2. Ga naar het tabblad **toewijzingen** vanuit het linkerdeel venster

![Tabblad toewijzingen](./media/network-watcher-builtin-policy/2_assignments-tab.png)

3. Klik op de knop **beleid toewijzen** 

![Knop beleid toewijzen](./media/network-watcher-builtin-policy/3_assign-policy-button.png)

4. Klik op het menu drie punten onder beleids definities om het beschik bare beleid te bekijken

5. Gebruik het filter type en kies ' ingebouwd '. Zoek vervolgens naar ' stroom logboek '

U moet de beleids lijst twee ingebouwde beleids regels voor flow logboeken zien ![](./media/network-watcher-builtin-policy/4_filter-for-flow-log-policies.png)

6. Kies het beleid dat u wilt toewijzen

- *"Het stroom logboek moet worden geconfigureerd voor elke netwerk beveiligings groep"* is het controle beleid dat niet-compatibele nsg's markeert, dat wil zeggen nsg's zonder logboek registratie van de stroom is ingeschakeld
- *' Een bron van een stroom logboek met een netwerk beveiligings groep voor het doel implementeren '* is het beleid met een implementatie actie, waarbij stroom logboeken worden ingeschakeld op alle Nsg's zonder stroom logboeken

Er zijn afzonderlijke instructies voor elk beleid hieronder.  

## <a name="audit-policy"></a>Controlebeleid 

### <a name="how-the-policy-works"></a>Hoe het beleid werkt

Met het beleid worden alle bestaande ARM-objecten van het type ' micro soft. Network/networkSecurityGroups ' gecontroleerd. Dit is het aantal Nsg's in een bepaald bereik en controleert op het bestaan van gekoppelde stroom logboeken via de eigenschap flow logs van het NSG. Als de eigenschap niet bestaat, wordt de NSG gemarkeerd.

Als u de volledige definitie van het beleid wilt zien, gaat u naar het [tabblad definities](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions) en zoekt u naar stroom Logboeken om het beleid te vinden.

### <a name="assignment"></a>Toewijzing

1. Vul uw beleids gegevens in

- Bereik: een abonnement is de gemeen schappelijke keuze, u kunt ook een beheer groep of resource groep kiezen die voor u van belang is.  
- Beleids definitie: moet worden gekozen, zoals wordt weer gegeven in de sectie ' Zoek de beleids regels '.
- Naam toewijzing: Kies een beschrijving 

2. Klik op controleren + maken om uw toewijzing te controleren

Voor het beleid zijn geen para meters vereist. Wanneer u een controle beleid toewijst, hoeft u de details niet op het tabblad herbemiddeling in te vullen.  

![Controle beleid controleren](./media/network-watcher-builtin-policy/5_1_audit-policy-review.png)

### <a name="results"></a>Resultaten

Als u de resultaten wilt controleren, opent u het tabblad naleving en zoekt u naar de naam van uw toewijzing.
Wanneer het beleid wordt uitgevoerd, ziet u iets dat lijkt op de volgende scherm afbeelding. Als uw beleid nog niet is uitgevoerd, wacht u enige tijd. 

![Resultaten van controle beleid](./media/network-watcher-builtin-policy/7_1_audit-policy-results.png)

## <a name="deploy-if-not-exists-policy"></a>Beleid voor implementeren als niet-bestaat 

### <a name="policy-structure"></a>Beleids structuur

Met het beleid worden alle bestaande ARM-objecten van het type ' micro soft. Network/networkSecurityGroups ' gecontroleerd. Dit is het aantal Nsg's in een bepaald bereik en controleert op het bestaan van gekoppelde stroom logboeken via de eigenschap flow logs van het NSG. Als de eigenschap niet bestaat, implementeert het beleid een stroom logboek. 

Als u de volledige definitie van het beleid wilt zien, gaat u naar het [tabblad definities](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions) en zoekt u naar stroom Logboeken om het beleid te vinden. 

### <a name="assignment"></a>Toewijzing

1. Vul uw beleids gegevens in

- Bereik: een abonnement is de gemeen schappelijke keuze, u kunt ook een beheer groep of resource groep kiezen die voor u van belang is.  
- Beleids definitie: moet worden gekozen, zoals wordt weer gegeven in de sectie ' Zoek de beleids regels '.
- Naam toewijzing: Kies een beschrijving 

2. Beleids parameters toevoegen 

De Network Watcher-service is een regionale service. Met deze para meters kan de beleids actie van het implementeren van stroom logboeken worden uitgevoerd. 
- NSG regio: Azure-regio's waarop het beleid van doel is
- Opslag-ID: volledige Resource-ID van het opslag account. Opmerking: dit opslag account moet zich in dezelfde regio bevinden als de NSG. 
- Netwerk volgers RG: naam van de resource groep die uw Network Watcher resource bevat. Als u de naam niet hebt gewijzigd, kunt u ' NetworkWatcherRG ' invoeren. Dit is de standaard waarde.
- Network Watcher naam: naam van de regionale Network Watcher-service. Indeling: NetworkWatcher_RegionName. Voor beeld: NetworkWatcher_centralus. Bekijk de volledige lijst.

![DINE-beleids parameters](./media/network-watcher-builtin-policy/5_2_1_dine-policy-details-alt.png)

3. Details van herstel toevoegen

- Schakel het selectie vakje ' herstel taak maken ' in als u wilt dat het beleid van invloed is op bestaande resources 
- Het selectie vakje ' een beheerde identiteit maken ' moet al zijn ingeschakeld
- Dezelfde locatie als vorige hebt geselecteerd voor uw beheerde identiteit 
- U hebt machtigingen voor Inzender of eigenaar nodig om dit beleid te kunnen gebruiken. Als u deze machtigingen hebt, ziet u geen fouten.

![DINE-beleid herstellen](./media/network-watcher-builtin-policy/5_2_2_dine-remediation.png) 

4. Klik op controleren + maken om uw toewijzing te controleren. Dit lijkt op de volgende scherm afbeelding.

![DINE-beleids beoordeling](./media/network-watcher-builtin-policy/5_2_3_dine-review.png) 


### <a name="results"></a>Resultaten

Als u de resultaten wilt controleren, opent u het tabblad naleving en zoekt u naar de naam van uw toewijzing.
U ziet een item zoals de volgende scherm afbeelding wanneer het beleid wordt weer geven. Als uw beleid nog niet is uitgevoerd, wacht u enige tijd.

![Resultaten van DINE-beleid](./media/network-watcher-builtin-policy/7_2_dine-policy-results.png)  


## <a name="next-steps"></a>Volgende stappen 

-   In deze [zelf studie](./quickstart-configure-network-security-group-flow-logs-from-arm-template.md) gaat u dieper met arm-sjablonen voor het implementeren van stroom logboeken en Traffic Analytics.
-   Meer informatie over [Network Watcher](./index.yml)