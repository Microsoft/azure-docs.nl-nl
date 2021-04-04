---
title: Power shell gebruiken om een gebruikers delegering SA'S te maken voor een container of BLOB
titleSuffix: Azure Storage
description: Meer informatie over het maken van een gebruiker met Azure Active Directory referenties met behulp van Power shell.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 875b2a9f35562dd8f0d5df3c631e5ade1e3fbf75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91714518"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-powershell"></a>Een SAS voor gebruikers overdracht maken voor een container of BLOB met Power shell

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

In dit artikel wordt beschreven hoe u Azure Active Directory (Azure AD)-referenties gebruikt om een gebruikers delegering SA'S te maken voor een container of BLOB met Azure PowerShell.

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-powershell-module"></a>De PowerShell-module installeren

Als u een gebruikers delegering SA'S met Power shell wilt maken, installeert u versie 1.10.0 of hoger van de module AZ. storage. Volg deze stappen om de meest recente versie van de module te installeren:

1. Eerdere installaties van Azure PowerShell verwijderen:

    - Verwijder eerdere installaties van Azure PowerShell van Windows met de instelling **Apps & onderdelen** onder **instellingen**.
    - Verwijder alle **Azure** -modules uit `%Program Files%\WindowsPowerShell\Modules` .

1. Zorg ervoor dat de meest recente versie van PowerShellGet is geïnstalleerd. Open een Windows Power shell-venster en voer de volgende opdracht uit om de meest recente versie te installeren:

    ```powershell
    Install-Module PowerShellGet –Repository PSGallery –Force
    ```

1. Sluit het Power shell-venster en open het opnieuw nadat u PowerShellGet hebt geïnstalleerd.

1. Installeer de nieuwste versie van Azure PowerShell:

    ```powershell
    Install-Module Az –Repository PSGallery –AllowClobber
    ```

1. Zorg ervoor dat u Azure PowerShell versie 3.2.0 of hoger hebt geïnstalleerd. Voer de volgende opdracht uit om de nieuwste versie van de Azure Storage Power shell-module te installeren:

    ```powershell
    Install-Module -Name Az.Storage -Repository PSGallery -Force
    ```

1. Sluit het Power shell-venster en open het opnieuw.

Als u wilt controleren welke versie van de module AZ. Storage is geïnstalleerd, voert u de volgende opdracht uit:

```powershell
Get-Module -ListAvailable -Name Az.Storage -Refresh
```

Zie [Install Azure PowerShell with PowerShellGet](/powershell/azure/install-az-ps)(Engelstalig) voor meer informatie over het installeren van Azure PowerShell.

## <a name="sign-in-to-azure-powershell-with-azure-ad"></a>Meld u aan bij Azure PowerShell met Azure AD

Roep de opdracht [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) aan om u aan te melden met uw Azure ad-account:

```powershell
Connect-AzAccount
```

Zie [Aanmelden met Azure PowerShell](/powershell/azure/authenticate-azureps)voor meer informatie over het aanmelden met Power shell.

## <a name="assign-permissions-with-azure-rbac"></a>Machtigingen toewijzen met Azure RBAC

Als u een gebruikers delegering SA'S wilt maken op basis van Azure PowerShell, moet aan het Azure AD-account dat wordt gebruikt om u aan te melden bij Power shell een rol worden toegewezen die de actie **micro soft. Storage/Storage accounts/blobServices/generateUserDelegationKey** bevat. Met deze machtiging is het mogelijk dat Azure AD-account de *gebruikers delegerings sleutel* aanvraagt. De sleutel voor gebruikers overdracht wordt gebruikt voor het ondertekenen van de SA'S van de gebruikers delegering. De rol voor het opgeven van de actie **micro soft. Storage/Storage accounts/blobServices/generateUserDelegationKey** moet worden toegewezen op het niveau van het opslag account, de resource groep of het abonnement. Voor meer informatie over Azure RBAC-machtigingen voor het maken van een SA'S voor het delegeren van gebruikers, zie de sectie **machtigingen toewijzen met Azure RBAC** in [een gebruiker met sa's voor het delegeren van gebruikers maken](/rest/api/storageservices/create-user-delegation-sas).

Als u onvoldoende machtigingen hebt voor het toewijzen van Azure-functies aan een Azure AD-beveiligingsprincipal, moet u mogelijk de eigenaar of beheerder van het account vragen de benodigde machtigingen toe te wijzen.

In het volgende voor beeld wordt de rol **Storage BLOB data Inzender** toegewezen, inclusief de actie **micro soft. Storage/Storage accounts/blobServices/generateUserDelegationKey** . De rol is bereik op het niveau van het opslag account.

Vergeet niet om de waarden van de tijdelijke aanduidingen tussen de punthaken te vervangen door uw eigen waarden:

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Zie [ingebouwde rollen van Azure](../../role-based-access-control/built-in-roles.md)voor meer informatie over de ingebouwde rollen die de actie **micro soft. Storage/Storage accounts/blobServices/generateUserDelegationKey** bevatten.

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Azure AD-referenties gebruiken voor het beveiligen van een SAS

Wanneer u een SAS voor gebruikers overdracht met Azure PowerShell maakt, wordt de sleutel voor gebruikers overdracht die wordt gebruikt om de SA'S te ondertekenen, impliciet voor u gemaakt. De begin tijd en verloop tijd die u opgeeft voor de SA'S, worden ook gebruikt als de begin tijd en verloop tijd voor de gebruikers delegerings sleutel. 

Omdat het maximale interval waarover de gebruikers overdracht-sleutel geldig is, zeven dagen na de begin datum is, moet u een verloop tijd opgeven voor de SA'S die binnen zeven dagen na de begin tijd vallen. De SAS is ongeldig nadat de gebruikers delegerings sleutel is verlopen, waardoor een SAS met een verloop tijd van meer dan zeven dagen nog geldig is gedurende 7 dagen.

Als u een gebruikers delegering SAS voor een container of BLOB met Azure PowerShell wilt maken, moet u eerst een nieuw Azure Storage context-object maken, waarbij u de `-UseConnectedAccount` para meter opgeeft. De `-UseConnectedAccount` para meter geeft aan dat met de opdracht het context object wordt gemaakt onder het Azure ad-account waarmee u zich hebt aangemeld.

Vergeet niet om de waarden van de tijdelijke aanduidingen tussen de punthaken te vervangen door uw eigen waarden:

```powershell
$ctx = New-AzStorageContext -StorageAccountName <storage-account> -UseConnectedAccount
```

### <a name="create-a-user-delegation-sas-for-a-container"></a>Een SAS voor gebruikers overdracht maken voor een container

Als u een SAS-token voor gebruikers overdracht voor een container wilt retour neren, roept u de opdracht [New-AzStorageContainerSASToken](/powershell/module/az.storage/new-azstoragecontainersastoken) aan en geeft u het object Azure Storage context door dat u eerder hebt gemaakt.

In het volgende voor beeld wordt een SAS-token voor gebruikers overdracht voor een container geretourneerd. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

```powershell
New-AzStorageContainerSASToken -Context $ctx `
    -Name <container> `
    -Permission racwdl `
    -ExpiryTime <date-time>
```

Het geretourneerde SAS-token voor gebruikers overdracht is vergelijkbaar met:

```output
?sv=2018-11-09&sr=c&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-05T22%3A24%3A36Z&ske=2019-08-07T07%3A
00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=rwdl
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Een SAS voor gebruikers overdracht maken voor een BLOB

Als u een SAS-token voor gebruikers overdracht voor een BLOB wilt retour neren, roept u de opdracht [New-AzStorageBlobSASToken](/powershell/module/az.storage/new-azstorageblobsastoken) aan en geeft u het object Azure Storage context door dat u eerder hebt gemaakt.

De volgende syntaxis retourneert een gebruikers delegering SA'S voor een blob. In het voor beeld wordt de `-FullUri` para meter opgegeven, die de BLOB-URI retourneert waaraan het SAS-token is toegevoegd. Vergeet niet om de waarden van de tijdelijke aanduidingen tussen vier Kante haken te vervangen door uw eigen waarden:

```powershell
New-AzStorageBlobSASToken -Context $ctx `
    -Container <container> `
    -Blob <blob> `
    -Permission racwd `
    -ExpiryTime <date-time>
    -FullUri
```

De geretourneerde SAS-URI voor gebruikers overdracht is vergelijkbaar met:

```output
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?sv=2018-11-09&sr=b&sig=<sig>&skoid=<skoid>&sktid=<sktid>&skt=2019-08-06T21%3A16%3A54Z&ske=2019-08-07T07%3A00%3A00Z&sks=b&skv=2018-11-09&se=2019-08-07T07%3A00%3A00Z&sp=racwd
```

> [!NOTE]
> Een door de gebruiker overgedragen SA'S biedt geen ondersteuning voor het definiëren van machtigingen met een opgeslagen toegangs beleid.

## <a name="revoke-a-user-delegation-sas"></a>Een SAS voor gebruikers overdracht intrekken

Als u een gebruikers delegering SA'S van Azure PowerShell wilt intrekken, roept u de opdracht **REVOKE-AzStorageAccountUserDelegationKeys** aan. Met deze opdracht worden alle sleutels voor gebruikers overdracht die zijn gekoppeld aan het opgegeven opslag account ingetrokken. Shared Access signatures die zijn gekoppeld aan deze sleutels, worden ongeldig gemaakt.

Vergeet niet om de waarden van de tijdelijke aanduidingen tussen de punthaken te vervangen door uw eigen waarden:

```powershell
Revoke-AzStorageAccountUserDelegationKeys -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
```

> [!IMPORTANT]
> Zowel de gebruikers overdracht sleutel als Azure-roltoewijzingen worden in de cache opgeslagen door Azure Storage. er kan dus een vertraging optreden wanneer u het proces van het intrekken initieert en wanneer een bestaande SA'S van de gebruikers overdracht ongeldig wordt.

## <a name="next-steps"></a>Volgende stappen

- [Een SAS (REST API) voor gebruikers overdracht maken](/rest/api/storageservices/create-user-delegation-sas)
- [Sleutel bewerking voor gebruikers overdracht ophalen](/rest/api/storageservices/get-user-delegation-key)
