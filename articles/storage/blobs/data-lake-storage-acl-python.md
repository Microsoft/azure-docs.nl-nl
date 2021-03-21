---
title: Python gebruiken voor het beheren van Acl's in Azure Data Lake Storage Gen2
description: Gebruik python Manage Access Control Lists (ACL) in opslag accounts met een hiërarchische naam ruimte (HNS) ingeschakeld.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-python
ms.openlocfilehash: ba864aa1aa2462f21e05ab5e779c8e715d6bb973
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100654089"
---
# <a name="use-python-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Python gebruiken voor het beheren van Acl's in Azure Data Lake Storage Gen2

In dit artikel leest u hoe u python kunt gebruiken om de toegangs beheer lijsten van mappen en bestanden op te halen, in te stellen en bij te werken. 

ACL-overname is al beschikbaar voor nieuwe onderliggende items die zijn gemaakt onder een bovenliggende map. Maar u kunt Acl's recursief ook toevoegen, bijwerken en verwijderen voor de bestaande onderliggende items van een bovenliggende map zonder dat u deze wijzigingen afzonderlijk voor elk onderliggend item hoeft aan te brengen. 

[Pakket (python-pakket index)](https://pypi.org/project/azure-storage-file-datalake/)  |  Voor [beelden](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)  |  [Recursieve ACL-voor beelden](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)  |  [API-verwijzing](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)  |  Toewijzing van gen1 [naar Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Feedback geven](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

- Een opslag account met een hiërarchische naam ruimte (HNS) ingeschakeld. Volg [deze](create-data-lake-storage-account.md) instructies om er een te maken.

- Azure CLI-versie `2.6.0` of hoger.

- Een van de volgende beveiligings machtigingen:

  - Een [beveiligings-principal](../../role-based-access-control/overview.md#security-principal) ingericht Azure Active Directory (AD) waaraan de rol [Storage BLOB data owner](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) is toegewezen in het bereik van de doel container, de bovenliggende resource groep of het abonnement.  

  - De gebruiker die eigenaar is van de doel container of-map waarvoor u de ACL-instellingen wilt Toep assen. Als u Acl's recursief wilt instellen, geldt dit voor alle onderliggende items in de doel container of directory.
  
  - Sleutel van het opslag account.

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

### <a name="connect-by-using-azure-active-directory-ad"></a>Verbinding maken met behulp van Azure Active Directory (AD)

> [!NOTE]
> Als u Azure Active Directory (Azure AD) gebruikt om toegang te verlenen, moet u ervoor zorgen dat aan uw beveiligingsprincipal de [rol Storage BLOB data owner](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)is toegewezen. Zie voor meer informatie over hoe ACL-machtigingen worden toegepast en de gevolgen van het wijzigen van  [toegangs beheer model in azure data Lake Storage Gen2](./data-lake-storage-access-control-model.md).

U kunt de [Azure Identity client-bibliotheek voor python](https://pypi.org/project/azure-identity/) gebruiken om uw toepassing te verifiëren met Azure AD.

Haal een client-ID, een client geheim en een Tenant-ID op. Zie voor dit doen [een Token ophalen uit Azure AD voor het machtigen van aanvragen van een client toepassing](../common/storage-auth-aad-app.md). Als onderdeel van dit proces moet u een van de volgende [Azure-rollen op rollen gebaseerd toegangs beheer (Azure RBAC)](../../role-based-access-control/overview.md) toewijzen aan uw beveiligings-principal. 

|Rol|Mogelijkheid van ACL-instelling|
|--|--|
|[Eigenaar van opslagblobgegevens](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Alle mappen en bestanden in het account.|
|[Inzender voor Storage Blob-gegevens](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Alleen mappen en bestanden die eigendom zijn van de beveiligingsprincipal.|

In dit voor beeld wordt een **DataLakeServiceClient** -exemplaar gemaakt met behulp van een client-id, een client geheim en een Tenant-id.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Zie de documentatie [van de Azure Identity client-bibliotheek voor python](https://pypi.org/project/azure-identity/) voor meer voor beelden.

### <a name="connect-by-using-an-account-key"></a>Verbinding maken met behulp van een account sleutel

Dit is de eenvoudigste manier om verbinding te maken met een account.

In dit voor beeld wordt een **DataLakeServiceClient** -exemplaar gemaakt met behulp van een account sleutel.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithKey":::
 
- Vervang de waarde van de tijdelijke plaatsaanduiding `storage_account_name` door de naam van uw opslagaccount.

- Vervang de `storage_account_key` waarde van de tijdelijke aanduiding door de toegangs sleutel van uw opslag account.

## <a name="set-acls"></a>Acl's instellen

Wanneer u een ACL *instelt* , **vervangt** u de volledige ACL, inclusief alle items. Als u het machtigings niveau van een beveiligingsprincipal wilt wijzigen of een nieuwe beveiligingsprincipal wilt toevoegen aan de ACL zonder dat dit van invloed is op andere bestaande vermeldingen, moet u de toegangs beheer lijst in plaats daarvan *bijwerken* . Zie de sectie [Acl's bijwerken](#update-acls-recursively) in dit artikel als u een ACL wilt bijwerken in plaats van deze te vervangen.  

In deze sectie wordt beschreven hoe u:

- De ACL van een directory instellen
- De ACL van een bestand instellen

### <a name="set-the-acl-of-a-directory"></a>De ACL van een directory instellen

Haal de toegangs beheer lijst (ACL) van een directory op door de **DataLakeDirectoryClient.get_access_control** -methode aan te roepen en de ACL in te stellen door de **DataLakeDirectoryClient.set_access_control** -methode aan te roepen.

In dit voor beeld wordt de ACL van een directory met de naam opgehaald en ingesteld `my-directory` . De teken reeks `rwxr-xrw-` geeft de machtigingen lezen, schrijven en uitvoeren van de gebruiker, geeft de groep die eigenaar is alleen lees-en uitvoer machtigingen en geeft alle andere Lees-en schrijf rechten.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ACLDirectory":::

U kunt ook de toegangs beheer lijst van de hoofdmap van een container ophalen en instellen. Als u de hoofdmap wilt ophalen, roept u de **FileSystemClient._get_root_directory_client** -methode aan.

### <a name="set-the-acl-of-a-file"></a>De ACL van een bestand instellen

Haal de toegangs beheer lijst (ACL) van een bestand op door de **DataLakeFileClient.get_access_control** -methode aan te roepen en de ACL in te stellen door de **DataLakeFileClient.set_access_control** -methode aan te roepen.

In dit voor beeld wordt de ACL van een bestand met de naam opgehaald en ingesteld `my-file.txt` . De teken reeks `rwxr-xrw-` geeft de machtigingen lezen, schrijven en uitvoeren van de gebruiker, geeft de groep die eigenaar is alleen lees-en uitvoer machtigingen en geeft alle andere Lees-en schrijf rechten.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_FileACL":::

## <a name="set-acls-recursively"></a>ACL's recursief instellen

Wanneer u een ACL *instelt* , **vervangt** u de volledige ACL, inclusief alle items. Als u het machtigings niveau van een beveiligingsprincipal wilt wijzigen of een nieuwe beveiligingsprincipal wilt toevoegen aan de ACL zonder dat dit van invloed is op andere bestaande vermeldingen, moet u de toegangs beheer lijst in plaats daarvan *bijwerken* . Als u een ACL wilt bijwerken in plaats van deze te vervangen, raadpleegt u de sectie het [recursief bijwerken van acl's](#update-acls-recursively) in dit artikel.

Stel Acl's recursief in door de **DataLakeDirectoryClient.set_access_control_recursive** methode aan te roepen.

Als u een **standaard** -ACL-vermelding wilt instellen, voegt u de teken reeks toe `default:` aan het begin van elke ACL-invoer teken reeks.

In dit voor beeld wordt de ACL van een map met de naam ingesteld `my-parent-directory` .

Deze methode accepteert een Booleaanse para meter `is_default_scope` met de naam die aangeeft of de standaard-ACL moet worden ingesteld. Als deze para meter is `True` , wordt de lijst met ACL-vermeldingen voorafgegaan door de teken reeks `default:` .

De vermeldingen van de ACL geven de machtigingen gebruiker lezen, schrijven en uitvoeren, de groep die eigenaar is, de machtigingen lezen en uitvoeren, en verleent alle anderen geen toegang. De laatste ACL-vermelding in dit voor beeld geeft een specifieke gebruiker met de object-ID ' XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX ' machtigingen voor lezen en uitvoeren. Deze vermeldingen geven de machtigingen lezen, schrijven en uitvoeren van de gebruiker die eigenaar is, de groep die eigenaar is, de machtigingen lezen en uitvoeren, en krijgen alle andere geen toegang. De laatste ACL-vermelding in dit voor beeld geeft een specifieke gebruiker met de object-ID ' XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX ' machtigingen voor lezen en uitvoeren.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_SetACLRecursively":::

Zie het [voor beeld van python als](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)u een voor beeld wilt weer geven waarin acl's in batches worden verwerkt door een batch grootte op te geven.

## <a name="update-acls-recursively"></a>Acl's recursief bijwerken

Wanneer u een ACL *bijwerkt* , wijzigt u de ACL in plaats van de ACL te vervangen. U kunt bijvoorbeeld een nieuwe beveiligingsprincipal toevoegen aan de ACL zonder dat dit van invloed is op andere beveiligings-principals die worden vermeld in de ACL.  Zie de sectie [Acl's instellen](#set-acls) in dit artikel om de ACL te vervangen in plaats van deze bij te werken.

Als u een ACL recursief wilt bijwerken, maakt u een nieuw ACL-object met de ACL-vermelding die u wilt bijwerken, en gebruikt u dat object in de ACL-bewerking voor updates. Haal de bestaande ACL niet op. Zorg dat u alleen ACL-vermeldingen kunt bijwerken. Een ACL recursief bijwerken door het aanroepen van de **DataLakeDirectoryClient.update_access_control_recursive** methode. Als u een **standaard** -ACL-vermelding wilt bijwerken, voegt u de teken reeks toe `default:` aan het begin van elke ACL-invoer teken reeks.

In dit voor beeld wordt een ACL-vermelding met schrijf machtiging bijgewerkt.

In dit voor beeld wordt de ACL van een map met de naam ingesteld `my-parent-directory` . Deze methode accepteert een Booleaanse para meter `is_default_scope` met de naam die aangeeft of de standaard-ACL moet worden bijgewerkt. Als deze para meter is `True` , wordt de bijgewerkte ACL-vermelding voorafgegaan door de teken reeks `default:` .  

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_UpdateACLsRecursively":::

Zie het [voor beeld van python als](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)u een voor beeld wilt weer geven waarin acl's in batches worden verwerkt door een batch grootte op te geven.

## <a name="remove-acl-entries-recursively"></a>ACL-vermeldingen recursief verwijderen

U kunt een of meer ACL-vermeldingen verwijderen. Als u ACL'S-vermeldingen recursief wilt verwijderen, maakt u een nieuw ACL-object voor de ACL-vermelding die moet worden verwijderd en gebruikt u dat object vervolgens in ACL-bewerking verwijderen. Haal de bestaande ACL niet op. Geef de ACL-vermeldingen op die moeten worden verwijderd. 

Verwijder ACL-vermeldingen door de **DataLakeDirectoryClient.remove_access_control_recursive** methode aan te roepen. Als u een **standaard** -ACL-vermelding wilt verwijderen, moet u de teken reeks toevoegen `default:` aan het begin van de teken reeks voor de ACL-vermelding. 

In dit voor beeld wordt een ACL-vermelding verwijderd uit de toegangs beheer lijst van de map met de naam `my-parent-directory` . Deze methode accepteert een Booleaanse para meter `is_default_scope` met de naam die aangeeft of het item uit de standaard-ACL moet worden verwijderd. Als deze para meter is `True` , wordt de bijgewerkte ACL-vermelding voorafgegaan door de teken reeks `default:` . 

```python
def remove_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        if is_default_scope:
           acl = 'default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        directory_client.remove_access_control_recursive(acl=acl)

    except Exception as e:
     print(e)
```

Zie het [voor beeld van python als](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)u een voor beeld wilt weer geven waarin acl's in batches worden verwerkt door een batch grootte op te geven.

## <a name="recover-from-failures"></a>Herstellen van fouten

Er kunnen runtime-of machtigings fouten optreden. Start voor runtime-fouten het proces vanaf het begin. Machtigings fouten kunnen optreden als de beveiligingsprincipal niet voldoende machtigingen heeft om de toegangs beheer lijst van een map of bestand te wijzigen dat zich in de Directory-hiërarchie bevindt die wordt gewijzigd. Adresseer het probleem met de machtiging en kies vervolgens het proces hervatten vanaf het moment van de fout met behulp van een vervolg token of start het proces opnieuw vanaf het begin. U hoeft het vervolg token niet te gebruiken als u de voor keur hebt om vanaf het begin opnieuw op te starten. U kunt ACL-vermeldingen zonder negatieve gevolgen opnieuw Toep assen.

In dit voor beeld wordt een vervolg token geretourneerd in het geval van een fout. De toepassing kan deze voorbeeld methode opnieuw aanroepen nadat de fout is opgelost en door gegeven in het vervolg token. Als deze voorbeeld methode voor de eerste keer wordt aangeroepen, kan de toepassing de waarde ``None`` voor de vervolg token parameter door geven.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ResumeContinuationToken":::

Zie het [voor beeld van python als](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)u een voor beeld wilt weer geven waarin acl's in batches worden verwerkt door een batch grootte op te geven.

Als u wilt dat het proces wordt afgebroken door machtigings fouten, kunt u dit opgeven.

Om ervoor te zorgen dat het proces wordt voltooid, geeft u geen vervolg token door aan de **DataLakeDirectoryClient.set_access_control_recursive** methode.

In dit voor beeld worden ACL-vermeldingen recursief ingesteld. Als met deze code een machtigings fout optreedt, wordt die fout geregistreerd en wordt de uitvoering voortgezet. In dit voor beeld wordt het aantal fouten naar de console afgedrukt.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ContinueOnFailure":::

Zie het [voor beeld van python als](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)u een voor beeld wilt weer geven waarin acl's in batches worden verwerkt door een batch grootte op te geven.

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Zie ook

- [API-referentiedocumentatie](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)
- [Pakket (Python-pakketindex)](https://pypi.org/project/azure-storage-file-datalake/)
- [Voorbeelden](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
- [Toewijzing van gen1 naar Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [Bekende problemen](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Feedback geven](https://github.com/Azure/azure-sdk-for-python/issues)
- [Access Control model in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Toegangs beheer lijsten (Acl's) in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)