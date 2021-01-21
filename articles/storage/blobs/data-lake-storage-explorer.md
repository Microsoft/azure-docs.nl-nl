---
title: Azure Storage Explorer gebruiken met Azure Data Lake Storage Gen2
description: Gebruik de Azure Storage Explorer voor het beheren van mappen en toegangs beheer lijsten (ACL) voor opslag accounts met een hiërarchische naam ruimte (HNS) ingeschakeld.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 07/16/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: a63c309c8e728e3f76ad904d479557b368388954
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98624772"
---
# <a name="use-azure-storage-explorer-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Azure Storage Explorer gebruiken voor het beheren van adreslijsten, bestanden en ACL's in Azure Data Lake Storage Gen2

In dit artikel leest u hoe u [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) gebruikt voor het maken en beheren van mappen, bestanden en machtigingen in opslag accounts met een hiërarchische naam ruimte (HNS) ingeschakeld.

## <a name="prerequisites"></a>Vereisten

> [!div class="checklist"]
> * Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/pricing/free-trial/).
> * Een opslag account met een hiërarchische naam ruimte (HNS) ingeschakeld. Volg [deze](../common/storage-account-create.md) instructies om er een te maken.
> * Azure Storage Explorer geïnstalleerd op de lokale computer. Zie [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) voor meer informatie over het installeren van Azure Storage Explorer voor Windows, Macintosh of Linux.

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

Een container bevat directory's en bestanden. Vouw het opslag account uit dat u in de stap door gaan hebt gemaakt om er een te maken. Selecteer **Blobcontainers**, klik met de rechtermuisknop en selecteer **Blobcontainer maken**. Voer de naam voor de container in. Zie de sectie [een container maken](storage-quickstart-blobs-dotnet.md#create-a-container) voor een lijst met regels en beperkingen voor het benoemen van containers. Wanneer u klaar bent, drukt u op **Enter** om de container te maken. Zodra de container is gemaakt, wordt deze weer gegeven onder de map **BLOB containers** voor het geselecteerde opslag account.

![Microsoft Azure Storage Explorer-een container maken](media/data-lake-storage-explorer/creating-a-filesystem.png)

## <a name="create-a-directory"></a>Een map maken

Als u een map wilt maken, selecteert u de container die u in de stap door voeren hebt gemaakt. Kies de knop **nieuwe map** in het container lint. Voer de naam in voor uw Directory. Wanneer u klaar bent, drukt u op **Enter** om de map te maken. Zodra de map is gemaakt, wordt deze weer gegeven in het editor-venster.

![Microsoft Azure Storage Explorer-een directory maken](media/data-lake-storage-explorer/creating-a-directory.png)

## <a name="upload-blobs-to-the-directory"></a>Blobs uploaden naar de directory

Kies op het lint de knop **uploaden** . Met deze bewerking kunt u een map of bestand uploaden.

Kies de bestanden of map die u wilt uploaden.

![Microsoft Azure Storage Explorer - een blob uploaden](media/data-lake-storage-explorer/upload-file.png)

Wanneer u **OK** selecteert, worden de geselecteerde bestanden in een wachtrij geplaatst om te worden geüpload. Elk bestand wordt geüpload. Wanneer het uploaden is voltooid, worden de resultaten weergegeven in het venster **Activiteiten**.

## <a name="view-blobs-in-a-directory"></a>Blobs in een directory weergeven

Selecteer in de toepassing **Azure Storage Explorer** een directory in een opslagaccount. In het hoofdvenster ziet u een lijst met de blobs in de geselecteerde directory.

![Microsoft Azure Storage Explorer - lijst met blobs in een directory](media/data-lake-storage-explorer/list-files.png)

## <a name="download-blobs"></a>Blobs downloaden

Als u bestanden wilt downloaden met behulp van **Azure Storage Explorer**, selecteert u in het lint de optie **down load** , waarbij een bestand is geselecteerd. Er wordt een dialoogvenster geopend waarin u een bestandsnaam kunt invoeren. Selecteer **Opslaan** om het downloaden van een bestand naar de lokale locatie te starten.

## <a name="managing-access"></a>Toegang beheren

U kunt machtigingen instellen in de hoofdmap van de container. Hiervoor moet u zijn aangemeld bij Azure Storage Explorer met uw eigen account met rechten om dit te doen (in plaats van een connection string). Klik met de rechter muisknop op uw container en selecteer **machtigingen beheren**, het dialoog venster **machtiging beheren** .

![Microsoft Azure Storage Explorer - Toegang tot mappen beheren](media/storage-quickstart-blobs-storage-explorer/manageperms.png)

Via het dialoogvenster **Machtiging beheren** kunt u machtigingen beheren voor de eigenaar en de groep eigenaren. Ook kunt u nieuwe gebruikers en groepen toevoegen aan de toegangsbeheerlijst voor wie u vervolgens machtigingen kunt beheren.

Selecteer het veld **Gebruiker of groep toevoegen** om een nieuwe gebruiker of groep toe te voegen aan de toegangsbeheerlijst.

Voer de bijbehorende AAD-vermelding (Azure Active Directory) in die u aan de lijst wilt toevoegen en selecteer vervolgens **Toevoegen**.

De gebruiker of groep wordt nu weergegeven in het veld **Gebruikers en groepen:**, zodat u kunt beginnen met het beheren van hun machtigingen.

> [!NOTE]
> Het is een best practice en het wordt aanbevolen om een beveiligingsgroep in AAD te maken en machtigingen te onderhouden voor de groep in plaats van afzonderlijke gebruikers. Zie [Best practices voor Data Lake Storage Gen2](data-lake-storage-best-practices.md) voor meer informatie over deze aanbeveling en tevens andere best practices.

Er zijn twee machtigingscategorieën die u kunt toewijzen: Toegangs-ACL’s en Standaard-ACL’s.

* **Toegang**: toegangs-acl's bepalen de toegang tot een object. Bestanden en mappen hebben beide Toegangs-ACL's.

* **Standaard**: een sjabloon met acl's die zijn gekoppeld aan een map die de toegangs-acl's bepaalt voor alle onderliggende items die zijn gemaakt in die map. Bestanden hebben geen Standaard-ACL's.

In beide categorieën zijn er drie machtigingen die u vervolgens kunt toewijzen aan bestanden of mappen: **lezen**, **schrijven** en **uitvoeren**.

>[!NOTE]
> Wanneer u hier selecties maakt, worden machtigingen voor een reeds bestaand item in de map niet ingesteld. U moet naar elk afzonderlijk item gaan en de machtigingen handmatig instellen als het bestand al bestaat.

U kunt machtigingen voor afzonderlijke mappen beheren, evenals de afzonderlijke bestanden waarmee voor u een gedetailleerd toegangsbeheer mogelijk wordt. Het proces voor het beheren van machtigingen voor zowel mappen als bestanden is hetzelfde als hierboven beschreven. Klik met de rechtermuisknop op het bestand of map waarvoor u machtigingen wilt beheren en volg hetzelfde proces.

## <a name="private-endpoints-in-azure-data-lake-storage-gen2"></a>Privé-eind punten in Azure Data Lake Storage Gen2

Storage Explorer maakt gebruik van de [eind punten](../common/storage-private-endpoints.md#private-endpoints-for-azure-storage) van de BLOB (blob) & data Lake Storage Gen2 (DFS) bij het werken met Azure data Lake Storage Gen2. Als toegang tot Azure Data Lake Storage Gen2 is geconfigureerd met behulp van privé-eind punten, moet u ervoor zorgen dat er twee persoonlijke eind punten worden gemaakt voor het opslag account: een met de subresource van het doel `blob` en de andere met de doel-subresource `dfs` .

## <a name="next-steps"></a>Volgende stappen

Meer informatie over toegangs beheer lijsten in Data Lake Storage Gen2.

> [!div class="nextstepaction"]
> [Toegangsbeheer in Data Lake Storage Gen2](./data-lake-storage-access-control.md)
