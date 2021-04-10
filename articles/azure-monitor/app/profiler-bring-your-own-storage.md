---
title: BYOS configureren (gebruik uw eigen opslag) voor Profiler & Snapshot Debugger
description: BYOS configureren (gebruik uw eigen opslag) voor Profiler & Snapshot Debugger
ms.topic: conceptual
author: renatosalas
ms.author: regutier
ms.date: 01/14/2021
ms.reviewer: mbullwin
ms.openlocfilehash: 9c3ff91cbfb6423099040a6ea46eeb66f5461f48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100589672"
---
# <a name="configure-bring-your-own-storage-byos-for-application-insights-profiler-and-snapshot-debugger"></a>Uw eigen opslag (BYOS) configureren voor Application Insights Profiler en Snapshot Debugger

## <a name="what-is-bring-your-own-storage-byos-and-why-might-i-need-it"></a>Wat is uw eigen opslag (BYOS) en waarom heb ik deze mogelijk nodig? 
Wanneer u Application Insights Profiler of Snapshot Debugger gebruikt, worden artefacten die zijn gegenereerd door uw toepassing, geüpload naar Azure Storage-accounts via het open bare Internet. Deze accounts worden betaald en beheerd door micro soft voor verwerking en analyse. Micro soft beheert de beleids regels voor het versleutelen van de rest-en levensduur beheer van die artefacten.

Met uw eigen opslag kunt u deze artefacten uploaden naar een opslag account dat u beheert. Dit betekent dat u het beleid voor versleuteling op rest, het levens duur beheer beleid en netwerk toegang beheert. U bent echter verantwoordelijk voor de kosten van het betreffende opslagaccount.

> [!NOTE]
> Als u een persoonlijke koppeling inschakelt, moet u uw eigen opslag ruimte maken. [Raadpleeg de documentatie](../logs/private-link-security.md) voor meer informatie over persoonlijke koppelingen voor Application Insights.
>
> Als u Customer-Managed sleutels inschakelt, moet u uw eigen opslag ruimte maken. [Raadpleeg de documentatie](../logs/customer-managed-keys.md)voor meer informatie over Customer-Managed sleutels voor Application Insights.

## <a name="how-will-my-storage-account-be-accessed"></a>Hoe wordt mijn opslag account geopend?
1. Agents die worden uitgevoerd in uw Virtual Machines of App Service uploaden artefacten (profielen, moment opnamen en symbolen) naar BLOB-containers in uw account. Dit proces omvat het maken van contact met de Application Insights Profiler-of Snapshot Debugger-service om een SAS-token (Shared Access Signature) te verkrijgen voor een nieuwe Blob in uw opslag account.
1. De Application Insights Profiler-of Snapshot Debugger-service analyseert de binnenkomende Blob en schrijft de analyse resultaten en logboek bestanden terug naar de Blob-opslag. Afhankelijk van de beschik bare reken capaciteit kan dit proces na het uploaden op elk moment plaatsvinden.
1. Wanneer u de Profiler-traces of de analyse van de fout opsporing van de moment opname bekijkt, haalt de service de resultaten van de analyse op uit de Blob-opslag.

## <a name="prerequisites"></a>Vereisten
* Zorg ervoor dat u uw opslag account op dezelfde locatie maakt als uw Application Insights-resource. Bijvoorbeeld Als uw Application Insights-bron zich in VS West 2 bevindt, moet uw opslag account ook in VS-West 2. 
* Verleen de rol ' Storage BLOB data contributor ' aan de AAD-toepassing ' Diagnostic Services Trusted Storage Access ' in uw opslag account via de gebruikers interface van de Access Control (IAM).
* Als persoonlijke koppeling is ingeschakeld, configureert u de extra instelling om verbinding met onze vertrouwde micro soft-service van uw Virtual Network toe te staan. 

## <a name="how-to-enable-byos"></a>BYOS inschakelen

### <a name="create-storage-account"></a>Opslagaccount maken
Maak een gloed nieuw opslag account (als u dit niet hebt) op dezelfde locatie als uw Application Insights-resource.
Als uw Application Insights resource is ingeschakeld `West US 2` , moet uw opslag account zich in `West US 2` .

### <a name="grant-access-to-diagnostic-services-to-your-storage-account"></a>Toegang tot diagnostische services verlenen aan uw opslag account
Een BYOS-opslag account wordt gekoppeld aan een Application Insights bron. Er kan slechts één opslag account per Application Insights bron zijn en beide moeten zich op dezelfde locatie bevinden. U kunt hetzelfde opslag account gebruiken met meer dan één Application Insights resource.

Eerst moeten de Application Insights Profiler-en Snapshot Debugger-service toegang krijgen tot het opslag account. Als u toegang wilt verlenen, voegt u de rol toe `Storage Blob Data Contributor` aan de Aad-toepassing met `Diagnostic Services Trusted Storage Access` de naam via de pagina Access Control (IAM) in uw opslag account zoals weer gegeven in afbeelding 1,0. 

Stappen: 
1. Klik op de knop toevoegen in de sectie toewijzing van een rol toevoegen 
1. Selecteer de rol ' BLOB data Inzender voor opslag ' 
1. Selecteer ' Azure AD User, Group of Service Principal ' in de sectie ' toegang toewijzen aan ' 
1. Zoek & de app ' vertrouwde opslag voor diagnostische toegang ' selecteren 
1. Wijzigingen opslaan

_![ Afbeelding 1,0](media/profiler-bring-your-own-storage/figure-10.png)_ 
 _afbeelding 1,0_ 

Nadat u de rol hebt toegevoegd, wordt deze weer gegeven onder de sectie roltoewijzingen, zoals in de onderstaande afbeelding 1,1. 
_![ Afbeelding 1,1](media/profiler-bring-your-own-storage/figure-11.png)_ 
 _afbeelding 1,1_ 

Als u ook een persoonlijke koppeling gebruikt, hebt u één aanvullende configuratie nodig om verbinding met onze vertrouwde micro soft-service van uw Virtual Network mogelijk te maken. Raadpleeg de [documentatie voor opslag netwerk beveiliging](../../storage/common/storage-network-security.md#trusted-microsoft-services).

### <a name="link-your-storage-account-with-your-application-insights-resource"></a>Uw opslag account koppelen aan uw Application Insights-resource
Er zijn drie opties voor het configureren van BYOS voor diagnostische gegevens op code niveau (Profiler/Debugger).

* Azure PowerShell-cmdlets gebruiken
* Met behulp van de Azure-opdracht regel interface (CLI)
* Azure Resource Manager-sjabloon gebruiken

#### <a name="configure-using-azure-powershell-cmdlets"></a>Configureren met behulp van Azure PowerShell-cmdlets

1. Zorg ervoor dat AZ Power Shell 4.2.0 of hoger is geïnstalleerd.

    Raadpleeg de [officiële Azure PowerShell documentatie](/powershell/azure/install-az-ps)om Azure PowerShell te installeren.

1. Installeer de Application Insights Power shell-uitbrei ding.
    ```powershell
    Install-Module -Name Az.ApplicationInsights -Force
    ```

1. Meld u aan met uw Azure-account
    ```powershell
    Connect-AzAccount -Subscription "{subscription_id}"
    ```

    Raadpleeg de [documentatie bij Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount)voor meer informatie over het aanmelden.

1. Verwijder het vorige opslag account dat is gekoppeld aan de Application Insights resource.

    Pattern
    ```powershell
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "{resource_group_name}" -Name "{application_insights_name}"
    Remove-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id
    ```

    Voorbeeld:
    ```powershell
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "byos-test" -Name "byos-test-westus2-ai"
    Remove-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id
    ```

1. Verbind uw opslag account met uw Application Insights-resource.
    
    Pattern
    ```powershell
    $storageAccount = Get-AzStorageAccount -ResourceGroupName "{resource_group_name}" -Name "{storage_account_name}"
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "{resource_group_name}" -Name "{application_insights_name}"
    New-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id -LinkedStorageAccountResourceId $storageAccount.Id
    ```

    Voorbeeld:
    ```powershell
    $storageAccount = Get-AzStorageAccount -ResourceGroupName "byos-test" -Name "byosteststoragewestus2"
    $appInsights = Get-AzApplicationInsights -ResourceGroupName "byos-test" -Name "byos-test-westus2-ai"
    New-AzApplicationInsightsLinkedStorageAccount -ResourceId $appInsights.Id -LinkedStorageAccountResourceId $storageAccount.Id
    ```

#### <a name="configure-using-azure-cli"></a>Configureren met behulp van Azure CLI

1. Zorg ervoor dat u Azure CLI hebt geïnstalleerd.

    Raadpleeg de [officiële Azure cli-documentatie](/cli/azure/install-azure-cli)voor meer informatie over het installeren van Azure cli.

1. Installeer de Application Insights CLI-extensie.
    ```powershell
    az extension add -n application-insights
    ```

1. Verbind uw opslag account met uw Application Insights-resource.

    Pattern
    ```powershell
    az monitor app-insights component linked-storage link --resource-group "{resource_group_name}" --app "{application_insights_name}" --storage-account "{storage_account_name}"
    ```
    
    Voorbeeld:
    ```powershell
    az monitor app-insights component linked-storage link --resource-group "byos-test" --app "byos-test-westus2-ai" --storage-account "byosteststoragewestus2"
    ```
    
    Verwachte uitvoer:
    ```powershell
    {
      "id": "/subscriptions/{subscription}/resourcegroups/byos-test/providers/microsoft.insights/components/byos-test-westus2-ai/linkedstorageaccounts/serviceprofiler",
      "linkedStorageAccount": "/subscriptions/{subscription}/resourceGroups/byos-test/providers/Microsoft.Storage/storageAccounts/byosteststoragewestus2",
      "name": "serviceprofiler",
      "resourceGroup": "byos-test",
      "type": "microsoft.insights/components/linkedstorageaccounts"
    }
    ```

    > [!NOTE]
    > Raadpleeg de [Application INSIGHTS cli-documentatie voor informatie](/cli/azure/ext/application-insights/monitor/app-insights/component/linked-storage)over het uitvoeren van updates voor de gekoppelde opslag accounts aan uw Application Insights-resource.

#### <a name="configure-using-azure-resource-manager-template"></a>Configureren met behulp van Azure Resource Manager sjabloon

1. Maak een Azure Resource Manager-sjabloon bestand met de volgende inhoud (byos.template.jsaan).
    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "applicationinsights_name": {
          "type": "String"
        },
        "storageaccount_name": {
          "type": "String"
        }
      },
      "variables": {},
      "resources": [
        {
          "name": "[concat(parameters('applicationinsights_name'), '/serviceprofiler')]",
          "type": "Microsoft.Insights/components/linkedStorageAccounts",
          "apiVersion": "2020-03-01-preview",
          "properties": {
            "linkedStorageAccount": "[resourceId('Microsoft.Storage/storageAccounts', parameters('storageaccount_name'))]"
          }
        }
      ],
      "outputs": {}
    }
    ```

1. Voer de volgende Power shell-opdracht uit om de vorige sjabloon te implementeren (gekoppeld opslag account maken).

    Pattern
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "{your_resource_name}" -TemplateFile "{local_path_to_arm_template}"
    ```

    Voorbeeld:
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName "byos-test" -TemplateFile "D:\Docs\byos.template.json"
    ```

1. Geef de volgende para meters op wanneer u hierom wordt gevraagd in de Power shell-console:
    
    |           Parameter           |                                Beschrijving                               |
    |-------------------------------|--------------------------------------------------------------------------|
    | application_insights_name     | De naam van de Application Insights resource om BYOS in te scha kelen.            |
    | storage_account_name          | De naam van de bron van het opslag account die u wilt gebruiken als uw BYOS. |
    
    Verwachte uitvoer:
    ```powershell
    Supply values for the following parameters:
    (Type !? for Help.)
    application_insights_name: byos-test-westus2-ai
    storage_account_name: byosteststoragewestus2
    
    DeploymentName          : byos.template
    ResourceGroupName       : byos-test
    ProvisioningState       : Succeeded
    Timestamp               : 4/16/2020 1:24:57 AM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name                            Type                       Value
                              ==============================  =========================  ==========
                              application_insights_name        String                     byos-test-westus2-ai
                              storage_account_name             String                     byosteststoragewestus2
                              
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

1. Schakel diagnostische gegevens op code niveau (Profiler/Debugger) in voor de werk belasting van belang via de Azure Portal. (App Service > Application Insights) _![ Afbeelding 2,0](media/profiler-bring-your-own-storage/figure-20.png)_ 
 _afbeelding 2,0_

## <a name="troubleshooting"></a>Problemen oplossen
### <a name="template-schema-schema_uri-isnt-supported"></a>Het sjabloon schema {schema_uri} wordt niet ondersteund.
* Zorg ervoor dat de `$schema` eigenschap van de sjabloon geldig is. Het moet het volgende patroon volgen: `https://schema.management.azure.com/schemas/{schema_version}/deploymentTemplate.json#`
* Zorg ervoor dat de `schema_version` sjabloon zich in geldige waarden bevindt: `2014-04-01-preview, 2015-01-01, 2018-05-01, 2019-04-01, 2019-08-01` .
    Foutbericht:
    ```powershell
    New-AzResourceGroupDeployment : 11:53:49 AM - Error: Code=InvalidTemplate; Message=Deployment template validation failed: 'Template schema
    'https://schema.management.azure.com/schemas/2020-01-01/deploymentTemplate.json#' is not supported. Supported versions are
    '2014-04-01-preview,2015-01-01,2018-05-01,2019-04-01,2019-08-01'. Please see https://aka.ms/arm-template for usage details.'.
    ```

### <a name="no-registered-resource-provider-found-for-location-location"></a>Er is geen geregistreerde resource provider gevonden voor de locatie {location}.
* Zorg ervoor dat de `apiVersion` resource `microsoft.insights/components` is `2015-05-01` .
* Zorg ervoor dat de `apiVersion` resource `linkedStorageAccount` is `2020-03-01-preview` .
    Foutbericht:
    ```powershell
    New-AzResourceGroupDeployment : 6:18:03 PM - Resource microsoft.insights/components 'byos-test-westus2-ai' failed with message '{
      "error": {
        "code": "NoRegisteredProviderFound",
        "message": "No registered resource provider found for location 'westus2' and API version '2020-03-01-preview' for type 'components'. The supported api-versions are '2014-04-01,
    2014-08-01, 2014-12-01-preview, 2015-05-01, 2018-05-01-preview'. The supported locations are ', eastus, southcentralus, northeurope, westeurope, southeastasia, westus2, uksouth,
    canadacentral, centralindia, japaneast, australiaeast, koreacentral, francecentral, centralus, eastus2, eastasia, westus, southafricanorth, northcentralus, brazilsouth, switzerlandnorth,
    australiasoutheast'."
      }
    }'
    ```
### <a name="storage-account-location-should-match-ai-component-location"></a>De locatie van het opslag account moet overeenkomen met de locatie van AI-onderdelen.
* Zorg ervoor dat de locatie van de Application Insights resource hetzelfde is als het opslag account.
    Foutbericht:
    ```powershell
    New-AzResourceGroupDeployment : 1:01:12 PM - Resource microsoft.insights/components/linkedStorageAccounts 'byos-test-centralus-ai/serviceprofiler' failed with message '{
      "error": {
        "code": "BadRequest",
        "message": "Storage account location should match AI component location",
        "innererror": {
          "trace": [
            "System.ArgumentException"
          ]
        }
      }
    }'
    ```

Raadpleeg voor meer informatie over het oplossen van algemene profileringen de [Profiler-documentatie over het oplossen van problemen](profiler-troubleshooting.md).

Raadpleeg de [Snapshot debugger Troubleshooting-documentatie](snapshot-debugger-troubleshoot.md)voor algemene snapshot Debugger het oplossen van problemen. 

## <a name="faqs"></a>Veelgestelde vragen
* Als ik Profiler of moment opname heb ingeschakeld en vervolgens BYOS ingeschakeld, worden mijn gegevens gemigreerd naar mijn opslag account?
    _Nee, dat is niet het geval._

* Werkt BYOS met versleuteling op rest-en Customer-Managed sleutel?
    _Ja, voor een nauw keurige BYOS is het vereist dat Profiler/Debugger is ingeschakeld met Customer-Manager sleutels._

* Werkt BYOS in een omgeving die is geïsoleerd van Internet?
    _Klikt. BYOS is in feite een vereiste voor geïsoleerde netwerk scenario's._

* Werkt BYOS wanneer, Customer-Managed sleutels en privé koppeling zijn ingeschakeld? 
    _Ja, dit kan mogelijk zijn._

* Als ik BYOS heb ingeschakeld, kan ik dan teruggaan met de opslag accounts voor diagnostische services voor het opslaan van mijn verzamelde gegevens? 
    _Ja, u kunt de gegevens migratie van uw BYOS nu niet ondersteunen._

* Na het inschakelen van BYOS worden alle gerelateerde kosten van de IT-mede werkers overgenomen van de opslag en het netwerk? 
    _Ja_
