---
title: Voorbeeld van Azure PowerShell-script - Aangepaste WAF-regels maken
description: Voorbeeld van Azure PowerShell-script - Aangepaste Web Application Firewall-regels maken
author: vhorne
ms.service: application-gateway
ms.topic: sample
ms.date: 6/7/2019
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 312f052671036d8153dd19fcf4e559e825fd8464
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93396988"
---
# <a name="create-web-application-firewall-waf-custom-rules-with-azure-powershell"></a>Aangepaste WAF-regels (Web Application Firewall) maken met Azure PowerShell

Met dit script maakt u een Web Application Firewall van Application Gateway waarvoor aangepaste regels worden gebruikt. Met de aangepaste regel wordt verkeer geblokkeerd als de aanvraagheader User-Agent *evilbot* bevat.

## <a name="prerequisites"></a>Vereisten

### <a name="azure-powershell-module"></a>Azure PowerShell-module

Als u Azure PowerShell lokaal wilt installeren en gebruiken, is voor dit script moduleversie 2.1.0 of hoger van Azure PowerShell vereist.

1. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps).
2. Voer `Connect-AzAccount` uit om een verbinding met Azure tot stand te brengen.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-powershell[main](../../../powershell_scripts/application-gateway/waf-rules/waf-custom-rules.ps1 "Custom WAF rules")]

## <a name="clean-up-deployment"></a>Opschonen van implementatie

Gebruik de volgende opdracht om de resourcegroep, de toepassingsgateway en alle gerelateerde resources te verwijderen.

```powershell
Remove-AzResourceGroup -Name CustomRulesTest
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
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep en alle daarin opgenomen resources. |
|[New-AzApplicationGatewayAutoscaleConfiguration](/powershell/module/az.network/New-AzApplicationGatewayAutoscaleConfiguration)|Hiermee maakt u een configuratie voor automatisch schalen voor de toepassingsgateway.|
|[New-AzApplicationGatewayFirewallMatchVariable](/powershell/module/az.network/New-AzApplicationGatewayFirewallMatchVariable)|Hiermee maakt u een vergelijkingsvariabele voor de firewallvoorwaarde.|
|[New-AzApplicationGatewayFirewallCondition](/powershell/module/az.network/New-AzApplicationGatewayFirewallCondition)|Hiermee maakt u een vergelijkingsvoorwaarde voor de aangepaste regel.|
|[New-AzApplicationGatewayFirewallCustomRule](/powershell/module/az.network/New-AzApplicationGatewayFirewallCustomRule)|Hiermee maakt u een nieuwe aangepaste regel voor de het firewallbeleid van de toepassingsgateway.|
|[New-AzApplicationGatewayFirewallPolicy](/powershell/module/az.network/New-AzApplicationGatewayFirewallPolicy)|Hiermee maakt u een firewallbeleid voor de toepassingsgateway.|
|[New-AzApplicationGatewayWebApplicationFirewallConfiguration](/powershell/module/az.network/New-AzApplicationGatewayWebApplicationFirewallConfiguration)|Hiermee maakt u een WAF-configuratie voor een toepassingsgateway.|

## <a name="next-steps"></a>Volgende stappen

- Zie [Aangepaste regels voor Web Application Firewall](../../web-application-firewall/ag/custom-waf-rules-overview.md) voor meer informatie over aangepaste WAF-regels
- Zie voor meer informatie over de Azure PowerShell-module de [documentatie van Azure PowerShell](/powershell/azure/).
- Voorbeelden van extra PowerShell-voorbeeldscripts voor de toepassingsgateway zijn te vinden in de [documentatie over Azure Application Gateway](../powershell-samples.md).