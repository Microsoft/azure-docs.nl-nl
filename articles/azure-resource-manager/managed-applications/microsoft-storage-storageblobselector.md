---
title: StorageBlobSelector UI-element
description: Hierin wordt het element micro soft. storage. StorageBlobSelector UI voor Azure Portal beschreven.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: tomfitz
ms.openlocfilehash: 1085b70df67a3f16a7f7f8c5ae85c9ab271b62ac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92754418"
---
# <a name="microsoftstoragestorageblobselector-ui-element"></a>Micro soft. storage. StorageBlobSelector UI-element

Een besturings element voor het selecteren van een BLOB uit een Azure Storage-account.

## <a name="ui-sample"></a>UI-voor beeld

De gebruiker krijgt de mogelijkheid om te bladeren naar beschik bare opslag-blobs.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-browse.png" alt-text="Micro soft. storage. StorageBlobSelector: bladeren":::

Nadat u **Bladeren** hebt geselecteerd, kan de gebruiker een opslag account selecteren.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-select.png" alt-text="Micro soft. storage. StorageBlobSelector: opslag selecteren":::

De gebruiker ziet de containers in het opslag account en kan er een selecteren.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-containers.png" alt-text="Micro soft. storage. StorageBlobSelector-container selecteren":::

De gebruiker kan in de container een bestand selecteren.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-file.png" alt-text="Micro soft. storage. StorageBlobSelector-bestand":::

Het besturings element wordt bijgewerkt om de geselecteerde bestands naam weer te geven.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-result.png" alt-text="Micro soft. storage. StorageBlobSelector-bestand selecteren weer geven":::

## <a name="schema"></a>Schema

```json
{
  "name": "storageBlobSelection",
  "type": "Microsoft.Storage.StorageBlobSelector",
  "visible": true,
  "toolTip": "Select storage blob",
  "label": "Package (.zip, .cspkg)",
  "options": {
    "text": "Select Package"
  },
  "constraints": {
    "allowedFileExtensions": [ "zip", "cspkg" ]
  }
}
```

## <a name="sample-output"></a>Voorbeelduitvoer

```json
{
  "blobName": "test.zip",
  "sasUri": "https://azstorageaccnt1.blob.core.windows.net/container1/test.zip?sp=r&se=2020-10-10T07:46:22Z&sv=2019-12-12&sr=b&sig=X4EL8ZsRmiP1TVxkVfTcGyMj2sHg1zCbFBXsDmnNOyg%3D"
}

```

## <a name="remarks"></a>Opmerkingen

De eigenschap **constraints. allowedFileExtensions** geeft de toegestane bestands typen aan.

## <a name="next-steps"></a>Volgende stappen

* Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
* Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.
