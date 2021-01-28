---
title: Gegevens overdragen met Azure Data Box Gateway | Microsoft Docs
description: Meer informatie over hoe u shares kunt toevoegen en er verbinding mee kunt maken op uw Azure Data Box Gateway, zodat uw Data Box Gateway-apparaat gegevens kan overdragen naar Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: 0c0ef6157ebf70c896fbac5ff692246e4fad2c14
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98937209"
---
# <a name="tutorial-transfer-data-with-azure-data-box-gateway"></a>Zelfstudie: Gegevens overdragen met Azure Data Box Gateway


## <a name="introduction"></a>Inleiding

In dit artikel wordt beschreven hoe u shares toevoegt aan uw Data Box Gateway en er verbinding mee maakt. Nadat u de shares hebt toegevoegd, kunnen gegevens via het apparaat worden overgebracht naar Azure.

Deze procedure neemt in totaal ongeveer tien minuten in beslag.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Een share toevoegen
> * Verbinding maken met een share

## <a name="prerequisites"></a>Vereisten

Voordat u shares aan Data Box Gateway gaat toevoegen, controleert u het volgende:

- U hebt een virtueel apparaat ingericht en er verbinding mee gemaakt, zoals beschreven in [Data Box Gateway inrichten in Hyper-V](data-box-gateway-deploy-provision-hyperv.md) of [Data Box Gateway inrichten in VMware](data-box-gateway-deploy-provision-vmware.md).

- U hebt het virtuele apparaat geactiveerd zoals beschreven in [Uw Azure Data Box Gateway aansluiten activeren](data-box-gateway-deploy-connect-setup-activate.md).

- Het apparaat is klaar om bestandsshares te maken en gegevens over te dragen.

## <a name="add-a-share"></a>Een share toevoegen

Ga als volgt te werk om een share te maken:

1. Selecteer in [Azure Portal](https://portal.azure.com/) uw Data Box Gateway-resource en ga naar **Overzicht**. Uw apparaat zou nu online moeten zijn. Selecteer **+ Share toevoegen** op de opdrachtbalk van het apparaat.
   
   ![Een share toevoegen](./media/data-box-gateway-deploy-add-shares/click-add-share.png)

4. Houd u onder **Share toevoegen** aan de volgende procedure:

    1. Geef een unieke naam voor de share op. De namen van shares mogen alleen kleine letters, cijfers en afbreekstreepjes bevatten. De naam van de share moet tussen de 3 en 63 tekens bevatten en met een letter of cijfer beginnen. Elk afbreekstreepje moet worden voorafgegaan en gevolgd door een cijfer of letter.
    
    2. Selecteer een **Type** voor de share. Het type kan SMB of NFS zijn, waarbij SMB het standaardtype is. SMB is het standaardtype voor Windows-clients; NFS wordt gebruikt voor Linux-clients. De opties wijken enigszins af, afhankelijk van welk type u kiest.

    3. Geef een opslagaccount op waaronder de share wordt opgeslagen. Als er nog geen container bestaat, wordt in het opslagaccount een container gemaakt met de naam van de zojuist gemaakte share. Als er al een container bestaat, wordt de bestaande container gebruikt.
       > [!IMPORTANT]
       > Controleer of voor het Azure-opslagaccount dat u gebruikt geen onveranderbaarheidsbeleid is ingesteld als u het account gebruikt in combinatie met een Data Box Gateway-apparaat. Zie [Beleid voor onveranderbaarheid instellen en beheren voor blobopslag](../storage/blobs/storage-blob-immutability-policies-manage.md) voor meer informatie.
    
    4. Kies de **Opslagservice** vanuit blok-blob, pagina-blob of bestanden. Het type service dat u kiest, is afhankelijk van de indeling waarin u de gegevens in Azure wilt opslaan. In dit geval kiezen we ervoor de gegevens als blok-blobs in Azure op te slaan, dus we selecteren Blok-blob. Als u Pagina-blob kiest, dient u ervoor te zorgen dat uw gegevens op 512 bytes zijn uitgelijnd. VHDX is bijvoorbeeld altijd op 512 bytes uitgelijnd.
   
    5. Deze stap hangt af van of u een SMB- of een NFS-share gaat maken.
     
    - **SMB-share** - selecteer onder **Lokale gebruiker met alle machtigingen**, de optie **Nieuwe maken** of **Bestaande gebruiken**. Als u een nieuwe lokale gebruiker maakt, voert u een **gebruikersnaam** en **wachtwoord** in. Vervolgens **bevestigt u het wachtwoord**. Met deze actie worden de machtigingen aan de lokale gebruiker toegewezen. Het wijzigen van machtigingen op shareniveau wordt momenteel niet ondersteund.
    
        ![SMB-share toevoegen](./media/data-box-gateway-deploy-add-shares/add-share-smb-1.png)
        
        Als u het selectievakje **Alleen leesbewerkingen toestaan** selecteert voor deze sharegegevens, kunt u gebruikers met het kenmerk Alleen-lezen opgeven.
        
    - **NFS-share**: geef de IP-adressen op van de clients die toegang hebben tot de share.

        ![NFS-share toevoegen](./media/data-box-gateway-deploy-add-shares/add-share-nfs-1.png)
   
9. Selecteer **Maken** om de share te maken.
    
    U ontvangt een melding als wordt begonnen met het maken van de share. Zodra de share met de opgegeven instellingen is gemaakt, wordt de tegel **Shares** bijgewerkt in overeenstemming met de nieuwe share.
    
    ![De tegel Bijgewerkte shares](./media/data-box-gateway-deploy-add-shares/updated-list-of-shares.png) 

## <a name="connect-to-the-share"></a>Verbinding maken met de share

U kunt nu verbinding maken met een of meer shares die u hebt gemaakt in de laatste stap. Afhankelijk van of u een SMB- of een NFS-share hebt, kunnen de stappen variëren.

### <a name="connect-to-an-smb-share"></a>Verbinding maken met een SMB-share

Maak op de Windows Server-client die is verbonden met uw Data Box Gateway-apparaat, verbinding met een SMB-share door deze opdrachten in te voeren:


1. Typ in een opdrachtvenster:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Voer het wachtwoord voor de share in wanneer er om wordt gevraagd. De voorbeelduitvoer van deze opdracht wordt hier gepresenteerd.

    ```powershell
    Microsoft Windows [Version 18.8.16299.192) 
    (c) 2017 microsoft Corporation. All rights reserved . 
    
    C: \Users\GatewayUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser 
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60'  
    The command completed successfully. 
    
    C: \Users\GatewayUser>
    ```   


2. Selecteer Windows + R op uw toetsenbord. 
3. Geef in het venster **Uitvoeren** de `\\<device IP address>` op en selecteer **OK**. Verkenner wordt geopend. Als het goed is, ziet u nu de shares die u hebt gemaakt, als mappen. Dubbelklik in Verkenner op een share (map) om de inhoud te bekijken.
 
    ![Verbinding maken met een SMB-share](./media/data-box-gateway-deploy-add-shares/connect-to-share2.png)-->

    De gegevens worden naar deze shares geschreven terwijl ze worden gegenereerd en het apparaat pusht de gegevens naar de cloud.

### <a name="connect-to-an-nfs-share"></a>Verbinding maken met een NFS-share

Ga als volgt te werk in de Linux-client die is verbonden met het apparaat:

1. Zorg ervoor dat de NFSv4-client is geïnstalleerd op de client. Als u de NFS-client wilt installeren, gebruikt u de volgende opdracht:

   `sudo apt-get install nfs-common`

    Ga naar [Install NFSv4 client](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client) (NFSv4-client installeren) voor meer informatie.

2. Als de NFS-client is geïnstalleerd, gebruikt u de volgende opdracht om de NFS-share te koppelen die u op uw Data Box Gateway-apparaat hebt gemaakt:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    Voordat u de koppelingen gaat instellen, controleert u of de mappen die als koppelpunten op uw lokale computer fungeren, al zijn gemaakt en geen bestanden of submappen bevatten.

    Het volgende voorbeeld toont hoe u via NFS verbinding maakt met een share op een Gateway-apparaat. Het IP-adres van het virtuele apparaat is `10.10.10.60`, de share `mylinuxshare2` wordt gekoppeld op de Ubuntu-VM, het koppelpunt is `/home/databoxubuntuhost/gateway`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/gateway`

> [!NOTE] 
> De volgende beperkingen zijn van toepassing op deze release:
> - Nadat een bestand in de shares is gemaakt, kan de naam van het bestand niet meer worden gewijzigd.
> - Als een bestand uit een share wordt verwijderd, wordt de vermelding in het opslagaccount niet verwijderd.
> - Als u `rsync` gebruikt voor het kopiëren van gegevens, wordt de optie `rsync -a` niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie bent u meer te weten gekomen over verschillende onderwerpen met betrekking tot Data Box Gateway, zoals:

> [!div class="checklist"]
> * Een share toevoegen
> * Verbinding maken met een share


Ga naar de volgende zelfstudie om te lezen hoe u Data Box Gateway kunt beheren.

> [!div class="nextstepaction"]
> [Use local web UI to administer a Data Box Gateway](https://aka.ms/dbg-docs) (Lokale webgebruikersinterface gebruiken om Data Box Gateway te beheren)