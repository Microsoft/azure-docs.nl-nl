---
title: Intellectueel eigendom van beheerde Security service provider (MSSPs) beveiligen in azure Sentinel | Microsoft Docs
description: Meer informatie over hoe beheerde Security service providers (MSSPs) de intellectuele eigendom kunnen beveiligen die ze in azure Sentinel hebben gemaakt.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.assetid: 10cce91a-421b-4959-acdf-7177d261f6f2
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2021
ms.author: bagol
ms.openlocfilehash: 1c15c341d85fae667ee23883043350340ad866b8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315448"
---
# <a name="protecting-mssp-intellectual-property-in-azure-sentinel"></a>MSSP intellectueel eigendom beschermen in azure Sentinel

In dit artikel worden de methoden beschreven waarmee beheerde MSSPs (Security service providers) kunnen worden gebruikt voor het beveiligen van intellectueel eigendom die ze in azure Sentinel hebben ontwikkeld, zoals Azure Sentinel Analytics-regels, jacht-query's, playbooks en werkmappen.

De methode die u kiest, is afhankelijk van hoe elk van uw klanten Azure kopen; of u fungeert als een [Cloud solutions provider (CSP)](#cloud-solutions-providers-csp)of de klant beschikt over een [Enterprise overeenkomst (EA)/pay-as-you-go (PAYG)](#enterprise-agreements-ea--pay-as-you-go-payg) -account. De volgende secties beschrijven elk van deze methoden afzonderlijk.

## <a name="cloud-solutions-providers-csp"></a>Cloud Solutions providers (CSP)

Als u Azure wilt verkopen als een Cloud solutions provider (CSP), beheert u het Azure-abonnement van de klant. Dankzij de [beheerder (administrate)](/partner-center/azure-plan-manage)worden gebruikers in de groep beheerders agenten van uw MSSP-Tenant verleend met de eigenaar toegang tot het Azure-abonnement van de klant. de klant heeft standaard geen toegang.

Als andere gebruikers van de MSSP-Tenant, buiten de groep beheerders agenten, toegang moeten hebben tot de gebruikers omgeving, raden we u aan [Azure Lighthouse](multiple-tenants-service-providers.md)te gebruiken. Met Azure Lighthouse kunt u gebruikers of groepen toegang verlenen tot een specifiek bereik, zoals een resource groep of abonnement, met behulp van een van de ingebouwde rollen.

Als u klanten gebruikers toegang wilt geven tot de Azure-omgeving, raden we u aan deze toegang te verlenen op het niveau van de *resource groep* , in plaats van het hele abonnement, zodat u de onderdelen van de omgeving kunt weer geven of verbergen als dat nodig is.

Bijvoorbeeld:

- U kunt de klant toegang verlenen tot verschillende resource groepen waar hun toepassingen zich bevinden, maar toch de Azure Sentinel-werk ruimte in een afzonderlijke resource groep behouden, waarbij de klant geen toegang heeft.

- Gebruik deze methode om klanten de mogelijkheid te bieden om geselecteerde werkmappen en playbooks weer te geven. Dit zijn afzonderlijke resources die zich in hun eigen resource groep kunnen bevinden.

Zelfs met het verlenen van toegang op het niveau van de resource groep, kunnen klanten nog steeds toegang hebben tot logboek gegevens voor de resources waartoe ze toegang hebben, zoals Logboeken van een virtuele machine, zelfs zonder toegang tot Azure Sentinel. Zie [toegang tot Azure Sentinel-gegevens beheren per resource](resource-context-rbac.md)voor meer informatie.

> [!TIP]
> Als u uw klanten toegang wilt bieden tot het hele abonnement, kunt u de richt lijnen in [Enter prise Agreements (EA)/betalen naar gebruik (PAYG)](#enterprise-agreements-ea--pay-as-you-go-payg)bekijken.
>

### <a name="sample-azure-sentinel-csp-architecture"></a>Voor beeld van Azure Sentinel CSP-architectuur

In de volgende afbeelding wordt beschreven hoe de machtigingen die in de [vorige sectie](#cloud-solutions-providers-csp) worden beschreven, kunnen werken wanneer ze toegang bieden tot CSP-klanten:

:::image type="content" source="media/mssp-protect-intellectual-property/csp-customers.png" alt-text="Bescherm uw eigen eigendom van Azure-Sentinel met CSP-klanten.":::

In deze afbeelding:

- De gebruikers **die toegang krijgen tot het** CSP-abonnement, zijn de gebruikers in de groep beheerders agenten in de MSSP Azure AD-Tenant.
- Andere groepen van de MSSP krijgen toegang tot de klant omgeving via Azure Lighthouse.
- Klant toegang tot Azure-resources wordt beheerd door Azure RBAC op het niveau van de resource groep.

    Hierdoor kan MSSPs de Azure Sentinel-onderdelen zo nodig verbergen, zoals analyse regels en jacht-Query's.

Zie de [documentatie van Azure Lighthouse](/azure/lighthouse/concepts/cloud-solution-provider)voor meer informatie.

## <a name="enterprise-agreements-ea--pay-as-you-go-payg"></a>Enter prise Agreements (EA)/betalen naar gebruik (PAYG)

Als uw klant rechtstreeks van micro soft koopt, heeft de klant al volledige toegang tot de Azure-omgeving en kunt u niets verbergen in het Azure-abonnement van de klant.

Bescherm in plaats daarvan uw intellectuele eigendom die u als volgt hebt ontwikkeld in azure Sentinel, afhankelijk van het type resource dat u wilt beveiligen:

### <a name="analytics-rules-and-hunting-queries"></a>Analyse regels en jacht-query's

Analyse regels en jacht-query's zijn beide opgenomen in azure Sentinel en kunnen daarom niet worden gescheiden van de Azure Sentinel-werk ruimte.

Zelfs als een gebruiker toegang heeft tot de machtigingen van Azure Sentinel Reader, kunnen ze de query nog steeds weer geven. In dit geval raden we u aan om uw analyse regels te hosten en query's te doorzoeken in uw eigen MSSP-Tenant, in plaats van de Tenant van de klant.

Hiervoor hebt u een werk ruimte in uw eigen Tenant nodig waarop Azure Sentinel is ingeschakeld. ook moet u de werk ruimte van de klant bekijken via [Azure Lighthouse](multiple-tenants-service-providers.md).

Als u een analytische regel of een query in de MSSP-Tenant wilt maken die verwijst naar gegevens in de Tenant van de klant, moet u de `workspace` instructie als volgt gebruiken:

```kql
workspace('<customer-workspace>').SecurityEvent
| where EventID == ‘4625’
```

`workspace`Houd rekening met het volgende wanneer u een instructie toevoegt aan uw Analytics-regels:

- **Er zijn geen waarschuwingen in de werk ruimte van de klant**. Regels die op deze manier zijn gemaakt, maken geen waarschuwingen of incidenten in de werk ruimte van de klant. Zowel waarschuwingen als incidenten bevinden zich alleen in uw MSSP-werk ruimte.

- **Maak afzonderlijke waarschuwingen voor elke klant**. Wanneer u deze methode gebruikt, wordt u ook aangeraden afzonderlijke waarschuwings regels voor elke klant en detectie te gebruiken, aangezien de werkruimte instructie in elk geval verschillend is.

    U kunt de naam van de klant toevoegen aan de naam van de waarschuwings regel om de klant waar de waarschuwing wordt geactiveerd, gemakkelijk te identificeren. Afzonderlijke waarschuwingen kunnen leiden tot een groot aantal regels, die u mogelijk wilt beheren met behulp van scripts of [Azure Sentinel als code](https://techcommunity.microsoft.com/t5/azure-sentinel/deploying-and-managing-azure-sentinel-as-code/ba-p/1131928).

    Bijvoorbeeld:

    :::image type="content" source="media/mssp-protect-intellectual-property/mssp-rules-per-customer.png" alt-text="Maak afzonderlijke regels in uw MSSP-werk ruimte voor elke klant.":::

- **Maak afzonderlijke MSSP-werk ruimten voor elke klant**. Het maken van afzonderlijke regels voor elke klant en detectie kan ertoe leiden dat u het maximum aantal analyse regels voor uw werk ruimte bereikt (512). Als u veel klanten hebt en deze limiet verwacht te bereiken, wilt u mogelijk een afzonderlijke MSSP-werk ruimte maken voor elke klant.

    Bijvoorbeeld:

    :::image type="content" source="media/mssp-protect-intellectual-property/mssp-rules-and-workspace-per-customer.png" alt-text="Maak een werk ruimte en regels in uw MSSP-Tenant voor elke klant.":::

> [!IMPORTANT]
> De sleutel voor het gebruik van deze methode maakt gebruik van automatisering voor het beheren van een grote set regels in uw werk ruimten.
>
> Zie voor meer informatie [regels voor cross-Workspace Analytics](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-cross-workspace-analytics-rules/ba-p/1664211)
>

### <a name="workbooks"></a>Werkmappen

Als u een Azure Sentinel-werkmap hebt ontwikkeld die u niet wilt dat uw klant deze kopieert, host u de werkmap in uw MSSP-Tenant. Zorg ervoor dat u toegang hebt tot uw Customer-werk ruimten via Azure Lighthouse en zorg ervoor dat u de werkmap wijzigt om deze werk ruimten van de klant te gebruiken.

Bijvoorbeeld:

:::image type="content" source="media/mssp-protect-intellectual-property/cross-workspace-workbook.png" alt-text="Werkmappen in meerdere werk ruimten":::

Zie [werkmappen in meerdere werk ruimten](extend-sentinel-across-workspaces-tenants.md#cross-workspace-workbooks)voor meer informatie.

Als u wilt dat de klant de werkmap visualisaties kan bekijken, terwijl het code geheim blijft behouden, kunt u het beste de werkmap exporteren naar Power BI.

Uw werkmap exporteren naar Power BI:

- **Maakt de visualisaties van de werkmap gemakkelijker te delen**. U kunt de klant een koppeling sturen naar het dash board van Power BI, waar de gerapporteerde gegevens kunnen worden weer gegeven, zonder dat hiervoor toegangs rechten voor Azure zijn vereist.
- **Hiermee wordt de planning ingeschakeld**. Configureer Power BI voor het periodiek verzenden van e-mail berichten die een moment opname van het dash board voor die tijd bevatten.

Zie [Azure monitor logboek gegevens importeren in Power bi](/azure/azure-monitor/visualize/powerbi)voor meer informatie.

### <a name="playbooks"></a>Playbooks

U kunt uw playbooks als volgt beveiligen, afhankelijk van waar de analytische regels die de Playbook activeren zijn gemaakt:

- **Analytics-regels die zijn gemaakt in de MSSP-werk ruimte**.  Zorg ervoor dat u uw playbooks maakt in de MSSP-Tenant en dat u alle incident-en waarschuwings gegevens ophaalt uit de werk ruimte MSSP. U kunt de playbooks koppelen wanneer u een nieuwe regel maakt in uw werk ruimte.

    Bijvoorbeeld:

    :::image type="content" source="media/mssp-protect-intellectual-property/rules-in-mssp-workspace.png" alt-text="Regels die zijn gemaakt in de MSSP-werk ruimte.":::

- **Analytics-regels die zijn gemaakt in de werk ruimte van de klant**. Gebruik Azure Lighthouse om Analytics-regels van de werk ruimte van de klant te koppelen aan een Playbook die wordt gehost in uw MSSP-werk ruimte. In dit geval haalt de Playbook de waarschuwings-en incident gegevens en eventuele andere klant gegevens uit de werk ruimte van de klant.

    Bijvoorbeeld:

    :::image type="content" source="media/mssp-protect-intellectual-property/rules-in-customer-workspace.png" alt-text="Regels die zijn gemaakt in de werk ruimte van de klant.":::

In beide gevallen moet u, als de Playbook toegang nodig heeft tot de Azure-omgeving van de klant, een gebruiker of Service-Principal gebruiken die toegang heeft via Lighthouse.

Als de Playbook echter toegang nodig heeft tot niet-Azure-resources in de Tenant van de klant, zoals Azure AD, Office 365 of Microsoft 365 Defender, moet u een service-principal maken met de juiste machtigingen in de Tenant van de klant en vervolgens die identiteit toevoegen in de Playbook.

> [!NOTE]
> Als u Automation-regels gebruikt in combi natie met uw playbooks, moet u de machtigingen voor de Automation-regel instellen voor de resource groep waar de playbooks woont.
> Zie [machtigingen voor Automation-regels voor het uitvoeren van playbooks](automate-incident-handling-with-automation-rules.md#permissions-for-automation-rules-to-run-playbooks)voor meer informatie.
>

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie:

- [Azure Sentinel Technical Playbook voor MSSPs](https://cloudpartners.transform.microsoft.com/download?assetname=assets/Azure-Sentinel-Technical-Playbook-for-MSSPs.pdf&download=1)
- [Meerdere tenants beheren in azure Sentinel als een MSSP](multiple-tenants-service-providers.md)
- [Azure-Sentinel uitbreiden in werkruimten en tenants](extend-sentinel-across-workspaces-tenants.md)
- [Zelfstudie: Uw gegevens visualiseren en bewaken](tutorial-monitor-your-data.md)
- [Zelfstudie: Geautomatiseerde bedreigingsreacties instellen in Azure Sentinel](tutorial-respond-threats-playbook.md)
