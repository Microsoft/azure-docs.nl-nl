---
title: Aan de slag met Azure Data Lake Storage Gen1-Power shell | Microsoft Docs
description: Gebruik Azure PowerShell om een Azure Data Lake Storage Gen1 account te maken en basis bewerkingen uit te voeren.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 06/27/2018
ms.author: twooley
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 485b14f85d68290c5447c885b9bc4974318f7952
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92103727"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-azure-powershell"></a>Aan de slag met Azure Data Lake Storage Gen1 met behulp van Azure PowerShell

> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure-CLI](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Meer informatie over het gebruik van Azure PowerShell om een Azure Data Lake Storage Gen1 account te maken en basis bewerkingen uit te voeren, zoals het maken van mappen, uploaden en downloaden van gegevens bestanden, verwijderen van uw account enzovoort. Zie [overzicht van data Lake Storage gen1](data-lake-store-overview.md)voor meer informatie over data Lake Storage gen1.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Een Azure-abonnement**. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 of hoger**. Zie [Azure PowerShell installeren en configureren](/powershell/azure/).

## <a name="authentication"></a>Verificatie

In dit artikel wordt gebruikgemaakt van een eenvoudigere verificatie methode met Data Lake Storage Gen1 waarbij u wordt gevraagd de referenties van uw Azure-account in te voeren. Het toegangs niveau voor Data Lake Storage Gen1 account en bestands systeem wordt vervolgens bepaald door het toegangs niveau van de aangemelde gebruiker. Er zijn echter andere manieren om te verifiëren met Data Lake Storage Gen1, die verificatie door eind gebruikers of service-naar-service-verificatie zijn. Zie [Verificatie door eindgebruikers](data-lake-store-end-user-authenticate-using-active-directory.md) of [Service-to-serviceverificatie](./data-lake-store-service-to-service-authenticate-using-active-directory.md) voor instructies en meer informatie over verificatie.

## <a name="create-a-data-lake-storage-gen1-account"></a>Een Data Lake Storage Gen1-account maken

1. Open een nieuw Windows PowerShell-venster via het bureaublad. Voer het volgende code fragment in om u aan te melden bij uw Azure-account, het abonnement in te stellen en de Data Lake Storage Gen1 provider te registreren. Wanneer u wordt gevraagd om u aan te melden, moet u zich aanmelden als een van de abonnements beheerders/-eigenaar:

    ```PowerShell
    # Log in to your Azure account
    Connect-AzAccount

    # List all the subscriptions associated to your account
    Get-AzSubscription

    # Select a subscription
    Set-AzContext -SubscriptionId <subscription ID>

    # Register for Azure Data Lake Storage Gen1
    Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
    ```

1. Een Data Lake Storage Gen1-account is gekoppeld aan een Azure-resource groep. Begin met het maken van een resource groep.

    ```PowerShell
    $resourceGroupName = "<your new resource group name>"
    New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"
    ```

    ![Een Azure-resource groep maken](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Een Azure-resourcegroep maken")

1. Maak een Data Lake Storage Gen1-account. De naam die u opgeeft, mag alleen kleine letters en cijfers bevatten.

    ```PowerShell
    $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 account name>"
    New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"
    ```

    ![Een Data Lake Storage Gen1-account maken](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Een Data Lake Storage Gen1-account maken")

1. Controleer of het account is gemaakt.

    ```PowerShell
    Test-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name
    ```

    De uitvoer van de cmdlet moet **waar** zijn.

## <a name="create-directory-structures"></a>Mapstructuren maken

U kunt mappen onder uw Data Lake Storage Gen1-account maken om gegevens te beheren en op te slaan.

1. Geef een hoofdmap op.

    ```PowerShell
    $myrootdir = "/"
    ```

1. Maak onder de opgegeven hoofdmap een nieuwe map met de naam **mynewdirectory**.

    ```PowerShell
    New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/mynewdirectory
    ```

1. Controleer of de nieuwe map is gemaakt.

    ```PowerShell
    Get-AzDataLakeStoreChildItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir
    ```

    De uitvoer moet overeenkomen met de volgende schermafbeelding:

    ![Map verifiëren](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Map verifiëren")

## <a name="upload-data"></a>Gegevens uploaden

U kunt uw gegevens rechtstreeks naar Data Lake Storage Gen1 uploaden op het hoofd niveau of naar een map die u in het account hebt gemaakt. In de codefragmenten in deze sectie ziet u hoe u voorbeeldgegevens uploadt naar de map (**mynewfolder**) die u in de voorgaande sectie hebt gemaakt.

Als u nog geen voorbeeldgegevens hebt om te uploaden, kunt u de map **Ambulance Data** uit de [Azure Data Lake Git-opslagplaats](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData) gebruiken. Download het bestand en sla het in een lokale map op uw computer op, bijvoorbeeld C:\sampledata\.

```PowerShell
Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
   -Path "C:\sampledata\vehicle1_09142014.csv" `
   -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv
```

## <a name="rename-download-and-delete-data"></a>Gegevens een andere naam geven, downloaden en verwijderen

Als u de naam van een bestand wilt wijzigen, gebruikt u de volgende opdracht:

```PowerShell
Move-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv `
    -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv
```

Gebruik de volgende opdracht als u een bestand wilt uploaden:

```PowerShell
Export-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv `
    -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"
```

Gebruik de volgende opdracht als u een bestand wilt verwijderen:

```PowerShell
Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv
```

Wanneer dit wordt gevraagd, typt u **Y** om het item te verwijderen. Als u meer dan één bestand wilt verwijderen, kunt u alle paden, door komma's gescheiden, opgeven.

```PowerShell
Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv
```

## <a name="delete-your-account"></a>Uw account verwijderen

Gebruik de volgende opdracht om uw Data Lake Storage Gen1-account te verwijderen.

```PowerShell
Remove-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name
```

Wanneer dit wordt gevraagd, typt u **Y** om het account te verwijderen.

## <a name="next-steps"></a>Volgende stappen

* [Richt lijnen voor het afstemmen van prestaties voor het gebruik van Power shell met Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-powershell.md)
* [Azure Data Lake Storage Gen1 gebruiken voor big data vereisten](data-lake-store-data-scenarios.md)
* [Gegevens beveiligen in Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Azure Data Lake Analytics gebruiken met Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Azure HDInsight gebruiken met Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)