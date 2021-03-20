---
title: Zelfstudie voor het kopiëren van gegevens naar Azure Data Box Heavy via NFS | Microsoft Docs
description: In deze zelfstudie leert u hoe u verbinding kunt maken met en gegevens kunt kopiëren van uw hostcomputer naar Azure Data Box Heavy door gebruik te maken van NFS met de lokale web UI.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: a8199039ea803d5919e168edccaaa03c18985725
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87921024"
---
# <a name="tutorial-copy-data-to-azure-data-box-heavy-via-nfs"></a>Zelfstudie: Gegevens kopiëren naar Azure Data Box Heavy via NFS

In deze zelfstudie wordt beschreven hoe u verbinding maakt met Azure Data Box Heavy en hiernaartoe gegevens kopieert vanaf uw hostcomputer met behulp van de lokale webinterface.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Vereisten
> * Verbinding maken met Data Box Heavy
> * Gegevens kopiëren naar Data Box Heavy

## <a name="prerequisites"></a>Vereisten

Zorg voordat u begint voor het volgende:

1. U hebt de zelfstudie [ Azure Data Box Heavy instellen](data-box-heavy-deploy-set-up.md) voltooid.
2. U hebt Azure Data Box Heavy ontvangen en de orderstatus in de portal is **Geleverd**.
3. U beschikt over een hostcomputer waarop de gegevens staan die u naar de Data Box Heavy wilt kopiëren. Op uw hostcomputer moet
    - Een [ondersteund besturingssysteem](data-box-heavy-system-requirements.md) worden uitgevoerd.
    - Verbonden zijn met een netwerk met hoge snelheid. Voor de snelste kopieersnelheden kunnen twee verbindingen van 40 GbE (één per knooppunt) naast elkaar worden gebruikt. Als u niet beschikt over een 40-GbE-verbinding, is het advies dat u ten minste twee verbindingen van 10 GbE (één per knooppunt) gebruikt. 

## <a name="connect-to-data-box-heavy"></a>Verbinding maken met Data Box Heavy

Op basis van het geselecteerde opslagaccount maakt Data Box Heavy maximaal:
- Drie shares voor elk gekoppeld opslagaccount voor GPv1 en GPv2.
- Eén share voor premium opslag.
- Eén share voor een blob-opslagaccount.

Deze shares worden gemaakt op beide knooppunten van het apparaat.

Onder shares voor blok-blobs en pagina-blobs:
- Entiteiten op het hoogste niveau zijn containers.
- Entiteiten op het tweede niveau zijn blobs.

Onder shares voor Azure Files:
- Entiteiten op het hoogste niveau zijn shares.
- Entiteiten op het tweede niveau zijn bestanden.

In de volgende tabel ziet u het UNC-pad naar de shares op uw Data Box Heavy en de URL van het Azure Storage-pad waarnaar de gegevens worden geüpload. De uiteindelijke URL van het Azure Storage-pad kan worden afgeleid van het UNC-pad naar de shares.
 
| Storage           | UNC-pad                                                                       |
|-------------------|--------------------------------------------------------------------------------|
| Azure-blok-blobs | <li>UNC-pad naar shares: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Azure Storage-URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure-pagina-blobs  | <li>UNC-pad naar shares: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Azure Storage-URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>UNC-pad naar shares: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Azure Storage-URL: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

Als u een Linux-hostcomputer gebruikt, voert u de volgende stappen uit om uw apparaat zo te configureren dat toegang tot NFS-clients wordt toegestaan.

1. Geef de IP-adressen op van de clients die toegang hebben tot de share. Ga in de lokale gebruikersinterface naar de pagina **Verbinding maken en kopiëren**. Klik onder **NFS-instellingen** op **NFS-clienttoegang**. 

    ![NFS-clienttoegang configureren 1](media/data-box-deploy-copy-data/nfs-client-access.png)

2. Geef het IP-adres op van de NFS-client en klik op **Toevoegen**. U kunt deze stap herhalen om toegang voor meerdere NFS-clients te configureren. Klik op **OK**.

    ![NFS-clienttoegang configureren 2](media/data-box-deploy-copy-data/nfs-client-access2.png)

2. Zorg dat er een [ondersteunde versie](data-box-system-requirements.md) van de NFS-client op de Linux-hostcomputer is geïnstalleerd. Gebruik de specifieke versie voor uw Linux-distributie. 

3. Gebruik na het installeren van de NFS-client de volgende opdracht om de NFS-share op uw Data Box-apparaat te koppelen:

    `sudo mount <Data Box Heavy device IP>:/<NFS share on Data Box Heavy device> <Path to the folder on local Linux computer>`

    Het volgende voorbeeld toont hoe u via NFS verbinding maakt met een Azure Data Box Heavy-share. Het IP-adres van Azure Data Box Heavy is `10.161.23.130`, de share `Mystoracct_Blob` wordt gekoppeld aan de Ubuntu-VM, het koppelpunt is `/home/databoxheavyubuntuhost/databoxheavy`.

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxheavyubuntuhost/databoxheavy`
    
    Voor Mac-clients moet u als volgt een extra optie toevoegen: 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxheavyubuntuhost/databoxheavy`

    **Maak altijd een map voor de bestanden die u van plan bent te kopiëren in de bestandsshare en kopieer de bestanden vervolgens naar die map**. De map gemaakt onder shares met blok-blobs en pagina-blobs vertegenwoordigt een container waarnaar gegevens als blobs worden geüpload. Het is niet mogelijk om bestanden rechtstreeks te kopiëren naar de *root*-map in het opslagaccount.

## <a name="copy-data-to-data-box-heavy"></a>Gegevens kopiëren naar Data Box Heavy

Nadat u verbinding met de Data Box Heavy-shares hebt gemaakt, kopieert u de gegevens. Neem de volgende punten door voordat u gegevens gaat kopiëren:

- Zorg dat u de gegevens kopieert naar shares die overeenkomen met de juiste gegevensindeling. U moet bijvoorbeeld de blok-blobgegevens naar de share voor blok-blobs kopiëren. Kopieer VHD's naar pagina-blobs. Als de gegevensindeling niet met het betreffende sharetype overeenkomt, zal het uploaden van gegevens naar Azure op een later tijdstip mislukken.
-  Zorg er tijdens het kopiëren van de gegevens voor dat de gegevensgrootte voldoet aan de limieten die staan beschreven in de [limieten voor Azure-opslag en Data Box Heavy](data-box-heavy-limits.md). 
- Als de gegevens die door Data Box Heavy worden geüpload gelijktijdig door andere toepassingen buiten Data Box Heavy worden geüpload, kan dit fouten met de uploadtaak veroorzaken en daardoor beschadigde gegevens opleveren.
- We adviseren u om niet SMB en NFS tegelijk te gebruiken of om dezelfde gegevens naar dezelfde eindbestemming in Azure te kopiëren. In dergelijke gevallen kan de definitieve uitkomst niet worden vastgesteld.
- **Maak altijd een map voor de bestanden die u van plan bent te kopiëren in de bestandsshare en kopieer de bestanden vervolgens naar die map**. De map gemaakt onder shares met blok-blobs en pagina-blobs vertegenwoordigt een container waarnaar gegevens als blobs worden geüpload. Het is niet mogelijk om bestanden rechtstreeks te kopiëren naar de *root*-map in het opslagaccount.
- Bij het opnemen van hoofdlettergevoelige map- en bestandsnamen van een NSF-share naar NFS op Data Box Heavy: 
    - Blijven de hoofd- en kleine letters behouden in de naam.
    - De bestanden zijn niet hoofdlettergevoelig.
    
    Als u bijvoorbeeld `SampleFile.txt` en `Samplefile.Txt` kopieert, blijven de hoofd- en kleine letters in de naam behouden als de bestanden naar het apparaat worden gekopieerd, maar het tweede bestand overschrijft het eerste, omdat ze als hetzelfde bestand worden beschouwd.


Gebruik een hulpprogramma voor kopiëren dat op Robocopy lijkt als u een Linux-hostcomputer gebruikt. Voorbeelden van beschikbare alternatieven in Linux zijn [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) en [Ultracopier](https://ultracopier.first-world.info/).  

De opdracht `cp` is een van de beste opties om een map te kopiëren. Ga voor meer informatie over het gebruik naar [cp man-pagina’s](http://man7.org/linux/man-pages/man1/cp.1.html).

Volg deze richtlijnen als u rsync gebruikt voor een kopie met meerdere threads:

 - Installeer het **CIFS Utils**- of **NFS Utils**-pakket, afhankelijk van het bestandssysteem dat uw Linux-client gebruikt.

    `sudo apt-get install cifs-utils`

    `sudo apt-get install nfs-utils`

 -  Installeer **Rsync** en **Parallel** (varieert en is afhankelijk van de gedistribueerde Linux-versie).

    `sudo apt-get install rsync`
   
    `sudo apt-get install parallel` 

 - Maak een koppelpunt.

    `sudo mkdir /mnt/databoxheavy`

 - Koppel het volume.

    `sudo mount -t NFS4  //Databox-heavy-IP-Address/share_name /mnt/databoxheavy` 

 - Spiegel de mapstructuur.  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databoxheavy`

 - Kopieer bestanden. 

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databoxheavy/{}`

     waarbij j het aantal voor parallelle uitvoering opgeeft en X staat voor het aantal parallelle kopieën

     We adviseren u om met 16 parallelle kopieën te beginnen en het aantal threads te verhogen op basis van de beschikbare resources.

> [!IMPORTANT]
> De volgende Linux-bestandstypen worden niet ondersteund: symbolische koppelingen, tekenbestanden, blokbestanden, sockets en pijpen. Deze bestandstypen veroorzaken fouten tijdens de stap **Voorbereiden voor verzending**.

Open de doelmap om de gekopieerde bestanden weer te geven en te controleren. Download de foutbestanden om problemen op te lossen als er fouten zijn opgetreden tijdens het kopiëren. Zie [Foutenlogboeken bekijken tijdens het kopiëren van gegevens naar Data Box Heavy](data-box-logs.md#view-error-log-during-data-copy) voor meer informatie. Zie [Problemen met Data Box Heavy oplossen](data-box-troubleshoot.md) voor een gedetailleerde lijst met fouten tijdens het kopiëren van gegevens.

Om de gegevensintegriteit te garanderen wordt de controlesom inline berekend terwijl de gegevens worden gekopieerd. Verifieer de gebruikte ruimte en vrije ruimte op uw apparaat na het kopiëren.
    
   ![Vrije en ongebruikte ruimte verifiëren op het dashboard](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie zijn verschillende onderwerpen besproken over de Azure Data Box Heavy, zoals:

> [!div class="checklist"]
> * Vereisten
> * Verbinding maken met Data Box Heavy
> * Gegevens kopiëren naar Data Box Heavy


Ga naar de volgende zelfstudie om te lezen hoe u uw Data Box naar Microsoft verzendt.

> [!div class="nextstepaction"]
> [Uw Azure Data Box Heavy verzenden naar Microsoft](./data-box-heavy-deploy-picked-up.md)

