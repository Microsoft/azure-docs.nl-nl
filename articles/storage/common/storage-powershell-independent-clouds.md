---
title: Power shell gebruiken voor het beheren van gegevens in azure-onafhankelijke Clouds
titleSuffix: Azure Storage
description: Opslag beheren in de Cloud, overheids Cloud en Duitse Cloud met behulp van Azure PowerShell.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e924a5f6c765b5b964fe3b1492393b063d9d23b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92783569"
---
# <a name="managing-storage-in-the-azure-independent-clouds-using-powershell"></a>Opslag beheren in de onafhankelijke Azure-Clouds met behulp van Power shell

De meeste mensen gebruiken de open bare Azure-Cloud voor hun wereld wijde Azure-implementatie. Er zijn ook enkele onafhankelijke implementaties van Microsoft Azure om redenen van soevereiniteit, enzovoort. Deze onafhankelijke implementaties worden ' omgevingen ' genoemd. De volgende lijst bevat informatie over de onafhankelijke Clouds die momenteel beschikbaar zijn.

* [Azure Government Cloud](https://azure.microsoft.com/features/gov/)
* [Azure China 21Vianet-Cloud die wordt beheerd door 21Vianet in China](http://www.windowsazure.cn/)
* [Cloud van Azure Duitsland](../../germany/germany-welcome.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="using-an-independent-cloud"></a>Een onafhankelijke Cloud gebruiken

Als u Azure Storage wilt gebruiken in een van de onafhankelijke Clouds, maakt u verbinding met die Cloud in plaats van Azure public. Als u een van de onafhankelijke Clouds wilt gebruiken in plaats van Azure Public:

* U geeft de *omgeving* op waarmee u verbinding wilt maken.
* U kunt de beschik bare regio's bepalen en gebruiken.
* U gebruikt het juiste eindpunt achtervoegsel, wat afwijkt van Azure public.

Voor de voor beelden is Azure PowerShell module AZ versie 0,7 of hoger vereist. Voer in een Power shell-venster uit `Get-Module -ListAvailable Az` om de versie te vinden. Als niets wordt vermeld of als u een upgrade wilt uitvoeren, raadpleegt u [Azure PowerShell-module installeren](/powershell/azure/install-Az-ps).

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

Voer de cmdlet [Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment) uit om de beschik bare Azure-omgevingen te bekijken:

```powershell
Get-AzEnvironment
```

Meld u aan bij uw account dat toegang heeft tot de Cloud waarmee u verbinding wilt maken en stel de omgeving in. In dit voor beeld ziet u hoe u zich aanmeldt bij een account dat gebruikmaakt van de Azure Government Cloud.   

```powershell
Connect-AzAccount –Environment AzureUSGovernment
```

Gebruik de omgeving **AzureChinaCloud** om toegang te krijgen tot de cloud van China. Gebruik **AzureGermanCloud** om toegang te krijgen tot de Duitse Cloud.

Als u op dit punt de lijst met locaties nodig hebt om een opslag account of een andere resource te maken, kunt u een query uitvoeren op de locaties die beschikbaar zijn voor de geselecteerde Cloud met behulp van [Get-AzLocation](/powershell/module/az.resources/get-azlocation).

```powershell
Get-AzLocation | select Location, DisplayName
```

De volgende tabel bevat de locaties die voor de Duitse Cloud zijn geretourneerd.

|Locatie | Weergavenaam |
|----|----|
| `germanycentral` | Duitsland - centraal|
| `germanynortheast` | Duitsland - noordoost |


## <a name="endpoint-suffix"></a>Eind punt achtervoegsel

Het achtervoegsel van het eind punt voor elk van deze omgevingen wijkt af van het open bare Azure-eind punt. Het achtervoegsel van het BLOB-eind punt voor Azure Public is bijvoorbeeld **blob.core.Windows.net**. Voor de overheids-Cloud is het achtervoegsel van het BLOB-eind punt **blob.core.usgovcloudapi.net**.

### <a name="get-endpoint-using-get-azenvironment"></a>Eind punt ophalen met Get-AzEnvironment

Haal het achtervoegsel van het eind punt op met [Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment). Het eind punt is de eigenschap *StorageEndpointSuffix* van de omgeving.

De volgende code fragmenten laten zien hoe het achtervoegsel van het eind punt kan worden opgehaald. Al deze opdrachten retour neren iets als ' core.cloudapp.net ' of ' core.cloudapi.de ', enzovoort. Voeg het achtervoegsel aan de opslag service toe om toegang te krijgen tot de service. Bijvoorbeeld: ' queue.core.cloudapi.de ' heeft toegang tot de Queue-service in de Duitse Cloud.

Met dit code fragment worden alle omgevingen en het achtervoegsel van het eind punt voor elke omgeving opgehaald.

```powershell
Get-AzEnvironment | select Name, StorageEndpointSuffix 
```

Met deze opdracht worden de volgende resultaten geretourneerd.

| Name| StorageEndpointSuffix|
|----|----|
| AzureChinaCloud | core.chinacloudapi.cn|
| AzureCloud | core.windows.net |
| AzureGermanCloud | core.cloudapi.de|
| AzureUSGovernment | core.usgovcloudapi.net |

U kunt alle eigenschappen voor de opgegeven omgeving ophalen door **Get-AzEnvironment** aan te roepen en de naam van de Cloud op te geven. Dit code fragment retourneert een lijst met eigenschappen. Zoek naar **StorageEndpointSuffix** in de lijst. Het volgende voor beeld is voor de Duitse Cloud.

```powershell
Get-AzEnvironment -Name AzureGermanCloud
```

De resultaten zijn vergelijkbaar met de volgende waarden:

|Eigenschapsnaam|Waarde|
|----|----|
| Naam | `AzureGermanCloud` |
| EnableAdfsAuthentication | `False` |
| ActiveDirectoryServiceEndpointResourceI | `http://management.core.cloudapi.de/` |
| GalleryURL | `https://gallery.cloudapi.de/` |
| ManagementPortalUrl | `https://portal.microsoftazure.de/` |
| ServiceManagementUrl | `https://manage.core.cloudapi.de/` |
| PublishSettingsFileUrl| `https://manage.microsoftazure.de/publishsettings/index` |
| ResourceManagerUrl | `http://management.microsoftazure.de/` |
| SqlDatabaseDnsSuffix | `.database.cloudapi.de` |
| **StorageEndpointSuffix** | `core.cloudapi.de` |
| ... | ... |

Als u alleen de eigenschap voor het eindpunt achtervoegsel van het opslag account wilt ophalen, haalt u de specifieke Cloud op en vraagt u slechts één eigenschap op.

```powershell
$environment = Get-AzEnvironment -Name AzureGermanCloud
Write-Host "Storage EndPoint Suffix = " $environment.StorageEndpointSuffix
```

Met deze opdracht wordt de volgende informatie geretourneerd:

`Storage Endpoint Suffix = core.cloudapi.de`

### <a name="get-endpoint-from-a-storage-account"></a>Eind punt ophalen uit een opslag account

U kunt ook de eigenschappen van een opslag account bekijken om de eind punten op te halen:

```powershell
# Get a reference to the storage account.
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"
$storageAccount = Get-AzStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
  # Output the endpoints.
Write-Host "blob endpoint = " $storageAccount.PrimaryEndPoints.Blob 
Write-Host "file endpoint = " $storageAccount.PrimaryEndPoints.File
Write-Host "queue endpoint = " $storageAccount.PrimaryEndPoints.Queue
Write-Host "table endpoint = " $storageAccount.PrimaryEndPoints.Table
```

Voor een opslag account in de Government Cloud retourneert deze opdracht de volgende uitvoer:

```
blob endpoint = http://myexistingstorageaccount.blob.core.usgovcloudapi.net/
file endpoint = http://myexistingstorageaccount.file.core.usgovcloudapi.net/
queue endpoint = http://myexistingstorageaccount.queue.core.usgovcloudapi.net/
table endpoint = http://myexistingstorageaccount.table.core.usgovcloudapi.net/
```

## <a name="after-setting-the-environment"></a>Na het instellen van de omgeving

U kunt nu Power shell gebruiken voor het beheren van uw opslag accounts en toegang tot blob-, wachtrij-, bestands-en tabel gegevens. Zie [AZ. Storage](/powershell/module/az.storage)voor meer informatie.

## <a name="clean-up-resources"></a>Resources opschonen

Als u voor deze oefening een nieuwe resource groep en een opslag account hebt gemaakt, kunt u beide assets verwijderen door de resource groep te verwijderen. Als u de resourcegroep verwijdert, worden alle resources verwijderd die deze groep bevat.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Volgende stappen

* [Gebruikersaanmeldingen behouden tussen PowerShell-sessies](/powershell/azure/context-persistence)
* [Opslag Azure Government](../../azure-government/compare-azure-government-global-azure.md)
* [Ontwikkelaars handleiding Microsoft Azure Government](../../azure-government/documentation-government-developer-guide.md)
* [Opmerkingen voor ontwikkel aars voor Azure China 21Vianet-toepassingen](https://msdn.microsoft.com/library/azure/dn578439.aspx)
* [Documentatie voor Azure Duitsland](../../germany/germany-welcome.md)