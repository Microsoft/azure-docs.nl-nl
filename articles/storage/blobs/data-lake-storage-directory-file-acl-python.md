---
title: Python gebruiken voor het beheren van gegevens in Azure Data Lake Storage Gen2
description: Gebruik python voor het beheren van mappen en bestanden in opslag accounts waarvoor een hiërarchische naam ruimte is ingeschakeld.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-python
ms.openlocfilehash: a143c0aa19241b532cabff95fe6bf85679e4007c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100652289"
---
# <a name="use-python-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Python gebruiken voor het beheren van mappen en bestanden in Azure Data Lake Storage Gen2

In dit artikel leest u hoe u python gebruikt om directory's en bestanden te maken en te beheren in opslag accounts met een hiërarchische naam ruimte.

Zie [python gebruiken voor het beheren van acl's in azure data Lake Storage Gen2](data-lake-storage-acl-python.md)voor meer informatie over het ophalen, instellen en bijwerken van de acl's (toegangs beheer lijsten) van mappen en bestanden.

[Pakket (python-pakket index)](https://pypi.org/project/azure-storage-file-datalake/)  |  Voor [beelden](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)  |  [API-verwijzing](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)  |  Toewijzing van gen1 [naar Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Feedback geven](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

- Een opslag account waarvoor een hiërarchische naam ruimte is ingeschakeld. Volg [deze](create-data-lake-storage-account.md) instructies om er een te maken.

## <a name="set-up-your-project"></a>Uw project instellen

Installeer de Azure Data Lake Storage-client bibliotheek voor python met behulp van [PIP](https://pypi.org/project/pip/).

```
pip install azure-storage-file-datalake
```

Voeg deze import instructies toe aan de bovenkant van het code bestand.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## <a name="connect-to-the-account"></a>Verbinding maken met het account

Als u de fragmenten in dit artikel wilt gebruiken, moet u een **DataLakeServiceClient** -exemplaar maken dat het opslag account vertegenwoordigt. 

### <a name="connect-by-using-an-account-key"></a>Verbinding maken met behulp van een account sleutel

Dit is de eenvoudigste manier om verbinding te maken met een account. 

In dit voor beeld wordt een **DataLakeServiceClient** -exemplaar gemaakt met behulp van een account sleutel.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithKey":::

- Vervang de waarde van de tijdelijke plaatsaanduiding `storage_account_name` door de naam van uw opslagaccount.

- Vervang de `storage_account_key` waarde van de tijdelijke aanduiding door de toegangs sleutel van uw opslag account.

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Verbinding maken met behulp van Azure Active Directory (Azure AD)

U kunt de [Azure Identity client-bibliotheek voor python](https://pypi.org/project/azure-identity/) gebruiken om uw toepassing te verifiëren met Azure AD.

In dit voor beeld wordt een **DataLakeServiceClient** -exemplaar gemaakt met behulp van een client-id, een client geheim en een Tenant-id.  Zie [een Token ophalen uit Azure AD voor het machtigen van aanvragen van een client toepassing](../common/storage-auth-aad-app.md)om deze waarden op te halen.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Zie de documentatie [van de Azure Identity client-bibliotheek voor python](https://pypi.org/project/azure-identity/) voor meer voor beelden.

## <a name="create-a-container"></a>Een container maken

Een container fungeert als bestands systeem voor uw bestanden. U kunt er een maken door de **FileSystemDataLakeServiceClient.create_file_system** -methode aan te roepen.

In dit voor beeld wordt een container gemaakt met de naam `my-file-system` .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_CreateContainer":::

## <a name="create-a-directory"></a>Een map maken

Maak een verwijzing naar een directory door de methode **FileSystemClient.create_directory** aan te roepen.

In dit voor beeld wordt een map met de naam toegevoegd `my-directory` aan een container. 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>Een map een andere naam geven of verplaatsen

Wijzig de naam of verplaats een map door de **DataLakeDirectoryClient.rename_directory** methode aan te roepen. Geef een para meter door aan het pad van de gewenste map. 

In dit voor beeld wordt de naam van een submap gewijzigd in de naam `my-subdirectory-renamed` .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_RenameDirectory":::

## <a name="delete-a-directory"></a>Een map verwijderen

Verwijder een directory door de **DataLakeDirectoryClient.delete_directory** methode aan te roepen.

In dit voor beeld wordt een map met de naam verwijderd `my-directory` .  

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>Een bestand uploaden naar een map

Maak eerst een bestands verwijzing in de doel directory door een instantie van de klasse **DataLakeFileClient** te maken. Upload een bestand door de **DataLakeFileClient.append_data** methode aan te roepen. Zorg ervoor dat u het uploaden voltooit door de **DataLakeFileClient.flush_data** methode aan te roepen.

In dit voor beeld wordt een tekst bestand geüpload naar een map met de naam `my-directory` .   

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_UploadFile":::

> [!TIP]
> Als de bestands grootte groot is, moet uw code meerdere aanroepen naar de **DataLakeFileClient.append_data** methode. U kunt in plaats daarvan de **DataLakeFileClient.upload_data** -methode gebruiken. Op die manier kunt u het volledige bestand in één aanroep uploaden. 

## <a name="upload-a-large-file-to-a-directory"></a>Een groot bestand uploaden naar een map

Gebruik de methode **DataLakeFileClient.upload_data** om grote bestanden te uploaden zonder meerdere aanroepen naar de methode **DataLakeFileClient.append_data** te hoeven maken.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>Downloaden uit een directory 

Open een lokaal bestand om te schrijven. Maak vervolgens een **DataLakeFileClient** -exemplaar dat het bestand vertegenwoordigt dat u wilt downloaden. Roep de **DataLakeFileClient.read_file** om bytes te lezen uit het bestand en schrijf vervolgens die bytes naar het lokale bestand. 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_DownloadFromDirectory":::

## <a name="list-directory-contents"></a>Mapinhoud weergeven

Mapinhoud weer geven door de **FileSystemClient.get_paths** methode aan te roepen en vervolgens de resultaten te inventariseren.

In dit voor beeld wordt het pad afgedrukt van elke submap en elk bestand dat zich in een map met de naam bevindt `my-directory` .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_ListFilesInDirectory":::

## <a name="see-also"></a>Zie ook

- [API-referentiedocumentatie](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)
- [Pakket (Python-pakketindex)](https://pypi.org/project/azure-storage-file-datalake/)
- [Voorbeelden](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
- [Toewijzing van gen1 naar Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [Bekende problemen](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Feedback geven](https://github.com/Azure/azure-sdk-for-python/issues)