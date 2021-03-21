---
title: Azure-toepassing Insights automatiseren met Power shell | Microsoft Docs
description: Het maken en beheren van resources, waarschuwingen en beschikbaarheids tests in Power shell automatiseren met behulp van een Azure Resource Manager sjabloon.
ms.topic: conceptual
ms.date: 05/02/2020
ms.openlocfilehash: c2e3d33be487b6a92cb7038d814e17fcd5a10064
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100589797"
---
#  <a name="manage-application-insights-resources-using-powershell"></a>Application Insights-resources beheren met Power shell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Dit artikel laat u zien hoe u het maken en bijwerken van [Application Insights](./app-insights-overview.md) resources automatisch kunt automatiseren met behulp van Azure resource management. Als onderdeel van een bouw proces kunt u bijvoorbeeld het volgende doen. Naast de basis Application Insights resource kunt u [Beschik baarheid-webtesten](./monitor-web-app-availability.md)maken, [waarschuwingen](../alerts/alerts-log.md)instellen, het [prijs schema](pricing.md)instellen en andere Azure-resources maken.

De sleutel voor het maken van deze resources is JSON-sjablonen voor [Azure Resource Manager](../../azure-resource-manager/management/manage-resources-powershell.md). De basis procedure is: de JSON-definities van bestaande resources downloaden; para meters bepaalde waarden, zoals namen, en voer vervolgens de sjabloon uit wanneer u een nieuwe resource wilt maken. U kunt meerdere resources samenbundelen om ze allemaal in één keer te maken, bijvoorbeeld een app-monitor met beschikbaarheids tests, waarschuwingen en opslag voor continue export. Er zijn enkele finesses naar een aantal van de parameterizations, die hier wordt uitgelegd.

## <a name="one-time-setup"></a>Eenmalige installatie
Als u Power shell nog niet eerder hebt gebruikt met uw Azure-abonnement:

Installeer de Azure PowerShell-module op de computer waarop u de scripts wilt uitvoeren:

1. Installeer het [installatie programma voor het micro soft-webplatform (V5 of hoger)](https://www.microsoft.com/web/downloads/platform.aspx).
2. Gebruik het om Microsoft Azure PowerShell te installeren.

Naast het gebruik van Resource Manager-sjablonen beschikt u over een uitgebreide set [Application Insights Power shell-cmdlets](/powershell/module/az.applicationinsights), waarmee u eenvoudig Application Insights resources kunt configureren. De mogelijkheden die zijn ingeschakeld door de cmdlets zijn onder andere:

* Application Insights-resources maken en verwijderen
* Een lijst met Application Insights resources en de bijbehorende eigenschappen ophalen
* Continue export maken en beheren
* Toepassings sleutels maken en beheren
* Het dagelijks kapje instellen
* Het prijs plan instellen

## <a name="create-application-insights-resources-using-a-powershell-cmdlet"></a>Application Insights-resources maken met een Power shell-cmdlet

U kunt als volgt een nieuwe Application Insights-resource maken in het Azure-Oost-Data Center met behulp van de cmdlet [New-AzApplicationInsights](/powershell/module/az.applicationinsights/new-azapplicationinsights) :

```PS
New-AzApplicationInsights -ResourceGroupName <resource group> -Name <resource name> -location eastus
```


## <a name="create-application-insights-resources-using-a-resource-manager-template"></a>Application Insights resources maken met een resource manager-sjabloon

U kunt als volgt een nieuwe Application Insights resource maken met behulp van een resource manager-sjabloon.

### <a name="create-the-azure-resource-manager-template"></a>De Azure Resource Manager sjabloon maken

Maak een nieuw. JSON-bestand, zodat het `template1.json` in dit voor beeld kan worden aangeroepen. Kopieer deze inhoud hierin:

```JSON
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "appName": {
                "type": "string",
                "metadata": {
                    "description": "Enter the name of your Application Insights resource."
                }
            },
            "appType": {
                "type": "string",
                "defaultValue": "web",
                "allowedValues": [
                    "web",
                    "java",
                    "other"
                ],
                "metadata": {
                    "description": "Enter the type of the monitored application."
                }
            },
            "appLocation": {
                "type": "string",
                "defaultValue": "eastus",
                "metadata": {
                    "description": "Enter the location of your Application Insights resource."
                }
            },
            "retentionInDays": {
                "type": "int",
                "defaultValue": 90,
                "allowedValues": [
                    30,
                    60,
                    90,
                    120,
                    180,
                    270,
                    365,
                    550,
                    730
                ],
                "metadata": {
                    "description": "Data retention in days"
                }
            },
            "ImmediatePurgeDataOn30Days": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
                }
            },
            "priceCode": {
                "type": "int",
                "defaultValue": 1,
                "allowedValues": [
                    1,
                    2
                ],
                "metadata": {
                    "description": "Pricing plan: 1 = Per GB (or legacy Basic plan), 2 = Per Node (legacy Enterprise plan)"
                }
            },
            "dailyQuota": {
                "type": "int",
                "defaultValue": 100,
                "minValue": 1,
                "metadata": {
                    "description": "Enter daily quota in GB."
                }
            },
            "dailyQuotaResetTime": {
                "type": "int",
                "defaultValue": 0,
                "metadata": {
                    "description": "Enter daily quota reset hour in UTC (0 to 23). Values outside the range will get a random reset hour."
                }
            },
            "warningThreshold": {
                "type": "int",
                "defaultValue": 90,
                "minValue": 1,
                "maxValue": 100,
                "metadata": {
                    "description": "Enter the % value of daily quota after which warning mail to be sent. "
                }
            }
        },
        "variables": {
            "priceArray": [
                "Basic",
                "Application Insights Enterprise"
            ],
            "pricePlan": "[take(variables('priceArray'),parameters('priceCode'))]",
            "billingplan": "[concat(parameters('appName'),'/', variables('pricePlan')[0])]"
        },
        "resources": [
            {
                "type": "microsoft.insights/components",
                "kind": "[parameters('appType')]",
                "name": "[parameters('appName')]",
                "apiVersion": "2014-04-01",
                "location": "[parameters('appLocation')]",
                "tags": {},
                "properties": {
                    "ApplicationId": "[parameters('appName')]",
                    "retentionInDays": "[parameters('retentionInDays')]"
                },
                "dependsOn": []
            },
            {
                "name": "[variables('billingplan')]",
                "type": "microsoft.insights/components/CurrentBillingFeatures",
                "location": "[parameters('appLocation')]",
                "apiVersion": "2015-05-01",
                "dependsOn": [
                    "[resourceId('microsoft.insights/components', parameters('appName'))]"
                ],
                "properties": {
                    "CurrentBillingFeatures": "[variables('pricePlan')]",
                    "DataVolumeCap": {
                        "Cap": "[parameters('dailyQuota')]",
                        "WarningThreshold": "[parameters('warningThreshold')]",
                        "ResetTime": "[parameters('dailyQuotaResetTime')]"
                    }
                }
            }
        ]
    }
```

### <a name="use-the-resource-manager-template-to-create-a-new-application-insights-resource"></a>De Resource Manager-sjabloon gebruiken om een nieuwe Application Insights resource te maken

1. Meld u in Power shell aan bij Azure met `$Connect-AzAccount`
2. Stel uw context in op een abonnement met `Set-AzContext "<subscription ID>"`
2. Voer een nieuwe implementatie uit om een nieuwe Application Insights-resource te maken:
   
    ```PS
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName` is de groep waar u de nieuwe resources wilt maken.
   * `-TemplateFile` moet vóór de aangepaste para meters plaatsvinden.
   * `-appName` De naam van de resource die moet worden gemaakt.

U kunt andere para meters toevoegen: Hier vindt u de beschrijvingen in het gedeelte para meters van de sjabloon.

## <a name="get-the-instrumentation-key"></a>De instrumentatie sleutel ophalen

Nadat u een toepassings resource hebt gemaakt, wilt u de instrumentatie sleutel: 

1. `$Connect-AzAccount`
2. `Set-AzContext "<subscription ID>"`
3. `$resource = Get-AzResource -Name "<resource name>" -ResourceType "Microsoft.Insights/components"`
4. `$details = Get-AzResource -ResourceId $resource.ResourceId`
5. `$details.Properties.InstrumentationKey`

Als u een lijst met veel andere eigenschappen van uw Application Insights resource wilt weer geven, gebruikt u:

```PS
Get-AzApplicationInsights -ResourceGroupName Fabrikam -Name FabrikamProd | Format-List
```

Aanvullende eigenschappen zijn beschikbaar via de-cmdlets:
* `Set-AzApplicationInsightsDailyCap`
* `Set-AzApplicationInsightsPricingPlan`
* `Get-AzApplicationInsightsApiKey`
* `Get-AzApplicationInsightsContinuousExport`

Raadpleeg de [gedetailleerde documentatie](/powershell/module/az.applicationinsights) voor de para meters voor deze cmdlets.  

## <a name="set-the-data-retention"></a>De Bewaar periode voor gegevens instellen

Hieronder vindt u drie methoden om de gegevens retentie op een Application Insights resource programmatisch in te stellen.

### <a name="setting-data-retention-using-a-powershell-commands"></a>Gegevens retentie instellen met behulp van een Power shell-opdracht

Hier volgt een eenvoudige set Power shell-opdrachten voor het instellen van de gegevens retentie voor uw Application Insights-resource:

```PS
$Resource = Get-AzResource -ResourceType Microsoft.Insights/components -ResourceGroupName MyResourceGroupName -ResourceName MyResourceName
$Resource.Properties.RetentionInDays = 365
$Resource | Set-AzResource -Force
```

### <a name="setting-data-retention-using-rest"></a>Gegevens bewaaring instellen met behulp van REST

Als u de huidige gegevens retentie voor uw Application Insights resource wilt ophalen, kunt u het OSS-hulp programma [ARMClient](https://github.com/projectkudu/ARMClient)gebruiken.  (Meer informatie over ARMClient van artikelen op [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) en de [Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).)  Hier volgt een voor beeld van `ARMClient` om de huidige retentie te verkrijgen:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview
```

Als u de retentie wilt instellen, is de opdracht een soort gelijke PUT:

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview "{location: 'eastus', properties: {'retentionInDays': 365}}"
```

Voer de volgende handelingen uit om de gegevens retentie tot 365 dagen in te stellen met behulp van de bovenstaande sjabloon:

```PS
New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
       -TemplateFile .\template1.json `
       -retentionInDays 365 `
       -appName myApp
```

### <a name="setting-data-retention-using-a-powershell-script"></a>Bewaren van gegevens met behulp van een Power shell-script

Het volgende script kan ook worden gebruikt voor het wijzigen van de Bewaar periode. Kopieer dit script om op te slaan `Set-ApplicationInsightsRetention.ps1` .

```PS
Param(
    [Parameter(Mandatory = $True)]
    [string]$SubscriptionId,

    [Parameter(Mandatory = $True)]
    [string]$ResourceGroupName,

    [Parameter(Mandatory = $True)]
    [string]$Name,

    [Parameter(Mandatory = $True)]
    [string]$RetentionInDays
)
$ErrorActionPreference = 'Stop'
if (-not (Get-Module Az.Accounts)) {
    Import-Module Az.Accounts
}
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
if (-not $azProfile.Accounts.Count) {
    Write-Error "Ensure you have logged in before calling this function."    
}
$currentAzureContext = Get-AzContext
$profileClient = New-Object Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient($azProfile)
$token = $profileClient.AcquireAccessToken($currentAzureContext.Tenant.TenantId)
$UserToken = $token.AccessToken
$RequestUri = "https://management.azure.com/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Insights/components/$($Name)?api-version=2015-05-01"
$Headers = @{
    "Authorization"         = "Bearer $UserToken"
    "x-ms-client-tenant-id" = $currentAzureContext.Tenant.TenantId
}
## Get Component object via ARM
$GetResponse = Invoke-RestMethod -Method "GET" -Uri $RequestUri -Headers $Headers 

## Update RetentionInDays property
if($($GetResponse.properties | Get-Member "RetentionInDays"))
{
    $GetResponse.properties.RetentionInDays = $RetentionInDays
}
else
{
    $GetResponse.properties | Add-Member -Type NoteProperty -Name "RetentionInDays" -Value $RetentionInDays
}
## Upsert Component object via ARM
$PutResponse = Invoke-RestMethod -Method "PUT" -Uri "$($RequestUri)" -Headers $Headers -Body $($GetResponse | ConvertTo-Json) -ContentType "application/json"
$PutResponse
```

Dit script kan vervolgens worden gebruikt als:

```PS
Set-ApplicationInsightsRetention `
        [-SubscriptionId] <String> `
        [-ResourceGroupName] <String> `
        [-Name] <String> `
        [-RetentionInDays <Int>]
```

## <a name="set-the-daily-cap"></a>Het dagelijks kapje instellen

Als u de eigenschappen van het dagelijks kapje wilt ophalen, gebruikt u de cmdlet [set-AzApplicationInsightsPricingPlan](/powershell/module/az.applicationinsights/set-azapplicationinsightspricingplan) : 

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

Gebruik dezelfde cmdlet om de eigenschappen van het dagelijks kapje in te stellen. Om bijvoorbeeld de Cap in te stellen op 300 GB/dag,

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> -DailyCapGB 300
```

U kunt [ARMClient](https://github.com/projectkudu/ARMClient) ook gebruiken om dagelijkse Cap-para meters op te halen en in te stellen.  Als u de huidige waarden wilt ophalen, gebruikt u:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
```

## <a name="set-the-daily-cap-reset-time"></a>De tijd voor het instellen van de dagelijkse limiet instellen

Als u de tijd voor het opnieuw instellen van dagelijks Cap wilt instellen, kunt u [ARMClient](https://github.com/projectkudu/ARMClient)gebruiken. Hier volgt een voor beeld van het gebruik van `ARMClient` om de tijd voor opnieuw instellen in te stellen op een nieuw uur (in dit voor beeld 12:00 UTC):

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview "{'CurrentBillingFeatures':['Basic'],'DataVolumeCap':{'ResetTime':12}}"
```

<a id="price"></a>
## <a name="set-the-pricing-plan"></a>Het prijs plan instellen 

Gebruik de cmdlet [set-AzApplicationInsightsPricingPlan](/powershell/module/az.applicationinsights/set-azapplicationinsightspricingplan) om het huidige prijs plan op te halen:

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

Als u het prijs plan wilt instellen, gebruikt u dezelfde cmdlet met de `-PricingPlan` opgegeven waarde:  

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> -PricingPlan Basic
```

U kunt ook het prijs plan op een bestaande Application Insights resource instellen met behulp van de Resource Manager-sjabloon hierboven, waarbij de resource micro soft. Insights/onderdelen en het `dependsOn` knoop punt van de facturerings bron worden wegge laten. Als u dit bijvoorbeeld wilt instellen op het abonnement per GB (voorheen het basis abonnement genoemd), voert u de volgende handelingen uit:

```PS
        New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
               -TemplateFile .\template1.json `
               -priceCode 1 `
               -appName myApp
```

De `priceCode` wordt gedefinieerd als:

|priceCode|plannen|
|---|---|
|1|Per GB (voorheen de naam van het Basic-abonnement)|
|2|Per knoop punt (voorheen de naam van het bedrijfs plan)|

Ten slotte kunt u [ARMClient](https://github.com/projectkudu/ARMClient) gebruiken om prijs plannen en dagelijkse Cap-para meters op te halen en in te stellen.  Als u de huidige waarden wilt ophalen, gebruikt u:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
```

En u kunt al deze para meters instellen met:

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
"{'CurrentBillingFeatures':['Basic'],'DataVolumeCap':{'Cap':200,'ResetTime':12,'StopSendNotificationWhenHitCap':true,'WarningThreshold':90,'StopSendNotificationWhenHitThreshold':true}}"
```

Hiermee stelt u de dagelijkse limiet in op 200 GB per dag, configureert u de dagelijkse limiet voor het opnieuw instellen van 12:00 UTC, verzendt u e-mail berichten beide wanneer de limiet is bereikt, wordt het waarschuwings niveau bereikt en wordt de waarschuwings drempel ingesteld op 90% van de Cap.  

## <a name="add-a-metric-alert"></a>Een waarschuwing voor metrische gegevens toevoegen

Als u het maken van metrische waarschuwingen wilt automatiseren, raadpleegt u het artikel over de [sjabloon metrische waarschuwingen](../alerts/alerts-metric-create-templates.md#template-for-a-simple-static-threshold-metric-alert)


## <a name="add-an-availability-test"></a>Een beschikbaarheids test toevoegen

Als u beschikbaarheids testen wilt automatiseren, raadpleegt u het artikel over de [sjabloon metrische waarschuwingen](../alerts/alerts-metric-create-templates.md#template-for-an-availability-test-along-with-a-metric-alert).

## <a name="add-more-resources"></a>Meer resources toevoegen

Voor het automatiseren van het maken van een andere bron, maakt u hand matig een voor beeld en kopieert en para meters de code vervolgens uit [Azure Resource Manager](https://resources.azure.com/). 

1. Open [Azure Resource Manager](https://resources.azure.com/). Navigeer omlaag `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components` naar de resource van uw toepassing. 
   
    ![Navigatie in Azure Resource Explorer](./media/powershell/01.png)
   
    *Onderdelen* zijn de basis bronnen van Application Insights voor het weer geven van toepassingen. Er zijn afzonderlijke resources voor de bijbehorende waarschuwings regels en beschikbaarheids webtests.
2. Kopieer de JSON van het onderdeel naar de juiste plaats in `template1.json` .
3. Verwijder deze eigenschappen:
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. Open de `webtests` secties en en `alertrules` Kopieer de JSON voor afzonderlijke items naar uw sjabloon. (Kopieer niet van de `webtests` `alertrules` knoop punten of: Ga naar de items daaronder.)
   
    Elke webtest heeft een bijbehorende waarschuwings regel, zodat u beide kunt kopiëren.
   
5. Voeg deze regel in elke resource in:
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>De sjabloon para meters
Nu moet u de specifieke namen vervangen door para meters. Als u [een sjabloon wilt para meters](../../azure-resource-manager/templates/template-syntax.md), schrijft u expressies met behulp [van een set hulp functies](../../azure-resource-manager/templates/template-functions.md). 

U kunt niet slechts een deel van een teken reeks para meters, zodat u `concat()` teken reeksen kunt bouwen.

Hier volgen enkele voor beelden van de vervangingen die u wilt maken. Er zijn verschillende exemplaren van elke vervanging. Mogelijk hebt u anderen nodig in uw sjabloon. In deze voor beelden worden de para meters en variabelen gebruikt die aan het begin van de sjabloon zijn gedefinieerd.

| find | vervangen door |
| --- | --- |
| `"hidden-link:/subscriptions/.../../components/MyAppName"` |`"[concat('hidden-link:',`<br/>`resourceId('microsoft.insights/components',` <br/> `parameters('appName')))]"` |
| `"/subscriptions/.../../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"` (kleine letter) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>`Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`|

### <a name="set-dependencies-between-the-resources"></a>Afhankelijkheden instellen tussen de resources
Azure moet de resources in strikte volg orde instellen. Als u er zeker van wilt zijn dat de installatie is voltooid voordat de volgende begint, voegt u afhankelijkheids lijnen toe:

* In de resource beschikbaarheids test:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* In de waarschuwings resource voor een beschikbaarheids test:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>Volgende stappen
Andere automatiserings artikelen:

* [Maak een Application Insights resource](./create-new-resource.md#creating-a-resource-automatically) snelle methode zonder een sjabloon te gebruiken.
* [Webtests maken](../alerts/resource-manager-alerts-metric.md#availability-test-with-metric-alert)
* [Diagnostische Azure-gegevens verzenden naar Application Insights](powershell-azure-diagnostics.md)
* [Release aantekeningen maken](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)