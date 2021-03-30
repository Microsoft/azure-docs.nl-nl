---
title: Azure Table Storage-bewerkingen uitvoeren met Power shell | Microsoft Docs
description: Meer informatie over het uitvoeren van algemene taken, zoals het maken, opvragen en verwijderen van gegevens uit een Azure Table-opslag account met behulp van Power shell.
author: roygara
ms.service: storage
ms.topic: article
ms.date: 04/05/2019
ms.author: rogarana
ms.subservice: tables
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9a7502d48095fe18b983c1971d5145f51e766c95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93306910"
---
# <a name="perform-azure-table-storage-operations-with-azure-powershell"></a>Azure Table Storage-bewerkingen uitvoeren met Azure PowerShell 
[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

Azure Table Storage is een NoSQL-gegevens opslag die u kunt gebruiken om grote sets gestructureerde, niet-relationele gegevens op te slaan en op te vragen. De belangrijkste onderdelen van de service zijn tabellen, entiteiten en eigenschappen. Een tabel is een verzameling entiteiten. Een entiteit is een set eigenschappen. Elke entiteit kan Maxi maal 252 eigenschappen hebben, die alle naam/waarde-paren zijn. In dit artikel wordt ervan uitgegaan dat u al bekend bent met de Azure Table Storage service-concepten. Zie voor gedetailleerde informatie [over het Table service-gegevens model](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) en [aan de slag met Azure Table Storage met behulp van .net](../../cosmos-db/tutorial-develop-table-dotnet.md).

In dit artikel wordt beschreven hoe algemene Azure Table Storage-bewerkingen worden uitgevoerd. In deze zelfstudie leert u procedures om het volgende te doen: 

> [!div class="checklist"]
> * Een tabel maken
> * Een tabel ophalen
> * Tabel entiteiten toevoegen
> * Query uitvoeren op een tabel
> * Tabel entiteiten verwijderen
> * Een tabel verwijderen

In dit artikel wordt uitgelegd hoe u een nieuw Azure Storage account maakt in een nieuwe resource groep, zodat u het eenvoudig kunt verwijderen wanneer u klaar bent. Als u liever een bestaand opslag account wilt gebruiken, kunt u dat doen in plaats daarvan.

Voor de voor beelden zijn AZ Power shell-modules `Az.Storage (1.1.0 or greater)` en vereist `Az.Resources (1.2.0 or greater)` . Voer in een Power shell-venster uit `Get-Module -ListAvailable Az*` om de versie te vinden. Als niets wordt weer gegeven of als u een upgrade wilt uitvoeren, raadpleegt u [Azure PowerShell-module installeren](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Als u deze Azure-functie vanuit Power shell wilt gebruiken, moet u de `Az` module hebben geïnstalleerd. De huidige versie van `AzTable` is niet compatibel met de oudere AzureRM-module.
> Volg de [meest recente installatie-instructies voor het installeren van AZ module](/powershell/azure/install-az-ps) , indien nodig.

Nadat Azure PowerShell is geïnstalleerd of bijgewerkt, moet u de module **AzTable** installeren. Deze bevat de opdrachten voor het beheren van de entiteiten. Als u deze module wilt installeren, voert u Power shell uit als beheerder en gebruikt u de opdracht **install-module** .

> [!IMPORTANT]
> Voor de compatibiliteit van module namen hebben we deze module nog steeds in de oude naam `AzureRmStorageTables` in PowerShell Gallery. In dit document wordt alleen verwezen naar de nieuwe naam.

```powershell
Install-Module AzTable
```

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij uw Azure-abonnement met de opdracht `Add-AzAccount` en volg de instructies op het scherm.

```powershell
Add-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Lijst met locaties ophalen

Als u niet weet welke locatie u kunt gebruiken, kunt u een lijst met de beschikbare locaties weergeven. Selecteer de gewenste locatie in de lijst. In deze voor beelden wordt **Oost**-US gebruikt. Sla deze waarde op in de **locatie** van de variabele voor toekomstig gebruik.

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## <a name="create-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep met de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Sla de naam van de resource groep op in een variabele voor toekomstig gebruik. In dit voor beeld wordt een resource groep met de naam *pshtablesrg* gemaakt in de regio *eastus* .

```powershell
$resourceGroup = "pshtablesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Een opslagaccount maken

Maak een standaard opslag account voor algemene doel einden met lokaal redundante opslag (LRS) met behulp van [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Zorg ervoor dat u een unieke naam voor het opslag account opgeeft. Vervolgens haalt u de context op die het opslag account vertegenwoordigt. Wanneer u op een opslag account werkt, kunt u naar de context verwijzen in plaats van herhaaldelijk uw referenties op te geven.

```powershell
$storageAccountName = "pshtablestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
```

## <a name="create-a-new-table"></a>Een nieuwe tabel maken

Als u een tabel wilt maken, gebruikt u de cmdlet [New-AzStorageTable](/powershell/module/az.storage/New-AzStorageTable) . In dit voor beeld wordt de tabel genoemd `pshtesttable` .

```powershell
$tableName = "pshtesttable"
New-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="retrieve-a-list-of-tables-in-the-storage-account"></a>Een lijst met tabellen in het opslag account ophalen

Haal een lijst met tabellen op in het opslag account met behulp van [Get-AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable).

```powershell
Get-AzStorageTable –Context $ctx | select Name
```

## <a name="retrieve-a-reference-to-a-specific-table"></a>Een verwijzing naar een specifieke tabel ophalen

Als u bewerkingen wilt uitvoeren op een tabel, moet u een verwijzing naar de specifieke tabel hebben. Een referentie ophalen met [Get-AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable).

```powershell
$storageTable = Get-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="reference-cloudtable-property-of-a-specific-table"></a>Eigenschap Reference CloudTable van een specifieke tabel

> [!IMPORTANT]
> Het gebruik van CloudTable is verplicht bij het werken met de **AzTable** Power shell-module. Roep de opdracht **Get-AzStorageTable** om de verwijzing naar dit object op te halen. Met deze opdracht wordt ook de tabel gemaakt als deze nog niet bestaat.

Als u bewerkingen wilt uitvoeren voor een tabel met behulp van **AzTable**, moet u een verwijzing naar de eigenschap CloudTable van een specifieke tabel hebben.

```powershell
$cloudTable = (Get-AzStorageTable –Name $tableName –Context $ctx).CloudTable
```

[!INCLUDE [storage-table-entities-powershell-include](../../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>Een tabel verwijderen

Als u een tabel wilt verwijderen, gebruikt u [Remove-AzStorageTable](/powershell/module/az.storage/Remove-AzStorageTable). Met deze cmdlet wordt de tabel, inclusief alle bijbehorende gegevens, verwijderd.

```powershell
Remove-AzStorageTable –Name $tableName –Context $ctx

# Retrieve the list of tables to verify the table has been removed.
Get-AzStorageTable –Context $Ctx | select Name
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u aan het begin van deze procedure een nieuwe resource groep en een opslag account hebt gemaakt, kunt u alle activa die u in deze oefening hebt gemaakt, verwijderen door de resource groep te verwijderen. Met deze opdracht worden alle resources die deel uitmaken van de groep en de resource groep zelf verwijderd.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel leert u meer over algemene bewerkingen voor Azure Table Storage met Power shell, met inbegrip van het volgende: 

> [!div class="checklist"]
> * Een tabel maken
> * Een tabel ophalen
> * Tabel entiteiten toevoegen
> * Query uitvoeren op een tabel
> * Tabel entiteiten verwijderen
> * Een tabel verwijderen

Raadpleeg de volgende artikelen voor meer informatie

* [PowerShell Storage-cmdlets](/powershell/module/az.storage#storage)

* [Werken met Azure-tabellen vanuit Power shell-AzureRmStorageTable/AzTable PS module v 2.0](https://paulomarquesc.github.io/working-with-azure-storage-tables-from-powershell)

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) is een gratis, zelfstandige app van Microsoft waarmee u visueel met Azure Storage-gegevens kunt werken in Windows, macOS en Linux.