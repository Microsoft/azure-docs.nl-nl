---
title: "Zelfstudie: Kopiëren vanaf VHD's naar beheerde schijven"
titleSuffix: Azure Data Box
description: Meer informatie over het kopiëren van gegevens van VHD's van on-premises VM-workloads naar uw Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 10/20/2019
ms.author: alkohli
ms.openlocfilehash: 1394cf6511a65a0e406e51229953e8666d4d4d8d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94337675"
---
# <a name="tutorial-use-data-box-to-import-data-as-managed-disks-in-azure"></a>Zelfstudie: Data Box gebruiken om gegevens als beheerde schijven in Azure te importeren

In deze zelfstudie wordt beschreven hoe u Azure Data Box gebruikt voor het migreren van uw on-premises VHD's naar beheerde schijven in Azure. De VHD's van on-premises VM's worden als pagina-blobs gekopieerd naar Data Box en als beheerde schijven geüpload in Azure. Deze beheerde schijven kunnen vervolgens worden gekoppeld aan virtuele Azure-machines.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Vereisten controleren
> * Verbinding maken met Data Box
> * Gegevens kopiëren naar Data Box

## <a name="prerequisites"></a>Vereisten

Zorg voordat u begint voor het volgende:

1. U hebt de zelfstudie [ Azure Data Box instellen](data-box-deploy-set-up.md) voltooid.
2. U hebt uw Data Box ontvangen en de bestelstatus in de portal is **Geleverd**.
3. U bent verbonden met een netwerk met hoge snelheid. Het wordt aangeraden dat u beschikt over minstens één 10-GbE-verbinding. Als er geen 10 GbE-verbinding beschikbaar is, kan een 1 GbE-gegevenskoppeling worden gebruikt, maar dit heeft wel gevolgen voor de kopieersnelheid.
4. U hebt het volgende gelezen:

    - Ondersteunde [grootten voor beheerde schijven in Azure-objectgroottelimieten](data-box-limits.md#azure-object-size-limits).
    - [Inleiding tot beheerde Azure-schijven](../virtual-machines/managed-disks-overview.md). 

5. U hebt een kopie van de brongegevens onderhouden totdat u hebt bevestigd dat de gegevens met Data Box naar Azure Storage zijn overgebracht.

## <a name="connect-to-data-box"></a>Verbinding maken met Data Box

Op basis van de opgegeven resourcegroepen, maakt Data Box één share voor elke gekoppelde resourcegroep. Als bijvoorbeeld `mydbmdrg1` en `mydbmdrg2` zijn gemaakt bij het plaatsen van de order, worden de volgende shares gemaakt:

- `mydbmdrg1_MDisk`
- `mydbmdrg2_MDisk`

Binnen elke share worden de volgende drie mappen gemaakt. Deze komen overeen met containers in uw opslagaccount.

- Premium - SSD
- Standard - HDD
- Standard - SSD

In de volgende tabel ziet u de UNC-paden naar de shares op uw Data Box.
 
|        Verbindingsprotocol           |             UNC-pad naar de share                                               |
|-------------------|--------------------------------------------------------------------------------|
| SMB |`\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<PremiumSSD>\file1.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<StandardHDD>\file2.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<StandardSSD>\file3.vhd` |  
| NFS |`//<DeviceIPAddress>/<ResourceGroup1_MDisk>/<PremiumSSD>/file1.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<StandardHDD>/file2.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<StandardSSD>/file3.vhd` |

De stappen om verbinding te maken, zijn afhankelijk van het feit of u SMB of NFS gebruikt om verbinding te maken met Data Box-shares.

> [!NOTE]
> Verbinding maken via REST wordt voor deze functie niet ondersteund.

### <a name="connect-to-data-box-via-smb"></a>Verbinding maken met Data Box via SMB

Als u een hostcomputer met Windows Server gebruikt, voert u deze stappen uit om verbinding met de Data Box te maken.

1. U moet eerst een verificatie uitvoeren en een sessie starten. Ga naar **Verbinding maken en kopiëren**. Klik op **Referenties ophalen** om de toegangsreferenties op te halen voor de shares die aan uw resourcegroep zijn gekoppeld. U kunt de toegangsreferenties ook verkrijgen via **Apparaatgegevens** in Azure Portal.

    > [!NOTE]
    > De referenties voor alle shares voor beheerde schijven zijn identiek.

    ![Verbinding maken en kopiëren, sharereferenties ophalen](media/data-box-deploy-copy-data-from-vhds/get-share-credentials1.png)

2. Kopieer in het dialoogvenster **Verbinding maken met share en gegevens kopiëren** de **gebruikersnaam** en het **wachtwoord** voor de share. Klik op **OK**.
    
    ![Verbinding maken en kopiëren, sharereferenties kopiëren](media/data-box-deploy-copy-data-from-vhds/get-share-credentials2.png)

3. Als u vanaf uw hostcomputer toegang wilt krijgen tot de shares die zijn gekoppeld aan uw resource (*mydbmdrg1* in het volgende voorbeeld), opent u een opdrachtvenster. Typ in de opdrachtprompt:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    De paden voor uw UNC-share in dit voorbeeld zijn als volgt:

    - `\\169.254.250.200\mydbmdrg1_MDisk`
    - `\\169.254.250.200\mydbmdrg2_MDisk`
    
4. Voer het wachtwoord voor de share in wanneer er om wordt gevraagd. Het volgende voorbeeld laat zien hoe u verbinding met een share kunt maken via de voorgaande opdracht.

    ```
    C:\>net use \\169.254.250.200\mydbmdrgl_MDisk /u:mdisk
    Enter the password for 'mdisk' to connect to '169.254.250.200':
    The command completed successfully.
    C: \>
    ```

5. Druk op Windows-toets+R. Geef in het venster **Uitvoeren** het `\\<device IP address>\<ShareName>` op. Klik op **OK** om Verkenner te openen.
    
    ![Verbinding maken met share via Verkenner](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer1.png)

    U ziet nu de volgende, vooraf gemaakte mappen in elke share.
    
    ![Verbinding maken met share via Verkenner, mappen voor een share](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer2.png)


### <a name="connect-to-data-box-via-nfs"></a>Verbinding maken met Data Box via NFS

Voer de volgende stappen uit om Data Box zo te configureren dat toegang tot NFS-clients wordt toegestaan als u een Linux-hostcomputer gebruikt.

1. Geef de IP-adressen op van de clients die toegang hebben tot de share. Ga in de lokale gebruikersinterface naar de pagina **Verbinding maken en kopiëren**. Klik onder **NFS-instellingen** op **NFS-clienttoegang**.

    ![NFS-clienttoegang configureren](media/data-box-deploy-copy-data-from-vhds/nfs-client-access1.png)

2. Geef het IP-adres op van de NFS-client en klik op **Toevoegen**. U kunt deze stap herhalen om toegang voor meerdere NFS-clients te configureren. Klik op **OK**.

    ![IP-adres voor NFS-client configureren](media/data-box-deploy-copy-data-from-vhds/nfs-client-access2.png)

2. Zorg dat er een [ondersteunde versie](data-box-system-requirements.md) van de NFS-client op de Linux-hostcomputer is geïnstalleerd. Gebruik de specifieke versie voor uw Linux-distributie.

3. Gebruik na het installeren van de NFS-client de volgende opdracht om de NFS-share op uw Data Box-apparaat te koppelen:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    Het volgende voorbeeld toont hoe u via NFS verbinding maakt met een Data Box-share. Het IP-adres van het Data Box-apparaat is `169.254.250.200`, de share `mydbmdrg1_MDisk` wordt gekoppeld op de Ubuntu-VM, het koppelpunt is `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 169.254.250.200:/mydbmdrg1_MDisk /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box"></a>Gegevens kopiëren naar Data Box

Nadat u verbinding met de gegevensserver hebt gemaakt, kopieert u de gegevens. Het VHD-bestand wordt als pagina-blob gekopieerd naar het faseringsopslagaccount. De pagina-blob wordt vervolgens geconverteerd naar een beheerde schijf en verplaatst naar een resourcegroep.

Neem de volgende punten door voordat u gegevens gaat kopiëren:

- Kopieer de VHD's altijd naar een van de vooraf gemaakte mappen. Als u de VHD's buiten deze mappen kopieert of kopieert naar een map die u zelf hebt gemaakt, worden de VHD's, en niet de beheerde schijven, naar een Azure Storage-account als pagina-blobs geüpload.
- Alleen de vaste VHD's kunnen worden geüpload om beheerde schijven te maken. VHDX-bestanden of dynamische en differentiërende VHD's worden niet ondersteund.
- Er kan slechts één beheerde schijf met een bepaalde naam in een resourcegroep aanwezig zijn voor alle vooraf gemaakte mappen. Dit betekent dat de VHD's die naar de vooraf gemaakte mappen zijn geüpload, unieke namen moeten hebben. Zorg ervoor dat de opgegeven naam niet overeenkomt met een bestaande beheerde schijf in een resourcegroep.
- Lees over groottelimieten voor beheerde schijven in [Azure-objectgroottelimieten](data-box-limits.md#azure-object-size-limits).

Afhankelijk van of u verbinding maakt via SMB of NFS, kunt u het volgende gebruiken:

- [Gegevens kopiëren via SMB](data-box-deploy-copy-data.md#copy-data-to-data-box)
- [Gegevens kopiëren via NFS](data-box-deploy-copy-data-via-nfs.md#copy-data-to-data-box)

Wacht tot de kopieertaken zijn voltooid. Zorg ervoor dat de kopieertaken zonder fouten zijn voltooid voordat u naar de volgende stap gaat.

![Geen fouten op de pagina **Verbinding maken en kopiëren**](media/data-box-deploy-copy-data-from-vhds/verify-no-errors-connect-and-copy.png)

Als er fouten optreden tijdens het kopieerproces, downloadt u de logboeken op de pagina **Verbinding maken en kopiëren**.

- Als u een bestand hebt gekopieerd dat niet gelijk is aan een veelvoud van 512 bytes, wordt het bestand niet als pagina-blob naar uw faseringsopslagaccount geüpload. In de logboeken wordt een fout vermeld. Verwijder het bestand en kopieer een bestand dat een veelvoud van 512 bytes is.

- Als u een VHDX hebt gekopieerd (deze bestanden worden niet ondersteund) met een lange naam, wordt in de logboeken een fout vermeld.

    ![Fout in de logboeken van de pagina **Verbinding maken en kopiëren**](media/data-box-deploy-copy-data-from-vhds/errors-connect-and-copy.png)

    Los de fouten op voordat u verdergaat met de volgende stap.

Om de gegevensintegriteit te garanderen wordt de controlesom inline berekend terwijl de gegevens worden gekopieerd. Verifieer de gebruikte ruimte en vrije ruimte op uw apparaat na het kopiëren.
    
![Vrije en ongebruikte ruimte verifiëren op het dashboard](media/data-box-deploy-copy-data-from-vhds/verify-used-space-dashboard.png)

Wanneer de kopieertaak is voltooid, kunt u naar **Voorbereiding voor verzending** gaan.


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie bent u meer te weten gekomen over verschillende onderwerpen met betrekking tot Azure Data Box, zoals:

> [!div class="checklist"]
> * Vereisten controleren
> * Verbinding maken met Data Box
> * Gegevens kopiëren naar Data Box


Ga naar de volgende zelfstudie om te lezen hoe u uw Data Box naar Microsoft verzendt.

> [!div class="nextstepaction"]
> [Uw Azure Data Box verzenden naar Microsoft](./data-box-deploy-picked-up.md)