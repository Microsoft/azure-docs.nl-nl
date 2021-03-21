---
title: Java gebruiken om Acl's in Azure Data Lake Storage Gen2 in te stellen
description: Gebruik Azure Storage bibliotheken voor Java voor het beheren van toegangs beheer lijsten (ACL) in opslag accounts waarvoor HNS (hiërarchische naam ruimte) is ingeschakeld.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.custom: devx-track-java
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: e7d6156fe5cd8ab32ff159bda64e0c06cfbac406
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100654092"
---
# <a name="use-java-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Java gebruiken voor het beheren van Acl's in Azure Data Lake Storage Gen2

In dit artikel leest u hoe u Java gebruikt om de toegangs beheer lijsten van mappen en bestanden op te halen, in te stellen en bij te werken. 

ACL-overname is al beschikbaar voor nieuwe onderliggende items die zijn gemaakt onder een bovenliggende map. Maar u kunt Acl's recursief ook toevoegen, bijwerken en verwijderen voor de bestaande onderliggende items van een bovenliggende map zonder dat u deze wijzigingen afzonderlijk voor elk onderliggend item hoeft aan te brengen. 

[Pakket (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)  |  Voor [beelden](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)  |  [API-verwijzing](/java/api/overview/azure/storage-file-datalake-readme)  |  Toewijzing van gen1 [naar Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Feedback geven](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

- Een opslag account met een hiërarchische naam ruimte (HNS) ingeschakeld. Volg [deze](create-data-lake-storage-account.md) instructies om er een te maken.

- Azure CLI-versie `2.6.0` of hoger.

- Een van de volgende beveiligings machtigingen:

  - Een [beveiligings-principal](../../role-based-access-control/overview.md#security-principal) ingericht Azure Active Directory (AD) waaraan de rol [Storage BLOB data owner](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) is toegewezen in het bereik van de doel container, de bovenliggende resource groep of het abonnement.  

  - De gebruiker die eigenaar is van de doel container of-map waarvoor u de ACL-instellingen wilt Toep assen. Als u Acl's recursief wilt instellen, geldt dit voor alle onderliggende items in de doel container of directory.
  
  - Sleutel van het opslag account.

## <a name="set-up-your-project"></a>Uw project instellen

Open [Deze pagina](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) en zoek de meest recente versie van de Java-bibliotheek om aan de slag te gaan. Open vervolgens het *pom.xml* -bestand in de tekst editor. Voeg een afhankelijkheids element toe dat verwijst naar die versie.

Als u van plan bent om uw client toepassing te verifiëren met behulp van Azure Active Directory (AD), voegt u een afhankelijkheid toe aan de Azure Secret-client bibliotheek. Zie  [het geheim-client bibliotheek pakket toevoegen aan uw project](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project).

Voeg vervolgens deze Imports-instructies toe aan het code bestand.

```java
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.AccessControlChangeCounters;
import com.azure.storage.file.datalake.models.AccessControlChangeResult;
import com.azure.storage.file.datalake.models.AccessControlType;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.PathRemoveAccessControlEntry;
import com.azure.storage.file.datalake.models.RolePermissions;
import com.azure.storage.file.datalake.options.PathSetAccessControlRecursiveOptions;
```

## <a name="connect-to-the-account"></a>Verbinding maken met het account

Als u de fragmenten in dit artikel wilt gebruiken, moet u een **DataLakeServiceClient** -exemplaar maken dat het opslag account vertegenwoordigt. 

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Verbinding maken met behulp van Azure Active Directory (Azure AD)

U kunt de [Azure Identity client-bibliotheek voor Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) gebruiken om uw toepassing te verifiëren met Azure AD.

Haal een client-ID, een client geheim en een Tenant-ID op. Zie voor dit doen [een Token ophalen uit Azure AD voor het machtigen van aanvragen van een client toepassing](../common/storage-auth-aad-app.md). Als onderdeel van dit proces moet u een van de volgende [Azure-rollen op rollen gebaseerd toegangs beheer (Azure RBAC)](../../role-based-access-control/overview.md) toewijzen aan uw beveiligings-principal. 

|Rol|Mogelijkheid van ACL-instelling|
|--|--|
|[Eigenaar van opslagblobgegevens](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Alle mappen en bestanden in het account.|
|[Inzender voor Storage Blob-gegevens](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Alleen mappen en bestanden die eigendom zijn van de beveiligingsprincipal.|

In dit voor beeld wordt een **DataLakeServiceClient** -exemplaar gemaakt met behulp van een client-id, een client geheim en een Tenant-id.  

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithAzureAD":::

> [!NOTE]
> Zie de [Azure Identity client-bibliotheek voor Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) -documentatie voor meer voor beelden.

### <a name="connect-by-using-an-account-key"></a>Verbinding maken met behulp van een account sleutel

Dit is de eenvoudigste manier om verbinding te maken met een account. 

In dit voor beeld wordt een **DataLakeServiceClient** -exemplaar gemaakt met behulp van een account sleutel.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithKey":::

## <a name="set-acls"></a>Acl's instellen

Wanneer u een ACL *instelt* , **vervangt** u de volledige ACL, inclusief alle items. Als u het machtigings niveau van een beveiligingsprincipal wilt wijzigen of een nieuwe beveiligingsprincipal wilt toevoegen aan de ACL zonder dat dit van invloed is op andere bestaande vermeldingen, moet u de toegangs beheer lijst in plaats daarvan *bijwerken* . Zie de sectie [Acl's bijwerken](#update-acls) in dit artikel als u een ACL wilt bijwerken in plaats van deze te vervangen.  

Als u ervoor kiest om de ACL in te *stellen* , moet u een vermelding toevoegen voor de gebruiker die eigenaar is, een vermelding voor de groep die eigenaar is en een vermelding voor alle andere gebruikers. Zie [gebruikers en identiteiten](data-lake-storage-access-control.md#users-and-identities)voor meer informatie over de gebruiker die eigenaar is, de groep die eigenaar is en alle andere gebruikers.

In deze sectie wordt beschreven hoe u:

- De ACL van een directory instellen
- De ACL van een bestand instellen
- ACL's recursief instellen 

### <a name="set-the-acl-of-a-directory"></a>De ACL van een directory instellen

In dit voor beeld wordt de ACL van een directory met de naam opgehaald en ingesteld `my-directory` . In dit voor beeld worden de machtigingen lezen, schrijven en uitvoeren voor de gebruiker die eigenaar is, de groep die eigenaar is, de machtigingen lezen en uitvoeren, en krijgt alle andere Lees toegang.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageDirectoryACLs":::

U kunt ook de toegangs beheer lijst van de hoofdmap van een container ophalen en instellen. Als u de hoofdmap wilt ophalen, geeft u een lege teken reeks () door aan `""` de methode **DataLakeFileSystemClient. getDirectoryClient** .

### <a name="set-the-acl-of-a-file"></a>De ACL van een bestand instellen

In dit voor beeld wordt de ACL van een bestand met de naam opgehaald en ingesteld `upload-file.txt` . In dit voor beeld worden de machtigingen lezen, schrijven en uitvoeren voor de gebruiker die eigenaar is, de groep die eigenaar is, de machtigingen lezen en uitvoeren, en krijgt alle andere Lees toegang.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageFileACLs":::

### <a name="set-acls-recursively"></a>ACL's recursief instellen

Stel Acl's recursief in door de methode **DataLakeDirectoryClient. setAccessControlRecursive** aan te roepen. Geef deze methode een [lijst](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) met [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) -objecten. Elk [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) definieert een ACL-vermelding.

Als u een **standaard** -ACL-vermelding wilt instellen, kunt u de methode **SetDefaultScope** van de [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) aanroepen en de waarde **True** door geven. 

In dit voor beeld wordt de ACL van een map met de naam ingesteld `my-parent-directory` . Deze methode accepteert een Booleaanse para meter `isDefaultScope` met de naam die aangeeft of de standaard-ACL moet worden ingesteld. Deze para meter wordt gebruikt in elke aanroep van de methode **setDefaultScope** van de [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html). De vermeldingen van de ACL geven de machtigingen gebruiker lezen, schrijven en uitvoeren, de groep die eigenaar is, de machtigingen lezen en uitvoeren, en verleent alle anderen geen toegang. De laatste ACL-vermelding in dit voor beeld geeft een specifieke gebruiker met de object-ID ' XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX ' machtigingen voor lezen en uitvoeren.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_SetACLRecursively":::

## <a name="update-acls"></a>Acl's bijwerken

Wanneer u een ACL *bijwerkt* , wijzigt u de ACL in plaats van de ACL te vervangen. U kunt bijvoorbeeld een nieuwe beveiligingsprincipal toevoegen aan de ACL zonder dat dit van invloed is op andere beveiligings-principals die worden vermeld in de ACL.  Zie de sectie [Acl's instellen](#set-acls) in dit artikel om de ACL te vervangen in plaats van deze bij te werken.

In deze sectie wordt beschreven hoe u:

- Een ACL bijwerken
- Acl's recursief bijwerken

### <a name="update-an-acl"></a>Een ACL bijwerken

Haal eerst de ACL van een directory op door de methode [PathAccessControl. getAccessControlList](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrol.getaccesscontrollist) aan te roepen. Kopieer de lijst met ACL-vermeldingen naar een nieuw **lijst** object van het type [PathAccessControlListEntry](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrolentry). Zoek vervolgens de vermelding die u wilt bijwerken en vervang deze in de lijst. Stel de toegangs beheer lijst in door de methode [DataLakeDirectoryClient. setAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) aan te roepen.

In dit voor beeld wordt de ACL van een map met de naam bijgewerkt `my-parent-directory` door de vermelding voor alle andere gebruikers te vervangen. 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_UpdateACL":::

U kunt ook de toegangs beheer lijst van de hoofdmap van een container ophalen en instellen. Als u de hoofdmap wilt ophalen, geeft u een lege teken reeks () door aan `""` de methode **DataLakeFileSystemClient. getDirectoryClient** .

### <a name="update-acls-recursively"></a>Acl's recursief bijwerken

Als u een ACL recursief wilt bijwerken, maakt u een nieuw ACL-object met de ACL-vermelding die u wilt bijwerken, en gebruikt u dat object in de ACL-bewerking voor updates. Haal de bestaande ACL niet op. Zorg dat u alleen ACL-vermeldingen kunt bijwerken.

Update de Acl's recursief door het aanroepen van de methode **DataLakeDirectoryClient. updateAccessControlRecursive** .  Geef deze methode een [lijst](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) met [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) -objecten. Elk [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) definieert een ACL-vermelding. 

Als u een **standaard** -ACL-vermelding wilt bijwerken, kunt u de methode **SetDefaultScope** van de [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) en geeft u de waarde **True** door. 

In dit voor beeld wordt een ACL-vermelding met schrijf machtiging bijgewerkt. Deze methode accepteert een Booleaanse para meter `isDefaultScope` met de naam die aangeeft of de standaard-ACL moet worden bijgewerkt. Die para meter wordt gebruikt bij het aanroepen van de methode **setDefaultScope** van de [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html). 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_UpdateACLRecursively":::

## <a name="remove-acl-entries"></a>ACL'S-vermeldingen verwijderen

U kunt een of meer ACL-vermeldingen verwijderen. In deze sectie wordt beschreven hoe u:

- Een ACL-vermelding verwijderen
- ACL-vermeldingen recursief verwijderen

### <a name="remove-an-acl-entry"></a>Een ACL-vermelding verwijderen

Haal eerst de ACL van een directory op door de methode [PathAccessControl. getAccessControlList](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrol.getaccesscontrollist) aan te roepen. Kopieer de lijst met ACL-vermeldingen naar een nieuw **lijst** object van het type [PathAccessControlListEntry](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrolentry). Zoek vervolgens de vermelding die u wilt verwijderen en roep de methode **Remove** van het **lijst** object aan. Stel de bijgewerkte ACL in door de methode [DataLakeDirectoryClient. setAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) aan te roepen.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_RemoveACLEntry":::

### <a name="remove-acl-entries-recursively"></a>ACL-vermeldingen recursief verwijderen

Als u ACL'S-vermeldingen recursief wilt verwijderen, maakt u een nieuw ACL-object voor de ACL-vermelding die moet worden verwijderd en gebruikt u dat object vervolgens in ACL-bewerking verwijderen. Haal de bestaande ACL niet op. Geef de ACL-vermeldingen op die moeten worden verwijderd.

Verwijder ACL-vermeldingen door de methode **DataLakeDirectoryClient. removeAccessControlRecursive** aan te roepen. Geef deze methode een [lijst](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) met [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) -objecten. Elk [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) definieert een ACL-vermelding. 

Als u een **standaard** -ACL-vermelding wilt verwijderen, kunt u de methode **SetDefaultScope** van de [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) en geeft u de waarde **True** door.  

In dit voor beeld wordt een ACL-vermelding verwijderd uit de toegangs beheer lijst van de map met de naam `my-parent-directory` . Deze methode accepteert een Booleaanse para meter `isDefaultScope` met de naam die aangeeft of het item uit de standaard-ACL moet worden verwijderd. Die para meter wordt gebruikt bij het aanroepen van de methode **setDefaultScope** van de [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html).

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_RemoveACLRecursively":::

## <a name="recover-from-failures"></a>Herstellen van fouten

Er kunnen runtime-of machtigings fouten optreden. Start voor runtime-fouten het proces vanaf het begin. Machtigings fouten kunnen optreden als de beveiligingsprincipal niet voldoende machtigingen heeft om de toegangs beheer lijst van een map of bestand te wijzigen dat zich in de Directory-hiërarchie bevindt die wordt gewijzigd. Adresseer het probleem met de machtiging en kies vervolgens het proces hervatten vanaf het moment van de fout met behulp van een vervolg token of start het proces opnieuw vanaf het begin. U hoeft het vervolg token niet te gebruiken als u de voor keur hebt om vanaf het begin opnieuw op te starten. U kunt ACL-vermeldingen zonder negatieve gevolgen opnieuw Toep assen.

In dit voor beeld wordt een vervolg token geretourneerd in het geval van een fout. De toepassing kan deze voorbeeld methode opnieuw aanroepen nadat de fout is opgelost en door gegeven in het vervolg token. Als deze voorbeeld methode voor de eerste keer wordt aangeroepen, kan de toepassing de waarde `null` voor de vervolg token parameter door geven.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ResumeSetACLRecursively":::

Als u wilt dat het proces wordt afgebroken door machtigings fouten, kunt u dit opgeven.

Om ervoor te zorgen dat het proces wordt voltooid, roept u de methode **setContinueOnFailure** van een [PathSetAccessControlRecursiveOptions](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) -object aan en geeft u de waarde **True** door.

In dit voor beeld worden ACL-vermeldingen recursief ingesteld. Als met deze code een machtigings fout optreedt, wordt die fout geregistreerd en wordt de uitvoering voortgezet. In dit voor beeld wordt het aantal fouten naar de console afgedrukt.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ContinueOnFailure":::

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Zie ook

- [API-referentiedocumentatie](/java/api/overview/azure/storage-file-datalake-readme)
- [Pakket (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
- [Voorbeelden](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
- [Toewijzing van gen1 naar Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [Bekende problemen](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Feedback geven](https://github.com/Azure/azure-sdk-for-java/issues)
- [Access Control model in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Toegangs beheer lijsten (Acl's) in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)