---
title: Een nieuwe Azure Monitor maken Application Insights resource op basis van werk ruimte | Microsoft Docs
description: Meer informatie over de stappen die nodig zijn om de nieuwe Azure Monitor Application Insights op werk ruimte gebaseerde resources in te scha kelen.
ms.topic: conceptual
ms.date: 10/06/2020
ms.openlocfilehash: 3ec0b25a24af13b29a3568165009f8a6d66e0218
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100578341"
---
# <a name="workspace-based-application-insights-resources"></a>Application Insights resources op basis van een werk ruimte

Resources op basis van een werk ruimte ondersteunen volledige integratie tussen Application Insights en Log Analytics. U kunt nu uw Application Insights telemetrie naar een gemeen schappelijke Log Analytics-werk ruimte verzenden, zodat u volledige toegang hebt tot alle functies van Log Analytics terwijl logboeken voor toepassings-, infra structuur-en platform bestanden in één geconsolideerde locatie worden bewaard.

Dit biedt ook algemene op rollen gebaseerd toegangs beheer (Azure RBAC) van Azure voor uw resources en elimineert de nood zaak van query's tussen apps en werk ruimten.

> [!NOTE]
> Gegevens opname en retentie voor op werk ruimte gebaseerde Application Insights resources worden gefactureerd via de Log Analytics werk ruimte waarin de gegevens zich bevinden. Meer [informatie]( ./pricing.md#workspace-based-application-insights) over facturering voor op werk ruimte gebaseerde Application Insights resources.

## <a name="new-capabilities"></a>Nieuwe functionaliteit

Met Application Insights op basis van een werk ruimte kunt u profiteren van de nieuwste mogelijkheden van Azure Monitor en Log Analytics met inbegrip van:

* Door de [klant beheerde sleutels (CMK)](../logs/customer-managed-keys.md) bieden versleuteling op rest voor uw gegevens met versleutelings sleutels waartoe alleen u toegang hebt.
* Met de [persoonlijke Azure-koppeling](../logs/private-link-security.md) kunt u Azure PaaS-services veilig koppelen aan uw virtuele netwerk met behulp van privé-eind punten.
* [Met uw eigen opslag (BYOS) voor Profiler en snapshot debugger](./profiler-bring-your-own-storage.md) hebt u volledige controle over het beleid voor versleuteling op rest, het beheer beleid voor levens duur en netwerk toegang tot alle gegevens die zijn gekoppeld aan Application Insights Profiler en snapshot debugger. 
* Met [capaciteits reserverings lagen](../logs/manage-cost-storage.md#pricing-model) kunt u Maxi maal 25% besparen op basis van de betalen naar gebruik-prijs. 
* Snellere gegevens opname via Log Analytics streaming-opname.

## <a name="create-workspace-based-resource"></a>Resource op basis van werk ruimte maken

Meld u aan bij de [Azure Portal](https://portal.azure.com)en maak een Application Insights resource:

> [!div class="mx-imgBorder"]
> ![Op werkruimte gebaseerde Application Insights-resource](./media/create-workspace-resource/create-workspace-based.png)

Als u nog geen bestaande Log Analytics-werk ruimte hebt, [raadpleegt u de documentatie voor het maken van log Analytics werk ruimte](../logs/quick-create-workspace.md).

**Op werk ruimte gebaseerde resources zijn momenteel beschikbaar in alle commerciële regio's en Azure Government**

Zodra de resource is gemaakt, worden de bijbehorende werkruimte gegevens weer gegeven in het deel venster **overzicht** :

![Werkruimtenaam](./media/create-workspace-resource/workspace-name.png)

Als u op de tekst van de blauwe koppeling klikt, gaat u naar de gekoppelde Log Analytics-werk ruimte waar u kunt profiteren van de nieuwe geïntegreerde werk ruimte query omgeving.

> [!NOTE]
> We bieden nog steeds volledige compatibiliteit voor uw Application Insights klassieke resource query's, werkmappen en waarschuwingen op basis van Logboeken in de Application Insights ervaring. Als u wilt zoeken/weer geven op [basis van de nieuwe, op werk ruimte gebaseerde tabel structuur/schema,](apm-tables.md) moet u eerst naar uw log Analytics-werk ruimte navigeren. Door **Logboeken (Analytics)** te selecteren in de Application Insights deel Vensters krijgt u toegang tot de klassieke Application Insights query-ervaring.

## <a name="copy-the-connection-string"></a>De verbindingsreeks kopiëren

De [Connection String](./sdk-connection-string.md?tabs=net) identificeert de resource waaraan u de telemetriegegevens wilt koppelen. U kunt ook de eind punten wijzigen die door de resource worden gebruikt als een bestemming voor uw telemetrie. U moet de connection string kopiëren en toevoegen aan de code van uw toepassing of aan een omgevings variabele.

## <a name="monitoring-configuration"></a>Bewakings configuratie

Wanneer een Application Insights bron op basis van een werk ruimte is gemaakt, is het configureren van de bewaking relatief eenvoudig.

### <a name="code-based-application-monitoring"></a>Toepassings bewaking op basis van code

Voor het bewaken van toepassingen op basis van code installeert u gewoon de juiste Application Insights SDK en wijst u deze naar de instrument sleutel of connection string naar de zojuist gemaakte resource.  

Raadpleeg de Language/Framework-specifieke documentatie voor gedetailleerde documentatie over het instellen van een Application Insights SDK voor bewaking op basis van code.

- [ASP.NET](./asp-net.md)
- [ASP.NET Core ](./asp-net-core.md)
- [Achtergrond taken & moderne console toepassingen (.NET/.NET core)](./worker-service.md)
- [Klassieke console toepassingen (.NET)](./console.md) 
- [Diagnostisch ](./java-get-started.md?tabs=maven)
- [JavaScript](./javascript.md)
- [Node.js](./nodejs.md)
- [Python](./opencensus-python.md)

### <a name="codeless-monitoring-and-visual-studio-resource-creation"></a>Bewaking en het maken van Visual Studio-resources op code

Voor het coderen van services, zoals Azure Functions en Azure-app Services, moet u eerst uw op werk ruimte gebaseerde Application Insights resource maken en vervolgens naar die resource verwijzen tijdens de configuratie fase bewaking.

Hoewel deze services de mogelijkheid bieden om een nieuwe Application Insights resource te maken binnen hun eigen bron aanmaak proces, zijn resources die zijn gemaakt via deze opties voor de gebruikers interface momenteel beperkt tot de klassieke Application Insights-ervaring.

Hetzelfde geldt voor de functie voor het maken van Application Insights resources in Visual Studio voor ASP.NET en ASP.NET Core. U moet een bestaande resource op basis van een werk ruimte selecteren in de gebruikers interface van Visual Studio monitoring-activering. Als u nieuwe resource maken selecteert in Visual Studio, beperkt u het maken van een klassieke Application Insights-resource.

## <a name="creating-a-resource-automatically"></a>Een resource automatisch maken

### <a name="azure-cli"></a>Azure CLI

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
                                         [--ingestion-access {Disabled, Enabled}]
                                         [--kind]
                                         [--only-show-errors]
                                         [--query-access {Disabled, Enabled}]
                                         [--tags]
                                         [--workspace]
```

#### <a name="example"></a>Voorbeeld

```azurecli
az monitor app-insights component create --app demoApp --location eastus --kind web -g my_resource_group --workspace "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/test1234/providers/microsoft.operationalinsights/workspaces/test1234555"
```

Raadpleeg de [documentatie van Azure cli](/cli/azure/ext/application-insights/monitor/app-insights/component#ext-application-insights-az-monitor-app-insights-component-create)voor de volledige Azure cli-documentatie voor deze opdracht.

### <a name="azure-powershell"></a>Azure PowerShell

De `New-AzApplicationInsights` Power shell-opdracht biedt momenteel geen ondersteuning voor het maken van een Application Insights resource op basis van een werk ruimte. Als u een resource op basis van een werk ruimte wilt maken met Power shell, kunt u de onderstaande Azure Resource Manager sjablonen gebruiken en implementeren met Power shell.

### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen

#### <a name="template-file"></a>Sjabloonbestand

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string"
        },
        "type": {
            "type": "string"
        },
        "regionId": {
            "type": "string"
        },
        "tagsArray": {
            "type": "object"
        },
        "requestSource": {
            "type": "string"
        },
        "workspaceResourceId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "microsoft.insights/components",
            "location": "[parameters('regionId')]",
            "tags": "[parameters('tagsArray')]",
            "apiVersion": "2020-02-02-preview",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Application_Type": "[parameters('type')]",
                "Flow_Type": "Redfield",
                "Request_Source": "[parameters('requestSource')]",
                "WorkspaceResourceId": "[parameters('workspaceResourceId')]"
            }
        }
    ]
}
```

#### <a name="parameters-file"></a>Parameterbestand

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "type": {
            "value": "web"
        },
        "name": {
            "value": "customresourcename"
        },
        "regionId": {
            "value": "eastus"
        },
        "tagsArray": {
            "value": {}
        },
        "requestSource": {
            "value": "Custom"
        },
        "workspaceResourceId": {
            "value": "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/my_resource_group/providers/microsoft.operationalinsights/workspaces/myworkspacename"
        }
    }
}

```

## <a name="modifying-the-associated-workspace"></a>De gekoppelde werk ruimte wijzigen

Zodra een Application Insights resource op basis van een werk ruimte is gemaakt, kunt u de gekoppelde Log Analytics werk ruimte wijzigen.

Selecteer in het deel venster Resource Application Insights **Eigenschappen**  >  **werk ruimte wijzigen**  >  **log Analytics werk ruimten**

## <a name="export-telemetry"></a>Telemetrie exporteren

De verouderde functionaliteit voor continue export wordt niet ondersteund voor op werk ruimte gebaseerde resources. Selecteer in plaats daarvan diagnostische **instellingen**  >  **Diagnostische instelling toevoegen** vanuit uw Application Insights-resource. U kunt alle tabellen selecteren of een subset van tabellen die u wilt archiveren naar een opslag account of naar een Azure Event hub streamen.

> [!NOTE]
> Er zijn momenteel geen extra kosten voor het exporteren van de telemetrie. De prijs informatie voor deze functie is beschikbaar op de [pagina met Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/).  Vóór het begin van de facturering worden meldingen verzonden. Als u ervoor kiest om <feature name> na de kennisgevings periode verder te gaan met, wordt u gefactureerd tegen het toepasselijke rente bedrag. 
 

## <a name="next-steps"></a>Volgende stappen

* [Metrische gegevens verkennen](../essentials/metrics-charts.md)
* [Analytics-query's schrijven](../logs/log-query-overview.md)
