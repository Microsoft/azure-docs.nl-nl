---
title: Azure Data Lake Storage Gen2 python-SDK voor bestanden & Acl's
description: Gebruik python Manage directory's en File and Directory Access Control Lists (ACL) in opslag accounts met een hiërarchische naam ruimte (HNS) ingeschakeld.
author: normesta
ms.service: storage
ms.date: 09/10/2020
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-python
ms.openlocfilehash: 7bbdf7961a934245b71829b7b50fc62c5b069d6b
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95913280"
---
# <a name="use-python-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Python gebruiken voor het beheren van mappen, bestanden en Acl's in Azure Data Lake Storage Gen2

In dit artikel wordt beschreven hoe u python gebruikt om directory's, bestanden en machtigingen te maken en te beheren in opslag accounts met een hiërarchische naam ruimte (HNS) ingeschakeld. 

[Pakket (python-pakket index)](https://pypi.org/project/azure-storage-file-datalake/)  |  Voor [beelden](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)  |  [API-verwijzing](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)  |  Toewijzing van gen1 [naar Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Feedback geven](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Vereisten

> [!div class="checklist"]
> * Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
> * Een opslag account met een hiërarchische naam ruimte (HNS) ingeschakeld. Volg [deze](../common/storage-account-create.md) instructies om er een te maken.

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

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- Vervang de waarde van de tijdelijke plaatsaanduiding `storage_account_name` door de naam van uw opslagaccount.

- Vervang de `storage_account_key` waarde van de tijdelijke aanduiding door de toegangs sleutel van uw opslag account.

### <a name="connect-by-using-azure-active-directory-ad"></a>Verbinding maken met behulp van Azure Active Directory (AD)

U kunt de [Azure Identity client-bibliotheek voor python](https://pypi.org/project/azure-identity/) gebruiken om uw toepassing te verifiëren met Azure AD.

In dit voor beeld wordt een **DataLakeServiceClient** -exemplaar gemaakt met behulp van een client-id, een client geheim en een Tenant-id.  Zie [een Token ophalen uit Azure AD voor het machtigen van aanvragen van een client toepassing](../common/storage-auth-aad-app.md)om deze waarden op te halen.

```python
def initialize_storage_account_ad(storage_account_name, client_id, client_secret, tenant_id):
    
    try:  
        global service_client

        credential = ClientSecretCredential(tenant_id, client_id, client_secret)

        service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
            "https", storage_account_name), credential=credential)
    
    except Exception as e:
        print(e)
```

> [!NOTE]
> Zie de documentatie [van de Azure Identity client-bibliotheek voor python](https://pypi.org/project/azure-identity/) voor meer voor beelden.

## <a name="create-a-container"></a>Een container maken

Een container fungeert als bestands systeem voor uw bestanden. U kunt er een maken door de **FileSystemDataLakeServiceClient.create_file_system** -methode aan te roepen.

In dit voor beeld wordt een container gemaakt met de naam `my-file-system` .

```python
def create_file_system():
    try:
        global file_system_client

        file_system_client = service_client.create_file_system(file_system="my-file-system")
    
    except Exception as e:
        print(e) 
```


## <a name="create-a-directory"></a>Een map maken

Maak een verwijzing naar een directory door de methode **FileSystemClient.create_directory** aan te roepen.

In dit voor beeld wordt een map met de naam toegevoegd `my-directory` aan een container. 

```python
def create_directory():
    try:
        file_system_client.create_directory("my-directory")
    
    except Exception as e:
     print(e) 
```

## <a name="rename-or-move-a-directory"></a>Een map een andere naam geven of verplaatsen

Wijzig de naam of verplaats een map door de **DataLakeDirectoryClient.rename_directory** methode aan te roepen. Geef een para meter door aan het pad van de gewenste map. 

In dit voor beeld wordt de naam van een submap gewijzigd in de naam `my-subdirectory-renamed` .

```python
def rename_directory():
    try:
       
       file_system_client = service_client.get_file_system_client(file_system="my-file-system")
       directory_client = file_system_client.get_directory_client("my-directory")
       
       new_dir_name = "my-directory-renamed"
       directory_client.rename_directory(rename_destination=directory_client.file_system_name + '/' + new_dir_name)

    except Exception as e:
     print(e) 
```

## <a name="delete-a-directory"></a>Een map verwijderen

Verwijder een directory door de **DataLakeDirectoryClient.delete_directory** methode aan te roepen.

In dit voor beeld wordt een map met de naam verwijderd `my-directory` .  

```python
def delete_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")
        directory_client = file_system_client.get_directory_client("my-directory")

        directory_client.delete_directory()
    except Exception as e:
     print(e) 
```


## <a name="upload-a-file-to-a-directory"></a>Een bestand uploaden naar een map 

Maak eerst een bestands verwijzing in de doel directory door een instantie van de klasse **DataLakeFileClient** te maken. Upload een bestand door de **DataLakeFileClient.append_data** methode aan te roepen. Zorg ervoor dat u het uploaden voltooit door de **DataLakeFileClient.flush_data** methode aan te roepen.

In dit voor beeld wordt een tekst bestand geüpload naar een map met de naam `my-directory` .   

```python
def upload_file_to_directory():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.create_file("uploaded-file.txt")
        local_file = open("C:\\file-to-upload.txt",'rb')

        file_contents = local_file.read()

        file_client.append_data(data=file_contents, offset=0, length=len(file_contents))

        file_client.flush_data(len(file_contents))

    except Exception as e:
      print(e) 
```

> [!TIP]
> Als de bestands grootte groot is, moet uw code meerdere aanroepen naar de **DataLakeFileClient.append_data** methode. U kunt in plaats daarvan de **DataLakeFileClient.upload_data** -methode gebruiken. Op die manier kunt u het volledige bestand in één aanroep uploaden. 

## <a name="upload-a-large-file-to-a-directory"></a>Een groot bestand uploaden naar een map

Gebruik de methode **DataLakeFileClient.upload_data** om grote bestanden te uploaden zonder meerdere aanroepen naar de methode **DataLakeFileClient.append_data** te hoeven maken.

```python
def upload_file_to_directory_bulk():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        local_file = open("C:\\file-to-upload.txt",'rb')

        file_contents = local_file.read()

        file_client.upload_data(file_contents, overwrite=True)

    except Exception as e:
      print(e) 
```

## <a name="download-from-a-directory"></a>Downloaden uit een directory 

Open een lokaal bestand om te schrijven. Maak vervolgens een **DataLakeFileClient** -exemplaar dat het bestand vertegenwoordigt dat u wilt downloaden. Roep de **DataLakeFileClient.read_file** om bytes te lezen uit het bestand en schrijf vervolgens die bytes naar het lokale bestand. 

```python
def download_file_from_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        local_file = open("C:\\file-to-download.txt",'wb')

        file_client = directory_client.get_file_client("uploaded-file.txt")

        download = file_client.download_file()

        downloaded_bytes = download.readall()

        local_file.write(downloaded_bytes)

        local_file.close()

    except Exception as e:
     print(e)
```
## <a name="list-directory-contents"></a>Mapinhoud weergeven

Mapinhoud weer geven door de **FileSystemClient.get_paths** methode aan te roepen en vervolgens de resultaten te inventariseren.

In dit voor beeld wordt het pad afgedrukt van elke submap en elk bestand dat zich in een map met de naam bevindt `my-directory` .

```python
def list_directory_contents():
    try:
        
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        paths = file_system_client.get_paths(path="my-directory")

        for path in paths:
            print(path.name + '\n')

    except Exception as e:
     print(e) 
```

## <a name="manage-access-control-lists-acls"></a>Toegangs beheer lijsten (Acl's) beheren

U kunt toegangs machtigingen van mappen en bestanden ophalen, instellen en bijwerken.

> [!NOTE]
> Als u Azure Active Directory (Azure AD) gebruikt om toegang te verlenen, moet u ervoor zorgen dat aan uw beveiligingsprincipal de [rol Storage BLOB data owner](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)is toegewezen. Zie voor meer informatie over hoe ACL-machtigingen worden toegepast en de gevolgen van het wijzigen van  [toegangs beheer in azure data Lake Storage Gen2](./data-lake-storage-access-control.md).

### <a name="manage-directory-acls"></a>Directory-Acl's beheren

Haal de toegangs beheer lijst (ACL) van een directory op door de **DataLakeDirectoryClient.get_access_control** -methode aan te roepen en de ACL in te stellen door de **DataLakeDirectoryClient.set_access_control** -methode aan te roepen.

> [!NOTE]
> Als uw toepassing toegang autoriseert met behulp van Azure Active Directory (Azure AD), moet u ervoor zorgen dat de beveiligings-principal die door uw toepassing wordt gebruikt om toegang te verlenen, is toegewezen aan de [rol Storage BLOB data owner](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Zie voor meer informatie over hoe ACL-machtigingen worden toegepast en de gevolgen van het wijzigen van  [toegangs beheer in azure data Lake Storage Gen2](./data-lake-storage-access-control.md).

In dit voor beeld wordt de ACL van een directory met de naam opgehaald en ingesteld `my-directory` . De teken reeks `rwxr-xrw-` geeft de machtigingen lezen, schrijven en uitvoeren van de gebruiker, geeft de groep die eigenaar is alleen lees-en uitvoer machtigingen en geeft alle andere Lees-en schrijf rechten.

```python
def manage_directory_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_dir_permissions = "rwxr-xrw-"
        
        directory_client.set_access_control(permissions=new_dir_permissions)
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
    
    except Exception as e:
     print(e) 
```

U kunt ook de toegangs beheer lijst van de hoofdmap van een container ophalen en instellen. Als u de hoofdmap wilt ophalen, roept u de **FileSystemClient._get_root_directory_client** -methode aan.

### <a name="manage-file-permissions"></a>Bestands machtigingen beheren

Haal de toegangs beheer lijst (ACL) van een bestand op door de **DataLakeFileClient.get_access_control** -methode aan te roepen en de ACL in te stellen door de **DataLakeFileClient.set_access_control** -methode aan te roepen.

> [!NOTE]
> Als uw toepassing toegang autoriseert met behulp van Azure Active Directory (Azure AD), moet u ervoor zorgen dat de beveiligings-principal die door uw toepassing wordt gebruikt om toegang te verlenen, is toegewezen aan de [rol Storage BLOB data owner](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Zie voor meer informatie over hoe ACL-machtigingen worden toegepast en de gevolgen van het wijzigen van  [toegangs beheer in azure data Lake Storage Gen2](./data-lake-storage-access-control.md).

In dit voor beeld wordt de ACL van een bestand met de naam opgehaald en ingesteld `my-file.txt` . De teken reeks `rwxr-xrw-` geeft de machtigingen lezen, schrijven en uitvoeren van de gebruiker, geeft de groep die eigenaar is alleen lees-en uitvoer machtigingen en geeft alle andere Lees-en schrijf rechten.

```python
def manage_file_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_file_permissions = "rwxr-xrw-"
        
        file_client.set_access_control(permissions=new_file_permissions)
        
        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e) 
```

### <a name="set-an-acl-recursively"></a>Recursief instellen van een ACL

U kunt Acl's recursief toevoegen, bijwerken en verwijderen voor de bestaande onderliggende items van een bovenliggende map zonder dat u deze wijzigingen afzonderlijk voor elk onderliggend item hoeft aan te brengen. Zie [acl's (Access Control Lists) recursief instellen voor Azure data Lake Storage Gen2](recursive-access-control-lists.md)voor meer informatie.

## <a name="see-also"></a>Zie ook

* [API-referentiedocumentatie](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)
* [Pakket (Python-pakketindex)](https://pypi.org/project/azure-storage-file-datalake/)
* [Voorbeelden](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
* [Toewijzing van gen1 naar Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [Bekende problemen](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Feedback geven](https://github.com/Azure/azure-sdk-for-python/issues)