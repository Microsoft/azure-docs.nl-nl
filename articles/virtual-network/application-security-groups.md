---
title: Overzicht van Azure-toepassings beveiligings groepen
titlesuffix: Azure Virtual Network
description: Meer informatie over het gebruik van toepassings beveiligings groepen.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 3542ae2e94c2fa3d3e9d6100738b2aabded94d15
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005262"
---
# <a name="application-security-groups"></a>Toepassingsbeveiligingsgroepen

Met behulp van toepassingsbeveiligingsgroepen kunt u netwerkbeveiliging configureren als een natuurlijk verlengstuk van de structuur van een toepassing, waarbij u virtuele machines kunt groeperen en netwerkbeveiligingsbeleid kunt definiëren op basis van die groepen. U kunt het beveiligingsbeleid op grote schaal opnieuw gebruiken zonder handmatig onderhoud van expliciete IP-adressen. Het platform verwerkt de complexiteit van expliciete IP-adressen en meerdere regelsets, zodat u zich kunt richten op uw bedrijfslogica. Kijk eens naar de volgende voorbeelden voor een beter begrip van toepassingsbeveiligingsgroepen:

![Toepassingsbeveiligingsgroepen](./media/security-groups/application-security-groups.png)

In de vorige afbeelding zijn *NIC1* en *NIC2* leden van de toepassingsbeveiligingsgroep *AsgWeb*. *NIC3* is een lid van de toepassingsbeveiligingsgroep *AsgLogic*. *NIC4* is een lid van de toepassingsbeveiligingsgroep *AsgDb*. Hoewel elke netwerk interface in dit voor beeld lid is van slechts één netwerk beveiligings groep, kan een netwerk interface lid zijn van meerdere toepassings beveiligings groepen, tot aan de [Azure-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Aan geen van de netwerkinterfaces is een netwerkbeveiligingsgroep gekoppeld. *NSG1* is gekoppeld aan beide subnetten en bevat de volgende regels:

## <a name="allow-http-inbound-internet"></a>Allow-HTTP-Inbound-Internet

Deze regel is vereist om verkeer van internet naar de webservers te laten lopen. Binnenkomend verkeer van internet wordt geweigerd door de standaardbeveiligingsregel **DenyAllInbound**. Daarom is er geen extra regel nodig voor de toepassingsbeveiligingsgroepen *AsgLogic* of *AsgDb*.

|Prioriteit|Bron|Bronpoorten| Doel | Doelpoorten | Protocol | Access |
|---|---|---|---|---|---|---|
| 100 | Internet | * | AsgWeb | 80 | TCP | Toestaan |

## <a name="deny-database-all"></a>Deny-Database-All

De standaardbeveiligingsregel **AllowVNetInBound** staat communicatie toe tussen resources in hetzelfde virtuele netwerk. Daarom is deze regel vereist voor het weigeren van verkeer dat van een willekeurige resource afkomstig is.

|Prioriteit|Bron|Bronpoorten| Doel | Doelpoorten | Protocol | Access |
|---|---|---|---|---|---|---|
| 120 | * | * | AsgDb | 1433 | Alle | Weigeren |

## <a name="allow-database-businesslogic"></a>Allow-Database-BusinessLogic

Deze regel staat verkeer toe van de toepassingsbeveiligingsgroep *AsgLogic* naar de toepassingsbeveiligingsgroep *AsgDb*. De prioriteit voor deze regel is hoger dan de prioriteit voor de regel *Deny-Database-All*. Als gevolg hiervan wordt deze regel verwerkt vóór de regel *Deny-Database-All*, zodat verkeer van de toepassingsbeveiligingsgroep *AsgLogic* wordt toegestaan, terwijl al het andere verkeer wordt geblokkeerd.

|Prioriteit|Bron|Bronpoorten| Doel | Doelpoorten | Protocol | Access |
|---|---|---|---|---|---|---|
| 110 | AsgLogic | * | AsgDb | 1433 | TCP | Toestaan |

De regels die een toepassingsbeveiligingsgroep als bron of doel opgeven, worden alleen toegepast op netwerkinterfaces die lid van de toepassingsbeveiligingsgroep. Als de netwerkinterface geen lid is van een toepassingsbeveiligingsgroep, wordt de regel niet toegepast op de netwerkinterface, ook niet als de netwerkbeveiligingsgroep aan het subnet is gekoppeld.

Toepassingsbeveiligingsgroepen hebben de volgende beperkingen:

-    Er gelden beperkingen voor het aantal toepassingsbeveiligingsgroepen dat u met een abonnement kunt hebben. Er gelden ook andere beperkingen met betrekking tot toepassingsbeveiligingsgroepen. Zie [Netwerkenlimieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) voor meer informatie.
- U kunt in een beveiligingsregel een en dezelfde toepassingsbeveiligingsgroep opgeven als de bron en het doel. Het is niet mogelijk om meerdere toepassingsbeveiligingsgroepen in de bron of het doel op te geven.
- Alle netwerkinterfaces die zijn toegewezen aan een toepassingsbeveiligingsgroep, moeten zich in hetzelfde virtuele netwerk bevinden als de eerste netwerkinterface die aan de toepassingsbeveiligingsgroep is toegewezen. Bijvoorbeeld, als de eerste netwerkinterface die aan een toepassingsbeveiligingsgroep met de naam *AsgWeb* is toegewezen, zich in het virtuele netwerk met de naam *VNet1* bevindt, moeten alle volgende netwerkinterfaces die zijn toegewezen aan *AsgWeb*, aanwezig zijn in *VNet1*. U kunt geen netwerkinterfaces van verschillende virtuele netwerken toevoegen aan dezelfde toepassingsbeveiligingsgroep.
- Als u in een beveiligingsregel een toepassingsbeveiligingsgroep als bron en doel opgeeft, moeten de netwerkinterfaces in beide toepassingsbeveiligingsgroepen zich in hetzelfde virtuele netwerk bevinden. Als *AsgLogic* bijvoorbeeld netwerkinterfaces van *VNet1* bevat en *AsgDb* netwerkinterfaces van *VNet2*, is het niet mogelijk om in een regel *AsgLogic* toe te wijzen als bron en *AsgDb* als doel. Alle netwerkinterfaces voor zowel de bron- als de doeltoepassingsbeveiligingsgroepen moeten aanwezig zijn in hetzelfde virtuele netwerk.

> [!TIP]
> Als u het gewenste aantal beveiligingsregels wilt verminderen en tevens het wijzigen van de regels wilt beperken, plant u de toepassingsbeveiligingsgroepen die u nodig hebt en maakt u, indien mogelijk, regels met behulp van servicetags of toepassingsbeveiligingsgroepen in plaats van afzonderlijke IP-adressen of -bereiken.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Een netwerkbeveiligingsgroep maken](tutorial-filter-network-traffic.md).
