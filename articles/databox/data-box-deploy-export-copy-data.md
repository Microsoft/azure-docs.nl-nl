---
title: Zelfstudie voor het kopiëren van gegevens via SMB van uw Azure Data Box | Microsoft Docs
description: Leer hoe u gegevens kopieert naar uw Azure Data Box via SMB
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 495b4427fb7e456c60a489b9ce3d19c2a44bd918
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680890"
---
# <a name="tutorial-copy-data-from-azure-data-box-via-smb"></a>Zelfstudie: Gegevens kopiëren uit Azure Data Box via SMB

In deze zelfstudie wordt beschreven hoe u verbinding maakt en gegevens kopieert vanaf uw Data Box naar een on-premises server met de lokale webinterface. Het Data Box-apparaat bevat gegevens die zijn geëxporteerd uit uw Azure Storage-account.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Vereisten
> * Verbinding maken met Data Box
> * Gegevens kopiëren uit Data Box

## <a name="prerequisites"></a>Vereisten

Zorg voordat u begint voor het volgende:

1. U hebt het order voor Azure Data Box geplaatst.
    - Bekijk voor een importorder de [Zelfstudie: Azure Data Box bestellen](data-box-deploy-ordered.md) voltooid.
    - Bekijk voor een exportorder de [Zelfstudie: Azure Data Box bestellen](data-box-deploy-export-ordered.md) voltooid.
2. U hebt uw Data Box ontvangen en de bestelstatus in de portal is **Geleverd**.
3. U hebt een hostcomputer waarnaar u de gegevens uit uw Data Box wilt kopiëren. Op uw hostcomputer moet
   * Een [ondersteund besturingssysteem](data-box-system-requirements.md) worden uitgevoerd.
   * Verbonden zijn met een netwerk met hoge snelheid. Het wordt aangeraden dat u beschikt over minstens één 10-GbE-verbinding. Als er geen 10 GbE-verbinding beschikbaar is, kan een 1 GbE-gegevenskoppeling worden gebruikt, maar dit heeft wel gevolgen voor de kopieersnelheid.

## <a name="connect-to-data-box"></a>Verbinding maken met Data Box

[!INCLUDE [data-box-shares](../../includes/data-box-shares.md)]

Als u een hostcomputer met Windows Server gebruikt, voert u deze stappen uit om verbinding met de Data Box te maken.

1. U moet eerst een verificatie uitvoeren en een sessie starten. Ga naar **Verbinding maken en kopiëren**. Selecteer **Referenties ophalen** om de toegangsreferenties te krijgen voor de shares die aan uw opslagaccount zijn gekoppeld. 

    ![Sharereferenties ophalen](media/data-box-deploy-export-copy-data/get-share-credentials-1.png)

2. Kopieer in het dialoogvenster Verbinding maken met share en gegevens kopiëren de **Gebruikersnaam** en het **Wachtwoord** voor de share. Selecteer **OK**.
    
    ![Referenties voor delen ophalen, toegang delen en gegevens kopiëren](media/data-box-deploy-export-copy-data/get-share-credentials-2.png)

3. Om vanaf uw hostcomputer toegang te krijgen tot de shares die zijn gekoppeld aan uw opslagaccount (*exportbvtdataset2* in het volgende voorbeeld), opent u een opdrachtvenster. Typ in de opdrachtprompt:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Afhankelijk van de gegevensindeling, zijn dit de paden voor de shares:
    - Azure-blok-blob - `\\169.254.143.85\exportbvtdataset2_BlockBlob`
    - Azure-pagina-blob - `\\169.254.143.85\exportbvtdataset2_PageBlob`
    - Azure Files - `\\169.254.143.85\exportbvtdataset2_AzFile`

4. Voer het wachtwoord voor de share in wanneer er om wordt gevraagd. Het volgende voorbeeld laat zien hoe u verbinding met een share kunt maken via de voorgaande opdracht.

    ```
    C:\Users\Databoxuser>net use \\169.254.143.85\exportbvtdataset2_BlockBlob /u:exportbvtdataset2
    Enter the password for 'exportbvtdataset2' to connect to '169.254.143.85':
    The command completed successfully.
    ```

5. Druk op Windows-toets+R. Geef in het venster **Uitvoeren** het `\\<device IP address>` op. Selecteer **OK** om Verkenner te openen.
    
    ![Verbinding maken met share via Verkenner, apparaat-id invoeren](media/data-box-deploy-export-copy-data/connect-shares-file-explorer-1.png)

    Als het goed is, worden de shares nu weergegeven als mappen.
    
    ![Verbinding maken met share via Verkenner, shares weergeven](media/data-box-deploy-export-copy-data/connect-shares-file-explorer-2.png)

    
Als u een Linux-client gebruikt, gebruikt u de volgende opdracht om de SMB-share koppelen. De parameter "vers" hieronder is de versie van SMB die ondersteuning biedt voor uw Linux-host. Geef de juiste versie op in de onderstaande opdracht. Zie [Ondersteunde bestandssystemen voor Linux-clients](./data-box-system-requirements.md#supported-file-transfer-protocols-for-clients) voor versies van SMB die door Data Box worden ondersteund 

```console
sudo mount -t nfs -o vers=2.1 169.254.143.85:/exportbvtdataset2_BlockBlob /home/databoxubuntuhost/databox
```

## <a name="copy-data-from-data-box"></a>Gegevens kopiëren uit Data Box

Nadat u verbinding met de Data Box-shares hebt gemaakt, moet u de gegevens kopiëren.

[!INCLUDE [data-box-export-review-logs](../../includes/data-box-export-review-logs.md)]


 Begin met het kopiëren van gegevens nadat u verbinding met de SMB-share hebt gemaakt. U kunt elk programma voor het kopiëren van bestanden dat compatibel is met SMB, zoals Robocopy, gebruiken om de gegevens te kopiëren. Er kunnen meerdere kopieertaken worden gestart met Robocopy. 


Ga voor meer informatie over opdrachten voor Robocopy naar [Robocopy en een paar voorbeelden](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx).

Ga naar het **Dashboard** en verifieer de gebruikte ruimte en vrije ruimte op uw apparaat na het kopiëren.

U kunt uw Data Box nu naar Microsoft verzenden.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie bent u meer te weten gekomen over verschillende onderwerpen met betrekking tot Azure Data Box, zoals:

> [!div class="checklist"]
>
> * Vereisten
> * Verbinding maken met Data Box
> * Gegevens kopiëren uit Data Box

Ga naar de volgende zelfstudie om te lezen hoe u uw Data Box naar Microsoft verzendt.

> [!div class="nextstepaction"]
> [Uw Azure Data Box verzenden naar Microsoft](./data-box-deploy-export-picked-up.md)