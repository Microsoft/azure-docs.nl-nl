---
title: Azure CLI gebruiken om Acl's in Azure Data Lake Storage Gen2 in te stellen
description: Gebruik de Azure CLI voor het beheren van toegangs beheer lijsten (ACL) in opslag accounts met een hiërarchische naam ruimte.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5ec7d2b243a5eadab2d22dea14ebeac8eabb1722
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103563161"
---
# <a name="use-azure-cli-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Azure CLI gebruiken voor het beheren van Acl's in Azure Data Lake Storage Gen2

Dit artikel laat u zien hoe u de [Azure Command-Line interface (CLI)](/cli/azure/) kunt gebruiken om de toegangs beheer lijsten van mappen en bestanden op te halen, in te stellen en bij te werken.

ACL-overname is al beschikbaar voor nieuwe onderliggende items die zijn gemaakt onder een bovenliggende map. Maar u kunt Acl's recursief ook toevoegen, bijwerken en verwijderen voor de bestaande onderliggende items van een bovenliggende map zonder dat u deze wijzigingen afzonderlijk voor elk onderliggend item hoeft aan te brengen.

[Naslag informatie](/cli/azure/storage/fs/access)  |  Voor [beelden](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)  |  [Feedback geven](https://github.com/Azure/azure-cli-extensions/issues)

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

- Een opslag account waarvoor een hiërarchische naam ruimte is ingeschakeld. Volg [deze](create-data-lake-storage-account.md) instructies om er een te maken.

- Azure CLI-versie `2.14.0` of hoger.

- Een van de volgende beveiligings machtigingen:

  - Een [beveiligings-principal](../../role-based-access-control/overview.md#security-principal) ingericht Azure Active Directory (Azure AD) waaraan de rol [Storage BLOB data owner](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) is toegewezen in het bereik van de doel container, de bovenliggende resource groep of het abonnement.  

  - De gebruiker die eigenaar is van de doel container of-map waarvoor u de ACL-instellingen wilt Toep assen. Als u Acl's recursief wilt instellen, geldt dit voor alle onderliggende items in de doel container of directory.
  
  - Sleutel van het opslag account.

## <a name="ensure-that-you-have-the-correct-version-of-azure-cli-installed"></a>Controleer of u de juiste versie van Azure CLI hebt geïnstalleerd

1. Open de [Azure Cloud shell](../../cloud-shell/overview.md)of open een opdracht console toepassing zoals Windows Power shell als u de Azure cli lokaal hebt [geïnstalleerd](/cli/azure/install-azure-cli) .

2. Controleer of de versie van de Azure CLI die is geïnstalleerd `2.14.0` of hoger is met behulp van de volgende opdracht.

   ```azurecli
    az --version
   ```

   Als uw versie van Azure CLI lager is dan `2.14.0` , installeert u een nieuwere versie. Raadpleeg [De Azure CLI installeren](/cli/azure/install-azure-cli).

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

## <a name="get-acls"></a>Acl's ophalen

De ACL van een **Directory** ophalen met behulp van de `az storage fs access show` opdracht.

In dit voor beeld wordt de ACL van een Directory opgehaald en wordt de ACL vervolgens naar de console afgedrukt.

```azurecli
az storage fs access show -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

De toegangs machtigingen van een **bestand** ophalen met behulp van de `az storage fs access show` opdracht. 

In dit voor beeld wordt de ACL van een bestand opgehaald en wordt de ACL vervolgens naar de console afgedrukt.

```azurecli
az storage fs access show -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

In de volgende afbeelding ziet u de uitvoer na het ophalen van de ACL van een directory.

![ACL-uitvoer ophalen](./media/data-lake-storage-directory-file-acl-cli/get-acl.png)

In dit voor beeld heeft de gebruiker die eigenaar is, de machtigingen lezen, schrijven en uitvoeren. De groep die eigenaar is, heeft alleen lees-en uitvoer machtigingen. Zie [toegangs beheer in azure data Lake Storage Gen2](data-lake-storage-access-control.md)voor meer informatie over toegangs beheer lijsten.

## <a name="set-acls"></a>Acl's instellen

Wanneer u een ACL *instelt* , **vervangt** u de volledige ACL, inclusief alle items. Als u het machtigings niveau van een beveiligingsprincipal wilt wijzigen of een nieuwe beveiligingsprincipal wilt toevoegen aan de ACL zonder dat dit van invloed is op andere bestaande vermeldingen, moet u de toegangs beheer lijst in plaats daarvan *bijwerken* . Zie de sectie [Acl's bijwerken](#update-acls) in dit artikel als u een ACL wilt bijwerken in plaats van deze te vervangen.  

Als u ervoor kiest om de ACL in te *stellen* , moet u een vermelding toevoegen voor de gebruiker die eigenaar is, een vermelding voor de groep die eigenaar is en een vermelding voor alle andere gebruikers. Zie [gebruikers en identiteiten](data-lake-storage-access-control.md#users-and-identities)voor meer informatie over de gebruiker die eigenaar is, de groep die eigenaar is en alle andere gebruikers.

In deze sectie wordt beschreven hoe u:

- Een ACL instellen
- ACL's recursief instellen

### <a name="set-an-acl"></a>Een ACL instellen

Gebruik de `az storage fs access set` opdracht om de ACL van een **Directory** in te stellen. 

In dit voor beeld wordt de ACL ingesteld op een map voor de gebruiker die eigenaar is van de groep of andere gebruikers, en wordt de ACL vervolgens naar de console afgedrukt.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

In dit voor beeld wordt de *standaard* -ACL ingesteld op een map voor de gebruiker die eigenaar is of de groep die eigenaar is, en wordt de ACL vervolgens naar de console afgedrukt.

```azurecli
az storage fs access set --acl "default:user::rw-,group::rw-,other::-wx" -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

Gebruik de `az storage fs access set` opdracht om de ACL van een **bestand** in te stellen. 

In dit voor beeld wordt de toegangs beheer lijst voor een bestand ingesteld op de gebruiker die eigenaar is van de groep of van andere gebruikers, en wordt de ACL vervolgens naar de console afgedrukt.

```azurecli
az storage fs access set --acl "user::rw-,group::rw-,other::-wx" -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Als u de toegangs beheer lijst van een specifieke groep of gebruiker wilt instellen, gebruikt u de bijbehorende object-Id's. Bijvoorbeeld `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` of `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

In de volgende afbeelding ziet u de uitvoer na het instellen van de ACL van een bestand.

![ACL-uitvoer 2 ophalen](./media/data-lake-storage-directory-file-acl-cli/set-acl-file.png)

In dit voor beeld hebben de gebruiker die eigenaar is en de groep die eigenaar is alleen lees-en schrijf machtigingen. Alle andere gebruikers hebben machtigingen voor schrijven en uitvoeren. Zie [toegangs beheer in azure data Lake Storage Gen2](data-lake-storage-access-control.md)voor meer informatie over toegangs beheer lijsten.

### <a name="set-acls-recursively"></a>ACL's recursief instellen

Stel Acl's recursief in met behulp van de opdracht [AZ Storage FS Access set-recursief](/cli/azure/storage/fs/access#az_storage_fs_access_set_recursive) .

In dit voor beeld wordt de ACL van een map met de naam ingesteld `my-parent-directory` . Deze vermeldingen geven de machtigingen lezen, schrijven en uitvoeren van de gebruiker die eigenaar is, de groep die eigenaar is, de machtigingen lezen en uitvoeren, en krijgen alle andere geen toegang. De laatste ACL-vermelding in dit voor beeld geeft een specifieke gebruiker met de object-ID ' XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX ' machtigingen voor lezen en uitvoeren.

```azurecli
az storage fs access set-recursive --acl "user::rwx,group::r-x,other::---,user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Als u een **standaard** -ACL-vermelding wilt instellen, voegt u het voor voegsel toe `default:` aan elk item. Bijvoorbeeld `default:user::rwx` of `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`.

## <a name="update-acls"></a>Acl's bijwerken

Wanneer u een ACL *bijwerkt* , wijzigt u de ACL in plaats van de ACL te vervangen. U kunt bijvoorbeeld een nieuwe beveiligingsprincipal toevoegen aan de ACL zonder dat dit van invloed is op andere beveiligings-principals die worden vermeld in de ACL.  Zie de sectie [Acl's instellen](#set-acls) in dit artikel om de ACL te vervangen in plaats van deze bij te werken.

Als u een ACL wilt bijwerken, maakt u een nieuw ACL-object met de ACL-vermelding die u wilt bijwerken en gebruikt u dat object in de ACL-bewerking voor updates. Haal de bestaande ACL niet op. Zorg dat u alleen ACL-vermeldingen kunt bijwerken.

In deze sectie wordt beschreven hoe u:

- Een ACL bijwerken
- Acl's recursief bijwerken

### <a name="update-an-acl"></a>Een ACL bijwerken

Een andere manier om deze machtiging in te stellen, is met behulp van de `az storage fs access set` opdracht. 

De ACL van een map of bestand bijwerken door de `-permissions` para meter in te stellen op de korte vorm van een ACL.

In dit voor beeld wordt de ACL van een **Directory** bijgewerkt.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

In dit voor beeld wordt de ACL van een **bestand** bijgewerkt.

```azurecli
az storage fs access set --permissions rwxrwxrwx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Als u de ACL van een specifieke groep of gebruiker wilt bijwerken, gebruikt u de bijbehorende object-Id's. Bijvoorbeeld `group:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` of `user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

U kunt ook de gebruiker en groep van een map of bestand eigenaar bijwerken door de `--owner` `group` para meters of in te stellen op de entiteit-id of UPN (User Principal Name) van een gebruiker.

In dit voor beeld wordt de eigenaar van een map gewijzigd.

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory -f my-file-system --account-name mystorageaccount --auth-mode login
```

In dit voor beeld wordt de eigenaar van een bestand gewijzigd. 

```azurecli
az storage fs access set --owner xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p my-directory/upload.txt -f my-file-system --account-name mystorageaccount --auth-mode login

```

### <a name="update-acls-recursively"></a>Acl's recursief bijwerken

Acl's recursief bijwerken met behulp van de opdracht  [AZ Storage FS Access update-recursieve](/cli/azure/storage/fs/access#az_storage_fs_access_update_recursive) .

In dit voor beeld wordt een ACL-vermelding met schrijf machtiging bijgewerkt.

```azurecli
az storage fs access update-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:rwx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Als u een **standaard** -ACL-vermelding wilt bijwerken, voegt u het voor voegsel toe `default:` aan elk item. Bijvoorbeeld `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx:r-x`.

## <a name="remove-acl-entries-recursively"></a>ACL-vermeldingen recursief verwijderen

U kunt een of meer ACL-vermeldingen recursief verwijderen. Als u een ACL-vermelding wilt verwijderen, maakt u een nieuw ACL-object voor de ACL-vermelding die moet worden verwijderd, en gebruikt u dat object in de bewerking ACL'S verwijderen. Haal de bestaande ACL niet op. Geef de ACL-vermeldingen op die moeten worden verwijderd.

Verwijder ACL-vermeldingen met behulp van de opdracht [AZ Storage FS Access Remove-recursieve](/cli/azure/storage/fs/access#az_storage_fs_access_remove_recursive) .

In dit voor beeld wordt een ACL-vermelding verwijderd uit de hoofd directory van de container.  

```azurecli
az storage fs access remove-recursive --acl "user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login
```

> [!NOTE]
> Als u een **standaard** -ACL-vermelding wilt verwijderen, voegt u het voor voegsel toe `default:` aan elk item. Bijvoorbeeld `default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

## <a name="recover-from-failures"></a>Herstellen van fouten

Er kunnen runtime-of machtigings fouten optreden bij het recursief wijzigen van Acl's. Start voor runtime-fouten het proces vanaf het begin. Machtigings fouten kunnen optreden als de beveiligingsprincipal niet voldoende machtigingen heeft om de toegangs beheer lijst van een map of bestand te wijzigen dat zich in de Directory-hiërarchie bevindt die wordt gewijzigd. Adresseer het probleem met de machtiging en kies vervolgens het proces hervatten vanaf het moment van de fout met behulp van een vervolg token of start het proces opnieuw vanaf het begin. U hoeft het vervolg token niet te gebruiken als u de voor keur hebt om vanaf het begin opnieuw op te starten. U kunt ACL-vermeldingen zonder negatieve gevolgen opnieuw Toep assen.

Als er een fout optreedt, kunt u een vervolg token retour neren door de `--continue-on-failure` para meter in te stellen op `false` . Nadat u de fouten hebt verhelpen, kunt u het proces hervatten vanaf het moment van de fout door de opdracht opnieuw uit te voeren en vervolgens de `--continuation` para meter in te stellen op het vervolg token.

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure false --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

Als u wilt dat het proces wordt afgebroken door machtigings fouten, kunt u dit opgeven.

Om ervoor te zorgen dat het proces wordt voltooid, stelt u de `--continue-on-failure` para meter in op `true` .

```azurecli
az storage fs access set-recursive --acl "user::rw-,group::r-x,other::---" --continue-on-failure true --continuation xxxxxxx -p my-parent-directory/ -f my-container --account-name mystorageaccount --auth-mode login  
```

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Zie ook

- [Voorbeelden](https://github.com/Azure/azure-cli/blob/dev/src/azure-cli/azure/cli/command_modules/storage/docs/ADLS%20Gen2.md)
- [Feedback geven](https://github.com/Azure/azure-cli-extensions/issues)
- [Bekende problemen](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Access Control model in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Toegangs beheer lijsten (Acl's) in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)