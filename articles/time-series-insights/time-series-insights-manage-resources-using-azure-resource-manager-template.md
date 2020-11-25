---
title: Uw omgeving beheren met Azure Resource Manager sjablonen-Azure Time Series Insights | Microsoft Docs
description: Meer informatie over het programmatisch beheren van uw Azure Time Series Insights-omgeving met behulp van Azure Resource Manager.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: 493750e69b1fdc935b04d6dc705cfd046b6b086e
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "96011656"
---
# <a name="create-azure-time-series-insights-gen-1-resources-using-azure-resource-manager-templates"></a>Azure Time Series Insights gen 1-resources maken met behulp van Azure Resource Manager sjablonen

> [!CAUTION]
> Dit is een Gen1-artikel.

In dit artikel wordt beschreven hoe u Azure Time Series Insights-resources maakt en implementeert met behulp van [Azure Resource Manager sjablonen](../azure-resource-manager/index.yml), Power shell en de resource provider van Azure time series Insights.

Azure Time Series Insights ondersteunt de volgende bronnen:

   | Resource | Beschrijving |
   | --- | --- |
   | Omgeving | Een Azure Time Series Insights omgeving is een logische groepering van gebeurtenissen die worden gelezen vanuit gebeurtenis-Brokers, opgeslagen en beschikbaar worden gesteld voor query's. Lees [uw Azure time series Insights-omgeving plannen](time-series-insights-environment-planning.md) voor meer informatie |
   | Bron van gebeurtenis | Een gebeurtenis bron is een verbinding met een gebeurtenis-Broker van waaruit Azure Time Series Insights gebeurtenissen in de omgeving leest en opneemt. De momenteel ondersteunde gebeurtenis bronnen zijn IoT Hub en Event hub. |
   | Gegevensset voor referentie | Referentie gegevens sets bevatten meta gegevens over de gebeurtenissen in de omgeving. Meta gegevens in de referentie gegevens sets worden samengevoegd met gebeurtenissen tijdens de ingang. Referentie gegevens sets worden als resources gedefinieerd op basis van hun gebeurtenis sleutel eigenschappen. De daad werkelijke meta gegevens waaruit de referentie gegevensverzameling bestaat, worden geüpload of gewijzigd via data plan-Api's. |
   | Toegangs beleid | Toegangs beleid verleent machtigingen voor het uitgeven van gegevens query's, het bewerken van referentie gegevens in de omgeving en het delen van opgeslagen query's en perspectieven die aan de omgeving zijn gekoppeld. Lees [granting Data Access to a Azure time series Insights environment using Azure Portal](./concepts-access-policies.md) voor meer informatie. |

Een resource manager-sjabloon is een JSON-bestand dat de infra structuur en configuratie van resources in een resource groep definieert. De volgende documenten beschrijven sjabloon bestanden in meer detail:

- [Implementatie van Azure Resource Manager-sjabloon](../azure-resource-manager/templates/overview.md)
- [Resources implementeren met Resource Manager-sjablonen en Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)
- [Resource typen van micro soft. TimeSeriesInsights](/azure/templates/microsoft.timeseriesinsights/allversions)

De Quick Start-sjabloon [201-timeseriesinsights-met-eventhub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-timeseriesinsights-environment-with-eventhub) wordt gepubliceerd op github. Met deze sjabloon wordt een Azure Time Series Insights omgeving gemaakt, een onderliggende gebeurtenis bron die is geconfigureerd voor het gebruik van gebeurtenissen van een event hub en toegangs beleid waarmee toegang wordt verleend tot de gegevens van de omgeving. Als er geen bestaande Event hub wordt opgegeven, wordt er een gemaakt met de implementatie.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="specify-deployment-template-and-parameters"></a>Implementatie sjabloon en-para meters opgeven

In de volgende procedure wordt beschreven hoe u Power shell gebruikt om een Azure Resource Manager sjabloon te implementeren waarmee een Azure Time Series Insights omgeving wordt gemaakt, een onderliggende gebeurtenis bron die is geconfigureerd voor het gebruik van gebeurtenissen van een event hub en toegangs beleid waarmee toegang wordt verleend tot de gegevens van de omgeving. Als er geen bestaande Event hub wordt opgegeven, wordt er een gemaakt met de implementatie.

1. Installeer Azure PowerShell door de instructies in aan de slag te volgen [met Azure PowerShell](/powershell/azure/get-started-azureps).

1. Kloon of kopieer de sjabloon [201-timeseriesinsights-with-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.json) van github.

   - Een parameter bestand maken

     Als u een parameter bestand wilt maken, kopieert u het bestand [201-timeseriesinsights-environment-with-eventhub](https://github.com/Azure/azure-quickstart-templates/blob/master/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json) .

      [!code-json[deployment-parameters](~/quickstart-templates/201-timeseriesinsights-environment-with-eventhub/azuredeploy.parameters.json)]

    <div id="required-parameters"></div>

   - Vereiste parameters

     | Parameter | Beschrijving |
     | --- | --- |
     | eventHubNamespaceName | De naam ruimte van de bron Event Hub. |
     | eventHubName | De naam van de bron Event Hub. |
     | consumerGroupName | De naam van de Consumer groep die door de Azure Time Series Insights-service wordt gebruikt om de gegevens van de Event Hub te lezen. **Opmerking:** Om bron conflicten te voor komen, moet deze Consumer groep worden toegewezen aan de Azure Time Series Insights-service en niet worden gedeeld met andere lezers. |
     | environmentName | De naam van de omgeving. De naam mag niet de volgende tekens bevatten:   `<` ,,,,,,,, `>` `%` `&` `:` `\\` `?` `/` en alle Stuur codes. Alle andere tekens zijn toegestaan.|
     | eventSourceName | De naam van de onderliggende bron van de gebeurtenis bron. De naam mag niet de volgende tekens bevatten:   `<` ,,,,,,,, `>` `%` `&` `:` `\\` `?` `/` en alle Stuur codes. Alle andere tekens zijn toegestaan. |

    <div id="optional-parameters"></div>

   - Optionele parameters

     | Parameter | Beschrijving |
     | --- | --- |
     | existingEventHubResourceId | Een optionele Resource-ID van een bestaande Event hub die wordt verbonden met de Azure Time Series Insights omgeving via de bron van de gebeurtenis. **Opmerking:** De gebruiker die de sjabloon implementeert, moet bevoegdheden hebben om de listkeys ophalen-bewerking uit te voeren op de Event hub. Als er geen waarde wordt door gegeven, wordt er een nieuwe Event Hub gemaakt door de sjabloon. |
     | environmentDisplayName | Een optionele beschrijvende naam die wordt weer gegeven in hulp middelen of gebruikers interfaces in plaats van de naam van de omgeving. |
     | environmentSkuName | De naam van de SKU. Lees de pagina met prijzen voor [Azure time series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/)voor meer informatie.  |
     | environmentSkuCapacity | De capaciteit van de eenheid van de SKU. Lees de pagina met prijzen voor [Azure time series Insights](https://azure.microsoft.com/pricing/details/time-series-insights/)voor meer informatie.|
     | environmentDataRetentionTime | De minimale time span van de activiteiten van de omgeving zijn beschikbaar voor query's. De waarde moet worden opgegeven in de ISO 8601-indeling, bijvoorbeeld `P30D` voor een Bewaar beleid van 30 dagen. |
     | eventSourceDisplayName | Een optionele beschrijvende naam die wordt weer gegeven in hulp middelen of gebruikers interfaces in plaats van de naam van de gebeurtenis bron. |
     | eventSourceTimestampPropertyName | De gebeurtenis eigenschap die wordt gebruikt als tijds tempel van de gebeurtenis bron. Als er geen waarde is opgegeven voor timestampPropertyName, of als null of lege teken reeks is opgegeven, wordt de aanmaak tijd van de gebeurtenis gebruikt. |
     | eventSourceKeyName | De naam van de gedeelde toegangs sleutel die door de Azure Time Series Insights-service wordt gebruikt om verbinding te maken met de Event Hub. |
     | accessPolicyReaderObjectIds | Een lijst met object-Id's van de gebruikers of toepassingen in azure AD die lezers toegang moeten hebben tot de omgeving. U kunt de objectId voor de Service-Principal verkrijgen door de cmdlets **Get-AzADUser** of **Get-AzADServicePrincipal** aan te roepen. Het maken van een toegangs beleid voor Azure AD-groepen wordt nog niet ondersteund. |
     | accessPolicyContributorObjectIds | Een lijst met object-Id's van de gebruikers of toepassingen in azure AD die Inzender toegang moeten hebben tot de omgeving. U kunt de objectId voor de Service-Principal verkrijgen door de cmdlets **Get-AzADUser** of **Get-AzADServicePrincipal** aan te roepen. Het maken van een toegangs beleid voor Azure AD-groepen wordt nog niet ondersteund. |

   - Als voor beeld wordt het volgende parameter bestand gebruikt voor het maken van een omgeving en een gebeurtenis bron die gebeurtenissen uit een bestaande Event Hub leest. Er worden ook twee toegangs beleidsregels gemaakt waarmee Inzender toegang tot de omgeving kan worden verleend.

     ```JSON
     {
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
             "eventHubNamespaceName": {
                 "value": "tsiTemplateTestNamespace"
             },
             "eventHubName": {
                 "value": "tsiTemplateTestEventHub"
             },
             "consumerGroupName": {
                 "value": "tsiTemplateTestConsumerGroup"
             },
             "environmentName": {
                 "value": "tsiTemplateTestEnvironment"
             },
             "eventSourceName": {
                 "value": "tsiTemplateTestEventSource"
             },
             "existingEventHubResourceId": {
                 "value": "/subscriptions/{yourSubscription}/resourceGroups/MyDemoRG/providers/Microsoft.EventHub/namespaces/tsiTemplateTestNamespace/eventhubs/tsiTemplateTestEventHub"
             },
             "accessPolicyContributorObjectIds": {
                 "value": [
                     "AGUID001-0000-0000-0000-000000000000",
                     "AGUID002-0000-0000-0000-000000000000"
                 ]
             }
         }
     }
     ```

   - Lees het artikel [para meters](../azure-resource-manager/templates/parameter-files.md) voor meer informatie.

## <a name="deploy-the-quickstart-template-locally-using-powershell"></a>De Quick Start-sjabloon lokaal implementeren met Power shell

> [!IMPORTANT]
> Met de volgende opdracht regel bewerkingen wordt de [AZ Power shell-module](/powershell/azure/)beschreven.

1. Meld u in Power shell aan bij uw Azure-account.

    - Voer de volgende opdracht uit vanuit een Power shell-prompt:

      ```powershell
      Connect-AzAccount
      ```

    - U wordt gevraagd om u aan te melden bij uw Azure-account. Nadat u zich hebt aangemeld, voert u de volgende opdracht uit om uw beschik bare abonnementen weer te geven:

      ```powershell
      Get-AzSubscription
      ```

    - Met deze opdracht wordt een lijst met beschik bare Azure-abonnementen geretourneerd. Kies een abonnement voor de huidige sessie door de volgende opdracht uit te voeren. Vervang door `<YourSubscriptionId>` de GUID voor het Azure-abonnement dat u wilt gebruiken:

      ```powershell
      Set-AzContext -SubscriptionID <YourSubscriptionId>
      ```

1. Maak een nieuwe resource groep als deze niet bestaat.

   - Als u geen bestaande resource groep hebt, maakt u een nieuwe resource groep met de opdracht **New-AzResourceGroup** . Geef de naam op van de resource groep en de locatie die u wilt gebruiken. Bijvoorbeeld:

     ```powershell
     New-AzResourceGroup -Name MyDemoRG -Location "West US"
     ```

   - Als dit lukt, wordt een samen vatting van de nieuwe resource groep weer gegeven.

     ```powershell
     ResourceGroupName : MyDemoRG
     Location          : westus
     ProvisioningState : Succeeded
     Tags              :
     ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
     ```

1. De implementatie testen.

   - Valideer uw implementatie door de cmdlet uit te voeren `Test-AzResourceGroupDeployment` . Wanneer u de implementatie test, geeft u de para meters op exact dezelfde manier op als bij het uitvoeren van de implementatie.

     ```powershell
     Test-AzResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
     ```

1. De implementatie maken

    - Als u de nieuwe implementatie wilt maken, voert u de `New-AzResourceGroupDeployment` cmdlet uit en geeft u de benodigde para meters op wanneer u hierom wordt gevraagd. De para meters bevatten een naam voor uw implementatie, de naam van uw resource groep en het pad of de URL naar het sjabloon bestand. Als de **modus** parameter niet is opgegeven, wordt de standaard waarde **Incrementeel** gebruikt. Lees [incrementele en volledige implementaties](../azure-resource-manager/templates/deployment-modes.md)voor meer informatie.

    - Met de volgende opdracht wordt u gevraagd om de vijf vereiste para meters in het Power shell-venster:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
      ```

    - Als u in plaats daarvan een parameter bestand wilt opgeven, gebruikt u de volgende opdracht:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
      ```

    - U kunt ook inline-para meters gebruiken wanneer u de cmdlet implementatie uitvoert. De opdracht is als volgt:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
      ```

    - Als u een [volledige](../azure-resource-manager/templates/deployment-modes.md) implementatie wilt uitvoeren, stelt u de para meter voor de **modus** in op **voltooid**:

      ```powershell
      New-AzResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
      ```

1. De implementatie controleren

    - Als de resources met succes zijn geïmplementeerd, wordt een samen vatting van de implementatie weer gegeven in het Power shell-venster:

      ```powershell
       DeploymentName          : MyDemoDeployment
       ResourceGroupName       : MyDemoRG
       ProvisioningState       : Succeeded
       Timestamp               : 10/11/2019 3:20:37 AM
       Mode                    : Incremental
       TemplateLink            :
       Parameters              :
                                 Name                                Type                       Value
                                 ==================================  =========================  ==========
                                 eventHubNewOrExisting               String                     new
                                 eventHubResourceGroup               String                     MyDemoRG
                                 eventHubNamespaceName               String                     tsiquickstartns
                                 eventHubName                        String                     tsiquickstarteh
                                 consumerGroupName                   String                     tsiquickstart
                                 environmentName                     String                     tsiquickstart
                                 environmentDisplayName              String                     tsiquickstart
                                 environmentSkuName                  String                     S1
                                 environmentSkuCapacity              Int                        1
                                 environmentDataRetentionTime        String                     P30D
                                 eventSourceName                     String                     tsiquickstart
                                 eventSourceDisplayName              String                     tsiquickstart
                                 eventSourceTimestampPropertyName    String
                                 eventSourceKeyName                  String                     manage
                                 accessPolicyReaderObjectIds         Array                      []
                                 accessPolicyContributorObjectIds    Array                      []
                                 location                            String                     westus

       Outputs                 :
                                  Name              Type                       Value
                                  ================  =========================  ==========
                                  dataAccessFQDN    String
                                  11aa1aa1-a1aa-1a1a-a11a-aa111a111a11.env.timeseries.azure.com

       DeploymentDebugLogLevel :
      ```

1. Implementeer de Quick Start-sjabloon via de Azure Portal

   - De start pagina van de Snelstartgids op GitHub bevat ook een knop **implementeren naar Azure** . Als u op deze knop klikt, wordt er een aangepaste implementatie pagina in de Azure Portal. Op deze pagina kunt u waarden voor elk van de para meters invoeren of selecteren in de tabellen [vereiste para meters](#required-parameters) of [optionele para meters](#optional-parameters) . Wanneer u de instellingen hebt ingevuld, wordt de sjabloon implementatie gestart wanneer u op de knop **aanschaffen** klikt.
    </br>
    </br>
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-timeseriesinsights-environment-with-eventhub%2Fazuredeploy.json" target="_blank">
       <img src="https://azuredeploy.net/deploybutton.png" alt="The Deploy to Azure button."/>
    </a>

## <a name="next-steps"></a>Volgende stappen

- Lees [Azure time series Insights Management](/rest/api/time-series-insights-management/)voor informatie over het programmatisch beheren van Azure time series Insights-resources met behulp van rest-api's.