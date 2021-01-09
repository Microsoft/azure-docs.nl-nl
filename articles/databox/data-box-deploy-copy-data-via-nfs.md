---
title: Zelfstudie voor het kopiëren van gegevens naar Azure Data Box via NFS | Microsoft Docs
description: In deze zelfstudie leert u hoe u verbinding kunt maken met en gegevens kunt kopiëren van uw hostcomputer naar Azure Data Box door gebruik te maken van NFS met de lokale web UI.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/29/2020
ms.author: alkohli
ms.openlocfilehash: d53a619dc6ca5fb0f43f6097664f50bf22943928
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97678890"
---
# <a name="tutorial-copy-data-to-azure-data-box-via-nfs"></a>Zelfstudie: Gegevens kopiëren naar Azure Data Box via NFS

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
2. U hebt uw Data Box ontvangen en de bestelstatus in de portal is bijgewerkt naar **Geleverd**.
3. U hebt een hostcomputer waarop de gegevens staan die u naar de Data Box wilt kopiëren. Op uw hostcomputer moet
    - Een [ondersteund besturingssysteem](data-box-system-requirements.md) worden uitgevoerd.
    - Verbonden zijn met een netwerk met hoge snelheid. Het wordt aangeraden dat u beschikt over minstens één 10-GbE-verbinding. Als er geen 10 GbE-verbinding beschikbaar is, kan een 1 GbE-gegevenskoppeling worden gebruikt, maar dit heeft invloed op de kopieersnelheden. 

## <a name="connect-to-data-box"></a>Verbinding maken met Data Box

Data Box maakt op basis van het geselecteerde opslagaccount maximaal:
- Drie shares voor elk gekoppeld opslagaccount voor GPv1 en GPv2.
- Eén share voor premium opslag. 
- Eén share voor een blob-opslagaccount. 

Onder blok-blob- en pagina-blob-shares zijn entiteiten op het eerste niveau containers en entiteiten op het tweede niveau blobs. Onder shares voor Azure Files zijn entiteiten op het eerste niveau shares en entiteiten op het tweede niveau bestanden.

In de volgende tabel ziet u het UNC-pad naar de shares op uw Data Box en de URL van het Azure Storage-pad waarnaar de gegevens worden geüpload. De uiteindelijke URL van het Azure Storage-pad kan worden afgeleid van het UNC-pad naar de shares.
 
| Azure Storage-type| Data Box-shares                                       |
|-------------------|--------------------------------------------------------------------------------|
| Azure-blok-blobs | <li>UNC-pad naar shares: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Azure Storage-URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure-pagina-blobs  | <li>UNC-pad naar shares: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Azure Storage-URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>UNC-pad naar shares: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Azure Storage-URL: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

Voer de volgende stappen uit om Data Box zo te configureren dat toegang tot NFS-clients wordt toegestaan als u een Linux-hostcomputer gebruikt.

1. Geef de IP-adressen op van de clients die toegang hebben tot de share. Ga in de lokale gebruikersinterface naar de pagina **Verbinding maken en kopiëren**. Klik onder **NFS-instellingen** op **NFS-clienttoegang**. 

    ![NFS-clienttoegang configureren](media/data-box-deploy-copy-data/nfs-client-access-1.png)

2. Geef het IP-adres op van de NFS-client en klik op **Toevoegen**. U kunt deze stap herhalen om toegang voor meerdere NFS-clients te configureren. Klik op **OK**.

    ![IP-adres van een NFS-client configureren](media/data-box-deploy-copy-data/nfs-client-access2.png)

2. Zorg dat er een [ondersteunde versie](data-box-system-requirements.md) van de NFS-client op de Linux-hostcomputer is geïnstalleerd. Gebruik de specifieke versie voor uw Linux-distributie. 

3. Gebruik na het installeren van de NFS-client de volgende opdracht om de NFS-share op uw Data Box-apparaat te koppelen:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    Het volgende voorbeeld toont hoe u via NFS verbinding maakt met een Data Box-share. Het IP-adres van het Data Box-apparaat is `10.161.23.130`, de share `Mystoracct_Blob` wordt gekoppeld op de Ubuntu-VM, het koppelpunt is `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`
    
    Voor Mac-clients moet u als volgt een extra optie toevoegen: 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`

    **Maak altijd een map voor de bestanden die u van plan bent te kopiëren in de bestandsshare en kopieer de bestanden vervolgens naar die map**. De map gemaakt onder shares met blok-blobs en pagina-blobs vertegenwoordigt een container waarnaar gegevens als blobs worden geüpload. Het is niet mogelijk om bestanden rechtstreeks te kopiëren naar de *root*-map in het opslagaccount.

## <a name="copy-data-to-data-box"></a>Gegevens kopiëren naar Data Box

Nadat u verbinding met de Data Box-shares hebt gemaakt, moet u de gegevens kopiëren. Neem de volgende punten door voordat u gegevens gaat kopiëren:

* Zorg dat u de gegevens kopieert naar shares die overeenkomen met de juiste gegevensindeling. U moet bijvoorbeeld de blok-blobgegevens naar de share voor blok-blobs kopiëren. Kopieer VHD's naar pagina-blobs. Als de gegevensindeling niet met het betreffende sharetype overeenkomt, zal het uploaden van gegevens naar Azure op een later tijdstip mislukken.
*  Zorg er tijdens het kopiëren van gegevens voor dat de gegevensgrootte voldoet aan de limieten die worden vermeld in de [limieten voor Azure-opslagaccounts](data-box-limits.md#azure-storage-account-size-limits).
* Als de gegevens die door Data Box worden geüpload gelijktijdig door andere toepassingen buiten Data Box worden geüpload, kan dit tot fouten voor de uploadtaak en beschadigde gegevens leiden.
* We adviseren u om niet SMB en NFS tegelijk te gebruiken of om dezelfde gegevens naar dezelfde eindbestemming in Azure te kopiëren. In dergelijke gevallen kan de definitieve uitkomst niet worden vastgesteld.
* **Maak altijd een map voor de bestanden die u van plan bent te kopiëren in de bestandsshare en kopieer de bestanden vervolgens naar die map**. De map gemaakt onder shares met blok-blobs en pagina-blobs vertegenwoordigt een container waarnaar gegevens als blobs worden geüpload. Het is niet mogelijk om bestanden rechtstreeks te kopiëren naar de *root*-map in het opslagaccount.
* Bij het opnemen van hoofdlettergevoelige map- en bestandsnamen van een NSF-share naar NFS op Data Box:
  * Blijven de hoofd- en kleine letters behouden in de naam.
  * De bestanden zijn niet hoofdlettergevoelig.

    Als u bijvoorbeeld `SampleFile.txt` en `Samplefile.Txt` kopieert, blijven de hoofd- en kleine letters behouden in de naam als ze wordt gekopieerd naar Data Box, maar het tweede bestand overschrijft het eerste, omdat ze worden gezien als één bestand.

> [!IMPORTANT]
> Zorg dat u een kopie van de brongegevens in uw on-premises omgeving hebt totdat u kunt bevestigen dat de gegevens met Data Box naar Azure Storage zijn overgebracht.

Gebruik een hulpprogramma voor kopiëren dat op Robocopy lijkt als u een Linux-hostcomputer gebruikt. Voorbeelden van beschikbare alternatieven in Linux zijn [`rsync`](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) of [Ultracopier](https://ultracopier.first-world.info/).  

De opdracht `cp` is een van de beste opties om een map te kopiëren. Ga voor meer informatie over het gebruik naar [cp man-pagina’s](http://man7.org/linux/man-pages/man1/cp.1.html).

Volg deze richtlijnen als u `rsync` gebruikt voor een kopie met meerdere threads:

* Installeer het **CIFS Utils**- of **NFS Utils**-pakket, afhankelijk van het bestandssysteem dat uw Linux-client gebruikt.

    `sudo apt-get install cifs-utils`

    `sudo apt-get install nfs-utils`

* Installeer `rsync` en **Parallel** (varieert en is afhankelijk van de gedistribueerde Linux-versie).

    `sudo apt-get install rsync`
   
    `sudo apt-get install parallel` 

* Maak een koppelpunt.

    `sudo mkdir /mnt/databox`

* Koppel het volume.

    `sudo mount -t NFS4  //Databox IP Address/share_name /mnt/databox` 

* Spiegel de mapstructuur.  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databox`

* Kopieer bestanden.

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databox/{}`

     waarbij j het aantal voor parallelle uitvoering opgeeft en X staat voor het aantal parallelle kopieën

     We adviseren u om met 16 parallelle kopieën te beginnen en het aantal threads te verhogen op basis van de beschikbare resources.

> [!IMPORTANT]
> De volgende Linux-bestandstypen worden niet ondersteund: symbolische koppelingen, tekenbestanden, blokbestanden, sockets en pijpen. Deze bestandstypen veroorzaken fouten tijdens de stap **Voorbereiden voor verzending**.

Als er fouten zijn tijdens het kopieerproces, krijgt u een melding.

![Fouten downloaden en bekijken op Verbinding maken en kopiëren](media/data-box-deploy-copy-data/view-errors-1.png)

Selecteer **Lijst met problemen downloaden**.

![De lijst met problemen voor een kopieerfout downloaden](media/data-box-deploy-copy-data/view-errors-2.png)

Open de lijst om de details van de fout weer te geven en selecteer de oplossings-URL om de aanbevolen oplossing weer te geven.

![Problemen in de lijst met problemen voor een kopieerfout](media/data-box-deploy-copy-data/view-errors-3.png)

Zie [Foutenlogboeken bekijken tijdens het kopiëren van gegevens naar Data Box](data-box-logs.md#view-error-log-during-data-copy) voor meer informatie. Zie [Problemen met Data Box oplossen](data-box-troubleshoot.md) voor een gedetailleerde lijst met fouten tijdens het kopiëren van gegevens.

Om de gegevensintegriteit te garanderen wordt de controlesom inline berekend terwijl de gegevens worden gekopieerd. Verifieer de gebruikte ruimte en vrije ruimte op uw apparaat na het kopiëren.

   ![Vrije en ongebruikte ruimte verifiëren op het dashboard](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)

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
