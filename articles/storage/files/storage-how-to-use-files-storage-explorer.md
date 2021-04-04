---
title: Azure-bestandsshares beheren met Azure Storage Explorer
description: In deze snelstart leert u hoe Azure Storage Explorer gebruikt om Azure-bestandsshares te beheren.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/18/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: dea88bf93000422ce1d832be4a0f60474ff27938
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94629781"
---
# <a name="quickstart-create-and-manage-azure-file-shares-with-azure-storage-explorer"></a>Snelstart: Azure-bestandsshares maken en beheren met Azure Storage Explorer
In deze handleiding worden de basisbeginselen besproken van het werken met [Azure-bestandsshares](storage-files-introduction.md) met behulp van Azure Storage Explorer. Azure-bestandsshares zijn net als andere bestandsshares, maar worden in de cloud opgeslagen en ondersteund op het Azure-platform. Azure-bestandsshares ondersteunen het SMB-protocol volgens de industriestandaard en bieden de mogelijkheid bestanden te delen tussen meerdere computers, toepassingen en exemplaren. 

Azure Storage Explorer is een populair clienthulpprogramma dat beschikbaar is voor Windows, macOS en Linux. Met Azure Storage Explorer kunt u Azure-bestandsshares en andere opslagresources beheren.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten
Voor deze snelstartgids moet Azure Storage Explorer zijn geïnstalleerd. Ga naar [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) om het te downloaden en te installeren.

## <a name="create-a-storage-account"></a>Create a storage account
U kunt Azure Storage Explorer niet gebruiken voor het maken van nieuwe resources. Voor deze demo maakt u het opslagaccount in [Azure Portal](https://portal.azure.com/). 

[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="connect-storage-explorer-to-azure-resources"></a>Azure Storage Explorer verbinden met Azure-resources
Wanneer u Azure Storage Explorer de eerste keer start, wordt het venster **Microsoft Azure Storage Explorer - Verbinding maken** weergegeven. Azure Storage Explorer biedt verschillende manieren om verbinding te maken met opslagaccounts: 

- **Aanmelden met uw Azure-account**: u kunt u aanmelden met behulp van uw gebruikersreferenties voor uw organisatie of uw Microsoft-account. 
- **Verbinding maken met een specifiek opslagaccount met behulp van een verbindingsreeks of SAS-token**: een verbindingsreeks is een speciale tekenreeks die een opslagaccountnaam en -sleutel/SAS-token bevat. Het token geeft Azure Storage Explorer rechtstreeks toegang tot het opslagaccount (in plaats van dat gewoon alle opslagaccounts in een Azure-account worden weergegeven). Zie [Azure Storage-verbindingsreeksen configureren](../common/storage-configure-connection-string.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) voor meer informatie over verbindingsreeksen.
- **Verbinding maken met een specifiek opslagaccount met behulp van de naam en sleutel van het opslagaccount**: gebruik de naam en de sleutel van het opslagaccount om verbinding te maken met Azure-opslag.

Voor deze snelstartgids meldt u zich aan met uw Azure-account. Selecteer **Een Azure-account toevoegen** en selecteer **Aanmelden**. Volg de aanwijzingen om u aan te melden bij uw Azure-account.

![Een schermafbeelding van Microsoft Azure Storage Explorer - venster Verbinding maken](./media/storage-how-to-use-files-storage-explorer/connect-to-azure-storage-1.png)

### <a name="create-a-file-share"></a>Een bestandsshare maken
Ga als volgt te werk om uw eerste Azure-bestandsshare te maken in het `storageacct<random number>`-opslagaccount:

1. Vouw het opslagaccount uit dat u hebt gemaakt.
2. Klik met de rechtermuisknop op **Bestandsshares** en selecteer **Bestandsshare maken**.  
    ![Een schermopname van de map File Shares en het contextmenu](media/storage-how-to-use-files-storage-explorer/create-file-share-1.png)

3. Voer voor de bestandsshare *myshare* in en druk op Enter.

De namen van shares mogen alleen kleine letters, cijfers en enkele afbreekstreepjes bevatten, maar mogen niet met een afbreekstreepje beginnen. Zie [Naming and referencing shares, directories, files, and metadata](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata) (Shares, mappen, bestanden en metagegevens een naam geven en hiernaar verwijzen) voor meer informatie over de naamgeving van bestandsshares en bestanden.

Nadat de bestandsshare is gemaakt, wordt er in het rechterdeelvenster een tabblad voor de bestandsshare geopend. 

## <a name="use-your-azure-file-share"></a>Azure-bestandsshare gebruiken
U beschikt nu over een Azure-bestandsshare. De volgende stap is het koppelen van de bestandsshare met SMB op [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) of [macOS](storage-how-to-use-files-mac.md). U kunt ook Azure Storage Explorer gebruiken om met de Azure-bestandsshare te werken. Het voordeel van het gebruik van Azure Storage Explorer ten opzichte van het koppelen van de bestandsshare met behulp van SMB is dat alle aanvragen die zijn ingediend via Azure Storage Explorer, worden gedaan met behulp van de REST API van het bestand. U kunt de REST API van het bestand gebruiken om bestanden en mappen te maken, te wijzigen en te verwijderen op clients die niet over SMB beschikken.

### <a name="create-a-directory"></a>Een map maken
Het toevoegen van een map biedt een hiërarchische structuur voor het beheren van de bestandsshare. U kunt meerdere niveaus maken in uw map. U moet er echter voor zorgen dat de bovenliggende mappen bestaan voordat u submappen maakt. Voor het pad myDirectory/mySubDirectory moet u bijvoorbeeld eerst de map *myDirectory* maken. Daarna kunt u *mySubDirectory* maken. 

1. Ga naar het tabblad voor de bestandsshare en selecteer in het menu bovenaan de knop **Nieuwe map**. Het deelvenster **Nieuwe map maken** wordt geopend.
    ![Een schermopname van de knop Nieuwe map in context](media/storage-how-to-use-files-storage-explorer/create-directory-1.png)

2. Voer voor de naam van de map *myDirectory* in en selecteer **OK**. 

De map *myDirectory* wordt vermeld op het tabblad voor de bestandsshare *myshare*.

### <a name="upload-a-file"></a>Bestand uploaden 
U kunt een bestand van uw lokale computer uploaden naar de nieuwe map in de bestandsshare. U kunt een hele map uploaden of één bestand.

1. Selecteer **Uploaden** in het bovenste menu. U kunt nu een map of bestand uploaden.
2. Selecteer **Bestand uploaden** en selecteer het bestand dat u van uw lokale computer wilt uploaden.
3. Voer *myDirectory* in bij **Uploaden naar een map** en selecteer **Uploaden**. 

Wanneer u klaar bent, wordt het bestand weergegeven in de lijst in het deelvenster *myDirectory*.

### <a name="download-a-file"></a>Een bestand downloaden
Als u een kopie van een bestand vanuit uw bestandsshare wilt downloaden, klikt u met de rechtermuisknop op het bestand en selecteert u **Downloaden**. Kies waar u het bestand op uw lokale computer wilt opslaan en selecteer **Opslaan**.

De voortgang van de download wordt weergegeven in het deelvenster **Activiteiten** onderaan het venster.

## <a name="clean-up-resources"></a>Resources opschonen
U kunt Azure Storage Explorer niet gebruiken voor het verwijderen van resources. U kunt [Azure Portal](https://portal.azure.com/) gebruiken als u wilt opschonen vanuit deze snelstartgids. 

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Wat is Azure Files?](storage-files-introduction.md)