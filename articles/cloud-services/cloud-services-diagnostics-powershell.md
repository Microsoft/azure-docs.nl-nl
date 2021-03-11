---
title: Diagnostische gegevens inschakelen in azure Cloud Services (klassiek) met behulp van Power shell | Microsoft Docs
description: Meer informatie over het gebruik van Power shell voor het verzamelen van diagnostische gegevens van een Azure-Cloud service met de uitbrei ding Azure Diagnostics.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 23528190d2eb60cc6ea3fe94bb9f7a2374526f6a
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102618437"
---
# <a name="enable-diagnostics-in-azure-cloud-services-classic-using-powershell"></a>Diagnostische gegevens inschakelen in azure Cloud Services (klassiek) met behulp van Power shell

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

U kunt Diagnostische gegevens verzamelen, zoals toepassings logboeken, prestatie meter items etc. vanuit een Cloud service met de uitbrei ding Azure Diagnostics. In dit artikel wordt beschreven hoe u de Azure Diagnostics-extensie voor een Cloud service inschakelt met behulp van Power shell.  Zie [Azure PowerShell installeren en configureren](/powershell/azure/) voor de vereiste onderdelen voor dit artikel.

## <a name="enable-diagnostics-extension-as-part-of-deploying-a-cloud-service"></a>De extensie voor diagnostische gegevens inschakelen als onderdeel van het implementeren van een cloudservice
Deze benadering is van toepassing op een continue integratie type van scenario's, waarbij de diagnostische uitbrei ding kan worden ingeschakeld als onderdeel van de implementatie van de Cloud service. Wanneer u een nieuwe Cloud service-implementatie maakt, kunt u de uitbrei ding voor diagnostische gegevens inschakelen door de para meter *ExtensionConfiguration* door te geven aan de cmdlet [New-Azure](/powershell/module/servicemanagement/azure.service/new-azuredeployment) . De para meter *ExtensionConfiguration* maakt gebruik van een reeks diagnostische configuraties die kunnen worden gemaakt met de cmdlet [New-AzureServiceDiagnosticsExtensionConfig](/powershell/module/servicemanagement/azure.service/new-azureservicediagnosticsextensionconfig) .

In het volgende voor beeld ziet u hoe u Diagnostische gegevens kunt inschakelen voor een Cloud service met een webrole-en WorkerRole, die elk een andere diagnostische configuratie heeft.

```powershell
$service_name = "MyService"
$service_package = "CloudService.cspkg"
$service_config = "ServiceConfiguration.Cloud.cscfg"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration @($webrole_diagconfig,$workerrole_diagconfig)
```

Als in het configuratie bestand voor diagnostische gegevens een `StorageAccount` element met de naam van een opslag account is opgegeven, `New-AzureServiceDiagnosticsExtensionConfig` gebruikt de cmdlet automatisch dat opslag account. Om dit te laten werken, moet het opslag account zich in hetzelfde abonnement benemen als de Cloud service die wordt geïmplementeerd.

Vanuit Azure SDK 2,6 gaat u naar de extensie configuratie bestanden die worden gegenereerd door de MSBuild-doel uitvoer, de naam van het opslag account op basis van de diagnostische configuratie teken reeks die is opgegeven in het service configuratie bestand (. cscfg). In het onderstaande script ziet u hoe u de extensie configuratie bestanden kunt parseren vanuit de uitvoer van de publicatie doel en de uitbrei ding diagnostische gegevens configureren voor elke rol bij het implementeren van de Cloud service.

```powershell
$service_name = "MyService"
$service_package = "C:\build\output\CloudService.cspkg"
$service_config = "C:\build\output\ServiceConfiguration.Cloud.cscfg"

#Find the Extensions path based on service configuration file
$extensionsSearchPath = Join-Path -Path (Split-Path -Parent $service_config) -ChildPath "Extensions"

$diagnosticsExtensions = Get-ChildItem -Path $extensionsSearchPath -Filter "PaaSDiagnostics.*.PubConfig.xml"
$diagnosticsConfigurations = @()
foreach ($extPath in $diagnosticsExtensions)
{
    #Find the RoleName based on file naming convention PaaSDiagnostics.<RoleName>.PubConfig.xml
    $roleName = ""
    $roles = $extPath -split ".",0,"simplematch"
    if ($roles -is [system.array] -and $roles.Length -gt 1)
    {
        $roleName = $roles[1]
        $x = 2
        while ($x -le $roles.Length)
            {
               if ($roles[$x] -ne "PubConfig")
                {
                    $roleName = $roleName + "." + $roles[$x]
                }
                else
                {
                    break
                }
                $x++
            }
        $fullExtPath = Join-Path -path $extensionsSearchPath -ChildPath $extPath
        $diagnosticsconfig = New-AzureServiceDiagnosticsExtensionConfig -Role $roleName -DiagnosticsConfigurationPath $fullExtPath
        $diagnosticsConfigurations += $diagnosticsconfig
    }
}
New-AzureDeployment -ServiceName $service_name -Slot Production -Package $service_package -Configuration $service_config -ExtensionConfiguration $diagnosticsConfigurations
```

Visual Studio Online maakt gebruik van een vergelijk bare methode voor automatische implementaties van Cloud Services met de uitbrei ding voor diagnostische gegevens. Zie [Publish-AzureCloudDeployment.ps1](https://github.com/Microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureCloudPowerShellDeploymentV1/Publish-AzureCloudDeployment.ps1) voor een volledig voor beeld.

Als er geen `StorageAccount` is opgegeven in de diagnostische configuratie, moet u de para meter *StorageAccountName* door geven aan de cmdlet. Als de para meter *StorageAccountName* is opgegeven, gebruikt de cmdlet altijd het opslag account dat is opgegeven in de para meter en niet de-naam die is opgegeven in het configuratie bestand voor diagnostische gegevens.

Als het diagnostische opslag account zich in een ander abonnement dan de Cloud service bevindt, moet u de para meters *StorageAccountName* en *StorageAccountKey* expliciet door geven aan de cmdlet. De para meter *StorageAccountKey* is niet nodig wanneer het diagnostische opslag account zich in hetzelfde abonnement bevindt, omdat de cmdlet automatisch de sleutel waarde kan opvragen en instellen bij het inschakelen van de uitbrei ding voor diagnostische gegevens. Als het opslag account voor diagnostische gegevens echter zich in een ander abonnement bevindt, kan de cmdlet de sleutel mogelijk niet automatisch ophalen en moet u de sleutel expliciet opgeven via de para meter *StorageAccountKey* .

```powershell
$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath -StorageAccountName $diagnosticsstorage_name -StorageAccountKey $diagnosticsstorage_key
```

## <a name="enable-diagnostics-extension-on-an-existing-cloud-service"></a>De extensie voor diagnostische gegevens inschakelen voor een bestaande cloudservice
U kunt de cmdlet [set-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/set-azureservicediagnosticsextension) gebruiken om de configuratie van diagnostische gegevens in te scha kelen of bij te werken in een Cloud service die al wordt uitgevoerd.

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

```powershell
$service_name = "MyService"
$webrole_diagconfigpath = "MyService.WebRole.PubConfig.xml"
$workerrole_diagconfigpath = "MyService.WorkerRole.PubConfig.xml"

$webrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WebRole" -DiagnosticsConfigurationPath $webrole_diagconfigpath
$workerrole_diagconfig = New-AzureServiceDiagnosticsExtensionConfig -Role "WorkerRole" -DiagnosticsConfigurationPath $workerrole_diagconfigpath

Set-AzureServiceDiagnosticsExtension -DiagnosticsConfiguration @($webrole_diagconfig,$workerrole_diagconfig) -ServiceName $service_name
```

## <a name="get-current-diagnostics-extension-configuration"></a>De huidige configuratie van de extensie voor diagnostische gegevens ophalen
Gebruik de cmdlet [Get-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/get-azureservicediagnosticsextension) om de huidige diagnostische configuratie voor een Cloud service op te halen.

```powershell
Get-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

## <a name="remove-diagnostics-extension"></a>De extensie voor diagnostische gegevens verwijderen
Als u Diagnostische gegevens in een Cloud service wilt uitschakelen, kunt u de cmdlet [Remove-AzureServiceDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/remove-azureservicediagnosticsextension) gebruiken.

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService"
```

Als u de uitbrei ding voor diagnostische gegevens hebt ingeschakeld met behulp van *set-AzureServiceDiagnosticsExtension* of de *New-AzureServiceDiagnosticsExtensionConfig* zonder de para meter *Role* , kunt u de extensie verwijderen met *Remove-AzureServiceDiagnosticsExtension* zonder de para meter *Role* . Als de para meter *Role* is gebruikt bij het inschakelen van de uitbrei ding, moet deze ook worden gebruikt bij het verwijderen van de extensie.

De extensie voor diagnostische gegevens verwijderen voor elke afzonderlijke rol:

```powershell
Remove-AzureServiceDiagnosticsExtension -ServiceName "MyService" -Role "WebRole"
```

## <a name="next-steps"></a>Volgende stappen
* Zie voor meer informatie over het gebruik van Azure Diagnostics en andere technieken voor het oplossen van problemen, [Diagnostische gegevens inschakelen in Azure Cloud Services en virtual machines](cloud-services-dotnet-diagnostics.md).
* Het [Configuratie schema voor diagnostische gegevens](../azure-monitor/agents/diagnostics-extension-schema-windows.md) bevat uitleg over de verschillende XML-configuratie opties voor de uitbrei ding van diagnostische gegevens.
* Zie [een virtuele Windows-machine met behulp van Azure Resource Manager sjabloon maken](../virtual-machines/extensions/diagnostics-template.md) voor informatie over het inschakelen van de uitbrei ding voor diagnostische gegevens voor virtual machines.