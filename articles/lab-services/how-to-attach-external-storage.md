---
title: Externe bestands opslag gebruiken in Lab-Services | Microsoft Docs
description: Meer informatie over het instellen van een lab dat gebruikmaakt van externe bestands opslag in Lab-Services.
author: emaher
ms.topic: article
ms.date: 03/30/2021
ms.author: enewman
ms.openlocfilehash: 888e04db76567051f8c5eae7cf94c77e684cb146
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111636"
---
# <a name="using-external-file-storage-in-lab-services"></a>Externe bestands opslag gebruiken in test services

In dit artikel vindt u enkele van de opties voor externe bestands opslag bij gebruik van Azure Lab Services.  [Azure files](https://azure.microsoft.com/services/storage/files/) biedt volledig beheerde bestands shares in de cloud die [toegankelijk zijn via SMB 2,1 en SMB 3,0.](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows)  Een Azure Files share kan openbaar of privé in een virtueel netwerk zijn aangesloten.  Het kan ook worden geconfigureerd om de AD-referenties van student te gebruiken om verbinding te maken met de bestands share.  Het gebruik van Azure NetApp Files met NFS-volumes voor Linux-machines is een andere optie voor externe bestands opslag met Azure Lab Services.  

## <a name="deciding-which-solution-to-use"></a>Bepalen welke oplossing u moet gebruiken

Elke oplossing heeft andere vereisten en mogelijkheden.  De volgende tabel bevat belang rijke punten die u kunt overwegen voor elke oplossing.  

|     Oplossing    |    Belang rijk om te weten    |
| -------------- | ------------------------ |
| [Azure Files delen met openbaar eind punt](#azure-files-share) | <ul><li>Iedereen heeft lees-en schrijf toegang.</li><li>Geen vnet-peering vereist.</li><li>Toegankelijk voor alle Vm's, niet alleen voor Lab-Vm's.</li><li>Als u Linux gebruikt, hebben studenten toegang tot de sleutel van het opslag account.</li></ul> |
| [Azure Files delen met een persoonlijk eind punt](#azure-files-share) | <ul><li>Iedereen heeft lees-en schrijf toegang.</li><li>Vnet-peering is vereist.</li><li>Alleen toegankelijk voor Vm's op hetzelfde netwerk (of een peered netwerk) als een opslag account.</li><li>Als u Linux gebruikt, hebben studenten toegang tot de sleutel van het opslag account.</li></ul> |
| [Azure Files met autorisatie van de identiteits basis](#azure-files-with-identity-base-authorization) | <ul><li>Lees-of lees-en schrijf machtigingen kunnen worden ingesteld voor de map of het bestand.</li><li>Vnet-peering is vereist.</li><li>Het opslag account moet zijn verbonden met Active Directory.</li><li>Lab-Vm's moeten lid zijn van een domein.</li><li>De sleutel van het opslag account die door studenten niet wordt gebruikt om verbinding te maken met de bestands share.</li></ul> |
| [NetApp-bestanden met NFS-volumes](#netapp-files-with-nfs-volumes) | <ul><li>De toegang lezen of lezen/schrijven kan worden ingesteld voor volumes.</li><li>Machtigingen worden ingesteld met behulp van het IP-adres van student VM.</li><li>Vnet-peering is vereist.</li><li>Mogelijk moet u zich registreren voor het gebruik van de NetApp-bestanden service.</li><li>Alleen Linux.</li></ul>

De kosten voor het gebruik van externe opslag zijn niet inbegrepen in de kosten van het gebruik van Azure Lab Services.  Zie [Azure files prijzen](https://azure.microsoft.com/pricing/details/storage/files/) en [Azure NetApp files prijzen](https://azure.microsoft.com/pricing/details/netapp/)voor meer informatie over prijzen.

## <a name="azure-files-share"></a>Azure Files share

Azure-bestands shares worden geopend met behulp van een openbaar of persoonlijk eind punt.  Azure-bestands shares worden gekoppeld met behulp van de sleutel van het opslag account als wacht woord.  Met deze benadering heeft iedereen lees-/schrijftoegang tot de bestands share.

Als u een openbaar eind punt gebruikt voor de Azure Files share, is het belang rijk om het volgende te onthouden:

- Het virtuele netwerk voor het opslag account hoeft niet te worden gekoppeld aan het lab-account.  De bestands share kan op elk moment worden gemaakt voordat de sjabloon-VM wordt gepubliceerd.
- De bestands share is toegankelijk vanaf elke computer als een gebruiker beschikt over de sleutel van het opslag account.  
- Linux-studenten kunnen de sleutel voor het opslag account zien.  Referenties voor het koppelen van een Azure Files share worden opgeslagen `{file-share-name}.cred` op virtuele Linux-machines en kunnen worden gelezen door sudo.  Omdat studenten standaard sudo toegang hebben in virtuele machines van Azure Lab service, kunnen ze de sleutel van het opslag account lezen.  Als het eind punt van het opslag account openbaar is, kunnen studenten toegang krijgen tot de bestands share buiten hun student-VM.  Overweeg de sleutel voor het opslag account te draaien nadat de klasse is beëindigd en persoonlijke bestands shares worden gebruikt.

Als u een persoonlijk eind punt gebruikt voor de Azure Files share, is het belang rijk om het volgende te onthouden:

- De toegang is beperkt tot verkeer dat afkomstig is van het particuliere netwerk en waartoe geen toegang kan worden verkregen via het open bare Internet.  Alleen Vm's in het particuliere virtuele netwerk hebben Vm's in een netwerk dat is gekoppeld aan het particuliere virtuele netwerk of computers die zijn verbonden met een VPN voor het particuliere netwerk toegang tot de bestands share.  
- Linux-studenten kunnen de sleutel voor het opslag account zien.  Referenties voor het koppelen van een Azure Files share worden opgeslagen `{file-share-name}.cred` op virtuele Linux-machines en kunnen worden gelezen door sudo.  Omdat studenten standaard sudo toegang hebben in virtuele machines van Azure Lab service, kunnen ze de sleutel van het opslag account lezen.  Overweeg om de sleutel van het opslag account te roteren nadat de klasse is afgelopen.
- Voor deze benadering moet het virtuele netwerk van de bestands share worden gekoppeld aan het lab-account.  Het virtuele netwerk voor het Azure Storage account moet worden gekoppeld aan het virtuele netwerk voor het lab-account **voordat** het lab wordt gemaakt.

> [!NOTE]
> Bestands shares die groter zijn dan 5 TB zijn alleen beschikbaar voor [[lokaal redundante opslag (LRS)-accounts]](/azure/storage/files/storage-files-how-to-create-large-file-share#restrictions).

Volg deze stappen om een virtuele machine te maken die is verbonden met een Azure-bestands share.

1. Maak een [Azure Storage-account](/azure/storage/files/storage-how-to-create-file-share). Kies op de pagina connectiviteits methode het open bare eind punt of persoonlijk eind punt.
2. Als u gebruikt, maakt u een [persoonlijk eind punt](/azure/private-link/create-private-endpoint-storage-portal) zodat de bestands shares toegankelijk zijn vanuit het virtuele netwerk.  Maak een [privé-DNS-zone](/azure/dns/private-dns-privatednszone) of gebruik een bestaande. Persoonlijke Azure DNS zones bieden naam omzetting binnen een virtueel netwerk.
3. Maak een [Azure-bestands share](/azure/storage/files/storage-how-to-create-file-share). De bestands share is bereikbaar voor de naam van de open bare host van het opslag account.
4. Koppel de Azure-bestands share in de sjabloon-VM:
    - [Windows](/azure/storage/files/storage-how-to-use-files-windows)
    - [[Linux]](/azure/storage/files/storage-how-to-use-files-linux).  Zie [Azure files gebruiken met Linux](#using-azure-files-with-linux) om te voor komen dat er problemen zijn met het koppelen van vm's van studenten.
5. [Publiceer](how-to-create-manage-template.md#publish-the-template-vm) de sjabloon-VM.

> [!IMPORTANT]
> Zorg ervoor dat de firewall uitgaande SMB-verbinding niet blokkeert via poort 445. Standaard is SMB toegestaan voor virtuele Azure-machines.

### <a name="using-azure-files-with-linux"></a>Azure Files met Linux gebruiken

Als u de _standaard instructies_ gebruikt voor het koppelen van een Azure-bestands share, lijkt de bestands share te verdwijnen op vm's van studenten zodra de sjabloon is gepubliceerd.  Hieronder wordt een script gewijzigd waarmee dit probleem wordt opgelost.  

```bash
#!/bin/bash

# Assign variables values for your storage account and file share
storage_account_name=""
storage_account_key=""
fileshare_name=""

# Do not use 'mnt' for mount directory.
# Using ‘mnt’ will cause issues on student VMs.
mount_directory="prm-mnt" 

sudo mkdir /$mount_directory/$fileshare_name
if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
fi
if [ ! -f "/etc/smbcredentials/$storage_account_name.cred" ]; then
    sudo bash -c "echo ""username=$storage_account_name"" >> /etc/smbcredentials/$storage_account_name.cred"
    sudo bash -c "echo ""password=$storage_account_key"" >> /etc/smbcredentials/$storage_account_name.cred"
fi
sudo chmod 600 /etc/smbcredentials/$storage_account_name.cred

sudo bash -c "echo ""//$storage_account_name.file.core.windows.net/$fileshare_name /$mount_directory/$fileshare_name cifs nofail,vers=3.0,credentials=/etc/smbcredentials/$storage_account_name.cred,dir_mode=0777,file_mode=0777,serverino"" >> /etc/fstab"
sudo mount -t cifs //$storage_account_name.file.core.windows.net/$fileshare_name /$mount_directory/$fileshare_name -o vers=3.0,credentials=/etc/smbcredentials/$storage_account_name.cred,dir_mode=0777,file_mode=0777,serverino
```

Als de sjabloon-VM al is gepubliceerd die de Azure-bestands share koppelt aan de `/mnt` Directory, kan de student een van de volgende handelingen uitvoeren.

- Verplaats de instructie om `/mnt` deze aan de bovenkant van het `/etc/fstab` bestand te koppelen.  
- Wijzig de instructie om deze `/mnt/{file-share-name}` te koppelen aan een andere Directory, zoals `/prm-mnt/{file-share-name}` .

Studenten moeten `mount -a` worden uitgevoerd om mappen opnieuw te koppelen.

Zie [Azure files gebruiken met Linux](/azure/storage/files/storage-how-to-use-files-linux)voor meer algemene informatie over het gebruik van bestands shares met Linux.

## <a name="azure-files-with-identity-base-authorization"></a>Azure Files met autorisatie van de identiteits basis

Azure Files shares kunnen ook worden geopend met behulp van AD-verificatie als

1. De student-VM is lid van een domein.
2. AD-verificatie is [ingeschakeld op het Azure Storage-account](/azure/storage/files/storage-files-active-directory-overview) dat als host fungeert voor de bestands share.  

Het netwerk station is gekoppeld aan de virtuele machine met behulp van de identiteit van de gebruiker, niet voor de sleutel van het opslag account.  Toegang tot het opslag account kan open bare of persoonlijke eind punten gebruiken.

Laten we eens kijken naar enkele belang rijke punten van deze benadering.

- Machtigingen kunnen worden ingesteld op een map-of bestands niveau.
- De huidige gebruikers referenties worden gebruikt voor verificatie bij de bestands share.

Als u een openbaar eind punt gebruikt voor de Azure Files share, is het belang rijk om het volgende te onthouden:

- Het virtuele netwerk voor het opslag account hoeft niet te worden gekoppeld aan het lab-account.  De bestands share kan op elk moment worden gemaakt voordat de sjabloon-VM wordt gepubliceerd.

Als u een persoonlijk eind punt gebruikt voor de Azure Files share, is het belang rijk om het volgende te onthouden:

- De toegang is beperkt tot verkeer dat afkomstig is van het particuliere netwerk en waartoe geen toegang kan worden verkregen via het open bare Internet.  Alleen Vm's in het particuliere virtuele netwerk hebben Vm's in een netwerk dat is gekoppeld aan het particuliere virtuele netwerk of computers die zijn verbonden met een VPN voor het particuliere netwerk toegang tot de bestands share.  
- Voor deze benadering moet het virtuele netwerk van de bestands share worden gekoppeld aan het lab-account.  Het virtuele netwerk voor het Azure Storage account moet worden gekoppeld aan het virtuele netwerk voor het lab-account **voordat** het lab wordt gemaakt.

Volg de onderstaande stappen voor het maken van een AD-verificatie Azure Files share en domein worden toegevoegd aan de Lab-Vm's.

1. Maak een [Azure Storage-account](/azure/storage/files/storage-how-to-create-file-share).
2. Als u gebruikt, maakt u een [persoonlijk eind punt](/azure/private-link/create-private-endpoint-storage-portal) zodat de bestands shares toegankelijk zijn vanuit het virtuele netwerk.  Maak een [privé-DNS-zone](/azure/dns/private-dns-privatednszone) of gebruik een bestaande. Persoonlijke Azure DNS zones bieden naam omzetting binnen een virtueel netwerk.
3. Maak een [Azure-bestands share](/azure/storage/files/storage-how-to-create-file-share).
4. Volg de stappen om autorisatie op basis van een identiteit in te scha kelen.  Als u gebruikmaakt van on-premises AD die is gesynchroniseerd met Azure AD, volgt u de stappen voor [on-premises Active Directory Domain Services authenticatie via SMB voor Azure-bestands shares](/azure/storage/files/storage-files-identity-auth-active-directory-enable).  Als u alleen Azure AD gebruikt, voert u de stappen uit om [Azure Active Directory Domain Services verificatie in te scha kelen op Azure files](/azure/storage/files/storage-files-identity-auth-active-directory-domain-service-enable).
    >[!IMPORTANT]
    >Neem contact op met het team dat uw AD beheert om te controleren of aan alle vereisten in de instructies wordt voldaan.
5. Machtigings rollen voor SMB-shares toewijzen in Azure.  Zie [machtigingen op share niveau](/azure/storage/files/storage-files-identity-ad-ds-assign-permissions)voor meer informatie over de machtigingen die aan elke rol worden verleend.
    1. De rol opslag bestands gegevens SMB-share met verhoogde bevoegdheid voor Inzender delen moet worden toegewezen aan de persoon of groep die machtigingen voor de inhoud van de bestands share instelt.
    2. De rol ' opslag bestands gegevens SMB delen bijdrager ' moet worden toegewezen aan studenten die bestanden moeten toevoegen of bewerken op de bestands share.
    3. De rol ' opslag bestands gegevens SMB share lezer ' moet worden toegewezen aan studenten waarvoor alleen de bestanden van de bestands share moeten worden gelezen.
6. Stel machtigingen in op map/bestands niveau voor de bestands share.  Machtigingen moeten worden ingesteld op een computer die lid is van een domein en die netwerk toegang heeft tot de bestands share.  **Als u machtigingen op Directory-of bestands niveau wilt wijzigen, koppelt u de bestands share met de opslag sleutel, niet uw AAD-referenties.**  [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl) Power shell-opdracht of [icacls](/windows-server/administration/windows-commands/icacls) zijn de aanbevolen hulpprogram ma's voor het toewijzen van machtigingen.
7. [Vergelijkt het virtuele netwerk](how-to-connect-peer-virtual-network.md) voor het opslag account met het lab-account.
8. [Maak het leslokaal Lab](how-to-manage-classroom-labs.md).
9. Sla een script op de sjabloon-VM op die studenten kunnen uitvoeren om verbinding te maken met het netwerk station.  Voorbeeld script ophalen:
    1. Open het opslagaccount in Azure Portal.
    1. Selecteer **Bestands shares** onder **Bestands service** in het menu.
    1. Zoek de share waarmee u verbinding wilt maken, selecteer de knop met weglatings tekens helemaal rechts en kies **verbinding maken**.
    1. De **verbinding maken** wordt geopend met instructies voor Windows, Linux en macOS.  Als u Windows gebruikt, stelt u de **verificatie methode** in op **Active Directory**.
    1. Kopieer de code in het voor beeld en sla deze op de sjabloon computer op in een `.ps1` bestand voor Windows of `.sh` bestand voor Linux.
10. Down load en voer script uit op de sjabloon computer om [studenten computers samen te voegen met het domein](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Scripts/ActiveDirectoryJoin/README.md#usage).  Met het `Join-AzLabADTemplate` script wordt [de VM van de sjabloon automatisch gepubliceerd](how-to-create-manage-template.md#publish-the-template-vm) .  
    > [!NOTE]
    > De sjabloon machine is geen lid van een domein. Docenten moeten een gepubliceerde student-VM voor zichzelf toewijzen om bestanden op de share te bekijken.
11. Studenten die gebruikmaken van Windows kunnen verbinding maken met de Azure Files share via [Verkenner](/azure/storage/files/storage-how-to-use-files-windows) met hun referenties zodra het pad naar de bestands share is opgegeven.  Studenten kunnen ook het script uitvoeren dat hierboven is gemaakt om verbinding te maken met het netwerk station.  Voor studenten die Linux gebruiken, voert u het script uit dat hierboven is gemaakt.

## <a name="netapp-files-with-nfs-volumes"></a>NetApp-bestanden met NFS-volumes

[Azure NetApp files](https://azure.microsoft.com/services/netapp/) is een hoogwaardige, met data limiet gecontroleerde File Storage-service.

- Toegangs beleid kan per volume worden ingesteld.
- Machtigings beleid is gebaseerd op een IP-adres voor elk volume.
- Als studenten een eigen volume nodig hebben dat andere studenten geen toegang hebben, moeten er machtigings beleid worden toegewezen nadat het lab is gepubliceerd.
- In de context van Azure Lab Services worden alleen Linux-machines ondersteund.
- Het virtuele netwerk voor de Azure NetApp Files capaciteits groep moet worden gekoppeld aan het virtuele netwerk van het lab-account **voordat** het lab wordt gemaakt.

Volg de onderstaande stappen om een Azure NetApp Files share in Azure Lab Services te gebruiken.

1. Onboarding naar [Azure NetApp files](https://aka.ms/azurenetappfiles), indien nodig.
2. Zie [Azure NetApp files-en NFS-volume instellen](/azure/azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes)voor het maken van een NetApp-bestands capaciteits groep en NFS-volume (s).  Zie [service niveaus voor Azure NetApp files](/azure/azure-netapp-files/azure-netapp-files-service-levels)voor meer informatie over service niveaus.
3. [Vergelijkt het virtuele netwerk](how-to-connect-peer-virtual-network.md) voor de NetApp-bestands capaciteits groep met het lab-account.
4. [Maak het leslokaal Lab](how-to-manage-classroom-labs.md).
5. Installeer de vereiste onderdelen voor het gebruik van NFS-bestands shares op de sjabloon-VM.
    - Ubuntu

        ```bash
        sudo apt update
        sudo apt install nfs-common
        ```

    - CentOS

        ```bash
        sudo yum install nfs-utils
        ```

6. Sla op de sjabloon-VM hieronder script op als `mount_fileshare.sh` om [de NetApp-bestands share te koppelen](/azure/azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines).  Wijs de `capacity_pool_ipaddress` variabele het IP-adres van het koppel doel voor de capaciteits groep toe.  Haal de koppelings instructies voor het volume op om de juiste waarde te vinden.  Het script verwacht de pad/naam van het NetApp-bestanden volume.  Vergeet niet om `chmod u+x mount_fileshare.sh` ervoor te zorgen dat het script kan worden uitgevoerd door gebruikers.

    ```bash
    #!/bin/bash
    
    if [ $# -eq 0 ];  then
        echo "Must provide volume name."
        exit 1
    fi
    
    volume_name=$1
    capacity_pool_ipaddress=0.0.0.0 # IP address of capacity pool
    
    # Do not use 'mnt' for mount directory.
    # Using ‘mnt’ may cause issues on student VMs.
    mount_directory="prm-mnt" 
    
    sudo mkdir -p /$mount_directory
    sudo mkdir /$mount_directory/$folder_name
    
    sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp $capacity_pool_ipaddress:/$volume_name /$mount_directory/$volume_name
    sudo bash -c "echo ""$capacity_pool_ipaddress:/$volume_name /$mount_directory/$volume_name nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0"" >> /etc/fstab"
    ```

7. Als alle studenten toegang delen tot hetzelfde volume met NetApp-bestanden, `mount_fileshare.sh` kan het script worden uitgevoerd op de sjabloon machine voordat het wordt gepubliceerd.  Als studenten elk hun eigen volume krijgen, slaat u het script op dat later door de student wordt uitgevoerd.
8. [Publiceer](how-to-create-manage-template.md#publish-the-template-vm) de sjabloon-VM.
9. [Beleid](/azure/azure-netapp-files/azure-netapp-files-configure-export-policy) voor bestands share configureren.  Met het export beleid kunt u toestaan dat één virtuele machine of meerdere virtuele machines toegang hebben tot een volume.  Alleen-lezen of lezen/schrijven-toegang kan worden verleend.
10. Studenten moeten hun VM starten en het script uitvoeren om de bestands share te koppelen.  Ze hoeven het script maar één keer uit te voeren.  De opdracht ziet er als volgt uit `./mount_fileshare.sh myvolumename` .

## <a name="next-steps"></a>Volgende stappen

De volgende stappen zijn gebruikelijk voor het instellen van elk lab.

- [Een sjabloon maken en beheren](how-to-create-manage-template.md)
- [Gebruikers toevoegen](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quota instellen](how-to-configure-student-usage.md#set-quotas-for-users)
- [Een planning instellen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [E-mail registratie koppelingen naar studenten](how-to-configure-student-usage.md#send-invitations-to-users)