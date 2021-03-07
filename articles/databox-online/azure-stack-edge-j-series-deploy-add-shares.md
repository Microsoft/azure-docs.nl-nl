---
title: Zelfstudie voor het overdragen van gegevens naar shares met Azure Stack Edge Pro GPU | Microsoft Docs
description: Informatie over het toevoegen van en verbinding maken met shares op een Azure Stack Edge Pro GPU-apparaat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/22/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to shares on Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: 1964ddb3ca9ecd2b445850c06c10b313a9bcbae9
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102438810"
---
# <a name="tutorial-transfer-data-via-shares-with-azure-stack-edge-pro-gpu"></a>Zelfstudie: Gegevens overdragen via shares met Azure Stack Edge Pro GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

In deze zelfstudie wordt beschreven hoe u shares toevoegt aan een Azure Stack Edge Pro-apparaat en er verbinding mee maakt. Nadat u de shares hebt toegevoegd, kunnen via Azure Stack Edge Pro gegevens worden overgedragen naar Azure.

Deze procedure neemt in totaal ongeveer tien minuten in beslag.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Een share toevoegen
> * Verbinding maken met de share

## <a name="prerequisites"></a>Vereisten

Voordat u shares aan Azure Stack Edge Pro gaat toevoegen, controleert u het volgende:

* U hebt uw fysieke apparaat geïnstalleerd zoals beschreven in [Azure Stack Edge Pro installeren](azure-stack-edge-gpu-deploy-install.md).

* U hebt het fysieke apparaat geactiveerd, zoals beschreven in [Azure Stack Edge Pro activeren](azure-stack-edge-gpu-deploy-activate.md).

## <a name="add-a-share"></a>Een share toevoegen

Ga als volgt te werk om een share te maken:

1. Selecteer in [Azure Portal](https://portal.azure.com/) uw Azure Stack Edge-resource en ga naar het **Overzicht**. Als het goed is, is uw apparaat online. Selecteer **Cloudopslaggateway**.

   ![Apparaat is online](./media/azure-stack-edge-j-series-deploy-add-shares/device-online-1.png)

2. Selecteer **+ Share toevoegen** op de opdrachtbalk van het apparaat.

   ![Een share toevoegen](./media/azure-stack-edge-j-series-deploy-add-shares/select-add-share-1.png)

3. Voer in het deelvenster **Share toevoegen** de volgende stappen uit:

    a. Geef in het vak **Naam** een unieke naam op voor de share.  
    De naam van de share mag alleen bestaan uit letters, cijfers en afbreekstreepjes. De naam moet tussen de 3 en 63 tekens lang zijn en beginnen met een letter of cijfer. Afbreekstreepjes moeten worden voorafgegaan door een letter of cijfer.
    
    b. Selecteer een **Type** voor de share.  
    Het type kan **SMB** of **NFS** zijn. SMB is het standaardtype. SMB is het standaardtype voor Windows-clients; NFS wordt gebruikt voor Linux-clients.  
    De andere opties verschillen enigszins, afhankelijk van of u kiest voor SMB- of NFS-shares. 

    c. Geef een opslagaccount op waaronder de share wordt opgeslagen.

    d. Selecteer in de vervolgkeuzelijst **Storage-service** de optie **Blok-blob**, **Pagina-blob** of **Bestanden**.  
    Het type service dat u selecteert, is afhankelijk van de gewenste indeling voor de gegevens in Azure. In dit voorbeeld selecteren we **Blok-blob** omdat we de gegevens in Azure willen opslaan als blob-blokken. Als u **Pagina-blob** selecteert, moet u ervoor zorgen dat uw gegevens zijn uitgelijnd op 512 bytes. VHDX is bijvoorbeeld altijd op 512 bytes uitgelijnd.

   > [!IMPORTANT]
   > Controleer of voor het Azure-opslagaccount dat u gebruikt geen onveranderbaarheidsbeleid is ingesteld als u het account gebruikt in combinatie met een Azure Stack Edge Pro- of Data Box Gateway-apparaat. Zie [Beleid voor onveranderbaarheid instellen en beheren voor blobopslag](../storage/blobs/storage-blob-immutability-policies-manage.md) voor meer informatie.

    e. Maak een nieuwe blobcontainer of gebruik een bestaande container in de vervolgkeuzelijst. Als u een blobcontainer maakt, moet u ook een naam voor die container opgeven. Als er nog geen container bestaat, wordt in het opslagaccount een container gemaakt met de naam van de zojuist gemaakte share.
   
    f. Voer een van de volgende stappen uit, afhankelijk van of u een SMB-share of een NFS-share hebt gemaakt: 
     
    - **SMB-share**: Selecteer onder **Lokale gebruiker met Alle machtigingen** de optie **Nieuwe maken** of **Bestaande gebruiken**. Als u een nieuwe lokale gebruiker maakt, voert u een gebruikersnaam en wachtwoord in. Vervolgens bevestigt u het wachtwoord. Met deze actie worden machtigingen toegewezen aan de lokale gebruiker. Het wijzigen van machtigingen op shareniveau wordt momenteel niet ondersteund. Als u het selectievakje **Alleen leesbewerkingen toestaan** selecteert voor deze sharegegevens, kunt u gebruikers met het kenmerk Alleen-lezen opgeven.
    
        ![SMB-share toevoegen](./media/azure-stack-edge-j-series-deploy-add-shares/add-share-smb-1.png)
   
    - **NFS-share**: Voer de IP-adressen van de clients in die toegang hebben tot de share.

        ![NFS-share toevoegen](./media/azure-stack-edge-j-series-deploy-add-shares/add-share-nfs-1.png)
   
4. Selecteer **Maken** om de share te maken.
    
    U ontvangt een melding als wordt begonnen met het maken van de share. Zodra de share met de opgegeven instellingen is gemaakt, wordt de tegel **Shares** bijgewerkt in overeenstemming met de nieuwe share.
    

## <a name="connect-to-the-share"></a>Verbinding maken met de share

U kunt nu verbinding maken met een of meer shares die u hebt gemaakt in de laatste stap. Afhankelijk van of u een SMB- of een NFS-share hebt, kunnen de stappen variëren.

De eerste stap is ervoor zorgen dat de apparaatnaam kan worden omgezet wanneer u een SMB- of NFS-share gebruikt.

### <a name="modify-host-file-for-name-resolution"></a>Hostbestand voor naamomzetting wijzigen

U voegt nu het IP-adres van het apparaat en de beschrijvende naam van het apparaat dat u hebt gedefinieerd in de lokale webgebruikersinterface van het apparaat, toe aan:

- Het hostbestand op de client, OF
- Het hostbestand op de DNS-server

> [!IMPORTANT]
> We raden aan het hostbestand op de DNS-server aan te passen voor de apparaatnaamomzetting.

Voer de volgende stappen uit op de Windows-client die u gebruikt om verbinding te maken met het apparaat:

1. Start **Kladblok** als beheerder en open vervolgens het bestand met **hosts** dat zich bevindt in `C:\Windows\System32\Drivers\etc`.

    ![Hosts-bestand in Windows Verkenner](media/azure-stack-edge-j-series-deploy-add-shares/client-hosts-file-1.png)


2. Voeg de volgende vermeldingen toe aan het **hosts**-bestand, waarbij u de juiste waarden voor uw apparaat invoert: 

    ```
    <Device IP>   <device friendly name>
    ``` 
    U kunt het IP-adres van het apparaat ophalen op de pagina **Netwerk** en de beschrijvende naam van het apparaat op de pagina **Apparaat** in de lokale webgebruikersinterface. In de volgende schermopname van hosts-bestand wordt de vermelding weergegeven:

    ![Hosts-bestand 2 in Windows Verkenner](media/azure-stack-edge-j-series-deploy-add-shares/client-hosts-file-2.png)

### <a name="connect-to-an-smb-share"></a>Verbinding maken met een SMB-share

Maak op de Windows Server-client die met uw Azure Stack Edge Pro-apparaat is verbonden, verbinding met een SMB-share door deze opdrachten in te voeren:


1. Typ in een opdrachtvenster:

    `net use \\<Device name>\<share name>  /u:<user name for the share>`

    > [!NOTE]
    > U kunt alleen verbinding maken met een SMB-share via de apparaatnaam en niet via het IP-adres.

2. Voer het wachtwoord voor de share in als u hierom wordt gevraagd.  
   De voorbeelduitvoer van deze opdracht wordt hier gepresenteerd.

    ```powershell
    Microsoft Windows [Version 10.0.18363.476)
    (c) 2017 Microsoft Corporation. All rights reserved.
    
    C: \Users\AzureStackEdgeUser>net use \\myasetest1\myasesmbshare1 /u:aseuser
    Enter the password for 'aseuser' to connect to 'myasetest1':
    The command completed successfully.
    
    C: \Users\AzureStackEdgeUser>
    ```   

3. Selecteer Windows + R op het toetsenbord.

4. Geef in het venster **Uitvoeren** de `\\<device name>` op en selecteer vervolgens **OK**.  

    ![Windows-dialoogvenster Uitvoeren](media/azure-stack-edge-j-series-deploy-add-shares/run-window-1.png)

   Verkenner wordt geopend. Als het goed is, ziet u nu de shares die u hebt gemaakt, als mappen. Dubbelklik in Verkenner op een share (map) om de inhoud te bekijken.
 
    ![Verbinding maken met een SMB-share](./media/azure-stack-edge-j-series-deploy-add-shares/file-explorer-smbshare-1.png)

    De gegevens worden naar deze shares geschreven terwijl ze worden gegenereerd en het apparaat pusht de gegevens naar de cloud.

### <a name="connect-to-an-nfs-share"></a>Verbinding maken met een NFS-share

Voer de volgende procedure uit met de Linux-client die is verbonden met het Azure Stack Edge Pro-apparaat:

1. Zorg ervoor dat de NFSv4-client is geïnstalleerd op de client. Als u de NFS-client wilt installeren, gebruikt u de volgende opdracht:

   `sudo apt-get install nfs-common`

    Ga naar [Install NFSv4 client](https://help.ubuntu.com/community/NFSv4Howto) (NFSv4-client installeren) voor meer informatie.

2. Nadat de NFS-client is geïnstalleerd, koppelt u de NFS-share die u hebt gemaakt, aan het Azure Stack Edge Pro-apparaat met behulp van de volgende opdracht:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS share on device> /home/username/<Folder on local Linux computer>`

    U kunt het IP-adres van het apparaat ophalen op de pagina **Netwerk** van de lokale webgebruikersinterface.

    > [!IMPORTANT]
    > Als u de `sync`-optie gebruikt wanneer u shares koppelt, verbetert u de overdrachtssnelheden van grote bestanden.
    > Voordat u de share koppelt, controleert u of de mappen die zullen fungeren als koppelpunten op de lokale computer, al zijn gemaakt. Deze mappen mogen geen bestanden of submappen bevatten.

    Het volgende voorbeeld toont hoe u via NFS verbinding maakt met een share op een Azure Stack Edge Pro-apparaat. Het IP-adres van het apparaat is `10.10.10.60`. De share `mylinuxshare2` is gekoppeld aan de ubuntuVM. Het koppelpunt van de share is `/home/azurestackedgeubuntuhost/edge`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/azurestackedgeubuntuhost/Edge`

> [!NOTE] 
> De volgende beperkingen zijn van toepassing op deze release:
> - Nadat een bestand in de share is gemaakt, kan de naam van het bestand niet meer worden gewijzigd. 
> - Als een bestand uit een share wordt verwijderd, wordt de vermelding in het Azure-opslagaccount niet verwijderd.
> - Wanneer u `rsync` gebruikt om over NFS te kopiëren, gebruikt u de vlag `--inplace` . 

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie bent u meer te weten gekomen over de volgende Azure Stack Edge Pro-onderwerpen:

> [!div class="checklist"]
> * Een share toevoegen
> * Verbinding maken met een share

Als u wilt leren hoe u gegevens kunt transformeren met behulp van Azure Stack Edge Pro, gaat u verder met de volgende zelfstudie:

> [!div class="nextstepaction"]
> [Gegevens transformeren met Azure Stack Edge Pro](./azure-stack-edge-j-series-deploy-configure-compute.md)