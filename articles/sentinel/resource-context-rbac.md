---
title: Toegang tot Azure-Sentinelgegevens beheren op resource | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u de toegang tot Azure-Sentinelgegevens kunt beheren door de resources waartoe een gebruiker toegang heeft. Door de toegang tot de resource te beheren, kunt u alleen toegang geven tot specifieke gegevens, zonder de volledige Azure-Sentinel-ervaring. Deze methode is ook bekend als resource-context RBAC.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: bagol
ms.openlocfilehash: 26124f8f650e1006244b4871e26962d417d90fd4
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054629"
---
# <a name="manage-access-to-azure-sentinel-data-by-resource"></a>Toegang tot Azure-Sentinelgegevens per resource beheren

Normaal gesp roken hebben gebruikers die toegang hebben tot een Azure Sentinel-werk ruimte ook toegang tot alle werkruimte gegevens, met inbegrip van de inhoud van de beveiliging. Beheerders kunnen [Azure-rollen](roles.md) gebruiken om toegang te configureren tot specifieke functies in azure Sentinel, afhankelijk van de toegangs vereisten in hun team.

Het is echter mogelijk dat u bepaalde gebruikers toegang wilt geven tot specifieke gegevens in uw Azure Sentinel-werk ruimte, maar geen toegang hoeven te hebben tot de hele Azure-Sentinel-omgeving. U kunt bijvoorbeeld een niet-SOC-team (non-Security Operations) opgeven met toegang tot de Windows-gebeurtenis gegevens voor de servers waarvan ze eigenaar zijn.

In dergelijke gevallen wordt u aangeraden om uw op rollen gebaseerd toegangs beheer (RBAC) te configureren op basis van de resources die zijn toegestaan voor uw gebruikers, in plaats van ze toegang te geven tot de Azure Sentinel-werk ruimte of specifieke Azure-Sentinel-functies. Deze methode is ook bekend als het instellen van **resource-context RBAC**.

Wanneer gebruikers toegang hebben tot gegevens van het Azure-verklikker netwerk via de resources waartoe ze toegang hebben in plaats van de Azure Sentinel-werk ruimte, kunnen ze logboeken en werkmappen weer geven met behulp van de volgende methoden:

- **Via de resource zelf**, zoals een virtuele machine van Azure. Gebruik deze methode om logboeken en werkmappen alleen voor een specifieke resource weer te geven.

- **Via Azure monitor**. Gebruik deze methode als u query's wilt maken die meerdere resources en/of resource groepen omvatten. Wanneer u naar Logboeken en werkmappen in Azure Monitor navigeert, definieert u uw bereik voor een of meer specifieke resource groepen of resources.

Resource-context RBAC inschakelen in Azure Monitor. Zie [toegang tot logboek gegevens en werk ruimten in azure monitor beheren](/azure/azure-monitor/logs/manage-access)voor meer informatie.

> [!NOTE]
> Als uw gegevens geen Azure-resource zijn, zoals syslog-, CEF-of AAD-gegevens, of gegevens die door een aangepaste Collector zijn verzameld, moet u de resource-ID die wordt gebruikt om de gegevens te identificeren en toegang inschakelen, hand matig configureren.
>
> Zie voor meer informatie [expliciet resource-context RBAC configureren](#explicitly-configure-resource-context-rbac).
>
## <a name="scenarios-for-resource-context-rbac"></a>Scenario's voor resource-context RBAC

De volgende tabel bevat de scenario's waarin de resource context RBAC het handigst is. Let op de verschillen in de toegangs vereisten tussen SOC teams en niet-SOC-teams.

| Type vereiste |SOC-team  |Niet-SOC-team  |
|---------|---------|---------|
|**Machtigingen**     | De hele werk ruimte        |   Alleen specifieke resources      |
|**Gegevens toegang**     |  Alle gegevens in de werk ruimte       | Alleen gegevens voor bronnen waartoe het team toegang heeft        |
|**Ervaring**     |  De volledige Azure-Sentinel-ervaring, mogelijk beperkt door de [functionele machtigingen](roles.md) die aan de gebruiker zijn toegewezen       |  Alleen query's en werkmappen vastleggen       |
|     |         |         |

Als uw team vergelijk bare toegangs vereisten heeft voor het niet-SOC-team dat is beschreven in de bovenstaande tabel, is de resource context RBAC mogelijk een goede oplossing voor uw organisatie.

## <a name="alternative-methods-for-implementing-resource-context-rbac"></a>Alternatieve methoden voor het implementeren van resource context RBAC

Afhankelijk van de machtigingen die zijn vereist in uw organisatie, biedt het gebruik van resource-context RBAC mogelijk geen volledige oplossing.

De volgende lijst bevat scenario's waarin andere oplossingen voor gegevens toegang kunnen voldoen aan uw vereisten:

|Scenario  |Oplossing  |
|---------|---------|
|**Een dochter onderneming heeft een SOC-team dat een volledige Azure-Sentinel-ervaring vereist**.     |  Gebruik in dit geval een architectuur met meerdere werk ruimten om uw gegevens machtigingen te scheiden. <br><br>Zie voor meer informatie: <br>- [Azure-Sentinel uitbreiden in werk ruimten en tenants](extend-sentinel-across-workspaces-tenants.md)<br>    - [Werk met incidenten in veel werk ruimten tegelijk](multiple-workspace-view.md)          |
|**U toegang wilt bieden tot een specifiek type gebeurtenis**.     |  Geef bijvoorbeeld een Windows-beheerder met toegang tot Windows-beveiligings gebeurtenissen in alle systemen. <br><br>In dergelijke gevallen gebruikt u [RBAC op tabel niveau](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043) om machtigingen voor elke tabel te definiëren.       |
| **Beperk de toegang tot een gedetailleerdere niveau, niet op basis van de resource, of alleen voor een subset van de velden in een gebeurtenis**   |   Stel dat u de toegang tot Office 365-logboeken wilt beperken op basis van de dochter van een gebruiker. <br><br>In dit geval geeft u toegang tot gegevens met behulp van ingebouwde integratie met [Power bi Dash boards en rapporten](/azure/azure-monitor/platform/powerbi).      |
| | |

## <a name="explicitly-configure-resource-context-rbac"></a>Resource context RBAC expliciet configureren

Gebruik de volgende stappen als u resource-context RBAC wilt configureren, maar uw gegevens zijn geen Azure-resource.

Gegevens in uw Azure-Sentinel-werk ruimte die geen Azure-resources zijn, zijn bijvoorbeeld syslog-, CEF-of AAD-gegevens, of gegevens die door een aangepaste Collector zijn verzameld.

De **resource context RBAC expliciet configureren**:

1. Zorg ervoor dat u [resource-context RBAC hebt ingeschakeld](/azure/azure-monitor/platform/manage-access) in azure monitor. 

1. [Maak een resource groep](/azure/azure-resource-manager/management/manage-resource-groups-portal) voor elk team van gebruikers die toegang nodig hebben tot uw resources zonder de volledige Azure-Sentinel-omgeving.

    Wijs [machtigingen voor logboek lezers](/azure/azure-monitor/platform/manage-access#resource-permissions) toe voor elk van de team leden.

1. Wijs resources toe aan de resource team groepen die u hebt gemaakt en codeer gebeurtenissen met de relevante resource-Id's.

    Wanneer Azure-resources gegevens verzenden naar Azure Sentinel, worden de records van het logboek automatisch gelabeld met de resource-ID van de gegevens bron.

    > [!TIP]
    > We raden u aan om de resources die u toegang verleent, te groeperen voor een specifieke resource groep die voor het doel einde is gemaakt.
    >
    > Als dat niet het geval is, moet u ervoor zorgen dat uw team rechtstreeks toegang heeft tot de resources die u wilt.
    >

    Zie voor meer informatie over resource-Id's:

    - [Resource-Id's met Logboeken door sturen](#resource-ids-with-log-forwarding)
    - [Resource-Id's met Logstash-verzameling](#resource-ids-with-logstash-collection)
    - [Resource-Id's met de Log Analytics-API-verzameling](#resource-ids-with-the-log-analytics-api-collection)

### <a name="resource-ids-with-log-forwarding"></a>Resource-Id's met Logboeken door sturen

Wanneer gebeurtenissen worden verzameld met [CEF (common Event Format)](connect-common-event-format.md) of [syslog](connect-syslog.md), wordt het door sturen van Logboeken gebruikt voor het verzamelen van gebeurtenissen van meerdere bron systemen.

Wanneer bijvoorbeeld een CEF of syslog die een VM doorstuurt, luistert naar de bronnen die syslog-gebeurtenissen verzenden en deze naar Azure Sentinel door sturen, wordt de resource-ID voor het door sturen van de logboek registratie toegewezen aan alle gebeurtenissen die worden doorgestuurd.

Als u meerdere teams hebt, moet u ervoor zorgen dat u een afzonderlijke virtuele machine voor het door sturen van Logboeken hebt voor het verwerken van de gebeurtenissen voor elk afzonderlijk team.

Als u bijvoorbeeld uw Vm's scheidt, zorgt u ervoor dat syslog-gebeurtenissen die deel uitmaken van team A worden verzameld met behulp van de Collector-VM A.

> [!TIP]
> - Wanneer u een on-premises virtuele machine of een andere Cloud-VM gebruikt, zoals AWS, als uw logboek doorstuur server, moet u ervoor zorgen dat deze een resource-ID heeft door [Azure Arc](/azure/azure-arc/servers/overview)te implementeren.
> - Als u de VM-omgeving voor het door sturen van logboeken wilt schalen, kunt u een [VM-schaalset](https://techcommunity.microsoft.com/t5/azure-sentinel/scaling-up-syslog-cef-collection/ba-p/1185854) maken om uw CEF-en SYLOG-logboeken te


### <a name="resource-ids-with-logstash-collection"></a>Resource-Id's met Logstash-verzameling

Als u uw gegevens verzamelt met de Azure Sentinel [Logstash-uitvoer-invoeg toepassing](connect-logstash.md), gebruikt u het veld **azure_resource_id** om uw aangepaste Collector zo te configureren dat de resource-id in uw uitvoer wordt vermeld.

Gebruik de resource-ID van de resource groep die u hebt [gemaakt voor uw gebruikers](#explicitly-configure-resource-context-rbac)als u gebruikmaakt van resource-context RBAC en wilt dat de gebeurtenissen die door de API worden verzameld, beschikbaar zijn voor specifieke gebruikers.

De volgende code toont bijvoorbeeld een voor beeld van een Logstash-configuratie bestand:

``` ruby
 input {
     beats {
         port => "5044"
     }
 }
 filter {
 }
 output {
     microsoft-logstash-output-azure-loganalytics {
       workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
       workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
       custom_log_table_name => "tableName"
       azure_resource_id => "/subscriptions/wvvu95a2-99u4-uanb-hlbg-2vatvgqtyk7b/resourceGroups/contosotest" # <your resource ID>   
     }
 }
```

> [!TIP]
> Mogelijk wilt u meerdere secties toevoegen `output` om onderscheid te maken tussen de labels die worden toegepast op verschillende gebeurtenissen.
>
### <a name="resource-ids-with-the-log-analytics-api-collection"></a>Resource-Id's met de Log Analytics-API-verzameling

Bij het verzamelen met behulp van de [log Analytics Data Collector-API](/azure/azure-monitor/platform/data-collector-api)kunt u gebeurtenissen met een resource-id toewijzen met behulp van de http [*x-MS-AzureResourceId-*](/azure/azure-monitor/platform/data-collector-api#request-headers) aanvraag header.

Gebruik de resource-ID van de resource groep die u hebt [gemaakt voor uw gebruikers](#explicitly-configure-resource-context-rbac)als u gebruikmaakt van resource-context RBAC en wilt dat de gebeurtenissen die door de API worden verzameld, beschikbaar zijn voor specifieke gebruikers.


## <a name="next-steps"></a>Volgende stappen

Zie [machtigingen in azure Sentinel](roles.md)voor meer informatie.
