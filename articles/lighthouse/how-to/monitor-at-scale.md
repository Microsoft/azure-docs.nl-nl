---
title: Gedelegeerde resources op schaal controleren
description: Meer informatie over het effectief gebruiken van Azure Monitor-logboeken op schaal bare wijze over de tenants van de klant die u beheert.
ms.date: 10/26/2020
ms.topic: how-to
ms.openlocfilehash: 96ca05faf2b3da8f214c14ae57eb186c7b71e1b3
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461524"
---
# <a name="monitor-delegated-resources-at-scale"></a>Gedelegeerde resources op schaal controleren

Als service provider hebt u mogelijk meerdere tenants voor klanten in [Azure Lighthouse](../overview.md). Met Azure Lighthouse kunnen service providers bewerkingen op verschillende tijdstippen in meerdere tenants tegelijk uitvoeren, waardoor beheer taken efficiënter zijn.

In dit onderwerp wordt beschreven hoe u [Azure monitor-logboeken](../../azure-monitor/platform/data-platform-logs.md) op schaal bare wijze kunt gebruiken voor de tenants van de klant die u beheert. Hoewel we in dit onderwerp naar service providers en klanten verwijzen, is deze richt lijn ook van toepassing op [ondernemingen die Azure Lighthouse gebruiken om meerdere tenants te beheren](../concepts/enterprise.md).

> [!NOTE]
> Zorg ervoor dat gebruikers in uw tenants voor beheer de [benodigde rollen hebben gekregen voor het beheren van log Analytics-werk ruimten](../../azure-monitor/platform/manage-access.md#manage-access-using-azure-permissions) op uw gedelegeerde klant abonnementen.

## <a name="create-log-analytics-workspaces"></a>Log Analytics-werk ruimten maken

Als u gegevens wilt verzamelen, moet u Log Analytics-werk ruimten maken. Deze Log Analytics-werk ruimten zijn unieke omgevingen voor gegevens die worden verzameld door Azure Monitor. Elke werk ruimte heeft een eigen gegevens opslagplaats en-configuratie, en gegevens bronnen en-oplossingen zijn geconfigureerd om hun gegevens op te slaan in een bepaalde werk ruimte.

We raden u aan deze werk ruimten rechtstreeks te maken in de tenants van de klant. Op deze manier blijven hun gegevens in hun tenants, in plaats van dat ze naar de andere worden geëxporteerd. Dit biedt ook gecentraliseerde bewaking van alle resources of services die door Log Analytics worden ondersteund, waardoor u meer flexibiliteit hebt in welke typen gegevens u kunt bewaken.

U kunt een Log Analytics-werk ruimte maken met behulp van de [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md), met behulp van [Azure cli](../../azure-monitor/learn/quick-create-workspace-cli.md)of met behulp van [Azure PowerShell](../../azure-monitor/platform/powershell-workspace-configuration.md).

> [!IMPORTANT]
> Zelfs als alle werk ruimten in de Tenant van de klant zijn gemaakt, moet de resource provider micro soft. Insights ook zijn geregistreerd bij een abonnement in de beheer Tenant.

## <a name="deploy-policies-that-log-data"></a>Beleid implementeren waarmee gegevens worden geregistreerd

Nadat u uw Log Analytics-werk ruimten hebt gemaakt, kunt u [Azure Policy](../../governance/policy/index.yml) implementeren in uw klant hiërarchieën zodat diagnostische gegevens worden verzonden naar de juiste werk ruimte in elke Tenant. De exacte beleids regels die u implementeert, kunnen variëren, afhankelijk van de resource typen die u wilt bewaken.

Zie [zelf studie: beleid maken en beheren om naleving](../../governance/policy/tutorials/create-and-manage.md)af te dwingen voor meer informatie over het maken van beleid. Dit [hulp programma](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/azure-diagnostics-policy-generator) van de Community biedt een script waarmee u beleids regels kunt maken voor het bewaken van de specifieke resource typen die u kiest.

Wanneer u hebt vastgesteld welk beleid u wilt implementeren, kunt u [ze op schaal implementeren op uw gedelegeerde abonnementen](policy-at-scale.md).

## <a name="analyze-the-gathered-data"></a>De verzamelde gegevens analyseren

Nadat u uw beleid hebt geïmplementeerd, worden de gegevens vastgelegd in de Log Analytics werk ruimten die u hebt gemaakt in elke Tenant van de klant. Als u inzicht wilt krijgen in alle beheerde klanten, kunt u gebruikmaken van hulpprogram ma's zoals [Azure monitor werkmappen](../../azure-monitor/platform/workbooks-overview.md) voor het verzamelen en analyseren van gegevens uit meerdere gegevens bronnen. 

## <a name="next-steps"></a>Volgende stappen

- Verken deze door [MVP gemaakte voorbeeld werkmap](https://github.com/scautomation/Azure-Automation-Update-Management-Workbooks), die de compatibiliteits rapportage voor patches bijhoudt door [updatebeheer logboeken te doorzoeken](../../automation/update-management/query-logs.md) op meerdere log Analytics-werk ruimten. 
- Meer informatie over [Azure monitor](../../azure-monitor/index.yml).
- Meer informatie over [Azure monitor-logboeken](../../azure-monitor/platform/data-platform-logs.md).
- Meer informatie over [beheerervaring in meerdere tenants](../concepts/cross-tenant-management-experience.md).