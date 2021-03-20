---
title: Azure Storage Explorer gebruiken met Azure Data Lake Storage Gen2
description: Gebruik de Azure Storage Explorer voor het beheren van mappen en toegangs beheer lijsten (ACL) voor opslag accounts met een hiërarchische naam ruimte (HNS) ingeschakeld.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: e6147918e7cd56aed5b5b333a8e9825a34d60fd4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100652272"
---
# <a name="use-azure-storage-explorer-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Azure Storage Explorer gebruiken voor het beheren van mappen en bestanden in Azure Data Lake Storage Gen2

In dit artikel leest u hoe u [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) gebruikt voor het maken en beheren van mappen en bestanden in opslag accounts met een hiërarchische naam ruimte (HNS) ingeschakeld.

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

## <a name="create-a-container"></a>Een container maken

Een container bevat directory's en bestanden. Vouw het opslag account uit dat u in de stap door gaan hebt gemaakt om er een te maken. Selecteer **BLOB-containers**, klik met de rechter muisknop en selecteer **BLOB-container maken**. Voer de naam voor de container in. Zie de sectie [een container maken](storage-quickstart-blobs-dotnet.md#create-a-container) voor een lijst met regels en beperkingen voor het benoemen van containers. Wanneer u klaar bent, drukt u op **Enter** om de container te maken. Zodra de container is gemaakt, wordt deze weer gegeven onder de map **BLOB containers** voor het geselecteerde opslag account.

![Microsoft Azure Storage Explorer-een container maken](media/data-lake-storage-explorer/creating-a-filesystem.png)

## <a name="create-a-directory"></a>Een map maken

Als u een map wilt maken, selecteert u de container die u in de stap door voeren hebt gemaakt. Kies de knop **nieuwe map** in het container lint. Voer de naam in voor uw Directory. Wanneer u klaar bent, drukt u op **Enter** om de map te maken. Zodra de map is gemaakt, wordt deze weer gegeven in het editor-venster.

![Microsoft Azure Storage Explorer-een directory maken](media/data-lake-storage-explorer/creating-a-directory.png)

## <a name="upload-blobs-to-the-directory"></a>Blobs uploaden naar de directory

Klik op het lint op de knop **uploaden** . Met deze bewerking kunt u een map of bestand uploaden.

Kies de bestanden of map die u wilt uploaden.

![Microsoft Azure Storage Explorer - een blob uploaden](media/data-lake-storage-explorer/upload-file.png)

Wanneer u **OK** selecteert, worden de geselecteerde bestanden in een wachtrij geplaatst om te worden geüpload. Elk bestand wordt geüpload. Wanneer het uploaden is voltooid, worden de resultaten weergegeven in het venster **Activiteiten**.

## <a name="view-blobs-in-a-directory"></a>Blobs in een directory weergeven

Selecteer in de toepassing **Azure Storage Explorer** een directory in een opslagaccount. In het hoofdvenster ziet u een lijst met de blobs in de geselecteerde directory.

![Microsoft Azure Storage Explorer - lijst met blobs in een directory](media/data-lake-storage-explorer/list-files.png)

## <a name="download-blobs"></a>Blobs downloaden

Als u bestanden wilt downloaden met behulp van **Azure Storage Explorer**, selecteert u in het lint de optie **down load** , waarbij een bestand is geselecteerd. Er wordt een dialoogvenster geopend waarin u een bestandsnaam kunt invoeren. Selecteer **Opslaan** om het downloaden van een bestand naar de lokale locatie te starten.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het beheren van machtigingen voor bestanden en mappen met behulp van toegangs beheer lijsten (Acl's)

> [!div class="nextstepaction"]
> [Azure Storage Explorer gebruiken voor het beheren van Acl's in Azure Data Lake Storage Gen2](./data-lake-storage-explorer-acl.md)
