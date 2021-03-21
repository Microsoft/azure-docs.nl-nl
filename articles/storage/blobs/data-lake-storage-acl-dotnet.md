---
title: .NET gebruiken om Acl's in Azure Data Lake Storage Gen2 in te stellen
description: Gebruik .NET voor het beheren van toegangs beheer lijsten (ACL) in opslag accounts met een hiërarchische naam ruimte (HNS) ingeschakeld.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: 626e89d8d758d5fe31ef6c913076a9154274bb61
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100654093"
---
# <a name="use-net-to-manage-acls-in-azure-data-lake-storage-gen2"></a>.NET gebruiken voor het beheren van Acl's in Azure Data Lake Storage Gen2

In dit artikel leest u hoe u .NET kunt gebruiken om de toegangs beheer lijsten van mappen en bestanden op te halen, in te stellen en bij te werken.

ACL-overname is al beschikbaar voor nieuwe onderliggende items die zijn gemaakt onder een bovenliggende map. Maar u kunt Acl's recursief ook toevoegen, bijwerken en verwijderen voor de bestaande onderliggende items van een bovenliggende map zonder dat u deze wijzigingen afzonderlijk voor elk onderliggend item hoeft aan te brengen.

[Pakket (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)  |  Voor [beelden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)  |  [Recursieve ACL-voor beeld](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)  |  [API-verwijzing](/dotnet/api/azure.storage.files.datalake)  |  Toewijzing van gen1 [naar Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)  |  [Feedback geven](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

- Een opslag account met een hiërarchische naam ruimte (HNS) ingeschakeld. Volg [deze](create-data-lake-storage-account.md) instructies om er een te maken.

- Azure CLI-versie `2.6.0` of hoger.

- Een van de volgende beveiligings machtigingen:

  - Een [beveiligings-principal](../../role-based-access-control/overview.md#security-principal) ingericht Azure Active Directory (AD) waaraan de rol [Storage BLOB data owner](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) is toegewezen in het bereik van de doel container, de bovenliggende resource groep of het abonnement.  

  - De gebruiker die eigenaar is van de doel container of-map waarvoor u de ACL-instellingen wilt Toep assen. Als u Acl's recursief wilt instellen, geldt dit voor alle onderliggende items in de doel container of directory.
  
  - Sleutel van het opslag account.

## <a name="set-up-your-project"></a>Uw project instellen

Om aan de slag te gaan, installeert u het [Azure. storage. files. DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) NuGet-pakket.

1. Open een opdracht venster (bijvoorbeeld: Windows Power shell).

2. Installeer in de projectmap het bestand Azure. storage. file. DataLake Preview met behulp van de `dotnet add package` opdracht.

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v 12.6.0 -s https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json
   ```

   Voeg deze instructies vervolgens toe aan de bovenkant van het code bestand.

   ```csharp
   using Azure;
   using Azure.Core;
   using Azure.Storage;
   using Azure.Storage.Files.DataLake;
   using Azure.Storage.Files.DataLake.Models;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   ```

## <a name="connect-to-the-account"></a>Verbinding maken met het account

Als u de fragmenten in dit artikel wilt gebruiken, moet u een [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) -exemplaar maken dat het opslag account vertegenwoordigt. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Verbinding maken met behulp van Azure Active Directory (AD)

> [!NOTE]
> Als u Azure Active Directory (Azure AD) gebruikt om toegang te verlenen, moet u ervoor zorgen dat aan uw beveiligingsprincipal de [rol Storage BLOB data owner](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)is toegewezen. Zie voor meer informatie over hoe ACL-machtigingen worden toegepast en de gevolgen van het wijzigen van  [toegangs beheer model in azure data Lake Storage Gen2](./data-lake-storage-access-control-model.md).

U kunt de [Azure Identity client-bibliotheek voor .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) gebruiken om uw toepassing te verifiëren met Azure AD.

Nadat u het pakket hebt geïnstalleerd, voegt u deze met de instructie toe aan de bovenkant van het code bestand.

```csharp
using Azure.Identity;
```

Haal een client-ID, een client geheim en een Tenant-ID op. Zie voor dit doen [een Token ophalen uit Azure AD voor het machtigen van aanvragen van een client toepassing](../common/storage-auth-aad-app.md). Als onderdeel van dit proces moet u een van de volgende [Azure-rollen op rollen gebaseerd toegangs beheer (Azure RBAC)](../../role-based-access-control/overview.md) toewijzen aan uw beveiligings-principal. 

|Rol|Mogelijkheid van ACL-instelling|
|--|--|
|[Eigenaar van opslagblobgegevens](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Alle mappen en bestanden in het account.|
|[Inzender voor Storage Blob-gegevens](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Alleen mappen en bestanden die eigendom zijn van de beveiligingsprincipal.|

In dit voor beeld wordt een [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) -exemplaar gemaakt met behulp van een client-id, een client geheim en een Tenant-id.  

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Zie de [Azure Identity client-bibliotheek voor .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) -documentatie voor meer voor beelden.

### <a name="connect-by-using-an-account-key"></a>Verbinding maken met behulp van een account sleutel

Dit is de eenvoudigste manier om verbinding te maken met een account.

In dit voor beeld wordt een [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) -exemplaar gemaakt met behulp van een account sleutel.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithKey":::

## <a name="set-acls"></a>Acl's instellen

Wanneer u een ACL *instelt* , **vervangt** u de volledige ACL, inclusief alle items. Als u het machtigings niveau van een beveiligingsprincipal wilt wijzigen of een nieuwe beveiligingsprincipal wilt toevoegen aan de ACL zonder dat dit van invloed is op andere bestaande vermeldingen, moet u de toegangs beheer lijst in plaats daarvan *bijwerken* . Zie de sectie [Acl's bijwerken](#update-acls) in dit artikel als u een ACL wilt bijwerken in plaats van deze te vervangen.  

Als u ervoor kiest om de ACL in te *stellen* , moet u een vermelding toevoegen voor de gebruiker die eigenaar is, een vermelding voor de groep die eigenaar is en een vermelding voor alle andere gebruikers. Zie [gebruikers en identiteiten](data-lake-storage-access-control.md#users-and-identities)voor meer informatie over de gebruiker die eigenaar is, de groep die eigenaar is en alle andere gebruikers.

In deze sectie wordt beschreven hoe u:

- De ACL van een directory instellen
- De ACL van een bestand instellen
- ACL's recursief instellen

### <a name="set-the-acl-of-a-directory"></a>De ACL van een directory instellen

Haal de toegangs beheer lijst (ACL) van een directory op door de methode [DataLakeDirectoryClient. GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) aan te roepen en de ACL in te stellen door de methode [DataLakeDirectoryClient. SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) aan te roepen.

In dit voor beeld wordt de ACL van een directory met de naam opgehaald en ingesteld `my-directory` . De teken reeks `user::rwx,group::r-x,other::rw-` geeft de machtigingen lezen, schrijven en uitvoeren van de gebruiker, geeft de groep die eigenaar is alleen lees-en uitvoer machtigingen en geeft alle andere Lees-en schrijf rechten.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ACLDirectory":::

U kunt ook de toegangs beheer lijst van de hoofdmap van een container ophalen en instellen. Als u de hoofdmap wilt ophalen, geeft u een lege teken reeks () door aan `""` de methode [DataLakeFileSystemClient. GetDirectoryClient](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getdirectoryclient) .

### <a name="set-the-acl-of-a-file"></a>De ACL van een bestand instellen

Haal de toegangs beheer lijst (ACL) van een bestand op door de methode [DataLakeFileClient. GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.getaccesscontrolasync) aan te roepen en de ACL in te stellen door de methode [DataLakeFileClient. SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakefileclient.setaccesscontrollist) aan te roepen.

In dit voor beeld wordt de ACL van een bestand met de naam opgehaald en ingesteld `my-file.txt` . De teken reeks `user::rwx,group::r-x,other::rw-` geeft de machtigingen lezen, schrijven en uitvoeren van de gebruiker, geeft de groep die eigenaar is alleen lees-en uitvoer machtigingen en geeft alle andere Lees-en schrijf rechten.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_FileACL":::

### <a name="set-acls-recursively"></a>ACL's recursief instellen

Stel Acl's recursief in door de methode **DataLakeDirectoryClient. SetAccessControlRecursiveAsync** aan te roepen. Geef deze methode een [lijst](/dotnet/api/system.collections.generic.list-1) van [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Elk [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) definieert een ACL-vermelding.

Als u een **standaard** -ACL-vermelding wilt instellen, kunt u de eigenschap [PathAccessControlItem. DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) van de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) instellen op **True**. 

In dit voor beeld wordt de ACL van een map met de naam ingesteld `my-parent-directory` . Deze methode accepteert een Booleaanse para meter `isDefaultScope` met de naam die aangeeft of de standaard-ACL moet worden ingesteld. Deze para meter wordt gebruikt in de constructor van de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). De vermeldingen van de ACL geven de machtigingen gebruiker lezen, schrijven en uitvoeren, de groep die eigenaar is, de machtigingen lezen en uitvoeren, en verleent alle anderen geen toegang. De laatste ACL-vermelding in dit voor beeld geeft een specifieke gebruiker met de object-ID ' XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX ' machtigingen voor lezen en uitvoeren.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_SetACLRecursively":::

Als u een voor beeld wilt weer geven waarin Acl's in batches worden ingesteld door een batch grootte op te geven, raadpleegt u het .NET-voor [beeld](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

## <a name="update-acls"></a>Acl's bijwerken

Wanneer u een ACL *bijwerkt* , wijzigt u de ACL in plaats van de ACL te vervangen. U kunt bijvoorbeeld een nieuwe beveiligingsprincipal toevoegen aan de ACL zonder dat dit van invloed is op andere beveiligings-principals die worden vermeld in de ACL.  Zie de sectie [Acl's instellen](#set-acls) in dit artikel om de ACL te vervangen in plaats van deze bij te werken.

In deze sectie wordt beschreven hoe u:

- Een ACL bijwerken
- Acl's recursief bijwerken

### <a name="update-an-acl"></a>Een ACL bijwerken

Haal eerst de ACL van een directory op door de methode [DataLakeDirectoryClient. GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) aan te roepen. Kopieer de lijst met ACL-vermeldingen naar een nieuwe [lijst](/dotnet/api/system.collections.generic.list-1) met [PathAccessControl](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrol) -objecten. Zoek vervolgens de vermelding die u wilt bijwerken en vervang deze in de lijst. Stel de toegangs beheer lijst in door de methode [DataLakeDirectoryClient. SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) aan te roepen.

In dit voor beeld wordt de hoofd-ACL van een container bijgewerkt door de ACL-vermelding voor alle andere gebruikers te vervangen. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_UpdateACL":::

### <a name="update-acls-recursively"></a>Acl's recursief bijwerken

Als u een ACL recursief wilt bijwerken, maakt u een nieuw ACL-object met de ACL-vermelding die u wilt bijwerken, en gebruikt u dat object in de ACL-bewerking voor updates. Haal de bestaande ACL niet op. Zorg dat u alleen ACL-vermeldingen kunt bijwerken.

Werk een ACL recursief bij door de methode **DataLakeDirectoryClient. UpdateAccessControlRecursiveAsync** aan te roepen.  Geef deze methode een [lijst](/dotnet/api/system.collections.generic.list-1) van [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Elk [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) definieert een ACL-vermelding.

Als u een **standaard** -ACL-vermelding wilt bijwerken, kunt u de eigenschap [PathAccessControlItem. DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) van de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) instellen op **True**.

In dit voor beeld wordt een ACL-vermelding met schrijf machtiging bijgewerkt. Deze methode accepteert een Booleaanse para meter `isDefaultScope` met de naam die aangeeft of de standaard-ACL moet worden bijgewerkt. Deze para meter wordt gebruikt in de constructor van de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_UpdateACLsRecursively":::

Zie .NET-voor [beeld](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)voor een voor beeld van het recursief bijwerken van acl's in batches door een batch grootte op te geven.

## <a name="remove-acl-entries"></a>ACL'S-vermeldingen verwijderen

U kunt een of meer ACL-vermeldingen verwijderen. In deze sectie wordt beschreven hoe u:

- Een ACL-vermelding verwijderen
- ACL-vermeldingen recursief verwijderen

### <a name="remove-an-acl-entry"></a>Een ACL-vermelding verwijderen

Haal eerst de ACL van een directory op door de methode [DataLakeDirectoryClient. GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) aan te roepen. Kopieer de lijst met ACL-vermeldingen naar een nieuwe [lijst](/dotnet/api/system.collections.generic.list-1) met [PathAccessControl](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrol) -objecten. Zoek vervolgens de vermelding die u wilt verwijderen en roep de [Remove](/dotnet/api/system.collections.ilist.remove) -methode van de verzameling aan. Stel de bijgewerkte ACL in door de methode [DataLakeDirectoryClient. SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) aan te roepen.

In dit voor beeld wordt de hoofd-ACL van een container bijgewerkt door de ACL-vermelding voor alle andere gebruikers te vervangen. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_RemoveACLEntry":::

### <a name="remove-acl-entries-recursively"></a>ACL-vermeldingen recursief verwijderen

Als u ACL'S-vermeldingen recursief wilt verwijderen, maakt u een nieuw ACL-object voor de ACL-vermelding die moet worden verwijderd en gebruikt u dat object vervolgens in ACL-bewerking verwijderen. Haal de bestaande ACL niet op. Geef de ACL-vermeldingen op die moeten worden verwijderd. 

Verwijder ACL-vermeldingen door de methode **DataLakeDirectoryClient. RemoveAccessControlRecursiveAsync** aan te roepen. Geef deze methode een [lijst](/dotnet/api/system.collections.generic.list-1) van [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Elk [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) definieert een ACL-vermelding. 

Als u een **standaard** -ACL-vermelding wilt verwijderen, kunt u de eigenschap [PathAccessControlItem. DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) van de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) instellen op **True**. 

In dit voor beeld wordt een ACL-vermelding verwijderd uit de toegangs beheer lijst van de map met de naam `my-parent-directory` . Deze methode accepteert een Booleaanse para meter `isDefaultScope` met de naam die aangeeft of het item uit de standaard-ACL moet worden verwijderd. Deze para meter wordt gebruikt in de constructor van de [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_RemoveACLRecursively":::

Zie .NET-voor [beeld](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)voor een voor beeld van het recursief verwijderen van acl's in batches door een batch grootte op te geven.

## <a name="recover-from-failures"></a>Herstellen van fouten

Er kunnen runtime-of machtigings fouten optreden bij het recursief wijzigen van Acl's. Start voor runtime-fouten het proces vanaf het begin. Machtigings fouten kunnen optreden als de beveiligingsprincipal niet voldoende machtigingen heeft om de toegangs beheer lijst van een map of bestand te wijzigen dat zich in de Directory-hiërarchie bevindt die wordt gewijzigd. Adresseer het probleem met de machtiging en kies vervolgens het proces hervatten vanaf het moment van de fout met behulp van een vervolg token of start het proces opnieuw vanaf het begin. U hoeft het vervolg token niet te gebruiken als u de voor keur hebt om vanaf het begin opnieuw op te starten. U kunt ACL-vermeldingen zonder negatieve gevolgen opnieuw Toep assen.

In dit voor beeld wordt een vervolg token geretourneerd in het geval van een fout. De toepassing kan deze voorbeeld methode opnieuw aanroepen nadat de fout is opgelost en door gegeven in het vervolg token. Als deze voorbeeld methode voor de eerste keer wordt aangeroepen, kan de toepassing de waarde `null` voor de vervolg token parameter door geven. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ResumeContinuationToken":::

Als u een voor beeld wilt weer geven waarin Acl's in batches worden ingesteld door een batch grootte op te geven, raadpleegt u het .NET-voor [beeld](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

Als u wilt dat het proces wordt afgebroken door machtigings fouten, kunt u dit opgeven.

Om ervoor te zorgen dat het proces wordt voltooid, geeft u een **AccessControlChangedOptions** -object door en stelt u de eigenschap **ContinueOnFailure** van dat object in op ``true`` .

In dit voor beeld worden ACL-vermeldingen recursief ingesteld. Als met deze code een machtigings fout optreedt, wordt die fout geregistreerd en wordt de uitvoering voortgezet. In dit voor beeld wordt het aantal fouten naar de console afgedrukt.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ContinueOnFailure":::

Als u een voor beeld wilt weer geven waarin Acl's in batches worden ingesteld door een batch grootte op te geven, raadpleegt u het .NET-voor [beeld](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0).

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Zie ook

- [API-referentiedocumentatie](/dotnet/api/azure.storage.files.datalake)
- [Pakket (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
- [Voorbeelden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
- [Toewijzing van gen1 naar Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
- [Bekende problemen](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Feedback geven](https://github.com/Azure/azure-sdk-for-net/issues)
- [Access Control model in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Toegangs beheer lijsten (Acl's) in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)