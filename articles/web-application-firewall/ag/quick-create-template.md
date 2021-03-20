---
title: 'Quickstart: Een Azure WAF v2 maken op Application Gateway - Azure Resource Manager-sjabloon'
titleSuffix: Azure Application Gateway
description: Leer hoe u een ARM-sjabloon (Azure Resource Manager-quickstartsjabloon) kunt gebruiken om een Web Application Firewall v2 te maken op Azure Application Gateway.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: quickstart
ms.date: 09/16/2020
ms.author: victorh
ms.custom: subject-armqs
ms.openlocfilehash: 4c5d5c4cafee9402e2afd7ddc047fd3b68e84540
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92091318"
---
# <a name="quickstart-create-an-azure-waf-v2-on-application-gateway-using-an-arm-template"></a>Quickstart: Een Azure WAF v2 maken op Application Gateway met behulp van een ARM-sjabloon

In deze quickstart gebruikt u een ARM-sjabloon (Azure Resource Manager-sjabloon) om een Azure Web Application Firewall v2 te maken op Application Gateway.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-wafv2%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="review-the-template"></a>De sjabloon controleren

Met deze sjabloon maakt u een eenvoudige Web Application Firewall v2 op Azure Application Gateway. Dit omvat een openbaar IP-frontend-IP-adres, HTTP-instellingen, een regel met een eenvoudige listener op poort 80 en een back-end-pool. Er wordt een WAF-beleid met een aangepaste regel gemaakt om het verkeer naar de back-end-pool te blokkeren op basis van een overeenkomend type voor een IP-adres.

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/ag-docs-wafv2/).

:::code language="json" source="~/quickstart-templates/ag-docs-wafv2/azuredeploy.json":::

Er worden meerdere Azure-resources gedefinieerd in de sjabloon:

- [**Microsoft.Network/applicationgateways**](/azure/templates/microsoft.network/applicationgateways)
- [**Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies**](/azure/templates/microsoft.network/ApplicationGatewayWebApplicationFirewallPolicies)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses) : een voor de toepassingsgateway en twee voor de virtuele machines.
- [**Microsoft.Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines) : twee virtuele machines
- [**Microsoft.Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces) : twee voor de virtuele machines
- [**Microsoft.Compute/virtualMachine/extensions**](/azure/templates/microsoft.compute/virtualmachines/extensions) : om IIS en de webpagina’s te configureren

## <a name="deploy-the-template"></a>De sjabloon implementeren

Het ARM-sjabloon implementeren in Azure:

1. Selecteer **Implementeren in Azure** om u aan te melden bij Azure, en open de sjabloon. Met de sjabloon maakt u een toepassingsgateway, de netwerkinfrastructuur, en twee virtuele machines in de back-endpool waarop IIS wordt uitgevoerd.

   [![Implementeren in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fag-docs-wafv2%2Fazuredeploy.json)

2. Selecteer of maak een resourcegroep.
3. Selecteer **Ik ga akkoord met de bovenstaande voorwaarden** en selecteer vervolgens **Kopen**. De implementatie kan 10 minuten of langer duren.

## <a name="validate-the-deployment"></a>De implementatie valideren

Hoewel IIS niet vereist is om de toepassingsgateway te maken, wordt het op de back-end-servers geïnstalleerd om te controleren of het maken van de WAF v2 op de toepassingsgateway in Azure is geslaagd.

Gebruik IIS om de toepassingsgateway te testen:

1. Zoek het openbare IP-adres voor de toepassingsgateway op de bijbehorende pagina **Overzicht**.![Noteer het openbare IP-adres van de toepassingsgateway](../../application-gateway/media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png). Of selecteer **Alle bronnen**, voer in het zoekvak *myAGPublicIPAddress* in en selecteer het adres vervolgens in de zoekresultaten. Het openbare IP-adres wordt weergegeven op de pagina **Overzicht**.
2. Kopieer het openbare IP-adres en plak het in de adresbalk van de browser om het IP-adres te bekijken.
3. Controleer het antwoord. Het antwoord **403 Verboden** geeft aan dat de WAF is gemaakt en verbindingen met de back-end-pool blokkeert.
4. Wijzig de aangepaste regel in **Verkeer toestaan**.
  Voer het volgende Azure PowerShell-script uit, waarbij u de naam van de resourcegroep vervangt:
   ```azurepowershell
   $rg = "<your resource group name>"
   $AppGW = Get-AzApplicationGateway -Name myAppGateway -ResourceGroupName $rg
   $pol = Get-AzApplicationGatewayFirewallPolicy -Name WafPol01 -ResourceGroupName $rg
   $pol[0].customrules[0].action = "allow"
   $rule = $pol.CustomRules
   Set-AzApplicationGatewayFirewallPolicy -Name WafPol01 -ResourceGroupName $rg -CustomRule $rule
   $AppGW.FirewallPolicy = $pol
   Set-AzApplicationGateway -ApplicationGateway $AppGW
   ```

   Vernieuw uw browser meerdere keren. Als het goed is, ziet u nu verbindingen naar myVM1 en myVM2.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de bij de toepassingsgateway gemaakte resources niet meer nodig hebt, verwijdert u de resourcegroep. Hiermee verwijdert u de toepassingsgateway en alle gerelateerde resources.

Als u de resourcegroep wilt verwijderen, roept u de cmdlet `Remove-AzResourceGroup` aan:

```azurepowershell-interactive
Remove-AzResourceGroup -Name "<your resource group name>"
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Een toepassingsgateway met een Web Application Firewall maken met behulp van de Azure-portal](application-gateway-web-application-firewall-portal.md)