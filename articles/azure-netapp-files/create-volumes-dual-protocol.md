---
title: Een NFSv3- en SMB-volume (dual-protocol) maken voor Azure NetApp Files | Microsoft Docs
description: Beschrijft hoe u een volume maakt dat gebruikmaakt van het dubbele protocol van NFSv3 en SMB met ondersteuning voor LDAP-gebruikerstoewijzing.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/19/2021
ms.author: b-juche
ms.openlocfilehash: c702c41228512eceebeaf45ccae709db38a85a51
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725674"
---
# <a name="create-a-dual-protocol-nfsv3-and-smb-volume-for-azure-netapp-files"></a>Een NFSv3- en SMB-volume (dual-protocol) maken voor Azure NetApp Files

Azure NetApp Files biedt ondersteuning voor het maken van volumes met NFS (NFSv3 en NFSv4.1), SMB3 of dual-protocol. In dit artikel wordt beschreven hoe u een volume maakt dat gebruikmaakt van het dubbele protocol van NFSv3 en SMB met ondersteuning voor LDAP-gebruikerstoewijzing. 

Zie Een NFS-volume maken [om NFS-volumes te maken.](azure-netapp-files-create-volumes.md) Zie Een SMB-volume maken [voor het maken van SMB-volumes.](azure-netapp-files-create-volumes-smb.md) 

## <a name="before-you-begin"></a>Voordat u begint 

* U moet al een capaciteitspool hebben gemaakt.  
    Zie [Een capaciteitspool instellen.](azure-netapp-files-set-up-capacity-pool.md)   
* Er moet een subnet zijn gedelegeerd aan Azure NetApp Files.  
    Zie [Een subnet delegeren aan Azure NetApp Files](azure-netapp-files-delegate-subnet.md).

## <a name="considerations"></a>Overwegingen

* Zorg ervoor dat u voldoet [aan de Vereisten voor Active Directory-verbindingen.](create-active-directory-connections.md#requirements-for-active-directory-connections) 
* Maak een zone voor reverse lookup op de DNS-server en voeg vervolgens een PTR-record (Pointer) toe van de AD-hostmachine in die zone voor reverse lookup. Anders mislukt het maken van het volume met twee protocollen.
* Zorg ervoor dat de NFS-client up-to-date is en de meest recente updates voor het besturingssysteem worden uitgevoerd.
* Zorg ervoor dat de Active Directory (AD) LDAP-server actief is en wordt uitgevoerd op de AD. U kunt dit doen door de functie Active Directory Lightweight Directory Services [(AD LDS)](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/hh831593(v=ws.11)) op de AD-machine te installeren en te configureren.
* Dual-protocolvolumes bieden momenteel geen ondersteuning voor Azure Active Directory Domain Services (AADDS). LDAP via TLS mag niet worden ingeschakeld als u AADDS gebruikt.
* De NFS-versie die wordt gebruikt door een volume met twee protocollen is NFSv3. Als zodanig zijn de volgende overwegingen van toepassing:
    * Dual-protocol biedt geen ondersteuning voor de uitgebreide Windows ACLS-kenmerken `set/get` van NFS-clients.
    * NFS-clients kunnen de machtigingen voor de NTFS-beveiligingsstijl niet wijzigen en Windows-clients kunnen de machtigingen voor UNIX-achtige dual-protocolvolumes niet wijzigen.   

    In de volgende tabel worden de beveiligingsstijlen en de effecten ervan beschreven:  
    
    | Beveiligingsstijl    | Clients die machtigingen kunnen wijzigen   | Machtigingen die clients kunnen gebruiken  | Resulterende effectieve beveiligingsstijl    | Clients die toegang hebben tot bestanden     |
    |-  |-  |-  |-  |-  |
    | `Unix`    | NFS   | NFSv3-modusbits   | UNIX  | NFS en Windows   |
    | `Ntfs`    | Windows   | NTFS ACL's     | NTFS  |NFS en Windows|
* UNIX-gebruikers die het NTFS-beveiligingsstijlvolume met behulp van NFS aan het volume monteren, worden geverifieerd als Windows-gebruiker voor UNIX en `root` `root` voor alle andere `pcuser` gebruikers. Zorg ervoor dat deze gebruikersaccounts aanwezig zijn in uw Active Directory voordat u het volume gaat koppelen wanneer u NFS gebruikt. 
* Als u grote topologies hebt en u de beveiligingsstijl gebruikt met een dual-protocol volume of LDAP met uitgebreide groepen, hebben Azure NetApp Files mogelijk geen toegang tot alle servers in uw `Unix` topologies.  Als deze situatie zich voordoet, neem dan contact op met uw accountteam voor hulp.  <!-- NFSAAS-15123 --> 
* U hebt geen basis-CA-certificaat voor de server nodig voor het maken van een volume met dubbele protocollen. Dit is alleen vereist als LDAP via TLS is ingeschakeld.


## <a name="create-a-dual-protocol-volume"></a>Een volume met dubbele protocollen maken

1.  Klik op de blade **Volumes** op de blade Capaciteitspools. Klik op **+ Volume toevoegen** om een volume te maken. 

    ![Navigeer naar Volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  Klik in het venster Een volume maken op **Maken** en geef informatie op voor de volgende velden op het tabblad Basisinformatie:   
    * **Volumenaam**      
        Geef de naam op voor het volume dat u wilt maken.   

        Een volumenaam moet uniek zijn binnen elke capaciteitspool. De naam moet minstens drie tekens bevatten. U kunt alle alfanumerieke tekens gebruiken.   

        U kunt of `default` niet gebruiken als de `bin` volumenaam.

    * **Capaciteitspool**  
        Geef de capaciteitspool op waar u het volume wilt maken.

    * **Quotum**  
        Geef de hoeveelheid logische opslag op die u wilt toewijzen aan het volume.  

        Het veld **Beschikbare quotum** toont hoeveel ongebruikte ruimte er is in de gekozen capaciteitspool, die u kunt gebruiken om een nieuw volume te maken. De grootte van het nieuwe volume mag niet groter zijn dan het beschikbare quotum.  

    * **Doorvoer (MiB/S)**   
        Als het volume is gemaakt in een handmatige QoS-capaciteitspool, geeft u de doorvoer op die u voor het volume wilt gebruiken.   

        Als het volume wordt gemaakt in een automatische QoS-capaciteitspool, is de waarde die in dit veld wordt weergegeven (quotum x doorvoer op serviceniveau).   

    * **Virtueel netwerk**  
        Geef het virtuele Azure-netwerk (VNet) op van waaruit u het volume wilt openen.  

        Het opgegeven VNet moet een subnet bevatten dat is gedelegeerd aan Azure NetApp Files. De Azure NetApp Files-service is alleen toegankelijk vanuit hetzelfde VNet, of vanuit een VNet in dezelfde regio als het volume via VNet-peering. U kunt het volume ook openen vanuit uw on-premises netwerk via Express Route.   

    * **Subnet**  
        Geef het subnet op dat u wilt gebruiken voor het volume.  
        Het opgegeven subnet moet zijn gedelegeerd aan Azure NetApp Files. 
        
        Als u geen subnet hebt gedelegeerd, kunt u klikken op **Nieuwe maken** op de pagina Een volume maken. Geef vervolgens op de pagina Subnet maken de subnetgegevens op, en selecteer **Microsoft.NetApp/volumes** om het subnet te delegeren aan Azure NetApp Files. In elk VNet kan slechts één subnet worden gedelegeerd aan Azure NetApp Files.   
 
        ![ Een volume maken](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Subnet maken](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Als u een bestaand momentopnamebeleid wilt  toepassen op het volume, klikt u op de sectie Geavanceerd weergeven om het uit te vouwen, geeft u op of u het momentopnamepad wilt verbergen en selecteert u een momentopnamebeleid in het pull-downmenu. 

        Zie Beleid voor momentopnamen beheren voor meer informatie over het maken [van een beleid voor momentopnamen.](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)

        ![Geavanceerde selectie tonen](../media/azure-netapp-files/volume-create-advanced-selection.png)

3. Klik op **Protocol** en voer de volgende acties uit:  
    * Selecteer **dual-protocol (NFSv3 en SMB)** als het protocoltype voor het volume.   

    * Geef het **volumepad** voor het volume op.   
    Dit volumepad is de naam van het gedeelde volume. De naam moet beginnen met een alfabetisch teken en moet uniek zijn binnen elk abonnement en elke regio.  

    * Geef de **te gebruiken beveiligingsstijl** op: NTFS (standaard) of UNIX.

    * Als u SMB3-protocolversleuteling wilt inschakelen voor het volume met dubbele protocollen, selecteert u **SMB3-protocolversleuteling inschakelen.**   

        Met deze functie wordt alleen versleuteling voor in-flight SMB3-gegevens mogelijk. NFSv3-gegevens die worden verzonden, worden niet versleuteld. SMB-clients die geen SMB3-versleuteling gebruiken, hebben geen toegang tot dit volume. Data-at-rest wordt versleuteld, ongeacht deze instelling. Zie [Veelgestelde vragen over SMB-versleuteling](azure-netapp-files-faqs.md#smb-encryption-faqs) voor meer informatie. 

        De **functie SMB3-protocolversleuteling** is momenteel beschikbaar als preview-versie. Als dit de eerste keer is dat u deze functie gebruikt, registreert u de functie voordat u deze gebruikt: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBEncryption
        ```

        Controleer de status van de functieregistratie: 

        > [!NOTE]
        > De **RegistrationState** kan maximaal 60 minuten in de status zijn `Registering` voordat u in verandert in `Registered` . Wacht totdat de status is `Registered` voordat u doorgaat.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBEncryption
        ```
        
        U kunt ook [Azure CLI-opdrachten en gebruiken](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) om de functie te registreren en de `az feature register` `az feature show` registratiestatus weer te geven.  

    * Configureer desgewenst [exportbeleid voor het volume](azure-netapp-files-configure-export-policy.md).

    ![Dual-protocol opgeven](../media/azure-netapp-files/create-volume-protocol-dual.png)

4. Klik **op Controleren en maken om** de volumedetails te controleren. Klik vervolgens **op Maken** om het volume te maken.

    Het volume dat u hebt gemaakt, wordt weergegeven op de pagina Volumes. 
 
    Een volume neemt het abonnement, de resourcegroep en de locatiekenmerken over van de bijbehorende capaciteitspool. U kunt de implementatiestatus van het volume controleren vanuit het tabblad Meldingen.

## <a name="allow-local-nfs-users-with-ldap-to-access-a-dual-protocol-volume"></a>Lokale NFS-gebruikers met LDAP toegang geven tot een volume met twee protocollen 

U kunt inschakelen dat lokale NFS-clientgebruikers niet aanwezig zijn op de Windows LDAP-server voor toegang tot een volume met dubbele protocollen met LDAP met uitgebreide groepen ingeschakeld. Schakel de optie Lokale **NFS-gebruikers met LDAP** toestaan als volgt in:

1. Klik **op Active Directory-verbindingen.**  Klik op een bestaande Active Directory-verbinding op het contextmenu (de drie punten `…` ) en selecteer **Bewerken.**  

2. Selecteer in **het venster Active Directory-instellingen** bewerken dat wordt weergegeven de optie **Lokale NFS-gebruikers met LDAP toestaan.**  

    ![Schermopname van de optie Lokale NFS-gebruikers met LDAP toestaan](../media/azure-netapp-files/allow-local-nfs-users-with-ldap.png)  


## <a name="manage-ldap-posix-attributes"></a>LDAP POSIX-kenmerken beheren

U kunt POSIX-kenmerken, zoals UID, Home Directory en andere waarden, beheren met behulp van de MMC-module Active Directory: gebruikers en computers MMC.  In het volgende voorbeeld ziet u de Active Directory Attribute Editor:  

![Active Directory-kenmerkeditor](../media/azure-netapp-files/active-directory-attribute-editor.png) 

U moet de volgende kenmerken instellen voor LDAP-gebruikers en LDAP-groepen: 
* Vereiste kenmerken voor LDAP-gebruikers:   
    `uid: Alice`, `uidNumber: 139`, `gidNumber: 555`, `objectClass: posixAccount`
* Vereiste kenmerken voor LDAP-groepen:   
    `objectClass: posixGroup`, `gidNumber: 555`

## <a name="configure-the-nfs-client"></a>De NFS-client configureren 

Volg de instructies in [Een NFS-client configureren voor Azure NetApp Files](configure-nfs-clients.md) NFS-client te configureren.  

## <a name="next-steps"></a>Volgende stappen  

* [Een NFS-client voor Azure NetApp Files configureren](configure-nfs-clients.md)
* [Problemen met SMB- of dual-protocolvolumes oplossen](troubleshoot-dual-protocol-volumes.md)
* [Problemen met LDAP-volumes oplossen](troubleshoot-ldap-volumes.md)
