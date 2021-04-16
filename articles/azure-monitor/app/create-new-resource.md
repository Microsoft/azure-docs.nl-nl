---
title: Een nieuwe Azure-toepassing Insights-resource maken | Microsoft Docs
description: Handmatig instellen Application Insights voor een nieuwe live-toepassing.
ms.topic: conceptual
ms.date: 02/10/2021
ms.openlocfilehash: 6158b5604046897e20053c67321f26d650c21b7f
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566219"
---
# <a name="create-an-application-insights-resource"></a>Een Application Insights-resource maken

Azure-toepassing Insights worden gegevens over uw toepassing weergegeven in Microsoft Azure *resource*. Het maken van een nieuwe resource maakt daarom deel uit van [het instellen van Application Insights voor het bewaken van een nieuwe toepassing.][start] Nadat u de nieuwe resource hebt gemaakt, kunt u de instrumentatiesleutel ervan op halen en deze gebruiken om de SDK Application Insights configureren. De instrumentatiesleutel koppelt uw telemetrie aan de resource.

> [!IMPORTANT]
> [Klassieke Application Insights is afgeschaft.](https://azure.microsoft.com/updates/we-re-retiring-classic-application-insights-on-29-february-2024/) Volg deze instructies [voor het upgraden naar werkruimte-gebaseerde Application Insights](convert-classic-resource.md).

## <a name="sign-in-to-microsoft-azure"></a>Meld u aan bij Microsoft Azure

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="create-an-application-insights-resource"></a>Een Application Insights-resource maken

Meld u aan bij [Azure Portal](https://portal.azure.com)en maak een Application Insights resource:

![Klik op het +-teken in de linkerbovenhoek. Selecteer Ontwikkelhulpprogramma's gevolgd door Application Insights](./media/create-new-resource/new-app-insights.png)

   | Instellingen        |  Waarde           | Beschrijving  |
   | ------------- |:-------------|:-----|
   | **Naam**      | `Unique value` | Naam die de app identificeert die u wilt bewaken. |
   | **Resourcegroep**     | `myResourceGroup`      | Naam voor de nieuwe of bestaande resourcegroep voor het hosten van App Insights-gegevens. |
   | **Regio** | `East US` | Kies een locatie in uw buurt of in de buurt van waar de app wordt gehost. |
   | **Resourcemodus** | `Classic` of `Workspace-based` | Met resources op basis van werkruimten kunt u uw Application Insights verzenden naar een algemene Log Analytics-werkruimte. Zie het artikel over resources op basis van [werkruimten voor meer informatie.](create-workspace-resource.md)

> [!NOTE]
> Hoewel u dezelfde resourcenaam in verschillende resourcegroepen kunt gebruiken, kan het nuttig zijn om een wereldwijd unieke naam te gebruiken. Dit kan handig zijn als u van plan bent om [query's voor verschillende resources](../logs/cross-workspace-query.md#identifying-an-application) uit te voeren, omdat dit de vereiste syntaxis vereenvoudigt.

Voer de juiste waarden in de vereiste velden in en selecteer **beoordelen en maken.**

> [!div class="mx-imgBorder"]
> ![Voer waarden in de vereiste velden in en selecteer vervolgens Controleren + maken.](./media/create-new-resource/review-create.png)

Wanneer uw app is gemaakt, wordt er een nieuw deelvenster geopend. In dit deelvenster ziet u prestatie- en gebruiksgegevens over uw bewaakte toepassing. 

## <a name="copy-the-instrumentation-key"></a>De instrumentatiesleutel kopiëren

De instrumentatiesleutel identificeert de resource waarmee u uw telemetriegegevens wilt koppelen. U moet de instrumentatiesleutel kopiëren en toevoegen aan de code van uw toepassing.

> [!IMPORTANT]
> Nieuwe **Azure-regio's vereisen** het gebruik van verbindingsreeksen in plaats van instrumentatiesleutels. [Verbindingsreeks](./sdk-connection-string.md?tabs=net) identificeert de resource waar u uw telemetriegegevens aan wilt koppelen. U kunt hiermee ook de eindpunten wijzigen die door uw resource worden gebruikt als bestemming voor uw telemetrie. U moet de connection string en toevoegen aan de code van uw toepassing of aan een omgevingsvariabele.

## <a name="install-the-sdk-in-your-app"></a>De SDK installeren in uw app

Installeer de Application Insights SDK in uw app. Deze stap is sterk afhankelijk van het type toepassing.

Gebruik de instrumentatiesleutel om de [SDK te configureren die u in uw toepassing installeert.][start]

De SDK bevat standaardmodules die telemetrie verzenden zonder dat u extra code moet schrijven. Als u gebruikersacties wilt bijhouden of problemen gedetailleerder wilt diagnosticeren, gebruikt u de [API][api] om uw eigen telemetrie te verzenden.

## <a name="creating-a-resource-automatically"></a>Automatisch een resource maken

### <a name="powershell"></a>PowerShell

Een nieuwe resource Application Insights maken

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

Voor de volledige PowerShell-documentatie voor deze cmdlet en voor informatie over het ophalen van de instrumentatiesleutel raadpleegt u [Azure PowerShell documentatie.](/powershell/module/az.applicationinsights/new-azapplicationinsights)

### <a name="azure-cli-preview"></a>Azure CLI (preview)

Voor toegang tot de preview Application Insights Azure CLI-opdrachten moet u eerst het volgende uitvoeren:

```azurecli
 az extension add -n application-insights
```

Als u de opdracht niet `az extension add` uitvoeren, ziet u een foutbericht met de volgende melding: `az : ERROR: az monitor: 'app-insights' is not in the 'az monitor' command group. See 'az monitor --help'.`

U kunt nu het volgende uitvoeren om uw Application Insights maken:

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

Raadpleeg de Azure CLI-documentatie voor de volledige Azure CLI-documentatie voor deze opdracht en voor meer informatie over het ophalen van de [instrumentatiesleutel.](/cli/azure/ext/application-insights/monitor/app-insights/component#ext-application-insights-az-monitor-app-insights-component-create)

## <a name="next-steps"></a>Volgende stappen
* [Diagnostische gegevens doorzoeken](./diagnostic-search.md)
* [Metrische gegevens verkennen](../essentials/metrics-charts.md)
* [Analytics-query's schrijven](../logs/log-query-overview.md)

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[diagnostic]: ./diagnostic-search.md
[metrics]: ../essentials/metrics-charts.md
[start]: ./app-insights-overview.md

