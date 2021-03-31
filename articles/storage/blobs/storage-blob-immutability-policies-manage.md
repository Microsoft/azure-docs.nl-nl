---
title: Onveranderbaarheid-beleid instellen en beheren voor Blob Storage-Azure Storage
description: Meer informatie over het gebruik van de WORM (Write Once, lees bare lezen) voor Blob-opslag (object) voor het opslaan van gegevens in een status die niet kan worden gewist en die niet kan worden gewijzigd voor een opgegeven interval.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: a09dbd7d778a4f7ea2a9aac9ca07b9e6d06bc1ae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84463600"
---
# <a name="set-and-manage-immutability-policies-for-blob-storage"></a>Onveranderbaarheid-beleid instellen en beheren voor Blob Storage

Onveranderbare opslag voor Azure Blob-opslag stelt gebruikers in staat om bedrijfskritische gegevens objecten op te slaan in een WORM (eenmaal schrijven, gelezen). Met deze status worden de gegevens niet-kan worden gewist en niet kunnen worden gewijzigd voor een door de gebruiker opgegeven interval. Voor de duur van het retentie-interval kunnen blobs worden gemaakt en gelezen, maar niet worden gewijzigd of verwijderd. Onveranderbare opslag is beschikbaar voor algemeen gebruik v2-en Blob Storage-accounts in alle Azure-regio's.

In dit artikel wordt beschreven hoe u Onveranderbaarheid-beleid kunt instellen en beheren voor gegevens in Blob Storage met behulp van de Azure Portal, Power shell of Azure CLI. Zie voor meer informatie over onveranderbare opslag [bedrijfs kritieke BLOB-gegevens met onveranderbare opslag](storage-blob-immutable-storage.md).

## <a name="set-retention-policies-and-legal-holds"></a>Bewaar beleid en juridische bewaringen instellen

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Maak een nieuwe container of selecteer een bestaande container om de blobs op te slaan die u in onveranderbare toestand wilt bewaren. De container moet zich in een v2-of Blob-opslag account voor algemeen gebruik bevinden.

2. Selecteer **toegangs beleid** in de container instellingen. Selecteer vervolgens **beleid toevoegen** onder **onveranderlijke Blob-opslag**.

    ![Container instellingen in de portal](media/storage-blob-immutability-policies-manage/portal-image-1.png)

3. Als u op tijd gebaseerde Bewaar periode wilt inschakelen, selecteert u **op tijd gebaseerd bewaren** in de vervolg keuzelijst.

    !["Op tijd gebaseerde Bewaar periode" geselecteerd onder beleids type](media/storage-blob-immutability-policies-manage/portal-image-2.png)

4. Voer het Bewaar interval in dagen in (acceptabele waarden zijn 1 tot 146000 dagen).

    ![Vak ' Bewaar periode bijwerken naar '](media/storage-blob-immutability-policies-manage/portal-image-5-retention-interval.png)

    De oorspronkelijke status van het beleid wordt ontgrendeld, zodat u de functie kunt testen en wijzigingen kunt aanbrengen in het beleid voordat u het vergrendelt. Het vergren delen van het beleid is essentieel voor de naleving van voor schriften zoals SEC 17a-4.

5. Het beleid vergren delen. Klik met de rechter muisknop op het weglatings teken (**...**) en het volgende menu wordt weer gegeven met extra acties:

    ![' Beleid vergren delen ' in het menu](media/storage-blob-immutability-policies-manage/portal-image-4-lock-policy.png)

6. Selecteer **beleid vergren delen** en bevestig de vergren deling. Het beleid is nu vergrendeld en kan niet worden verwijderd. alleen uitbrei dingen van het Bewaar interval zijn toegestaan. Het verwijderen van blobs en onderdrukkingen zijn niet toegestaan. 

    ![Het vergrendelings beleid in het menu bevestigen](media/storage-blob-immutability-policies-manage/portal-image-5-lock-policy.png)

7. Selecteer **beleid toevoegen** om geldige bewaringen in te scha kelen. Selecteer **juridisch vasthouden** in de vervolg keuzelijst.

    ![' Juridisch hold ' in het menu onder beleids type](media/storage-blob-immutability-policies-manage/portal-image-legal-hold-selection-7.png)

8. Maak een juridische bewaring met een of meer tags.

    ![Vak ' label naam ' onder het beleids type](media/storage-blob-immutability-policies-manage/portal-image-set-legal-hold-tags.png)

9. Als u een juridische blok kering wilt wissen, verwijdert u de code van de toegepaste juridische id.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

De functie is opgenomen in de volgende opdracht groepen: `az storage container immutability-policy`  en `az storage container legal-hold` . Voer `-h` deze uit om de opdrachten weer te geven.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

De AZ. Storage-module ondersteunt onveranderbare opslag.  Voer de volgende stappen uit om de functie in te scha kelen:

1. Controleer of u de meest recente versie van PowerShellGet hebt geïnstalleerd: `Install-Module PowerShellGet –Repository PSGallery –Force` .
2. Verwijder alle eerdere installatie van Azure PowerShell.
3. Azure PowerShell installeren: `Install-Module Az –Repository PSGallery –AllowClobber` .

Het volgende Power shell-voorbeeld script is ter referentie. Met dit script maakt u een nieuw opslag account en een nieuwe container. Vervolgens wordt uitgelegd hoe u wettelijke wacht ruimten instelt en wist, een op tijd gebaseerd Bewaar beleid maakt en vergrendelt (ook wel een Onveranderbaarheid-beleid genoemd) en het Bewaar interval uitbreidt.

Maak eerst een Azure Storage-account:

```powershell
$resourceGroup = "<Enter your resource group>"
$storageAccount = "<Enter your storage account name>"
$container = "<Enter your container name>"
$location = "<Enter the storage account location>"

# Log in to Azure
Connect-AzAccount
Register-AzResourceProvider -ProviderNamespace "Microsoft.Storage"

# Create your Azure resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create your Azure storage account
$account = New-AzStorageAccount -ResourceGroupName $resourceGroup -StorageAccountName `
    $storageAccount -SkuName Standard_ZRS -Location $location -Kind StorageV2

# Create a new container using the context
$container = New-AzStorageContainer -Name $container -Context $account.Context

# List the containers in the account
Get-AzStorageContainer -Context $account.Context

# Remove a container
Remove-AzStorageContainer -Name $container -Context $account.Context
```

Juridische bewaringen instellen en wissen:

```powershell
# Set a legal hold
Add-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag1>,<tag2>,...

# Clear a legal hold
Remove-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag3>
```

Op tijd gebaseerd Onveranderbaarheid-beleid maken of bijwerken:

```powershell
# Create a time-based immutability policy
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10
```

Onveranderbaarheid-beleid ophalen:

```powershell
# Get an immutability policy
Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container
```

Onveranderbaarheid-beleid vergren delen (toevoegen `-Force` om de prompt te negeren):

```powershell
# Lock immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container
Lock-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container `
    -Etag $policy.Etag
```

Onveranderbaarheid-beleid uitbreiden:

```powershell
# Extend immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy
```

Een ontgrendeld Onveranderbaarheid-beleid verwijderen (toevoegen `-Force` om de prompt te sluiten):

```powershell
# Remove an unlocked immutability policy
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Remove-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy
```

---

## <a name="enabling-allow-protected-append-blobs-writes"></a>Schrijven van beveiligde toevoeg-blobs toestaan inschakelen

### <a name="portal"></a>[Portal](#tab/azure-portal)

![Extra schrijf bewerkingen voor toevoegen toestaan](media/storage-blob-immutability-policies-manage/immutable-allow-additional-append-writes.png)

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

De functie is opgenomen in de volgende opdracht groepen: `az storage container immutability-policy`  en `az storage container legal-hold` . Voer `-h` deze uit om de opdrachten weer te geven.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
# Create an immutability policy with appends allowed
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10 -AllowProtectedAppendWrite $true
```

---

## <a name="next-steps"></a>Volgende stappen

[Bedrijfskritieke blobgegevens opslaan met onveranderlijke opslag](storage-blob-immutable-storage.md)
