---
title: Azure PowerShell-voorbeeldscript - Webverkeer beheren | Microsoft Docs
description: Azure PowerShell-voorbeeldscript - Webverkeer beheren met een toepassingsgateway en een virtuele-machineschaalset.
services: application-gateway
documentationcenter: networking
author: vhorne
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 01/29/2018
ms.author: victorh
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: a4f1961f9c7d79a95a0e8f4cc3fc18bdeac2f36f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89078704"
---
# <a name="manage-web-traffic-with-azure-powershell"></a>Webverkeer beheren met Azure PowerShell

Met dit script maakt u een toepassingsgateway die gebruikmaakt van een virtuele-machineschaalset die is ingesteld voor back-endservers. De toepassingsgateway kan vervolgens worden geconfigureerd om webverkeer te beheren. Nadat het script is uitgevoerd, kunt u de toepassingsgateway testen met behulp van het openbare IP-adres.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/application-gateway/create-vmss/create-vmss.ps1 "Create application gateway")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie 

Gebruik de volgende opdracht om de resourcegroep, de toepassingsgateway en alle gerelateerde resources te verwijderen.

```powershell
Remove-AzResourceGroup -Name myResourceGroupAG
```

## <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt om de implementatie te maken. Elk item in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee maakt u een resourcegroep waarin alle resources worden opgeslagen. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Hiermee maakt u de subnetconfiguratie. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Hiermee maakt u het virtuele netwerk met de subnetconfiguraties. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Hiermee maakt u het openbare IP-adres voor de toepassingsgateway. |
| [New-AzApplicationGatewayIPConfiguration](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) | Hiermee maakt u de configuratie die een subnet koppelt aan de toepassingsgateway. |
| [New-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) | Hiermee maakt u de configuratie die een openbaar IP-adres toewijst aan de toepassingsgateway. |
| [New-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport) | Hiermee wijst u een poort toe die moet worden gebruikt voor toegang tot de toepassingsgateway. |
| [New-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) | Hiermee maakt u een back-endpool voor een toepassingsgateway. |
| [New-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting) | Hiermee configureert u instellingen voor een back-endpool. |
| [New-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) | Hiermee maakt u een listener. |
| [New-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) | Hiermee maakt u een routeringsregel. |
| [New-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) | Hiermee geeft u de laag en capaciteit voor een toepassingsgateway op. |
| [New-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) | Maak een toepassingsgateway. |
| [Set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) | Maak een opslagprofiel voor de schaalset. |
| [Set-AzVmssOsProfile](/powershell/module/az.compute/set-azvmssosprofile) | Definieer het besturingssysteem voor de schaalset. |
| [Add-AzVmssNetworkInterfaceConfiguration](/powershell/module/az.compute/add-azvmssnetworkinterfaceconfiguration) | Definieer de netwerkinterface voor de schaalset. |
| [New-AzVmss](/powershell/module/az.compute/new-azvm) | Maak een virtuele-machineschaalset. |
| [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) | Hiermee haalt u het openbare IP-adres van een toepassingsgateway op. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep en alle daarin opgenomen resources. | 

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/).

Voorbeelden van extra PowerShell-voorbeeldscripts voor de toepassingsgateway zijn te vinden in de [documentatie over Azure Application Gateway](../powershell-samples.md).
