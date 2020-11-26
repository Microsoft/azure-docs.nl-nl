---
title: 'Quickstart: een blob maken met Azure CLI'
titleSuffix: Azure Storage
description: In deze quickstart leert u hoe u met de Azure CLI een blob uploadt naar Azure Storage, een blob downloadt en de blobs in een container weergeeft.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 08/17/2020
ms.author: tamram
ms.custom: devx-track-azurecli
ms.openlocfilehash: fa502f5ca95b1726da7f00f987b35be362ae865a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021755"
---
# <a name="quickstart-create-download-and-list-blobs-with-azure-cli"></a>Quickstart: Blobs maken, downloaden, uploaden en weergeven met Azure CLI

Azure CLI is de nieuwe opdrachtregel van Azure voor het beheren van Azure-resources. U kunt deze gebruiken in uw browser met Azure Cloud Shell. U kunt deze ook installeren op Mac OS, Linux of Windows en uitvoeren vanaf de opdrachtregel. In deze quickstart leert u hoe u Azure CLI kunt gebruiken om gegevens naar en van Azure Blob-opslag te uploaden en te downloaden.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment-h3.md)]

- Voor dit artikel is versie 2.0.46 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="authorize-access-to-blob-storage"></a>Toegang tot Blob Storage autoriseren

U kunt de toegang tot Blob Storage autoriseren via de Azure CLI met behulp van de Azure AD-referenties of door de toegangssleutel voor het opslagaccount te gebruiken. Het wordt aanbevolen om Azure AD-referenties te gebruiken. In dit artikel wordt beschreven hoe u Azure AD gebruikt om Blob Storage-bewerkingen te autoriseren.

Azure CLI-opdrachten voor gegevensbewerkingen voor Blob Storage ondersteunen de parameter `--auth-mode`, waarmee u kunt opgeven hoe een bepaalde bewerking moet worden geautoriseerd. Stel de parameter `--auth-mode` in op `login` om met Azure AD-referenties te autoriseren. Zie [Toegang verlenen tot blob- of wachtrijgegevens met Azure CLI](./authorize-data-operations-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) voor meer informatie.

Alleen Blob Storage-gegevensbewerkingen ondersteunen de parameter `--auth-mode`. Bij beheerbewerkingen, zoals het maken van een resourcegroep of opslagaccount worden automatisch Azure AD-referenties gebruikt voor autorisatie.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een Azure-resourcegroep met de opdracht [az group create](/cli/azure/group). Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

Vergeet niet om de waarden van de tijdelijke aanduidingen tussen de punthaken te vervangen door uw eigen waarden:

```azurecli
az group create \
    --name <resource-group> \
    --location <location>
```

## <a name="create-a-storage-account"></a>Create a storage account

Maak een algemeen opslagaccount met de opdracht [az storage account create](/cli/azure/storage/account). Het algemeen opslagaccount kan voor alle vier de services worden gebruikt: blobs, bestanden, tabellen en wachtrijen.

Vergeet niet om de waarden van de tijdelijke aanduidingen tussen de punthaken te vervangen door uw eigen waarden:

```azurecli
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_ZRS \
    --encryption-services blob
```

## <a name="create-a-container"></a>Een container maken

Blobs worden altijd naar een container geüpload. U kunt groepen blobs ordenen in containers net zoals u bestanden op uw computer in mappen ordent. Gebruik de opdracht [az storage container create](/cli/azure/storage/container) om een container te maken voor het opslaan van blobs.

In het volgende voorbeeld wordt uw Azure AD-account gebruikt om de bewerking voor het maken van de container te autoriseren. Voordat u de container maakt, moet u de rol [Storage Blob Data Contributor](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) aan uzelf toewijzen. Zelfs als u de eigenaar van het account bent, hebt u expliciete machtigingen nodig om gegevensbewerkingen uit te voeren voor het opslagaccount. Zie [Azure CLI gebruiken om een Azure-rol toe te wijzen voor toegang](../common/storage-auth-aad-rbac-cli.md?toc=/azure/storage/blobs/toc.json) voor meer informatie over het toewijzen van Azure-rollen.  

Vergeet niet om de waarden van de tijdelijke aanduidingen tussen de punthaken te vervangen door uw eigen waarden:

```azurecli
az ad signed-in-user show --query objectId -o tsv | az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee @- \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"

az storage container create \
    --account-name <storage-account> \
    --name <container> \
    --auth-mode login
```

> [!IMPORTANT]
> Het kan enkele minuten duren voordat Azure-roltoewijzingen worden doorgegeven.

U kunt ook de sleutel voor het opslagaccount gebruiken om de bewerking voor het maken van de container te autoriseren. Zie [Toegang verlenen tot blob- of wachtrijgegevens met Azure CLI](./authorize-data-operations-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) voor meer informatie over het autoriseren van gegevensbewerkingen met Azure CLI.

## <a name="upload-a-blob"></a>Een blob uploaden

Blob-opslag ondersteunt blok-blobs, toevoeg-blobs en pagina-blobs. In de voorbeelden van deze quickstart wordt getoond hoe u blok-blobs gebruikt.

Maak eerst een bestand om naar een blok-blob te uploaden. Als u Azure Cloud Shell gebruikt, gebruikt u de volgende opdracht om een bestand te maken:

```bash
vi helloworld
```

Wanneer het bestand wordt geopend, drukt u op **Insert**. Typ *Hallo wereld* en druk vervolgens op **Esc**. Typ vervolgens *: x* en druk op **Enter**.

In dit voorbeeld wordt met de opdracht [az storage blob upload](/cli/azure/storage/blob) een blob geüpload naar de container die u in de laatste stap hebt gemaakt. Het is niet nodig om een bestandspad op te geven omdat het bestand is gemaakt in de hoofdmap. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen de punthaken te vervangen door uw eigen waarden:

```azurecli
az storage blob upload \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file helloworld \
    --auth-mode login
```

Met deze bewerking wordt de blob gemaakt als deze nog niet bestaat, of overschreven als dat wel het geval is. Upload zoveel bestanden als u nodig hebt, voordat u doorgaat.

Als u meerdere bestanden tegelijk wilt uploaden, kunt u de opdracht [az storage blob upload-batch](/cli/azure/storage/blob) gebruiken.

## <a name="list-the-blobs-in-a-container"></a>De blobs in een container in een lijst weergeven

Gebruik de opdracht [az storage blob list](/cli/azure/storage/blob) om de blobs in de container weer te geven. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen de punthaken te vervangen door uw eigen waarden:

```azurecli
az storage blob list \
    --account-name <storage-account> \
    --container-name <container> \
    --output table \
    --auth-mode login
```

## <a name="download-a-blob"></a>Een blob downloaden

Gebruik de opdracht [az storage blob download](/cli/azure/storage/blob) om de blob te downloaden die u eerder hebt geüpload. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen de punthaken te vervangen door uw eigen waarden:

```azurecli
az storage blob download \
    --account-name <storage-account> \
    --container-name <container> \
    --name helloworld \
    --file ~/destination/path/for/file \
    --auth-mode login
```

## <a name="data-transfer-with-azcopy"></a>Gegevensoverdracht met AzCopy

Het AzCopy-opdrachtregelprogramma biedt high-performance, scriptbare gegevensoverdracht voor Azure Storage. U kunt met AzCopy gegevens overzetten naar en van Blob Storage en Azure Files. Zie [Aan de slag met AzCopy](../common/storage-use-azcopy-v10.md) voor meer informatie over AzCopy v10, de nieuwste versie van AzCopy. Zie [Gegevens overzetten met AzCopy en Blob Storage](../common/storage-use-azcopy-blobs.md) voor meer informatie over het gebruik van AzCopy v10 met Blob Storage.

In het volgende voorbeeld wordt AzCopy gebruikt om een lokaal bestand te uploaden naar een blob. Vergeet niet om de voorbeeldwaarden te vervangen door uw eigen waarden:

```bash
azcopy login
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources wilt verwijderen die u hebt gemaakt als onderdeel van deze quickstart, inclusief het opslagaccount, verwijdert u de resourcegroep met behulp van de opdracht [az group delete](/cli/azure/group). Vergeet niet om de waarden van de tijdelijke aanduidingen tussen de punthaken te vervangen door uw eigen waarden:

```azurecli
az group delete \
    --name <resource-group> \
    --no-wait
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u bestanden kunt overzetten tussen een lokaal bestandssysteem en een container in Azure Blob Storage. Bekijk de Azure CLI-voorbeelden voor Blob Storage voor meer informatie over het gebruik van Blob Storage met behulp van Azure CLI.

> [!div class="nextstepaction"]
> [Azure CLI-voorbeelden voor Blob Storage](./storage-samples-blobs-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)