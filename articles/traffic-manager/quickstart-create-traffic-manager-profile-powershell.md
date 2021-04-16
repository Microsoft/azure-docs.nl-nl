---
title: 'Snelstart: Een profiel maken voor toepassingen met hoge beschikbaarheid - Azure PowerShell - Azure Traffic Manager'
description: In dit quickstartartikel wordt beschreven hoe u een Traffic Manager-profiel maakt voor het bouwen van een webtoepassing met hoge beschikbaarheid.
services: traffic-manager
author: duongau
ms.author: duau
manager: twooley
ms.date: 10/01/2020
ms.topic: quickstart
ms.service: traffic-manager
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom:
- mode-api
ms.openlocfilehash: 0fd2ae59f62850da75eecd5423ad225e208dca80
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537649"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-powershell"></a>Quickstart: Een Traffic Manager-profiel maken voor webtoepassingen met hoge beschikbaarheid met behulp van Azure PowerShell

In deze quickstart wordt beschreven hoe u een Traffic Manager-profiel maakt die hoge beschikbaarheid van uw webtoepassing biedt.

In deze quickstart maakt u twee exemplaren van een webtoepassing. Ze worden elk in een andere Azure-regio uitgevoerd. U maakt een Traffic Manager-profiel op basis van [eindpuntprioriteit](traffic-manager-routing-methods.md#priority-traffic-routing-method). het profiel stuurt gebruikersverkeer door naar de primaire site waar de webtoepassing wordt uitgevoerd. Traffic Manager bewaakt de webtoepassing continu. Als de primaire site niet beschikbaar is, biedt Traffic Manager automatische failover voor de back-upsite.

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan nu een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u PowerShell lokaal wilt installeren en gebruiken, is voor dit artikel versie 5.4.1 of hoger van de Azure PowerShell-module vereist. Voer `Get-Module -ListAvailable Az` uit om te kijken welke versie is geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

## <a name="create-a-resource-group"></a>Een resourcegroep maken
Maak een resourcegroep met [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

```azurepowershell-interactive

# Variables
$Location1="WestUS"

# Create a Resource Group
New-AzResourceGroup -Name MyResourceGroup -Location $Location1
```

## <a name="create-a-traffic-manager-profile"></a>Een Traffic Manager-profiel maken

Maak een Traffic Manager-profiel met behulp van [New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) waarmee gebruikersverkeer wordt doorgestuurd op basis van eindpuntprioriteit.

```azurepowershell-interactive

# Generates a random value
$Random=(New-Guid).ToString().Substring(0,8)
$mytrafficmanagerprofile="mytrafficmanagerprofile$Random"

New-AzTrafficManagerProfile `
-Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup `
-TrafficRoutingMethod Priority `
-MonitorPath '/' `
-MonitorProtocol "HTTP" `
-RelativeDnsName $mytrafficmanagerprofile `
-Ttl 30 `
-MonitorPort 80
```

## <a name="create-web-apps"></a>Web-apps maken

Voor deze quickstart moeten twee exemplaren van een webtoepassing worden geïmplementeerd in twee verschillende Azure-regio's (*US - west* en *US - oost*). Elk exemplaar dient als primair en failover-eindpunt voor Traffic Manager.

### <a name="create-web-app-service-plans"></a>Web App Service-plannen maken
Maak Web App Service-plannen met [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) voor de twee exemplaren van de webtoepassing die u in twee verschillende Azure-regio's gaat implementeren.

```azurepowershell-interactive

# Variables
$App1Name="AppServiceTM1$Random"
$App2Name="AppServiceTM2$Random"
$Location1="WestUS"
$Location2="EastUS"

# Create an App service plan
New-AzAppservicePlan -Name "$App1Name-Plan" -ResourceGroupName MyResourceGroup -Location $Location1 -Tier Standard
New-AzAppservicePlan -Name "$App2Name-Plan" -ResourceGroupName MyResourceGroup -Location $Location2 -Tier Standard

```
### <a name="create-a-web-app-in-the-app-service-plan"></a>Een web-app maken in het App Service-plan
Maak twee exemplaren van de webtoepassing met [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) in de App Service-plannen in de Azure-regio's *US - west* en *US - oost*.

```azurepowershell-interactive
$App1ResourceId=(New-AzWebApp -Name $App1Name -ResourceGroupName MyResourceGroup -Location $Location1 -AppServicePlan "$App1Name-Plan").Id
$App2ResourceId=(New-AzWebApp -Name $App2Name -ResourceGroupName MyResourceGroup -Location $Location2 -AppServicePlan "$App2Name-Plan").Id

```

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-eindpunten toevoegen
Voeg de twee web-apps als volgt als Traffic Manager-eindpunten aan het Traffic Manager-profiel toe met behulp van [New-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint):
- Voeg de web-app toe die zich in de Azure-regio *US - west* bevindt als primair eindpunt om alle gebruikersverkeer te routeren. 
- Voeg de web-app toe die zich in de Azure-regio *US - oost* bevindt als het failovereindpunt. Als het primaire eindpunt niet beschikbaar is, wordt het verkeer automatisch naar het failover-eindpunt gerouteerd.

```azurepowershell-interactive
New-AzTrafficManagerEndpoint -Name "$App1Name-$Location1" `
-ResourceGroupName MyResourceGroup `
-ProfileName "$mytrafficmanagerprofile" `
-Type AzureEndpoints `
-TargetResourceId $App1ResourceId `
-EndpointStatus "Enabled"

New-AzTrafficManagerEndpoint -Name "$App2Name-$Location2" `
-ResourceGroupName MyResourceGroup `
-ProfileName "$mytrafficmanagerprofile" `
-Type AzureEndpoints `
-TargetResourceId $App2ResourceId `
-EndpointStatus "Enabled"
```

## <a name="test-traffic-manager-profile"></a>Traffic Manager-profiel testen

In deze sectie controleert u de domeinnaam van uw Traffic Manager-profiel. Tevens configureert u het primaire eindpunt zodanig dat het niet beschikbaar is. Ten slotte kunt u zien dat de web-app nog steeds beschikbaar is. Dat komt omdat Traffic Manager het verkeer naar het failover-eindpunt stuurt.

### <a name="determine-the-dns-name"></a>DNS-naam bepalen

Bepaal de DNS-naam van het Traffic Manager-profiel met behulp van [Get-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile).

```azurepowershell-interactive
Get-AzTrafficManagerProfile -Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup
```

Kopieer de waarde **RelativeDnsName**. De DNS-naam van uw Traffic Manager-profiel is *http://<* relativednsname *>.trafficmanager.net*. 

### <a name="view-traffic-manager-in-action"></a>Traffic Manager in werking zien
1. Voer in een webbrowser de DNS-naam van het Traffic Manager-profiel (*http://<* relativednsname *>.trafficmanager.net*) in om de standaardwebsite van uw web-app te bekijken.

    > [!NOTE]
    > In dit quickstartscenario worden alle aanvragen gerouteerd naar het primaire eindpunt. Het is ingesteld op **Priority 1**.
2. Als u een Traffic Manager-failover in actie wilt bekijken, schakelt u de primaire site uit met behulp van [Disable-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/disable-aztrafficmanagerendpoint).

   ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name $App1Name-$Location1 `
    -Type AzureEndpoints `
    -ProfileName $mytrafficmanagerprofile `
    -ResourceGroupName MyResourceGroup `
    -Force
   ```
3. Kopieer de DNS-naam van het Traffic Manager-profiel (*http://<* relativednsname *>.trafficmanager.net*) om de website in een nieuwe webbrowsersessie te bekijken.
4. Controleer of de web-app nog beschikbaar is.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder met [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) de resourcegroepen, de webtoepassingen en alle gerelateerde resources als u klaar bent.

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Traffic Manager-profiel gemaakt die een hoge beschikbaarheid van uw webtoepassing biedt. Voor meer informatie over het routeren van verkeer gaat u door naar de zelfstudies voor Traffic Manager.

> [!div class="nextstepaction"]
> [Traffic Manager tutorials](tutorial-traffic-manager-improve-website-response.md) (Traffic Manager-zelfstudies)
