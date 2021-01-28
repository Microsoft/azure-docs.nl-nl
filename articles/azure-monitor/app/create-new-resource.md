---
title: Een nieuwe Azure-toepassing Insights-resource maken | Microsoft Docs
description: Stel Application Insights bewaking hand matig in voor een nieuwe Live-toepassing.
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: adc40db493c701a21fd30d10a4452edee1b87ab9
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98951260"
---
# <a name="create-an-application-insights-resource"></a>Een Application Insights-resource maken

Met Azure-toepassing Insights worden gegevens over uw toepassing weer gegeven in een Microsoft Azure *resource*. Het maken van een nieuwe resource is daarom een onderdeel van het [instellen van Application Insights om een nieuwe toepassing te bewaken][start]. Nadat u de nieuwe resource hebt gemaakt, kunt u de bijbehorende instrumentatie sleutel ophalen en gebruiken om de Application Insights SDK te configureren. De instrumentatie sleutel koppelt uw telemetrie aan de resource.

## <a name="sign-in-to-microsoft-azure"></a>Aanmelden bij Microsoft Azure

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="create-an-application-insights-resource"></a>Een Application Insights-resource maken

Meld u aan bij de [Azure Portal](https://portal.azure.com)en maak een Application Insights resource:

![Klik op het plus teken (+) in de linkerbovenhoek. Selecteer Ontwikkelhulpprogramma's gevolgd door Application Insights](./media/create-new-resource/new-app-insights.png)

   | Instellingen        |  Waarde           | Beschrijving  |
   | ------------- |:-------------|:-----|
   | **Naam**      | `Unique value` | Naam waarmee de app wordt geïdentificeerd die u bewaken. |
   | **Resourcegroep**     | `myResourceGroup`      | Naam voor de nieuwe of bestaande resource groep om app Insights-gegevens te hosten. |
   | **Regio** | `East US` | Kies een locatie in uw buurt of in de buurt van waar de app wordt gehost. |
   | **Resource modus** | `Classic` of `Workspace-based` | Resources op basis van een werk ruimte zijn momenteel beschikbaar als open bare preview-versie en u kunt uw Application Insights telemetrie verzenden naar een algemene Log Analytics-werk ruimte. Zie het [artikel over resources op basis van werk ruimte](create-workspace-resource.md)voor meer informatie.

> [!NOTE]
> Hoewel u dezelfde resource naam in verschillende resource groepen kunt gebruiken, kan het nuttig zijn om een wereld wijd unieke naam te gebruiken. Dit kan handig zijn als u van plan bent om [query's voor meerdere resources uit te voeren](../log-query/cross-workspace-query.md#identifying-an-application) terwijl het de vereiste syntaxis vereenvoudigt.

Voer de juiste waarden in de vereiste velden in en selecteer vervolgens **controleren + maken**.

[!div class="mx-imgBorder"]
![Voer waarden in de vereiste velden in en selecteer vervolgens controleren + maken.](./media/create-new-resource/review-create.png)

Als uw app is gemaakt, wordt er een nieuw deel venster geopend. In dit deel venster ziet u prestatie-en gebruiks gegevens over uw bewaakte toepassing. 

## <a name="copy-the-instrumentation-key"></a>De instrumentatie sleutel kopiëren

De instrumentatie sleutel identificeert de resource waaraan u de telemetriegegevens wilt koppelen. U moet de instrumentatie sleutel kopiëren en toevoegen aan de code van uw toepassing.

> [!IMPORTANT]
> Nieuwe Azure-regio's **vereisen** het gebruik van verbindings reeksen in plaats van instrumentatie sleutels. Met de [verbindings reeks](./sdk-connection-string.md?tabs=net) wordt de resource geïdentificeerd waaraan u de telemetriegegevens wilt koppelen. U kunt ook de eind punten wijzigen die door de resource worden gebruikt als een bestemming voor uw telemetrie. U moet de connection string kopiëren en toevoegen aan de code van uw toepassing of aan een omgevings variabele.

## <a name="install-the-sdk-in-your-app"></a>De SDK installeren in uw app

Installeer de Application Insights SDK in uw app. Deze stap is sterk afhankelijk van het type van uw toepassing.

Gebruik de instrumentatie sleutel voor [het configureren van de SDK die u in uw toepassing installeert][start].

De SDK bevat standaard modules die telemetrie verzenden zonder dat u extra code hoeft te schrijven. [Gebruik de API][api] om uw eigen telemetrie te verzenden voor het volgen van gebruikers acties of het vaststellen van problemen met meer details.

## <a name="creating-a-resource-automatically"></a>Een resource automatisch maken

### <a name="powershell"></a>PowerShell

Een nieuwe Application Insights-resource maken

```powershell
New-AzApplicationInsights [-ResourceGroupName] <String> [-Name] <String> [-Location] <String> [-Kind <String>]
 [-Tag <Hashtable>] [-DefaultProfile <IAzureContextContainer>] [-WhatIf] [-Confirm] [<CommonParameters>]
```

#### <a name="example"></a>Voorbeeld

```powershell
New-AzApplicationInsights -Kind java -ResourceGroupName testgroup -Name test1027 -location eastus
```
#### <a name="results"></a>Resultaten

```powershell
Id                 : /subscriptions/{subid}/resourceGroups/testgroup/providers/microsoft.insights/components/test1027
ResourceGroupName  : testgroup
Name               : test1027
Kind               : web
Location           : eastus
Type               : microsoft.insights/components
AppId              : 8323fb13-32aa-46af-b467-8355cf4f8f98
ApplicationType    : web
Tags               : {}
CreationDate       : 10/27/2017 4:56:40 PM
FlowType           :
HockeyAppId        :
HockeyAppToken     :
InstrumentationKey : 00000000-aaaa-bbbb-cccc-dddddddddddd
ProvisioningState  : Succeeded
RequestSource      : AzurePowerShell
SamplingPercentage :
TenantId           : {subid}
```

Raadpleeg de [documentatie van Azure PowerShell](/powershell/module/az.applicationinsights/new-azapplicationinsights)voor de volledige Power shell-documentatie voor deze cmdlet en voor meer informatie over het ophalen van de instrumentatie sleutel.

### <a name="azure-cli-preview"></a>Azure CLI (preview-versie)

Als u toegang wilt krijgen tot de preview-Application Insights Azure CLI-opdrachten, moet u eerst het volgende uitvoeren:

```azurecli
 az extension add -n application-insights
```

Als u de opdracht niet uitvoert `az extension add` , wordt een fout bericht weer gegeven waarin staat: `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

U kunt nu het volgende uitvoeren om uw Application Insights-resource te maken:

```azurecli
az monitor app-insights component create --app
                                         --location
                                         --resource-group
                                         [--application-type]
                                         [--kind]
                                         [--tags]
```

#### <a name="example"></a>Voorbeeld

```azurecli
az monitor app-insights component create --app demoApp --location westus2 --kind web -g demoRg --application-type web
```

#### <a name="results"></a>Resultaten

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g demoApp  --application-type web
{
  "appId": "87ba512c-e8c9-48d7-b6eb-118d4aee2697",
  "applicationId": "demoApp",
  "applicationType": "web",
  "creationDate": "2019-08-16T18:15:59.740014+00:00",
  "etag": "\"0300edb9-0000-0100-0000-5d56f2e00000\"",
  "flowType": "Bluefield",
  "hockeyAppId": null,
  "hockeyAppToken": null,
  "id": "/subscriptions/{subid}/resourceGroups/demoApp/providers/microsoft.insights/components/demoApp",
  "instrumentationKey": "00000000-aaaa-bbbb-cccc-dddddddddddd",
  "kind": "web",
  "location": "eastus",
  "name": "demoApp",
  "provisioningState": "Succeeded",
  "requestSource": "rest",
  "resourceGroup": "demoApp",
  "samplingPercentage": null,
  "tags": {},
  "tenantId": {tenantID},
  "type": "microsoft.insights/components"
}
```

Raadpleeg de [documentatie van Azure cli](/cli/azure/ext/application-insights/monitor/app-insights/component#ext-application-insights-az-monitor-app-insights-component-create)voor de volledige Azure cli-documentatie voor deze opdracht en voor meer informatie over het ophalen van de instrumentatie sleutel.

## <a name="next-steps"></a>Volgende stappen
* [Diagnostische gegevens doorzoeken](./diagnostic-search.md)
* [Metrische gegevens verkennen](../platform/metrics-charts.md)
* [Analytics-query's schrijven](../log-query/log-query-overview.md)

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[diagnostic]: ./diagnostic-search.md
[metrics]: ../platform/metrics-charts.md
[start]: ./app-insights-overview.md

