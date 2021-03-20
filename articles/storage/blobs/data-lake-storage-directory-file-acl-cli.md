---
title: Azure CLI gebruiken voor het beheren van gegevens (Azure Data Lake Storage Gen2)
description: Gebruik de Azure CLI voor het beheren van mappen en bestanden in opslag accounts met een hiërarchische naam ruimte.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3e9afd4617eb7445ba83948d46eef0890832e2be
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100650351"
---
# <a name="use-azure-cli-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Azure CLI gebruiken voor het beheren van mappen en bestanden in Azure Data Lake Storage Gen2

In dit artikel leest u hoe u de [Azure Command-Line interface (CLI)](/cli/azure/) kunt gebruiken voor het maken en beheren van mappen en bestanden in opslag accounts met een hiërarchische naam ruimte.

Zie [Azure CLI gebruiken voor het beheren van acl's in azure data Lake Storage Gen2](data-lake-storage-acl-cli.md)voor meer informatie over het ophalen, instellen en bijwerken van de acl's (toegangs beheer lijsten) van mappen en bestanden.

Voor [beelden](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)  |  [Feedback geven](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

- Een opslag account waarvoor een hiërarchische naam ruimte is ingeschakeld. Volg [deze](create-data-lake-storage-account.md) instructies om er een te maken.

- Azure CLI-versie `2.6.0` of hoger.

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>Controleer of u de juiste versie van Azure CLI hebt geïnstalleerd

1. Open de [Azure Cloud shell](../../cloud-shell/overview.md)of open een opdracht console toepassing zoals Windows Power shell als u de Azure cli lokaal hebt [geïnstalleerd](/cli/azure/install-azure-cli) .

2. Controleer of de versie van de Azure CLI die is geïnstalleerd `2.6.0` of hoger is met behulp van de volgende opdracht.

   ```azurecli
    az --version
   ```

   Als uw versie van Azure CLI lager is dan `2.6.0` , installeert u een nieuwere versie. Raadpleeg [De Azure CLI installeren](/cli/azure/install-azure-cli).

## <a name="connect-to-the-account"></a>Verbinding maken met het account

1. Als u Azure CLI lokaal gebruikt, voert u de aanmeldings opdracht uit.

   ```azurecli
   az login
   ```

   Als de CLI uw standaardbrowser kan openen, gebeurt dat ook en wordt er een Azure-aanmeldingspagina geladen.

   Als dat niet het geval is, opent u een browserpagina op [https://aka.ms/devicelogin](https://aka.ms/devicelogin) en voert u de autorisatiecode in die wordt weergegeven in de terminal. Meld u vervolgens aan met uw account referenties in de browser.

   Zie voor meer informatie over verschillende verificatie methoden [toegang verlenen tot BLOB-of wachtrij gegevens met Azure cli](./authorize-data-operations-cli.md).

2. Als uw identiteit is gekoppeld aan meer dan één abonnement, stelt u uw actieve abonnement in op het abonnement van het opslag account dat als host voor uw statische website gaat.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Vervang de `<subscription-id>` waarde van de tijdelijke aanduiding door de id van uw abonnement.

> [!NOTE]
> In het voor beeld in dit artikel wordt de autorisatie Azure Active Directory (Azure AD) weer gegeven. Zie [toegang verlenen aan BLOB-of wachtrij gegevens met Azure cli](./authorize-data-operations-cli.md)voor meer informatie over verificatie methoden.

## <a name="create-a-container"></a>Een container maken

Een container fungeert als bestands systeem voor uw bestanden. U kunt er een maken met behulp van de `az storage fs create` opdracht. 

In dit voor beeld wordt een container gemaakt met de naam `my-file-system` .

```azurecli
az storage fs create -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-container-properties"></a>Container eigenschappen weer geven

U kunt de eigenschappen van een container afdrukken naar de-console met behulp van de `az storage fs show` opdracht.

```azurecli
az storage fs show -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="list-container-contents"></a>Container inhoud weer geven

De inhoud van een directory weer geven met behulp van de `az storage fs file list` opdracht.

In dit voor beeld wordt de inhoud van een container met de naam weer gegeven `my-file-system` .

```azurecli
az storage fs file list -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-container"></a>Een container verwijderen

Verwijder een container met behulp van de `az storage fs delete` opdracht.

In dit voor beeld wordt een container met de naam verwijderd `my-file-system` . 

```azurecli
az storage fs delete -n my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="create-a-directory"></a>Een map maken

Maak een directory verwijzing met behulp van de `az storage fs directory create` opdracht. 

In dit voor beeld wordt een map toegevoegd met de naam `my-directory` van een container `my-file-system` die zich in een account met de naam bevindt `mystorageaccount` .

```azurecli
az storage fs directory create -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-directory-properties"></a>Mapeigenschappen weer geven

U kunt de eigenschappen van een directory naar de-console afdrukken met behulp van de `az storage fs directory show` opdracht.

```azurecli
az storage fs directory show -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-directory"></a>Een map een andere naam geven of verplaatsen

Wijzig de naam van een map of verplaats deze met behulp van de `az storage fs directory move` opdracht.

In dit voor beeld wordt de naam van een map gewijzigd van de naam `my-directory` in de naam `my-new-directory` van de container.

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

In dit voor beeld wordt een map verplaatst naar een container met de naam `my-second-file-system` .

```azurecli
az storage fs directory move -n my-directory -f my-file-system --new-directory "my-second-file-system/my-new-directory" --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-directory"></a>Een map verwijderen

Een directory verwijderen met behulp van de `az storage fs directory delete` opdracht.

In dit voor beeld wordt een map met de naam verwijderd `my-directory` . 

```azurecli
az storage fs directory delete -n my-directory -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="check-if-a-directory-exists"></a>Controleren of er een map bestaat

Bepaal of een specifieke map bestaat in de container met behulp van de `az storage fs directory exists` opdracht.

In dit voor beeld wordt onthuld of een map `my-directory` met de naam bestaat in de `my-file-system` container. 

```azurecli
az storage fs directory exists -n my-directory -f my-file-system --account-name mystorageaccount --auth-mode login 
```

## <a name="download-from-a-directory"></a>Downloaden uit een directory

Down load een bestand vanuit een map met behulp van de `az storage fs file download` opdracht.

In dit voor beeld wordt een bestand gedownload met de naam `upload.txt` van een map met de naam `my-directory` . 

```azurecli
az storage fs file download -p my-directory/upload.txt -f my-file-system -d "C:\myFolder\download.txt" --account-name mystorageaccount --auth-mode login
```

## <a name="list-directory-contents"></a>Mapinhoud weergeven

De inhoud van een directory weer geven met behulp van de `az storage fs file list` opdracht.

In dit voor beeld wordt de inhoud weer gegeven van een map met `my-directory` de naam die zich bevindt in de `my-file-system` container van een opslag account met de naam `mystorageaccount` . 

```azurecli
az storage fs file list -f my-file-system --path my-directory --account-name mystorageaccount --auth-mode login
```

## <a name="upload-a-file-to-a-directory"></a>Een bestand uploaden naar een map

Upload een bestand naar een map met behulp van de `az storage fs directory upload` opdracht.

In dit voor beeld wordt een bestand geüpload `upload.txt` met de naam naar een map met de naam `my-directory` . 

```azurecli
az storage fs file upload -s "C:\myFolder\upload.txt" -p my-directory/upload.txt  -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="show-file-properties"></a>Bestands eigenschappen weer geven

U kunt de eigenschappen van een bestand naar de-console afdrukken met behulp van de `az storage fs file show` opdracht.

```azurecli
az storage fs file show -p my-file.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

## <a name="rename-or-move-a-file"></a>Een bestand een andere naam geven of verplaatsen

Wijzig de naam of verplaats een bestand met behulp van de `az storage fs file move` opdracht.

In dit voor beeld wordt de naam van een bestand gewijzigd van de naam `my-file.txt` in de naam `my-file-renamed.txt` .

```azurecli
az storage fs file move -p my-file.txt -f my-file-system --new-path my-file-system/my-file-renamed.txt --account-name mystorageaccount --auth-mode login
```

## <a name="delete-a-file"></a>Een bestand verwijderen

Verwijder een bestand met behulp van de `az storage fs file delete` opdracht.

In dit voor beeld wordt een bestand met de naam `my-file.txt`

```azurecli
az storage fs file delete -p my-directory/my-file.txt -f my-file-system  --account-name mystorageaccount --auth-mode login 
```

## <a name="see-also"></a>Zie ook

- [Voorbeelden](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)
- [Feedback geven](https://github.com/Azure/azure-cli-extensions/issues)
- [Bekende problemen](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Azure CLI gebruiken voor het beheren van Acl's in Azure Data Lake Storage Gen2](data-lake-storage-acl-cli.md)