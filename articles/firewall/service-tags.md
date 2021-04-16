---
title: Overzicht van Azure Firewall-servicetags
description: Een servicetag vertegenwoordigt een groep IP-adresvoorvoegsels die het maken van beveiligingsregel vereenvoudigt.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 4/5/2021
ms.author: victorh
ms.openlocfilehash: 3cc1e85a18eab1adb0a1dd8307a074cb43ba0c70
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529553"
---
# <a name="azure-firewall-service-tags"></a>Azure Firewall-servicetags

Een servicetag vertegenwoordigt een groep IP-adresvoorvoegsels die het maken van beveiligingsregel vereenvoudigt. U kunt niet uw eigen servicetag maken en ook niet opgeven welke IP-adressen zijn opgenomen in een tag. Microsoft beheert de adresvoorvoegsels die de servicetag omvat en werkt de servicetag automatisch bij wanneer adressen veranderen.

Azure Firewall-servicetags kunnen worden gebruikt in het veld Doel van netwerkregels. U kunt deze gebruiken in plaats van specifieke IP-adressen.

## <a name="supported-service-tags"></a>Ondersteunde servicetags

Zie [Servicetags voor virtuele netwerken](../virtual-network/service-tags-overview.md#available-service-tags) voor een lijst met servicetags die beschikbaar zijn voor gebruik in Azure Firewall-netwerkregels.

## <a name="configuration"></a>Configuratie

Azure Firewall ondersteunt de configuratie van servicetags via PowerShell, Azure CLI of de Azure Portal.

### <a name="configure-via-azure-powershell"></a>Configureren via Azure PowerShell

In dit voorbeeld moeten we eerst context krijgen voor onze eerder gemaakte Azure Firewall instantie.

```Get the context to an existing Azure Firewall
$FirewallName = "AzureFirewall"
$ResourceGroup = "AzureFirewall-RG"
$azfirewall = Get-AzFirewall -Name $FirewallName -ResourceGroupName $ResourceGroup
```

Vervolgens moeten we een nieuwe regel maken.  Voor bron of doel kunt u de tekstwaarde opgeven van de servicetag die u wilt gebruiken, zoals eerder in dit artikel is vermeld.

````Create new Network Rules using Service Tags
$rule = New-AzFirewallNetworkRule -Name "AllowSQL" -Description "Allow access to Azure Database as a Service (SQL, MySQL, PostgreSQL, Datawarehouse)" -SourceAddress "10.0.0.0/16" -DestinationAddress Sql -DestinationPort 1433 -Protocol TCP
$ruleCollection = New-AzFirewallNetworkRuleCollection -Name "Data Collection" -Priority 1000 -Rule $rule -ActionType Allow
````

Vervolgens moeten we de variabele met onze definitie Azure Firewall bijwerken met de nieuwe netwerkregels die we hebben gemaakt.

````Merge the new rules into our existing Azure Firewall variable
$azFirewall.NetworkRuleCollections.add($ruleCollection)
`````

Ten laatste moeten we de wijzigingen in de netwerkregel aanbrengen in het Azure Firewall exemplaar.
````Commit the changes to Azure
Set-AzFirewall -AzureFirewall $azfirewall
````

## <a name="next-steps"></a>Volgende stappen

Zie verwerkingslogica voor Azure Firewall regel voor Azure Firewall meer informatie [over de regels.](rule-processing.md)
