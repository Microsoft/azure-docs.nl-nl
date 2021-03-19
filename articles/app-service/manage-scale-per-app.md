---
title: Schalen per app voor high-density hosting
description: Schaal apps onafhankelijk van de App Service plannen en Optimaliseer de uitgeschaalde instanties in uw abonnement.
author: btardif
ms.assetid: a903cb78-4927-47b0-8427-56412c4e3e64
ms.topic: article
ms.date: 05/13/2019
ms.author: byvinyal
ms.custom: seodec18
ms.openlocfilehash: f1ca4958fe2608d0c040ef5b93827a7e71a4151c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "74672355"
---
# <a name="high-density-hosting-on-azure-app-service-using-per-app-scaling"></a>Hosten met hoge dichtheid op Azure App Service met schalen per app

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Wanneer u App Service gebruikt, kunt u uw apps schalen door het [app service plan](overview-hosting-plans.md) waarmee ze worden uitgevoerd, te schalen. Wanneer meerdere apps worden uitgevoerd in hetzelfde App Service-abonnement, worden alle apps in het plan uitgevoerd met elk uitgebreid exemplaar.

*Schalen per app* kan worden ingeschakeld op het niveau van de app service plan om een app te kunnen schalen onafhankelijk van het app service plan dat als host fungeert voor het abonnement. Op deze manier kan een App Service plan worden geschaald naar 10 instanties, maar een app kan zo worden ingesteld dat deze slechts vijf keer wordt gebruikt.

> [!NOTE]
> Schalen per app is alleen beschikbaar voor **Standard**-, **Premium**-, **Premium v2** -en **geïsoleerde** prijs categorieën.
>

Apps worden toegewezen aan een beschik bare App Service plan met behulp van een aanbevolen benadering voor een gelijkmatige verdeling van alle exemplaren. Hoewel een gelijkmatige distributie niet gegarandeerd is, zorgt het platform ervoor dat er geen twee exemplaren van dezelfde app worden gehost op hetzelfde exemplaar van de App Service-abonnement.

Het platform is niet afhankelijk van metrische gegevens om te beslissen over de toewijzing van werk nemers. Toepassingen worden alleen opnieuw gesaldeerd wanneer instanties worden toegevoegd aan of verwijderd uit het App Service plan.

## <a name="per-app-scaling-using-powershell"></a>Schalen per app met behulp van Power shell

Maak een plan met schalen per app door de ```-PerSiteScaling $true``` para meter aan de cmdlet door te geven ```New-AzAppServicePlan``` .

```powershell
New-AzAppServicePlan -ResourceGroupName $ResourceGroup -Name $AppServicePlan `
                            -Location $Location `
                            -Tier Premium -WorkerSize Small `
                            -NumberofWorkers 5 -PerSiteScaling $true
```

Schakel per app schalen in met een bestaand App Service plan door de `-PerSiteScaling $true` para meter door te geven aan ```Set-AzAppServicePlan``` de cmdlet.

```powershell
# Enable per-app scaling for the App Service Plan using the "PerSiteScaling" parameter.
Set-AzAppServicePlan -ResourceGroupName $ResourceGroup `
   -Name $AppServicePlan -PerSiteScaling $true
```

Configureer op het niveau van de app het aantal instanties dat de app kan gebruiken in het App Service plan.

In het onderstaande voor beeld is de app beperkt tot twee instanties, ongeacht het aantal exemplaren waarvan het onderliggende app service-plan wordt geschaald naar.

```powershell
# Get the app we want to configure to use "PerSiteScaling"
$newapp = Get-AzWebApp -ResourceGroupName $ResourceGroup -Name $webapp

# Modify the NumberOfWorkers setting to the desired value.
$newapp.SiteConfig.NumberOfWorkers = 2

# Post updated app back to azure
Set-AzWebApp $newapp
```

> [!IMPORTANT]
> `$newapp.SiteConfig.NumberOfWorkers` wijkt af van `$newapp.MaxNumberOfWorkers` . Schaal baarheid per app wordt gebruikt `$newapp.SiteConfig.NumberOfWorkers` om de schaal kenmerken van de app te bepalen.

## <a name="per-app-scaling-using-azure-resource-manager"></a>Schalen per app met behulp van Azure Resource Manager

Met de volgende Azure Resource Manager sjabloon maakt u:

- Een App Service plan dat is geschaald naar 10 instanties
- een app die is geconfigureerd om te worden geschaald naar een maximum van vijf exemplaren.

De eigenschap **PerSiteScaling** wordt ingesteld op True voor het app service plan `"perSiteScaling": true` . De App stelt het **aantal werk** rollen in dat moet worden gebruikt voor 5 `"properties": { "numberOfWorkers": "5" }` .

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

## <a name="recommended-configuration-for-high-density-hosting"></a>Aanbevolen configuratie voor high-density hosting

Het schalen per app is een functie die is ingeschakeld in zowel wereld wijde Azure-regio's als [app service omgevingen](environment/app-service-app-service-environment-intro.md). De aanbevolen strategie is echter het gebruik van App Service omgevingen om te profiteren van hun geavanceerde functies en de grotere capaciteit van het App Service plan.  

Volg deze stappen om hosting met hoge dichtheid voor uw apps te configureren:

1. Wijs een App Service plan aan als het hoge dichtheids plan en schaal het naar de gewenste capaciteit.
1. Stel de `PerSiteScaling` vlag in op True voor het app service plan.
1. Er worden nieuwe apps gemaakt en toegewezen aan dat App Service plan waarbij de eigenschap **numberOfWorkers** is ingesteld op **1**.
   - Het gebruik van deze configuratie levert de hoogst mogelijke dichtheid.
1. Het aantal werk nemers kan onafhankelijk per app worden geconfigureerd om extra resources toe te kennen wanneer dat nodig is. Bijvoorbeeld:
   - Een app met veel gebruik kan **numberOfWorkers** instellen op **3** om meer verwerkings capaciteit voor die app te krijgen.
   - Met toepassingen voor laag gebruik wordt **numberOfWorkers** ingesteld op **1**.

## <a name="next-steps"></a>Volgende stappen

- [Gedetailleerd overzicht van Azure App Service plannen](overview-hosting-plans.md)
- [Inleiding tot de App Service-omgeving](environment/app-service-app-service-environment-intro.md)
