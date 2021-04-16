---
title: 'Quickstart: Hoge beschikbaarheid instellen met Azure Front Door Service - Azure PowerShell'
description: In deze quickstart ziet u hoe u met behulp van Azure Front Door een hoge beschikbaarheid en een geavanceerde wereldwijde webtoepassing maakt met Azure PowerShell.
services: front-door
documentationcenter: na
author: duongau
ms.author: duau
manager: KumudD
ms.date: 09/21/2020
ms.topic: quickstart
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom:
- mode-api
ms.openlocfilehash: cd439a5931340f56401e5f6ba7a4e09f35ab7c7d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107539047"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application-using-azure-powershell"></a>Quickstart: Een Front Door maken voor een wereldwijde webtoepassing met hoge beschikbaarheid met Azure PowerShell

Ga aan de slag met Azure Front Door met behulp van Azure PowerShell om een maximaal beschikbare, wereldwijde webtoepassing met hoge prestaties te maken.

De Front Door stuurt webverkeer door naar specifieke bronnen in een back-end-pool. U hebt het front-end-domein gedefinieerd, resources toegevoegd aan een back-end-pool en een regel voor doorsturen gemaakt. In dit artikel wordt gebruikgemaakt van een eenvoudige configuratie van één back-end-pool met twee web-app-resources en één regel voor doorsturen met behulp van de standaardpadafstemming "/*".

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Azure PowerShell lokaal geïnstalleerd of Azure Cloud Shell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-resource-group"></a>Een resourcegroep maken

In Azure kunt u verwante resources toewijzen aan een resourcegroep. U kunt een bestaande resourcegroep gebruiken of een nieuwe maken.

Maak een resourcegroep met [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup):

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupFD -Location centralus
```

## <a name="create-two-instances-of-a-web-app"></a>Twee instanties van een web-app maken

Voor deze quickstart hebt u twee instanties nodig van een webtoepassing die in verschillende Azure-regio's worden uitgevoerd. Beide webtoepassingsinstanties worden in de modus Actief/actief uitgevoerd, dus verkeer kan ook eender welke instantie worden uitgevoerd. Deze configuratie is anders dan de configuratie Actief/stand-by, waarbij één van de instanties als failover fungeert.

Als u nog geen web-app hebt, voert u het volgende script uit om twee voorbeelden van web-apps in te stellen.

```azurepowershell-interactive
# Create first web app in Central US region.
$webapp1 = New-AzWebApp `
-Name "WebAppContoso-$(Get-Random)" `
-Location centralus `
-ResourceGroupName myResourceGroupFD `
-AppServicePlan myAppServicePlanCentralUS

# Create second web app in South Central US region.
$webapp2 = New-AzWebApp `
-Name "WebAppContoso-$(Get-Random)" `
-Location southcentralus `
-ResourceGroupName myResourceGroupFD `
-AppServicePlan myAppServicePlanSouthCentralUS
```

## <a name="create-a-front-door"></a>Een Front Door maken

In deze sectie wordt beschreven hoe u de volgende onderdelen van de Front Door kunt maken en configureren:
    
* Een front-endobject bevat het standaarddomein voor Front Door.
* Een backend-pool is een set redundante back-ends waarmee Front Door-load uw clientaanvraag in evenwicht brengt.
* Een regel voor doorsturen wijst uw front-endhost en een overeenkomstig URL-padpatroon toe aan een specifieke backend-pool.

### <a name="create-a-frontend-object"></a>Een front-endobject maken

Het front-endobject configureert de hostnaam voor de Front Door. De hostnaam heeft standaard het achtervoegsel **azurefd.net*.

```azurepowershell-interactive
# Create a unique name
$fdname = "contoso-frontend-$(Get-Random)"

#Create the frontend object
$FrontendEndObject = New-AzFrontDoorFrontendEndpointObject `
-Name "frontendEndpoint1" `
-HostName $fdname".azurefd.net"
```

### <a name="create-the-backend-pool"></a>Back-endpool maken

De back-endgroep bestaat uit de twee web-apps die aan het begin van deze snelstartgids zijn gemaakt. De instellingen voor de statustest en de taakverdeling die in deze stap werden gedefinieerd, maken gebruik van de standaardwaarden.

```azurepowershell-interactive
# Create backend objects that points to the hostname of the web apps
$backendObject1 = New-AzFrontDoorBackendObject `
-Address $webapp1.DefaultHostName
$backendObject2 = New-AzFrontDoorBackendObject `
-Address $webapp2.DefaultHostName

# Create a health probe object
$HealthProbeObject = New-AzFrontDoorHealthProbeSettingObject `
-Name "HealthProbeSetting"

# Create the load balancing setting object
$LoadBalancingSettingObject = New-AzFrontDoorLoadBalancingSettingObject `
-Name "Loadbalancingsetting" `
-SampleSize "4" `
-SuccessfulSamplesRequired "2" `
-AdditionalLatencyInMilliseconds "0"

# Create a backend pool using the backend objects, health probe, and load balancing settings
$BackendPoolObject = New-AzFrontDoorBackendPoolObject `
-Name "myBackendPool" `
-FrontDoorName $fdname `
-ResourceGroupName myResourceGroupFD `
-Backend $backendObject1,$backendObject2 `
-HealthProbeSettingsName "HealthProbeSetting" `
-LoadBalancingSettingsName "Loadbalancingsetting"
```

### <a name="create-a-routing-rule"></a>Een regel voor doorsturen maken

Met de regel voor routing wordt de back-endgroep toegewezen aan het front-enddomein en wordt de standaard waarde voor het overeenkomende pad ingesteld op "/*".

```azurepowershell-interactive
# Create a routing rule mapping the frontend host to the backend pool
$RoutingRuleObject = New-AzFrontDoorRoutingRuleObject `
-Name LocationRule `
-FrontDoorName $fdname `
-ResourceGroupName myResourceGroupFD `
-FrontendEndpointName "frontendEndpoint1" `
-BackendPoolName "myBackendPool" `
-PatternToMatch "/*"
```
### <a name="create-the-front-door"></a>De Front Door maken

Nu u de benodigde objecten hebt gemaakt, maakt u de Front Door:

```azurepowershell-interactive
# Creates the Front Door
New-AzFrontDoor `
-Name $fdname `
-ResourceGroupName myResourceGroupFD `
-RoutingRule $RoutingRuleObject `
-BackendPool $BackendPoolObject `
-FrontendEndpoint $FrontendEndObject `
-LoadBalancingSetting $LoadBalancingSettingObject `
-HealthProbeSetting $HealthProbeObject
```

Zodra de implementatie is voltooid, kunt u deze testen door de stappen in de volgende sectie te volgen.

## <a name="test-the-front-door"></a>De Front Door testen

Voer de volgende opdrachten uit om de hostnaam voor de Front Door te verkrijgen.

```azurepowershell-interactive
# Gets Front Door in resource group and output the hostname of the frontend domain.
$fd = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD
$fd.FrontendEndpoints[0].Hostname
```

Open een webbrowser en voer de hostnaam in via de opdrachten. De Front Door stuurt uw aanvraag door naar een van de back-endservers. 

:::image type="content" source="./media/quickstart-create-front-door-powershell/front-door-test-page.png" alt-text="Front Door-testpagina":::

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de resources die u met de Front Door hebt gemaakt niet meer nodig hebt, verwijdert u de resourcegroep. Wanneer u de resourcegroep verwijdert, verwijdert u ook de Front Door en alle resources die hieraan zijn gerelateerd. 

Als u de resourcegroep wilt verwijderen, roept u de cmdlet `Remove-AzResourceGroup` aan:

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroupFD
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u het volgende gemaakt:
* Front Door
* Twee web-apps

Ga verder naar de zelfstudies over Front Door voor informatie over het toevoegen van een aangepast domein aan uw Front Door.

> [!div class="nextstepaction"]
> [Een aangepast domein toevoegen](front-door-custom-domain.md)
