---
title: Schalen per app voor high-densityhosting
description: Schaal apps onafhankelijk van de App Service en optimaliseer de uitschaal-exemplaren in uw abonnement.
author: btardif
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.topic: article
ms.date: 05/13/2019
ms.author: byvinyal
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 756117a2a231fcb406fd3e3102a16c318c621aa0
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832604"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>High-densityhosting op Azure App Service met behulp van schalen per app

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Wanneer u App Service, kunt u uw apps schalen door het App Service [op te schalen.](overview-hosting-plans.md) Wanneer meerdere apps worden uitgevoerd in hetzelfde App Service abonnement, worden alle apps in het plan uitgevoerd op elk uitschaald exemplaar.

*Schalen per app* kan worden ingeschakeld op het niveau van het App Service-plan, zodat een app onafhankelijk kan worden geschaald van het App Service plan dat als host voor de app wordt gebruikt. Op deze manier kan App Service abonnement worden geschaald naar 10 exemplaren, maar kan een app worden ingesteld op slechts vijf exemplaren.

> [!NOTE]
> Schalen per app is alleen beschikbaar voor **prijslagen Standard,** **Premium,** **Premium V2** **en Isolated.**
>

Apps worden toegewezen aan beschikbare App Service plan met behulp van een best effort-benadering voor een gelijkmatige verdeling over meerdere exemplaren. Hoewel een gelijkmatige distributie niet wordt gegarandeerd, zorgt het platform ervoor dat twee exemplaren van dezelfde app niet worden gehost op hetzelfde App Service plan-exemplaar.

Het platform is niet afhankelijk van metrische gegevens om te beslissen over werktoewijzing. Toepassingen worden alleen opnieuw in balans gebracht wanneer exemplaren worden toegevoegd aan of verwijderd uit het App Service plan.

## <a name="per-app-scaling-using-powershell"></a>Schalen per app met Behulp van PowerShell

Maak een plan met schalen per app door de parameter door te geven ```-PerSiteScaling $true``` aan de ```New-AzAppServicePlan``` cmdlet .

```powershell
New-AzAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Schakel schalen per app in met een bestaand App Service Plan door de parameter door te geven `-PerSiteScaling $true` aan de ```Set-AzAppServicePlan``` cmdlet.

```powershell
# Enable per-app scaling for the App Service Plan using the "PerSiteScaling" parameter.
Set-AzAppServicePlan -ResourceGroupName $ResourceGroup `
   -Name $AppServicePlan -PerSiteScaling $true
```

Configureer op app-niveau het aantal exemplaren dat de app in het App Service gebruiken.

In het onderstaande voorbeeld is de app beperkt tot twee exemplaren, ongeacht naar hoeveel exemplaren het onderliggende App Service-plan wordt geschaald.

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzWebApp -ResourceGroupName $ResourceGroup -Name $webapp

# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2

# Post updated app back to azure
Set-AzWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers` verschilt van `$newapp.MaxNumberOfWorkers` . Bij het schalen per app wordt `$newapp.SiteConfig.NumberOfWorkers` gebruikgemaakt van om de schaalkenmerken van de app te bepalen.

## <a name="per-app-scaling-using-azure-resource-manager"></a>Schalen per app met behulp van Azure Resource Manager

De volgende Azure Resource Manager sjabloon maakt:

- Een App Service-abonnement dat is opgeschaald naar 10 exemplaren
- een app die is geconfigureerd om te worden geschaald naar maximaal vijf exemplaren.

Het App Service plan is om de **eigenschap PerSiteScaling in** te stellen op `"perSiteScaling": true` true. De app is bezig met het **instellen van het aantal werksters** dat moet worden gebruikt op 5 `"properties": { "numberOfWorkers": "5" }` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters":{
        "appServicePlanName": { "type": "string" },
        "appName": { "type": "string" }
        },
    "resources": [
    {
        "comments": "App Service Plan with per site perSiteScaling = true",
        "type": "Microsoft.Web/serverFarms",
        "sku": {
            "name": "P1",
            "tier": "Premium",
            "size": "P1",
            "family": "P",
            "capacity": 10
            },
        "name": "[parameters('appServicePlanName')]",
        "apiVersion": "2015-08-01",
        "location": "West US",
        "properties": {
            "name": "[parameters('appServicePlanName')]",
            "perSiteScaling": true
        }
    },
    {
        "type": "Microsoft.Web/sites",
        "name": "[parameters('appName')]",
        "apiVersion": "2015-08-01-preview",
        "location": "West US",
        "dependsOn": [ "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" ],
        "properties": { "serverFarmId": "[resourceId('Microsoft.Web/serverFarms', parameters('appServicePlanName'))]" },
        "resources": [ {
                "comments": "",
                "type": "config",
                "name": "web",
                "apiVersion": "2015-08-01",
                "location": "West US",
                "dependsOn": [ "[resourceId('Microsoft.Web/Sites', parameters('appName'))]" ],
                "properties": { "numberOfWorkers": "5" }
            } ]
        }]
}
```

## <a name="recommended-configuration-for-high-density-hosting"></a>Aanbevolen configuratie voor high-densityhosting

Schalen per app is een functie die is ingeschakeld in zowel globale Azure-regio's als [App Service Omgevingen.](environment/app-service-app-service-environment-intro.md) De aanbevolen strategie is echter om App Service-omgevingen te gebruiken om te profiteren van de geavanceerde functies en de grotere App Service plancapaciteit.  

Volg deze stappen om high-densityhosting voor uw apps te configureren:

1. Wijs een App Service plan aan als het high-densityplan en schaal dit uit naar de gewenste capaciteit.
1. Stel de `PerSiteScaling` vlag in op true voor App Service abonnement.
1. Nieuwe apps worden gemaakt en toegewezen aan dat App Service plan met de eigenschap **numberOfWorkers** ingesteld **op 1**.
   - Het gebruik van deze configuratie levert de hoogst mogelijke dichtheid op.
1. Het aantal werknemers kan onafhankelijk per app worden geconfigureerd om zo nodig aanvullende resources te verlenen. Bijvoorbeeld:
   - Een app voor hoog gebruik kan **numberOfWorkers instellen** op **3** om meer verwerkingscapaciteit voor die app te hebben.
   - Apps voor laag gebruik stellen **numberOfWorkers in** op **1**.

## <a name="next-steps"></a>Volgende stappen

- [Azure App Service uitgebreid overzicht van de plannen](overview-hosting-plans.md)
- [Inleiding tot de App Service-omgeving](environment/app-service-app-service-environment-intro.md)
