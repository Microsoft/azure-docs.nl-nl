---
title: "Storage Explorer: Acl's instellen in Azure Data Lake Storage Gen2"
description: Gebruik de Azure Storage Explorer voor het beheren van toegangs beheer lijsten (Acl's) in opslag accounts met een hiërarchische naam ruimte (HNS) ingeschakeld.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 3f5bd22619e49246583d8b9fc4e62ad8ab266993
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100654139"
---
# <a name="use-azure-storage-explorer-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Azure Storage Explorer gebruiken voor het beheren van Acl's in Azure Data Lake Storage Gen2

Dit artikel laat u zien hoe u [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) kunt gebruiken voor het beheren van toegangs beheer lijsten (acl's) in opslag accounts met een hiërarchische naam ruimte (HNS) ingeschakeld.

U kunt Storage Explorer gebruiken om de Acl's van mappen en bestanden weer te geven en vervolgens bij te werken. ACL-overname is al beschikbaar voor nieuwe onderliggende items die zijn gemaakt onder een bovenliggende map. Maar u kunt ook ACL-instellingen recursief Toep assen op de bestaande onderliggende items van een bovenliggende map zonder dat u deze wijzigingen afzonderlijk voor elk onderliggend item hoeft aan te brengen. 

In dit artikel wordt beschreven hoe u de ACL van een bestand of map wijzigt en hoe u ACL-instellingen recursief toepast op onderliggende directory's.

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).

- Een opslag account met een hiërarchische naam ruimte (HNS) ingeschakeld. Volg [deze](../common/storage-account-create.md) instructies om er een te maken.

- Azure Storage Explorer geïnstalleerd op de lokale computer. Zie [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) voor meer informatie over het installeren van Azure Storage Explorer voor Windows, Macintosh of Linux.

> [!NOTE]
> Storage Explorer maakt gebruik van de [eind punten](../common/storage-private-endpoints.md#private-endpoints-for-azure-storage) van de BLOB (blob) & data Lake Storage Gen2 (DFS) bij het werken met Azure data Lake Storage Gen2. Als toegang tot Azure Data Lake Storage Gen2 is geconfigureerd met behulp van privé-eind punten, moet u ervoor zorgen dat er twee persoonlijke eind punten worden gemaakt voor het opslag account: een met de subresource van het doel `blob` en de andere met de doel-subresource `dfs` .

## <a name="sign-in-to-storage-explorer"></a>Aanmelden bij Storage Explorer

Wanneer u Azure Storage Explorer de eerste keer start, wordt het venster **Microsoft Azure Storage Explorer - Verbinding maken** weergegeven. Hoewel Storage Explorer verschillende manieren biedt om verbinding te maken met opslagaccounts, wordt momenteel slechts één manier ondersteund voor het beheren van ACL's.

|Taak|Doel|
|---|---|
|Een Azure-account toevoegen | Leidt u naar de aanmeldingspagina van uw organisatie om u te verifiëren bij Azure. Dit is momenteel de enige ondersteunde verificatiemethode als u ACL's wilt beheren en instellen.|
|Een verbindingsreeks of een SAS-URI (Shared Access Signature) gebruiken | Kan worden gebruikt voor rechtstreekse toegang tot een container of opslagaccount met behulp van een SAS-token of een gedeelde verbindingsreeks. |
|De naam en sleutel van een opslagaccount gebruiken| Gebruik de naam en sleutel van uw opslagaccount om verbinding te maken met Azure Storage.|

Selecteer **Een Azure-account toevoegen** en klik op **Aanmelden...** . Volg de instructies op het scherm om u aan te melden bij uw Azure-account.

![Scherm afbeelding met Microsoft Azure Storage Explorer en de optie een Azure-account toevoegen en de knop aanmelden.](media/storage-quickstart-blobs-storage-explorer/connect.png)

Wanneer de verbinding tot stand is gebracht, wordt Azure Storage Explorer geladen en ziet u het tabblad **Explorer**. Deze weergave biedt u inzicht in al uw Azure Storage-accounts, evenals lokale opslag die is geconfigureerd via de [Azure-opslagemulator](../common/storage-use-azurite.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)-accounts of [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)-omgevingen.

![Het venster Microsoft Azure Storage Explorer - Verbinding maken](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="manage-an-acl"></a>Een ACL beheren

Klik met de rechter muisknop op de container, een map of een bestand en klik vervolgens op **Access Control-Lijsten beheren**.  Op de volgende scherm afbeelding ziet u het menu zoals deze wordt weer gegeven wanneer u met de rechter muisknop op een map klikt.

> [!div class="mx-imgBorder"]
> ![Met de rechter muisknop op een map in Azure Storage Explorer](./media/data-lake-storage-explorer-acl/manage-access-control-list-option.png)

In het dialoog venster **toegang beheren** kunt u machtigingen voor de eigenaar en de groep eigen aren beheren. Ook kunt u nieuwe gebruikers en groepen toevoegen aan de toegangsbeheerlijst voor wie u vervolgens machtigingen kunt beheren.

> [!div class="mx-imgBorder"]
> ![Het dialoog venster toegang beheren](./media/data-lake-storage-explorer-acl/manage-access-dialog-box.png)

Selecteer de knop **toevoegen** om een nieuwe gebruiker of groep toe te voegen aan de toegangs beheer lijst. Voer vervolgens het bijbehorende Azure Active Directory (Azure AD) in dat u aan de lijst wilt toevoegen en selecteer vervolgens **toevoegen**.  De gebruiker of groep wordt nu weergegeven in het veld **Gebruikers en groepen:**, zodat u kunt beginnen met het beheren van hun machtigingen.

> [!NOTE]
> Het is een best practice en wordt aanbevolen om een beveiligings groep te maken in azure AD en de machtigingen voor de groep te behouden in plaats van afzonderlijke gebruikers. Zie voor meer informatie over deze aanbeveling en andere aanbevolen procedures [Access Control model in azure data Lake Storage Gen2](data-lake-storage-explorer-acl.md).

Gebruik de besturings elementen van het selectie vakje om de toegangs-en standaard-Acl's in te stellen. Zie [typen acl's](data-lake-storage-access-control.md#types-of-acls)voor meer informatie over het verschil tussen deze typen acl's.

## <a name="apply-acls-recursively"></a>Acl's recursief Toep assen

U kunt ACL-vermeldingen recursief Toep assen op de bestaande onderliggende items van een bovenliggende map zonder dat u deze wijzigingen afzonderlijk voor elk onderliggend item hoeft aan te brengen.

Als u ACL-vermeldingen recursief wilt Toep assen, klikt u met de rechter muisknop op de container of een map en klikt u vervolgens op **Access Control lijsten door geven**.  Op de volgende scherm afbeelding ziet u het menu zoals deze wordt weer gegeven wanneer u met de rechter muisknop op een map klikt.

> [!div class="mx-imgBorder"]
> ![Klik met de rechter muisknop op een map en kies de instelling toegangs beheer door geven](./media/data-lake-storage-explorer-acl/propagate-access-control-list-option.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het Data Lake Storage Gen2-machtigings model.

> [!div class="nextstepaction"]
> [Access Control model in Azure Data Lake Storage Gen2](./data-lake-storage-access-control-model.md)
