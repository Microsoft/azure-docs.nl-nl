---
title: Power shell gebruiken voor het beheren van Acl's in Azure Data Lake Storage Gen2
description: Power shell-cmdlets gebruiken voor het beheren van toegangs beheer lijsten (ACL) in opslag accounts met een hiërarchische naam ruimte (HNS) ingeschakeld.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b606e69baec8d159a6a3fa7373500176260ef0d7
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654145"
---
# <a name="use-powershell-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Power shell gebruiken voor het beheren van Acl's in Azure Data Lake Storage Gen2

In dit artikel leest u hoe u Power shell kunt gebruiken om de toegangs beheer lijsten van mappen en bestanden op te halen, in te stellen en bij te werken. 

ACL-overname is al beschikbaar voor nieuwe onderliggende items die zijn gemaakt onder een bovenliggende map. Maar u kunt Acl's recursief ook toevoegen, bijwerken en verwijderen voor de bestaande onderliggende items van een bovenliggende map zonder dat u deze wijzigingen afzonderlijk voor elk onderliggend item hoeft aan te brengen. 

[Naslag informatie](/powershell/module/Az.Storage/)  |  [Recursieve ACL-voor beelden](https://recursiveaclpr.blob.core.windows.net/privatedrop/samplePS.ps1?sv=2019-02-02&st=2020-08-24T17%3A04%3A44Z&se=2021-08-25T17%3A04%3A00Z&sr=b&sp=r&sig=dNNKS%2BZcp%2F1gl6yOx6QLZ6OpmXkN88ZjBeBtym1Mejo%3D)  |  [Feedback geven](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

- Een opslag account met een hiërarchische naam ruimte (HNS) ingeschakeld. Volg [deze](create-data-lake-storage-account.md) instructies om er een te maken.

- Azure CLI-versie `2.6.0` of hoger.

- Een van de volgende beveiligings machtigingen:

  - Een [beveiligings-principal](../../role-based-access-control/overview.md#security-principal) ingericht Azure Active Directory (AD) waaraan de rol [Storage BLOB data owner](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) is toegewezen in het bereik van de doel container, de bovenliggende resource groep of het abonnement.  

  - De gebruiker die eigenaar is van de doel container of-map waarvoor u de ACL-instellingen wilt Toep assen. Als u Acl's recursief wilt instellen, geldt dit voor alle onderliggende items in de doel container of directory.
  
  - Sleutel van het opslag account.

## <a name="install-the-powershell-module"></a>De PowerShell-module installeren

1. Controleer of de versie van Power shell die is geïnstalleerd, `5.1` of hoger is met behulp van de volgende opdracht.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```

   Zie [bestaande Windows Power shell upgraden](/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell) voor informatie over het bijwerken van uw versie van Power shell

2. Installeer **AZ. Storage** -module.

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   Zie [de module Azure PowerShell installeren](/powershell/azure/install-az-ps) voor meer informatie over het installeren van Power shell-modules.

## <a name="connect-to-the-account"></a>Verbinding maken met het account

Kies hoe u wilt dat uw opdrachten autorisatie aanvragen voor het opslag account. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>Optie 1: autorisatie verkrijgen met behulp van Azure Active Directory (AD)

> [!NOTE]
> Als u Azure Active Directory (Azure AD) gebruikt om toegang te verlenen, moet u ervoor zorgen dat aan uw beveiligingsprincipal de [rol Storage BLOB data owner](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)is toegewezen. Zie voor meer informatie over hoe ACL-machtigingen worden toegepast en de gevolgen van het wijzigen van  [toegangs beheer model in azure data Lake Storage Gen2](./data-lake-storage-access-control-model.md).

Met deze methode zorgt het systeem ervoor dat uw gebruikers account beschikt over de juiste toewijzings-en ACL-machtigingen van Azure op rollen gebaseerde toegangs beheer (Azure RBAC).

1. Open een Windows Power shell-opdracht venster en meld u vervolgens aan bij uw Azure-abonnement met de `Connect-AzAccount` opdracht en volg de instructies op het scherm.

   ```powershell
   Connect-AzAccount
   ```

2. Als uw identiteit is gekoppeld aan meer dan één abonnement, stelt u uw actieve abonnement in op het abonnement van het opslag account waarin u directory's wilt maken en beheren. In dit voor beeld vervangt `<subscription-id>` u de waarde van de tijdelijke aanduiding door de id van uw abonnement.

   ```powershell
   Select-AzSubscription -SubscriptionId <subscription-id>
   ``` 

3. De context van het opslag account ophalen.

   ```powershell
   $ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
   ```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Optie 2: autorisatie verkrijgen met behulp van de sleutel van het opslag account

Met deze methode controleert het systeem geen Azure RBAC-of ACL-machtigingen. Haal de context van het opslag account op met behulp van een account sleutel.

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -StorageAccountKey '<storage-account-key>'
```

## <a name="get-acls"></a>Acl's ophalen

De ACL van een map of bestand ophalen met behulp van de- `Get-AzDataLakeGen2Item` cmdlet.

In dit voor beeld wordt de ACL van de hoofdmap van een **container** opgehaald en wordt de ACL vervolgens naar de console afgedrukt.

```powershell
$filesystemName = "my-file-system"
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

In dit voor beeld wordt de ACL van een **Directory** opgehaald en wordt de ACL vervolgens naar de console afgedrukt.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

In dit voor beeld wordt de ACL van een **bestand** opgehaald en wordt de ACL vervolgens naar de console afgedrukt.

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

In de volgende afbeelding ziet u de uitvoer na het ophalen van de ACL van een directory.

![ACL-uitvoer ophalen voor Directory](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

In dit voor beeld heeft de gebruiker die eigenaar is, de machtigingen lezen, schrijven en uitvoeren. De groep die eigenaar is, heeft alleen lees-en uitvoer machtigingen. Zie [toegangs beheer in azure data Lake Storage Gen2](data-lake-storage-access-control.md)voor meer informatie over toegangs beheer lijsten.

## <a name="set-acls"></a>Acl's instellen

Wanneer u een ACL *instelt* , **vervangt** u de volledige ACL, inclusief alle items. Als u het machtigings niveau van een beveiligingsprincipal wilt wijzigen of een nieuwe beveiligingsprincipal wilt toevoegen aan de ACL zonder dat dit van invloed is op andere bestaande vermeldingen, moet u de toegangs beheer lijst in plaats daarvan *bijwerken* . Zie de sectie [Acl's bijwerken](#update-acls) in dit artikel als u een ACL wilt bijwerken in plaats van deze te vervangen.  

Als u ervoor kiest om de ACL in te *stellen* , moet u een vermelding toevoegen voor de gebruiker die eigenaar is, een vermelding voor de groep die eigenaar is en een vermelding voor alle andere gebruikers. Zie [gebruikers en identiteiten](data-lake-storage-access-control.md#users-and-identities)voor meer informatie over de gebruiker die eigenaar is, de groep die eigenaar is en alle andere gebruikers.

In deze sectie wordt beschreven hoe u:

- Een ACL instellen
- ACL's recursief instellen

### <a name="set-an-acl"></a>Een ACL instellen

Gebruik de `set-AzDataLakeGen2ItemAclObject` cmdlet om een ACL te maken voor de gebruiker die eigenaar is, de groep die eigenaar is of andere gebruikers. Gebruik vervolgens de `Update-AzDataLakeGen2Item` cmdlet om de ACL door te voeren.

In dit voor beeld wordt de ACL ingesteld op de hoofdmap van een **container** voor de gebruiker die eigenaar is van de groep of andere gebruikers, en wordt de ACL vervolgens naar de console afgedrukt.

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Acl $acl
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

In dit voor beeld wordt de ACL ingesteld op een **map** voor de gebruiker die eigenaar is van de groep of andere gebruikers, en wordt de ACL vervolgens naar de console afgedrukt.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

> [!NOTE]
> Als u een **standaard** -ACL-vermelding wilt instellen, gebruikt u de para meter **-DefaultScope** wanneer u de opdracht **set-AzDataLakeGen2ItemAclObject** uitvoert. Bijvoorbeeld: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`.

In dit voor beeld wordt de toegangs beheer lijst voor een **bestand** ingesteld op de gebruiker die eigenaar is van de groep of van andere gebruikers, en wordt de ACL vervolgens naar de console afgedrukt.

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath -Acl $acl
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

> [!NOTE]
> Als u een **standaard** -ACL-vermelding wilt instellen, gebruikt u de para meter **-DefaultScope** wanneer u de opdracht **set-AzDataLakeGen2ItemAclObject** uitvoert. Bijvoorbeeld: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`.

In de volgende afbeelding ziet u de uitvoer na het instellen van de ACL van een bestand.

![ACL-uitvoer voor bestand ophalen](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

In dit voor beeld hebben de gebruiker die eigenaar is en de groep die eigenaar is alleen lees-en schrijf machtigingen. Alle andere gebruikers hebben machtigingen voor schrijven en uitvoeren. Zie [toegangs beheer in azure data Lake Storage Gen2](data-lake-storage-access-control.md)voor meer informatie over toegangs beheer lijsten.

### <a name="set-acls-recursively"></a>ACL's recursief instellen

Stel Acl's recursief in met behulp van de cmdlet **set-AzDataLakeGen2AclRecursive** .

In dit voor beeld wordt de ACL van een map met de naam ingesteld `my-parent-directory` . Deze vermeldingen geven de machtigingen lezen, schrijven en uitvoeren van de gebruiker die eigenaar is, de groep die eigenaar is, de machtigingen lezen en uitvoeren, en krijgen alle andere geen toegang. De laatste ACL-vermelding in dit voor beeld geeft een specifieke gebruiker met de object-ID ' XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX ' machtigingen voor lezen en uitvoeren.

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission r-x -InputObject $acl 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "---" -InputObject $acl
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission r-x -InputObject $acl 

Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Als u een **standaard** -ACL-vermelding wilt instellen, gebruikt u de para meter **-DefaultScope** wanneer u de opdracht **set-AzDataLakeGen2ItemAclObject** uitvoert. Bijvoorbeeld: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`.

Zie het artikel [set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) Reference voor een voor beeld van het recursief instellen van acl's in batches door een batch grootte op te geven.

## <a name="update-acls"></a>Acl's bijwerken

Wanneer u een ACL *bijwerkt* , wijzigt u de ACL in plaats van de ACL te vervangen. U kunt bijvoorbeeld een nieuwe beveiligingsprincipal toevoegen aan de ACL zonder dat dit van invloed is op andere beveiligings-principals die worden vermeld in de ACL.  Zie de sectie [Acl's instellen](#set-acls) in dit artikel om de ACL te vervangen in plaats van deze bij te werken.

Als u een ACL wilt bijwerken, maakt u een nieuw ACL-object met de ACL-vermelding die u wilt bijwerken en gebruikt u dat object in de ACL-bewerking voor updates. Haal de bestaande ACL niet op. Zorg dat u alleen ACL-vermeldingen kunt bijwerken.

In deze sectie wordt beschreven hoe u:

- Een ACL bijwerken
- Acl's recursief bijwerken

### <a name="update-an-acl"></a>Een ACL bijwerken

Haal eerst de ACL op. Gebruik vervolgens de `set-AzDataLakeGen2ItemAclObject` cmdlet om een ACL-vermelding toe te voegen of bij te werken. Gebruik de `Update-AzDataLakeGen2Item` cmdlet om de ACL door te voeren.

In dit voor beeld wordt de ACL voor een gebruiker gemaakt of bijgewerkt voor een **map** .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -InputObject $acl 
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
```

> [!NOTE]
> Als u een **standaard** -ACL-vermelding wilt bijwerken, gebruikt u de para meter **-DefaultScope** wanneer u de opdracht **set-AzDataLakeGen2ItemAclObject** uitvoert. Bijvoorbeeld: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -DefaultScope`.

### <a name="update-acls-recursively"></a>Acl's recursief bijwerken

Acl's recursief bijwerken met behulp van de cmdlet  **Update-AzDataLakeGen2AclRecursive** .

In dit voor beeld wordt een ACL-vermelding met schrijf machtiging bijgewerkt.

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx

Update-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Als u een **standaard** -ACL-vermelding wilt bijwerken, gebruikt u de para meter **-DefaultScope** wanneer u de opdracht **set-AzDataLakeGen2ItemAclObject** uitvoert. Bijvoorbeeld: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx -DefaultScope`.

Zie het artikel Naslag informatie over [Update AzDataLakeGen2AclRecursive](/powershell/module/az.storage/update-azdatalakegen2aclrecursive) voor een voor beeld van het recursief bijwerken van acl's in batches door een batch grootte op te geven.

## <a name="remove-acl-entries"></a>ACL'S-vermeldingen verwijderen

In deze sectie wordt beschreven hoe u:

- Een ACL-vermelding verwijderen
- ACL-vermeldingen recursief verwijderen

### <a name="remove-an-acl-entry"></a>Een ACL-vermelding verwijderen

In dit voor beeld wordt een item uit een bestaande ACL verwijderd.

```powershell
$id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

# Create the new ACL object.
[Collections.Generic.List[System.Object]]$aclnew =$acl

foreach ($a in $aclnew)
{
    if ($a.AccessControlType -eq "User"-and $a.DefaultScope -eq $false -and $a.EntityId -eq $id)
    {
        $aclnew.Remove($a);
        break;
    }
}
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $aclnew
```

### <a name="remove-acl-entries-recursively"></a>ACL-vermeldingen recursief verwijderen

U kunt een of meer ACL-vermeldingen recursief verwijderen. Als u een ACL-vermelding wilt verwijderen, maakt u een nieuw ACL-object voor de ACL-vermelding die moet worden verwijderd, en gebruikt u dat object in de bewerking ACL'S verwijderen. Haal de bestaande ACL niet op. Geef de ACL-vermeldingen op die moeten worden verwijderd.

Verwijder ACL-vermeldingen met behulp van de cmdlet **Remove-AzDataLakeGen2AclRecursive** .

In dit voor beeld wordt een ACL-vermelding verwijderd uit de hoofd directory van de container.  

```powershell
$filesystemName = "my-container"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" 

Remove-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName  -Acl $acl
```

> [!NOTE]
> Als u een **standaard** -ACL-vermelding wilt verwijderen, gebruikt u de para meter **-DefaultScope** wanneer u de opdracht **set-AzDataLakeGen2ItemAclObject** uitvoert. Bijvoorbeeld: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" -DefaultScope`.

Zie het artikel Naslag informatie over [Remove-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/remove-azdatalakegen2aclrecursive) om een voor beeld te zien van het recursief verwijderen van acl's in batches door een batch grootte op te geven.

## <a name="recover-from-failures"></a>Herstellen van fouten

Er kunnen runtime-of machtigings fouten optreden bij het recursief wijzigen van Acl's. 

Start voor runtime-fouten het proces vanaf het begin. Machtigings fouten kunnen optreden als de beveiligingsprincipal niet voldoende machtigingen heeft om de toegangs beheer lijst van een map of bestand te wijzigen dat zich in de Directory-hiërarchie bevindt die wordt gewijzigd. Adresseer het probleem met de machtiging en kies vervolgens het proces hervatten vanaf het moment van de fout met behulp van een vervolg token of start het proces opnieuw vanaf het begin. U hoeft het vervolg token niet te gebruiken als u de voor keur hebt om vanaf het begin opnieuw op te starten. U kunt ACL-vermeldingen zonder negatieve gevolgen opnieuw Toep assen.

In dit voor beeld worden resultaten geretourneerd naar de variabele en vervolgens worden mislukte items naar een ingedeelde tabel gepiped.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$result
$result.FailedEntries | ft 
```

Op basis van de uitvoer van de tabel kunt u eventuele machtigings fouten oplossen en de uitvoering hervatten met behulp van het vervolg token.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinuationToken $result.ContinuationToken
$result

```

Zie het artikel [set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) Reference voor een voor beeld van het recursief instellen van acl's in batches door een batch grootte op te geven.

Als u wilt dat het proces wordt afgebroken door machtigings fouten, kunt u dit opgeven.

In dit voor beeld wordt de `ContinueOnFailure` para meter gebruikt, zodat de uitvoering wordt voortgezet, zelfs als er een machtigings fout optreedt in de bewerking. 

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinueOnFailure

echo "[Result Summary]"
echo "TotalDirectoriesSuccessfulCount: `t$($result.TotalFilesSuccessfulCount)"
echo "TotalFilesSuccessfulCount: `t`t`t$($result.TotalDirectoriesSuccessfulCount)"
echo "TotalFailureCount: `t`t`t`t`t$($result.TotalFailureCount)"
echo "FailedEntries:"$($result.FailedEntries | ft) 
```

Zie het artikel [set-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) Reference voor een voor beeld van het recursief instellen van acl's in batches door een batch grootte op te geven.

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Zie ook

- [Bekende problemen](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [PowerShell Storage-cmdlets](/powershell/module/az.storage)
- [Access Control model in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Toegangs beheer lijsten (Acl's) in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
