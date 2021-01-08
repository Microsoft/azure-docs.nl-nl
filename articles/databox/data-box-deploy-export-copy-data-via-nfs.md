---
title: Zelfstudie voor het kopiëren van gegevens uit Azure Data Box via NFS | Microsoft Docs
description: Leer hoe u gegevens kopieert uit uw Azure Data Box via NFS
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: alkohli
ms.openlocfilehash: 64bb5e94c4b18626d1f85d7e61252aae74202eb9
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680620"
---
# <a name="tutorial-copy-data-from-azure-data-box-via-nfs"></a>Zelfstudie: Gegevens kopiëren uit Azure Data Box via NFS

In deze zelfstudie wordt beschreven hoe u verbinding maakt met de lokale webinterface van uw Data Box en van daaruit gegevens kopieert naar een on-premises gegevensserver via NFS. De gegevens op uw Data Box worden geëxporteerd uit uw Azure Storage-account.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Vereisten
> * Verbinding maken met Data Box
> * Gegevens kopiëren uit Data Box

## <a name="prerequisites"></a>Vereisten

Zorg voordat u begint voor het volgende:

1. U hebt de bestelling voor Azure Data Box geplaatst.
    - Bekijk voor een importorder de [Zelfstudie: Azure Data Box bestellen](data-box-deploy-ordered.md) voltooid.
    - Bekijk voor een exportorder de [Zelfstudie: Azure Data Box bestellen](data-box-deploy-export-ordered.md) voltooid.
2. U hebt uw Data Box ontvangen en de bestelstatus in de portal is **Geleverd**.
3. U hebt een hostcomputer waarnaar u de gegevens uit uw Data Box wilt kopiëren. Op uw hostcomputer moet
   * Een [ondersteund besturingssysteem](data-box-system-requirements.md) worden uitgevoerd.
   * Verbonden zijn met een netwerk met hoge snelheid. Het wordt aangeraden dat u beschikt over minstens één 10-GbE-verbinding. Als er geen 10 GbE-verbinding beschikbaar is, kan een 1 GbE-gegevenskoppeling worden gebruikt, maar dit heeft wel gevolgen voor de kopieersnelheid.

## <a name="connect-to-data-box"></a>Verbinding maken met Data Box

[!INCLUDE [data-box-shares](../../includes/data-box-shares.md)]

Voer de volgende stappen uit om Data Box zo te configureren dat toegang tot NFS-clients wordt toegestaan als u een Linux-hostcomputer gebruikt. Data Box kan maximaal vijf NFS-clients tegelijkertijd verbinden.

1. Geef de IP-adressen op van de clients die toegang hebben tot de share:

    1.  Ga in de lokale gebruikersinterface naar de pagina **Verbinding maken en kopiëren**. Klik onder **NFS-instellingen** op **NFS-clienttoegang**. 

        ![NFS-clienttoegang openen](media/data-box-deploy-export-copy-data/nfs-client-access-1.png)

    1. Geef het IP-adres van de client op en klik op **Toevoegen** om een NFS-client toe te voegen. Data Box kan maximaal vijf NFS-clients tegelijkertijd verbinden. Klik op **OK** als u klaar bent.

         ![Een NFS-client toevoegen](media/data-box-deploy-export-copy-data/nfs-client-access-2.png)

2. Zorg dat er een [ondersteunde versie](data-box-system-requirements.md) van de NFS-client op de Linux-hostcomputer is geïnstalleerd. Gebruik de specifieke versie voor uw Linux-distributie. 

3. Gebruik na het installeren van de NFS-client de volgende opdracht om de NFS-share op uw Data Box-apparaat te koppelen:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    Het volgende voorbeeld toont hoe u via NFS verbinding maakt met een Data Box-share. Het IP-adres van het Data Box-apparaat is `10.161.23.130`, de share `Mystoracct_Blob` wordt gekoppeld op de Ubuntu-VM, het koppelpunt is `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`
    
    Voor Mac-clients moet u als volgt een extra optie toevoegen: 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`

    **Maak altijd een map voor de bestanden die u van plan bent te kopiëren in de bestandsshare en kopieer de bestanden vervolgens naar die map**. De map gemaakt onder shares met blok-blobs en pagina-blobs vertegenwoordigt een container waarnaar gegevens als blobs worden geüpload. Het is niet mogelijk om bestanden rechtstreeks te kopiëren naar de *root*-map in het opslagaccount.

## <a name="copy-data-from-data-box"></a>Gegevens kopiëren uit Data Box

Nadat u verbinding met de Data Box-shares hebt gemaakt, moet u de gegevens kopiëren.

[!INCLUDE [data-box-export-review-logs](../../includes/data-box-export-review-logs.md)]

 U kunt nu beginnen met het kopiëren van de gegevens. Gebruik een hulpprogramma voor kopiëren dat op Robocopy lijkt als u een Linux-hostcomputer gebruikt. Voorbeelden van beschikbare alternatieven in Linux zijn [`rsync`](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) of [Ultracopier](https://ultracopier.first-world.info/).  

De opdracht `cp` is een van de beste opties om een map te kopiëren. Ga voor meer informatie over het gebruik naar [cp man-pagina’s](http://man7.org/linux/man-pages/man1/cp.1.html).

Volg deze richtlijnen als u de optie `rsync` gebruikt voor een kopie met meerdere threads:

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
