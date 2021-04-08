---
title: Snelstart voor het beheren van Azure-bestandsshares met de Azure-portal
description: Ontdek hoe u Azure-bestandsshares maakt en beheert in Azure Portal. Maak een opslagaccount, maak een Azure-bestandsshare en gebruik uw Azure-bestandsshare.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/18/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6a88124397812f7599ce54b46b23d22e626cf520
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94629815"
---
# <a name="quickstart-create-and-manage-azure-file-shares-with-the-azure-portal"></a>Snelstart: Azure-bestandsshares maken en beheren met de Azure-portal 
[Azure Files ](storage-files-introduction.md) is het eenvoudig te gebruiken cloudbestandssysteem van Microsoft. Azure-bestandsshares kunnen worden gekoppeld in Windows, Linux en macOS. In deze handleiding worden de basisbeginselen besproken van het werken met Azure-bestandsshares met behulp van [Azure Portal](https://portal.azure.com/).

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-a-storage-account"></a>Een opslagaccount maken
[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="create-an-azure-file-share"></a>Een Azure-bestandsshare maken
Een Azure-bestandsshare maken:

1. Selecteer het opslagaccount in uw dashboard.
2. Ga op de pagina van het opslagaccount naar het gedeelte **Services** en selecteer **Bestanden**.
    ![Een schermopname van het gedeelte Services van het opslagaccount, met de service Bestanden geselecteerd](media/storage-how-to-use-files-portal/create-file-share-1.png)

3. Klik in het menu bovenaan de pagina **Bestandsservice** op **Bestandsshare**. De pagina **Nieuwe bestandsshare** wordt weergegeven.
4. Typ *myshare* in het vak **Naam**.
5. Klik op **OK** om de Azure-bestandsshare te maken.

De namen van shares moeten bestaan uit kleine letters, cijfers en afbreekstreepjes, maar mogen niet beginnen met een afbreekstreepje. Zie [Shares, mappen, bestanden en metagegevens een naam geven en hiernaar verwijzen](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata) voor meer informatie over de naamgeving van bestandsshares en bestanden.

## <a name="use-your-azure-file-share"></a>Azure-bestandsshare gebruiken
Azure Files biedt drie methoden voor het werken met bestanden en mappen in uw Azure-bestandsshare: het [SMB-protocol (Server Message Block)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) volgens de industriestandaard, (de preview van) het NFS-protocol (Network File System) en het [File REST-protocol](/rest/api/storageservices/file-service-rest-api). 

Zie het volgende document op basis van het besturingssysteem om een bestandsshare met SMB te koppelen:
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [MacOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-from-the-azure-portal"></a>Een Azure-bestandsshare maken vanuit de Azure-portal
Alle aanvragen via Azure Portal worden gedaan via de REST-API van File, zodat u bestanden en mappen kunt maken, wijzigen en verwijderen in clients zonder toegang tot SMB. Het is mogelijk om rechtstreeks met het File REST-protocol te werken (dat wil zeggen HTTP REST-aanroepen zelf te maken). De meest voorkomende manier om het File REST-protocol te gebruiken (naast het gebruik van Azure Portal), is echter met de [Azure PowerShell-module](storage-how-to-use-files-powershell.md), de [Azure CLI](storage-how-to-use-files-cli.md) of een Azure Storage-SDK, die allemaal een mooie wrapper rond het File REST-protocol bieden in de script-/programmeertaal van uw keuze. 

De meeste gebruikers van Azure Files willen naar verwachting met hun Azure-bestandsshares werken via het SMB-protocol, omdat dit hen de mogelijkheid biedt om de bestaande toepassingen en hulpprogramma's te gebruiken die ze verwachten te kunnen gebruiken, maar er zijn diverse redenen waarom het gebruik van de File REST API in plaats van SMB voordelen oplevert, zoals:

- U wilt een snelle wijziging in de Azure-bestandsshare aanbrengen vanaf een apparaat terwijl u onderweg bent, zoals vanaf een laptop zonder SMB-toegang, een tablet of een mobiel apparaat.
- U wilt een script of toepassing uitvoeren vanaf een client die geen SMB-share kan koppelen, zoals on-premises clients waarvoor poort 445 niet is gedeblokkeerd.
- U profiteert van serverloze resources, zoals [Azure Functions](../../azure-functions/functions-overview.md). 

De volgende voorbeelden laten zien hoe u de Azure-portal gebruikt voor het bewerken van een Azure-bestandsshare met het File REST-protocol. 

U beschikt nu over een Azure-bestandsshare. De volgende stap is het koppelen van de bestandsshare met SMB op [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) of [macOS](storage-how-to-use-files-mac.md). U kunt ook met Azure Portal werken met uw Azure-bestandsshare. 

#### <a name="create-a-directory"></a>Een map maken
Ga als volgt te werk om in de hoofdmap van uw Azure-bestandsshare een nieuwe map te maken met de naam *myDirectory*:

1. Selecteer op de pagina **Bestandsservice** de bestandsshare **myshare**. De pagina voor de bestandsshare wordt geopend.
2. Selecteer **+ Map toevoegen** in het menu bovenaan de pagina. De pagina **Nieuwe map** wordt weergegeven.
3. Typ *myDirectory* en klik op **OK**.

#### <a name="upload-a-file"></a>Bestand uploaden 
Als u wilt uitproberen hoe het uploaden van een bestand in zijn werk gaat, moet u eerst een bestand maken of selecteren om te uploaden. De manier waarop u dit doet, kunt u helemaal zelf bepalen. Ga als volgt te werk wanneer u het bestand hebt geselecteerd dat u wilt uploaden:

1. Klik op de map **myDirectory**. Het deelvenster **myDirectory** wordt geopend.
2. Klik in het menu bovenaan op **Uploaden**. Het deelvenster **Bestanden uploaden** wordt geopend.  
    ![Een schermopname van het deelvenster Bestanden uploaden](media/storage-how-to-use-files-portal/upload-file-1.png)

3. Klik op het mappictogram om een venster te openen waarin u door uw lokale bestanden kunt bladeren. 
4. Selecteer een bestand en klik op **Openen**. 
5. Controleer de bestandsnaam op de pagina **Bestanden uploaden** en klik vervolgens op **Uploaden**.
6. Als het uploaden is voltooid, ziet u het bestand in de lijst op de pagina **myDirectory**.

#### <a name="download-a-file"></a>Bestand downloaden
U kunt een kopie downloaden van het bestand dat u hebt geüpload, door met de rechtermuisknop op het bestand te klikken. Als u vervolgens Downloaden hebt geselecteerd, verschilt de rest van de procedure afhankelijk van het besturingssysteem en browser die u gebruikt.

## <a name="clean-up-resources"></a>Resources opschonen
[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Wat is Azure Files?](storage-files-introduction.md)