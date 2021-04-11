---
title: 'Een Peering Service-verbinding registreren - Azure PowerShell '
description: In deze zelfstudie leert u hoe u een Peering Service-verbinding kunt registreren met PowerShell.
services: peering-service
author: derekolo
ms.service: peering-service
ms.topic: tutorial
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: ed4f628f6c3b888a640d61048c39f945a81901d7
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060836"
---
# <a name="tutorial-register-a-peering-service-connection-using-azure-powershell"></a>Zelfstudie: Een Peering Service-verbinding registreren met behulp van Azure PowerShell

In deze zelfstudie leert u hoe u Peering Service registreert met Azure PowerShell.

Azure Peering Service is een netwerkservice die de klantconnectiviteit verbetert met Microsoft-cloudservices, zoals Microsoft 365, Dynamics 365, SaaS-services (Software as a Service), Azure of een andere Microsoft-service die toegankelijk is via openbaar internet. In dit artikel leert u hoe u een Peering Service-verbinding kunt registreren met behulp van Azure PowerShell.

Als u nog geen Azure-abonnement hebt, maakt u nu een [account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u PowerShell lokaal wilt installeren en gebruiken, is voor deze quickstart Azure PowerShell-module versie 1.0.0 of hoger vereist. Voer `Get-Module -ListAvailable Az` uit om na te gaan welke versie er is geïnstalleerd. Zie [Azure PowerShell-module installeren](/powershell/azure/install-az-ps) voor informatie over de installatie en upgrades.

Tenslotte moet u, als u PowerShell lokaal uitvoert, ook `Connect-AzAccount` uitvoeren. Met deze opdracht wordt een verbinding met Azure gemaakt.

Gebruik de Azure PowerShell-module om Peering Service te registreren en te beheren. U kunt de Peering Service registreren of beheren via de PowerShell-opdrachtregel of in scripts.


## <a name="prerequisites"></a>Vereisten  
U moet het volgende hebben:

### <a name="azure-account"></a>Azure-account

U moet beschikken over een geldig en actief Microsoft Azure-account. Dit account is vereist voor het instellen van de Peering Service-verbinding. Peering Service is een resource binnen Azure-abonnementen.

### <a name="connectivity-provider"></a>Connectiveitsprovider

U kunt samenwerken met een internetprovider of een Internet Exchange-partner om Peering Service te verkrijgen om verbinding te maken tussen uw netwerk en het Microsoft-netwerk.

Zorg ervoor dat de connectiviteitsproviders een partnerschap hebben met Microsoft.

### <a name="register-a-subscription-with-the-resource-provider-and-feature-flag"></a>Een abonnement registreren bij de resourceprovider en functievlag

Voordat u verdergaat met de stappen voor het registreren van Peering Service, registreert u uw abonnement bij de resourceprovider en functievlag met behulp van Azure PowerShell. De Azure PowerShell-opdrachten worden hier opgegeven:

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AllowPeeringService ProviderNamespace Microsoft.Peering 

Register-AzResourceProvider -ProviderNamespace Microsoft.Peering 

```

### <a name="fetch-the-location-and-service-provider"></a>De locatie en serviceprovider ophalen 

Voer de volgende opdrachten uit in Azure PowerShell om de locatie en serviceprovider te verkrijgen waarmee de Peering Service moet worden ingeschakeld. 

Peering Service-locaties ophalen:

```azurepowershell-interactive
# Gets a list of available countries
Get-AzPeeringServiceCountry 
# Gets a list of metro locations serviced by country
Get-AzPeeringServiceLocation -Country "United States"
```

Peering Service-providers ophalen:
              
```azurepowershell-interactive
Get-AzPeeringServiceProvider
```

### <a name="register-the-peering-service-connection"></a>De Peering Service-verbinding registreren

Registreer de Peering Service-verbinding met behulp van de volgende reeks opdrachten via Azure PowerShell. In dit voorbeeld wordt de Peering Service met de naam myPeeringService geregistreerd.

```azurepowershell-interactive
$loc = "Washington"
$provider = "TestPeer1"
$resourceGroup = "MyResourceGroup"
$name = “myPeeringService”
$peeringService = New-AzPeeringService -ResourceGroupName $resourceGroup -Name $name -PeeringLocation $loc -PeeringServiceProvider $provider
```

### <a name="register-the-peering-service-prefix"></a>Het Peering Service-voorvoegsel registreren

Registreer het voorvoegsel dat wordt verschaft door de connectiviteitsprovider, door de volgende opdrachten uit te voeren via Azure PowerShell. In dit voorbeeld wordt het voorvoegsel met de naam myPrefix geregistreerd.

```azurepowershell-interactive
$loc = "Washington"
$provider = "TestPeer1"
$resourceGroup = "MyResourceGroup"
$name = “myPeeringService”
$peeringService = New-AzPeeringService -ResourceGroupName $resourceGroup -Name $name -PeeringLocation $loc -PeeringServiceProvider $provider
$prefixName = "myPrefix"
$prefix = “192.168.1.0/24”
$serviceKey = "6f48cdd6-2c2e-4722-af89-47e27b2513af"
$prefixService = $peeringService | New-AzPeeringServicePrefix -Name $prefixName -Prefix $prefix -ServiceKey $serviceKey
```

### <a name="list-all-the-peering-services-connections"></a>Alle Peering Services-verbindingen weergeven

Als u de lijst met alle Peering Services wilt weergeven, voert u de volgende opdracht uit:

```azurepowershell-interactive
$peeringService = Get-AzPeeringService
```

### <a name="list-all-the-peering-service-prefixes"></a>Alle Peering Services-voorvoegsels weergeven

Als u de lijst met alle Peering Services-voorvoegsels wilt weergeven, voert u de volgende opdracht uit:

```azurepowershell-interactive
 $prefixName = "myPrefix"
```

```azurepowershell-interactive
$prefix = Get-AzPeeringServicePrefix -PeeringServiceName "myPeeringService" -ResourceGroupName "MyResourceGroup" -Name "myPrefix"
```

### <a name="remove-the-peering-service-prefix"></a>Het Peering Service-voorvoegsel verwijderen

Als u het Peering Services-voorvoegsel wilt verwijderen, voert u de volgende opdracht uit:

```azurepowershell-interactive
Remove-AzPeeringServicePrefix -ResourceGroupName  "MyResourceGroup" -Name "myPrefix" -PeeringServiceName "myPeeringService"
```

## <a name="next-steps"></a>Volgende stappen

- Zie [Peering Service-verbinding](connection.md) voor meer informatie over de Peering Service-verbinding.
- Zie [Peering Service-verbindingstelemetrie](connection-telemetry.md) voor meer informatie over de Peering Service-verbindingstelemetrie.
- Zie [Een Peering Service-verbinding registreren - Azure-portal](azure-portal.md) als u de verbinding wilt registreren met behulp van de Azure-portal.
- Zie [Een Peering Service-verbinding registreren - Azure CLI ](cli.md) als u de verbinding wilt registreren met behulp van Azure CLI.
