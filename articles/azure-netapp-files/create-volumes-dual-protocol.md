---
title: Een NFSv3-en SMB-volume (Dual-Protocol) maken voor Azure NetApp Files | Microsoft Docs
description: Hierin wordt beschreven hoe u een volume maakt dat gebruikmaakt van het dubbele Protocol van NFSv3 en SMB met ondersteuning voor LDAP-gebruikers toewijzing.
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
ms.date: 01/28/2020
ms.author: b-juche
ms.openlocfilehash: 0079c123f908a38cc1e4923790439f18352bf3ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100574626"
---
# <a name="create-a-dual-protocol-nfsv3-and-smb-volume-for-azure-netapp-files"></a>Een NFSv3-en SMB-volume (Dual-Protocol) maken voor Azure NetApp Files

Azure NetApp Files biedt ondersteuning voor het maken van volumes met behulp van NFS (NFSv3 en NFSv 4.1), SMB3 of het dubbele protocol. In dit artikel wordt beschreven hoe u een volume maakt dat gebruikmaakt van het dubbele Protocol van NFSv3 en SMB met ondersteuning voor LDAP-gebruikers toewijzing.  


## <a name="before-you-begin"></a>Voordat u begint 

* U moet al een capaciteits groep hebben gemaakt.  
    Zie [een capaciteits pool instellen](azure-netapp-files-set-up-capacity-pool.md).   
* Er moet een subnet zijn gedelegeerd aan Azure NetApp Files.  
    Zie [een subnet delegeren aan Azure NetApp files](azure-netapp-files-delegate-subnet.md).

## <a name="considerations"></a>Overwegingen

* Zorg ervoor dat u voldoet aan de [vereisten voor Active Directory verbindingen](create-active-directory-connections.md#requirements-for-active-directory-connections). 
* Maak een zone voor reverse lookup op de DNS-server en voeg vervolgens een PTR-record (pointer) van de AD-hostcomputer toe aan de zone voor reverse lookup. Als dat niet het geval is, mislukt het maken van het volume met twee protocollen.
* Zorg ervoor dat de NFS-client up-to-date is en de meest recente updates voor het besturingssysteem worden uitgevoerd.
* Zorg ervoor dat de Active Directory (AD) LDAP-server op de AD actief is. U kunt dit doen door de functie [Active Directory Lightweight Directory Services (AD LDS)](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/hh831593(v=ws.11)) op de AD-machine te installeren en configureren.
* Dual-protocol volumes bieden momenteel geen ondersteuning voor Azure Active Directory Domain Services (AADDS).  
* De NFS-versie die door een volume met dubbele protocollen wordt gebruikt, is NFSv3. Daarom gelden de volgende overwegingen:
    * Het dubbele protocol biedt geen ondersteuning voor de uitgebreide kenmerken van Windows-ACL'S `set/get` van NFS-clients.
    * NFS-clients kunnen geen machtigingen wijzigen voor de NTFS-beveiligings stijl en Windows-clients kunnen geen machtigingen wijzigen voor volumes met UNIX-stijl Dual-protocol.   

    In de volgende tabel worden de beveiligings stijlen en hun effecten beschreven:  
    
    | Beveiligings stijl    | Clients die machtigingen kunnen wijzigen   | Machtigingen die clients kunnen gebruiken  | Resulterende effectief beveiligings stijl    | Clients die toegang hebben tot bestanden     |
    |-  |-  |-  |-  |-  |
    | `Unix`    | NFS   | NFSv3 modus-bits   | UNIX  | NFS en Windows   |
    | `Ntfs`    | Windows   | NTFS-Acl's     | NTFS  |NFS en Windows|
* UNIX-gebruikers die het volume van de NTFS-beveiligings stijl koppelen met behulp van NFS, worden geverifieerd als Windows-gebruiker `root` voor UNIX `root` en `pcuser` voor alle andere gebruikers. Zorg ervoor dat deze gebruikers accounts aanwezig zijn in uw Active Directory voordat u het volume koppelt bij het gebruik van NFS. 
* Als u grote topologieën hebt en u de `Unix` beveiligings stijl met een Dual-protocol volume of LDAP met uitgebreide groepen gebruikt, heeft Azure NetApp files mogelijk geen toegang meer tot alle servers in uw topologieën.  Als deze situatie zich voordoet, neemt u contact op met uw account team voor hulp.  <!-- NFSAAS-15123 --> 
* U hebt geen Server basis-CA-certificaat nodig voor het maken van een Dual-protocol volume. Het is alleen vereist als LDAP via TLS is ingeschakeld.


## <a name="create-a-dual-protocol-volume"></a>Een volume met dubbele protocollen maken

1.  Klik op de Blade **volumes** op de Blade capaciteits groepen. Klik op **+ Volume toevoegen** om een volume te maken. 

    ![Navigeren naar volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  Klik in het venster een volume maken op **maken** en geef informatie op over de volgende velden op het tabblad basis beginselen:   
    * **Volume naam**      
        Geef de naam op voor het volume dat u wilt maken.   

        Een volume naam moet uniek zijn binnen elke capaciteits groep. De naam moet minstens drie tekens bevatten. U kunt alle alfanumerieke tekens gebruiken.   

        U kunt niet gebruiken `default` of `bin` als de naam van het volume.

    * **Capaciteits pool**  
        Geef de capaciteits pool op waar u het volume wilt maken.

    * **Quota**  
        Geef de hoeveelheid logische opslag op die u wilt toewijzen aan het volume.  

        Het veld **Beschikbare quotum** toont hoeveel ongebruikte ruimte er is in de gekozen capaciteitspool, die u kunt gebruiken om een nieuw volume te maken. De grootte van het nieuwe volume mag niet groter zijn dan het beschikbare quotum.  

    * **Door Voer (MiB/S)**   
        Als het volume is gemaakt in een hand matige QoS-capaciteits groep, geeft u de door Voer op voor het volume.   

        Als het volume wordt gemaakt in een automatische QoS-capaciteits groep, is de waarde die wordt weer gegeven in dit veld (quotum x-door Voer van service niveau).   

    * **Virtueel netwerk**  
        Geef het virtuele Azure-netwerk (VNet) op van waaruit u toegang wilt krijgen tot het volume.  

        Het opgegeven VNet moet een subnet bevatten dat is gedelegeerd aan Azure NetApp Files. De Azure NetApp Files-service is alleen toegankelijk vanuit hetzelfde VNet, of vanuit een VNet in dezelfde regio als het volume via VNet-peering. U kunt het volume ook openen vanuit uw on-premises netwerk via een snelle route.   

    * **Subnet**  
        Geef het subnet op dat u wilt gebruiken voor het volume.  
        Het opgegeven subnet moet zijn gedelegeerd aan Azure NetApp Files. 
        
        Als u geen subnet hebt gedelegeerd, kunt u klikken op **Nieuwe maken** op de pagina Een volume maken. Geef vervolgens op de pagina Subnet maken de subnetgegevens op, en selecteer **Microsoft.NetApp/volumes** om het subnet te delegeren aan Azure NetApp Files. In elk Vnet kan slechts één subnet worden gedelegeerd aan Azure NetApp Files.   
 
        ![ Een volume maken](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Subnet maken](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Als u een bestaand momentopname beleid wilt Toep assen op het volume, klikt u op de **sectie Geavanceerd weer geven** om deze uit te vouwen, geeft u op of u het pad naar de moment opname wilt verbergen en selecteert u een momentopname beleid in de vervolg keuzelijst. 

        Zie voor meer informatie over het maken van een momentopname beleid [beleid voor moment opnamen beheren](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

        ![Geavanceerde selectie weer geven](../media/azure-netapp-files/volume-create-advanced-selection.png)

3. Klik op **Protocol** en voer de volgende acties uit:  
    * Selecteer **Dual-Protocol (NFSv3 en SMB)** als protocol type voor het volume.   

    * Geef het **pad** naar het volume voor het volume op.   
    Dit pad naar het volume is de naam van het gedeelde volume. De naam moet beginnen met een alfabetisch teken en moet uniek zijn binnen elk abonnement en elke regio.  

    * Geef de te gebruiken **beveiligings stijl** op: NTFS (standaard) of UNIX.

    * Optioneel kunt [u het export beleid voor het volume configureren](azure-netapp-files-configure-export-policy.md).

    ![Dual-protocol opgeven](../media/azure-netapp-files/create-volume-protocol-dual.png)

4. Klik op **beoordeling + maken** om de details van het volume te controleren. Klik vervolgens op **maken** om het volume te maken.

    Het volume dat u hebt gemaakt, wordt weer gegeven op de pagina volumes. 
 
    Een volume neemt het abonnement, de resourcegroep en de locatiekenmerken over van de bijbehorende capaciteitspool. U kunt de implementatiestatus van het volume controleren vanuit het tabblad Meldingen.

## <a name="manage-ldap-posix-attributes"></a>LDAP POSIX-kenmerken beheren

U kunt POSIX-kenmerken zoals UID, basismap en andere waarden beheren met de Active Directory MMC-module gebruikers en computers.  In het volgende voor beeld wordt de Active Directory kenmerk editor weer gegeven:  

![Active Directory kenmerk editor](../media/azure-netapp-files/active-directory-attribute-editor.png) 

U moet de volgende kenmerken voor LDAP-gebruikers en LDAP-groepen instellen: 
* Vereiste kenmerken voor LDAP-gebruikers:   
    `uid`: Alice, `uidNumber` : 139, `gidNumber` : 555, `objectClass` : posixAccount
* Vereiste kenmerken voor LDAP-groepen:   
    `objectClass`: "posixGroup", `gidNumber` : 555

## <a name="configure-the-nfs-client"></a>De NFS-client configureren 

Volg de instructies in [een NFS-client configureren voor Azure NetApp files](configure-nfs-clients.md) om de NFS-client te configureren.  

## <a name="next-steps"></a>Volgende stappen  

* [Een NFS-client voor Azure NetApp Files configureren](configure-nfs-clients.md)
* [Problemen met SMB-of Dual-protocol volumes oplossen](troubleshoot-dual-protocol-volumes.md)
