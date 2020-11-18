---
title: Zelfstudie voor het kopiëren van gegevens via SMB in Azure Data Box | Microsoft Docs
description: In deze zelfstudie leert u hoe u verbinding kunt maken met en gegevens kunt kopiëren van uw hostcomputer naar de Azure Data Box door gebruik te maken van SMB met de lokale webinterface.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 11/11/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: c68f76e56f49f055466f7332d7751ac468e034d8
ms.sourcegitcommit: 9706bee6962f673f14c2dc9366fde59012549649
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94616736"
---
::: zone target="docs"

# <a name="tutorial-copy-data-to-azure-data-box-via-smb"></a>Zelfstudie: Gegevens naar Azure Data Box Disk kopiëren via SMB

::: zone-end

::: zone target="chromeless"

## <a name="copy-data-to-azure-data-box"></a>Gegevens naar Azure Data Box kopiëren

::: zone-end

::: zone target="docs"

Deze zelfstudie beschrijft hoe u verbinding kunt maken en gegevens kunt kopiëren vanaf uw hostcomputer via de lokale gebruikersinterface.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Vereisten
> * Verbinding maken met Data Box
> * Gegevens kopiëren naar Data Box

## <a name="prerequisites"></a>Vereisten

Zorg voordat u begint voor het volgende:

1. U hebt de zelfstudie [ Azure Data Box instellen](data-box-deploy-set-up.md) voltooid.
2. U hebt uw Data Box ontvangen en de bestelstatus in de portal is **Geleverd**.
3. U hebt een hostcomputer waarop de gegevens staan die u naar de Data Box wilt kopiëren. Op uw hostcomputer moet
   * Een [ondersteund besturingssysteem](data-box-system-requirements.md) worden uitgevoerd.
   * Verbonden zijn met een netwerk met hoge snelheid. Het wordt aangeraden dat u beschikt over minstens één 10-GbE-verbinding. Als er geen 10 GbE-verbinding beschikbaar is, kan een 1 GbE-gegevenskoppeling worden gebruikt, maar dit heeft wel gevolgen voor de kopieersnelheid.

## <a name="connect-to-data-box"></a>Verbinding maken met Data Box

Data Box maakt op basis van het geselecteerde opslagaccount maximaal:

* Drie shares voor elk gekoppeld opslagaccount voor GPv1 en GPv2.
* Eén share voor premium opslag.
* Eén share voor een blob-opslagaccount.

Onder blok-blob- en pagina-blob-shares zijn entiteiten op het eerste niveau containers en entiteiten op het tweede niveau blobs. Onder shares voor Azure Files zijn entiteiten op het eerste niveau shares en entiteiten op het tweede niveau bestanden.

In de volgende tabel ziet u het UNC-pad naar de shares op uw Data Box en de URL van het Azure Storage-pad waarnaar de gegevens worden geüpload. De uiteindelijke URL van het Azure Storage-pad kan worden afgeleid van het UNC-pad naar de shares.
 
|Azure-opslagtypen  | Data Box-shares            |
|-------------------|--------------------------------------------------------------------------------|
| Azure-blok-blobs | <li>UNC-pad naar shares: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>Azure Storage-URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure-pagina-blobs  | <li>UNC-pad naar shares: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>Azure Storage-URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>UNC-pad naar shares: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>Azure Storage-URL: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

Als u een hostcomputer met Windows Server gebruikt, voert u deze stappen uit om verbinding met de Data Box te maken.

1. U moet eerst een verificatie uitvoeren en een sessie starten. Ga naar **Verbinding maken en kopiëren**. Selecteer **SMB** om de toegangsreferenties op te halen voor de shares die aan uw opslagaccount zijn gekoppeld. 

    ![Toegangsreferenties voor SMB-shares ophalen](media/data-box-deploy-copy-data/get-share-credentials1.png)

2. Kopieer in het dialoogvenster Verbinding maken met share en gegevens kopiëren de **Gebruikersnaam** en het **Wachtwoord** voor de share. Selecteer vervolgens **OK**.
    
    ![Gebruikersnaam en wachtwoord voor een share ophalen](media/data-box-deploy-copy-data/get-share-credentials2.png)

3. Om vanaf uw hostcomputer toegang te krijgen tot de shares die zijn gekoppeld aan uw opslagaccount (*utsac1* in het volgende voorbeeld), opent u een opdrachtvenster. Typ in de opdrachtprompt:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Afhankelijk van de gegevensindeling, zijn dit de paden voor de shares:
    - Azure-blok-blob - `\\10.126.76.138\utSAC1_202006051000_BlockBlob`
    - Azure-pagina-blob - `\\10.126.76.138\utSAC1_202006051000_PageBlob`
    - Azure Files - `\\10.126.76.138\utSAC1_202006051000_AzFile`

4. Voer het wachtwoord voor de share in wanneer er om wordt gevraagd. Als het wachtwoord speciale tekens bevat, plaatst u het tussen dubbele aanhalingstekens. Het volgende voorbeeld laat zien hoe u verbinding met een share kunt maken via de voorgaande opdracht.

    ```
    C:\Users\Databoxuser>net use \\10.126.76.138\utSAC1_202006051000_BlockBlob /u:testuser1
    Enter the password for 'testuser1' to connect to '10.126.76.138': "ab1c2def$3g45%6h7i&j8kl9012345"
    The command completed successfully.
    ```

4. Druk op Windows-toets+R. Geef in het venster **Uitvoeren** het `\\<device IP address>` op. Selecteer **OK** om Verkenner te openen.
    
    ![Verbinding maken met share via Verkenner](media/data-box-deploy-copy-data/connect-shares-file-explorer1.png)

    Als het goed is, worden de shares nu weergegeven als mappen.
    
    ![Shares weergegeven in Verkenner](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png)

    **Maak altijd een map voor de bestanden die u van plan bent te kopiëren in de bestandsshare en kopieer de bestanden vervolgens naar die map**. De map gemaakt onder shares met blok-blobs en pagina-blobs vertegenwoordigt een container waarnaar gegevens als blobs worden geüpload. Het is niet mogelijk om bestanden rechtstreeks te kopiëren naar de *root*-map in het opslagaccount.
    
Als u een Linux-client gebruikt, gebruikt u de volgende opdracht om de SMB-share koppelen. De parameter "vers" hieronder is de versie van SMB die ondersteuning biedt voor uw Linux-host. Geef de juiste versie op in de onderstaande opdracht. Zie [Ondersteunde bestandssystemen voor Linux-clients](./data-box-system-requirements.md#supported-file-transfer-protocols-for-clients) voor versies van SMB die door Data Box worden ondersteund 

```console
sudo mount -t nfs -o vers=2.1 10.126.76.138:/utSAC1_202006051000_BlockBlob /home/databoxubuntuhost/databox
```

## <a name="copy-data-to-data-box"></a>Gegevens kopiëren naar Data Box

Nadat u verbinding met de Data Box-shares hebt gemaakt, moet u de gegevens kopiëren. Neem de volgende punten door voordat u gegevens gaat kopiëren:

* Zorg dat u de gegevens kopieert naar shares die overeenkomen met de juiste gegevensindeling. U moet bijvoorbeeld de blok-blobgegevens naar de share voor blok-blobs kopiëren. Kopieer de VHD's naar pagina-blob. Als de gegevensindeling niet overeenkomt met het betreffende sharetype, zal het uploaden van gegevens naar Azure op een later tijdstip mislukken.
* Maak altijd een map onder de bestandsshare voor de bestanden die u van plan bent te kopiëren en kopieer de bestanden vervolgens naar die map. De map gemaakt onder shares met blok-blobs en pagina-blobs vertegenwoordigt een container waarnaar gegevens als blobs worden geüpload. U kunt bestanden niet rechtstreeks kopiëren naar de *hoofdmap* in het opslagaccount.
* Zorg er tijdens het kopiëren van gegevens voor dat de gegevensgrootte voldoet aan de limieten die worden vermeld in de [limieten voor Azure-opslagaccounts](data-box-limits.md#azure-storage-account-size-limits).
* Als u metagegevens (ACL's, tijdstempels en bestandskenmerken) wilt behouden bij het overdragen van gegevens naar Azure Files, volgt u de instructies in [ACL's, kenmerken en tijdstempels van bestanden behouden met Azure Data Box](data-box-file-acls-preservation.md)  
* Als de gegevens die door Data Box worden geüpload gelijktijdig door andere toepassingen buiten Data Box worden geüpload, kan dit tot fouten voor de uploadtaak en beschadigde gegevens leiden.
* We raden aan dat:
  * U niet zowel SMB als NFS tegelijkertijd gebruikt.
  * Dezelfde gegevens naar dezelfde eindbestemming kopieert in Azure.
  In dergelijke gevallen kan de definitieve uitkomst namelijk niet worden vastgesteld.

> [!IMPORTANT]
> Zorg dat u een kopie van de brongegevens in uw on-premises omgeving hebt totdat u kunt bevestigen dat de gegevens met Data Box naar Azure Storage zijn overgebracht.

Begin met het kopiëren van gegevens nadat u verbinding met de SMB-share hebt gemaakt. U kunt elk programma voor het kopiëren van bestanden dat compatibel is met SMB, zoals Robocopy, gebruiken om de gegevens te kopiëren. Er kunnen meerdere kopieertaken worden gestart met Robocopy. Gebruik de volgende opdracht:

```console
robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile>
```

De kenmerken worden in de volgende tabel beschreven.
    
|Kenmerk  |Beschrijving  |
|---------|---------|
|/e     |Hiermee kopieert u submappen, met inbegrip van lege mappen.         |
|/r:     |Hiermee geeft u het aantal nieuwe pogingen bij mislukte kopieerbewerkingen op.         |
|/w:     |Hiermee geeft u de wachttijd tussen nieuwe pogingen op in seconden.         |
|/is     |Neemt dezelfde bestanden op.         |
|/nfl     |Hiermee geeft u op dat bestandsnamen niet moeten worden vastgelegd.         |
|/ndl    |Hiermee geeft u op dat mapnamen niet moeten worden vastgelegd.        |
|/np     |Geeft op dat de voortgang van de kopieerbewerking (het aantal bestanden of mappen dat tot nu toe is gekopieerd) niet zal worden weergegeven. Als de voortgang wordt weergegeven, zullen de prestaties aanzienlijk verminderen.         |
|/MT     | Gebruik multithreading, 32 of 64-threads wordt aanbevolen. Deze optie wordt niet gebruikt voor versleutelde bestanden. U moet versleutelde en niet-versleutelde bestanden mogelijk scheiden. Kopiëren met één thread verlaagt de prestaties echter aanzienlijk.           |
|/fft     | Gebruik deze optie om de granulariteit van tijdstempels voor een bestandssysteem te verminderen.        |
|/b     | Kopieert bestanden in de back-upmodus.        |
|/z    | Kopieert bestanden in de modus voor opnieuw opstarten; gebruik deze optie als de omgeving instabiel is. Deze optie beperkt doorvoer vanwege de aanvullende logboekregistratie.      |
| /zb     | Gebruikt de modus voor opnieuw opstarten. Deze optie gebruikt de back-upmodus als de toegang is geweigerd. Deze optie beperkt doorvoer vanwege controlepunten.         |
|/efsraw     | Kopieert alle versleutelde bestanden in de onbewerkte EFS-modus. Alleen gebruiken met versleutelde bestanden.         |
|log+:\<LogFile>| Voegt de uitvoer toe aan het bestaande logboekbestand.|    
 
In het volgende voorbeeld ziet u de uitvoer van de Robocopy-opdracht voor het kopiëren van bestanden naar de Data Box.

```output
C:\Users>robocopy

    -------------------------------------------------------------------------------
    ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------

        Started : Thursday, March 8, 2018 2:34:53 PM
        Simple Usage :: ROBOCOPY source destination /MIR

        source :: Source Directory (drive:\path or \\server\share\path).
        destination :: Destination Dir  (drive:\path or \\server\share\path).
                /MIR :: Mirror a complete directory tree.

    For more usage information run ROBOCOPY /?

    ****  /MIR can DELETE files as well as copy them !

C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:32

    -------------------------------------------------------------------------------
    ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------

        Started : Thursday, March 8, 2018 2:34:58 PM
        Source : C:\Git\azure-docs-pr\contributor-guide\
            Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\

        Files : *.*

        Options : *.* /DCOPY:DA /COPY:DAT /MT:32 /R:5 /W:60

    ------------------------------------------------------------------------------

    100%        New File                 206        C:\Git\azure-docs-pr\contributor-guide\article-metadata.md
    100%        New File                 209        C:\Git\azure-docs-pr\contributor-guide\content-channel-guidance.md
    100%        New File                 732        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-index.md
    100%        New File                 199        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pr-criteria.md
                New File                 178        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-co100%  .md
                New File                 250        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-et100%  e.md
    100%        New File                 174        C:\Git\azure-docs-pr\contributor-guide\create-images-markdown.md
    100%        New File                 197        C:\Git\azure-docs-pr\contributor-guide\create-links-markdown.md
    100%        New File                 184        C:\Git\azure-docs-pr\contributor-guide\create-tables-markdown.md
    100%        New File                 208        C:\Git\azure-docs-pr\contributor-guide\custom-markdown-extensions.md
    100%        New File                 210        C:\Git\azure-docs-pr\contributor-guide\file-names-and-locations.md
    100%        New File                 234        C:\Git\azure-docs-pr\contributor-guide\git-commands-for-master.md
    100%        New File                 186        C:\Git\azure-docs-pr\contributor-guide\release-branches.md
    100%        New File                 240        C:\Git\azure-docs-pr\contributor-guide\retire-or-rename-an-article.md
    100%        New File                 215        C:\Git\azure-docs-pr\contributor-guide\style-and-voice.md
    100%        New File                 212        C:\Git\azure-docs-pr\contributor-guide\syntax-highlighting-markdown.md
    100%        New File                 207        C:\Git\azure-docs-pr\contributor-guide\tools-and-setup.md
    ------------------------------------------------------------------------------

                Total    Copied   Skipped  Mismatch    FAILED    Extras
    Dirs :         1         1         1         0         0         0
    Files :        17        17         0         0         0         0
    Bytes :     3.9 k     3.9 k         0         0         0         0
C:\Users>
```

Gebruik de volgende parameters in Robocopy om de prestaties te optimaliseren als u de gegevens kopieert.

|    Platform    |    Voornamelijk kleine bestanden van < 512 KB                           |    Voornamelijk middelgrote bestanden van 512 KB - 1 MB                      |    Voornamelijk grote bestanden van > 1 MB                             |   
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|
|    Data Box         |    2 Robocopy-sessies <br> 16 threads per sessie    |    3 Robocopy-sessies <br> 16 threads per sessie    |    2 Robocopy-sessies <br> 24 threads per sessie    |

Ga voor meer informatie over opdrachten voor Robocopy naar [Robocopy en een paar voorbeelden](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx).

Als er fouten zijn tijdens het kopieerproces, krijgt u een melding.

![Een melding over een fout tijdens het kopieerproces](media/data-box-deploy-copy-data/view-errors-1.png)

Selecteer **Lijst met problemen downloaden**.

![Verbinding maken en kopiëren, Lijst met problemen downloaden](media/data-box-deploy-copy-data/view-errors-2.png)

Open de lijst om de details van de fout weer te geven en selecteer de oplossings-URL om de aanbevolen oplossing weer te geven.

![Verbinding maken en kopiëren, fouten bij downloaden en weergeven](media/data-box-deploy-copy-data/view-errors-3.png)

Zie [Foutenlogboeken bekijken tijdens het kopiëren van gegevens naar Data Box](data-box-logs.md#view-error-log-during-data-copy) voor meer informatie. Zie [Problemen met Data Box oplossen](data-box-troubleshoot.md) voor een gedetailleerde lijst met fouten tijdens het kopiëren van gegevens.

Om de gegevensintegriteit te garanderen wordt de controlesom inline berekend terwijl de gegevens worden gekopieerd. Verifieer de gebruikte ruimte en vrije ruimte op uw apparaat na het kopiëren.

![Vrije en ongebruikte ruimte verifiëren op het dashboard](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)

::: zone-end

::: zone target="chromeless"

U kunt gegevens van de bronserver naar uw Data Box kopiëren via SMB, NFS, REST, een service voor het kopiëren van gegevens, of naar beheerde schijven.

Zorg er altijd voor dat de namen van shares en mappen, en de gegevensgrootte de richtlijnen volgen die worden beschreven in [Servicelimieten van Azure Storage en Data Box](data-box-limits.md).

## <a name="copy-data-via-smb"></a>Gegevens kopiëren via SMB

Ga als volgt te werk om gegevens te kopiëren via SMB:

1. Als u een Windows-host gebruikt, gebruikt u de volgende opdracht om verbinding te maken met de SMB-shares:

    `\\<IP address of your device>\ShareName`

2. Als u de toegangsreferenties voor de shares wilt ophalen, gaat u naar de pagina **Verbinding maken en kopiëren** in de lokale webgebruikersinterface van de Data Box.
3. Gebruik een programma voor het kopiëren van bestanden dat compatibel is met SMB, zoals Robocopy, om gegevens naar shares te kopiëren. 

Ga voor stapsgewijze instructies naar [Zelfstudie: Gegevens naar Azure Data Box Disk kopiëren via SMB](data-box-deploy-copy-data.md).

## <a name="copy-data-via-nfs"></a>Gegevens kopiëren via NFS

Ga als volgt te werk om gegevens te kopiëren via NFS:

1. Als u een NFS-host gebruikt, gebruikt u de volgende opdracht om de NFS-shares op uw Data Box te koppelen:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

2. Als u de toegangsreferenties voor de shares wilt ophalen, gaat u naar de pagina **Verbinding maken en kopiëren** in de lokale webgebruikersinterface van de Data Box.
3. Gebruik de opdracht `cp` of `rsync` om de gegevens te kopiëren.

Ga voor stapsgewijze instructies naar [Zelfstudie: Gegevens kopiëren naar Azure Data Box via NFS](data-box-deploy-copy-data-via-nfs.md).

## <a name="copy-data-via-rest"></a>Gegevens kopiëren via REST

Ga als volgt te werk om gegevens te kopiëren via REST:

1. Als u gegevens wilt kopiëren met behulp van Data Box-Blob-opslag via REST API's, kunt u verbinding maken via *http* of *https*.
2. Als u gegevens wilt kopiëren naar Data Box-Blob-opslag, kunt u AzCopy gebruiken.

Ga voor stapsgewijze instructies naar [Zelfstudie: Gegevens kopiëren naar Azure Data Box-Blob-opslag via REST API's](data-box-deploy-copy-data-via-nfs.md).

## <a name="copy-data-via-data-copy-service"></a>Gegevens kopiëren via een gegevenskopieerservice

Ga als volgt te werk om gegevens te kopiëren via een gegevenskopieerservice:

1. U moet een taak maken als u gegevens wilt kopiëren met behulp van de gegevenskopieerservice. Ga in de lokale webgebruikersinterface van uw Data Box naar **Beheren > Gegevens kopiëren > Maken**.
2. Vul de parameters in en maak een taak.

Ga voor stapsgewijze instructies naar [Zelfstudie: De gegevenskopieerservice gebruiken om gegevens te kopiëren naar Azure Data Box](data-box-deploy-copy-data-via-copy-service.md).

## <a name="copy-data-to-managed-disks"></a>Gegevens kopiëren naar beheerde schijven

Ga als volgt te werk om gegevens te kopiëren naar beheerde schijven:

1. Wanneer u het Data Box-apparaat bestelt, moet u beheerde schijven als opslaglocatie hebben geselecteerd.
2. U kunt verbinding maken met Data Box via SMB- of NFS-shares.
3. Vervolgens kunt u gegevens kopiëren via SMB- of NFS-hulpprogramma's.

Ga voor stapsgewijze instructies naar [Zelfstudie: Data Box gebruiken om gegevens als beheerde schijven in Azure te importeren](data-box-deploy-copy-data-from-vhds.md).

::: zone-end

::: zone target="docs"

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie bent u meer te weten gekomen over verschillende onderwerpen met betrekking tot Azure Data Box, zoals:

> [!div class="checklist"]
>
> * Vereisten
> * Verbinding maken met Data Box
> * Gegevens kopiëren naar Data Box

Ga naar de volgende zelfstudie om te lezen hoe u uw Data Box naar Microsoft verzendt.

> [!div class="nextstepaction"]
> [Uw Azure Data Box verzenden naar Microsoft](./data-box-deploy-picked-up.md)

::: zone-end