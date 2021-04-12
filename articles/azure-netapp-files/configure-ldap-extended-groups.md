---
title: Configureren voegt LDAP toe met uitgebreide groepen voor Azure NetApp Files NFS-volume toegang | Microsoft Docs
description: Hierin worden de overwegingen en stappen beschreven voor het inschakelen van LDAP met uitgebreide groepen wanneer u een NFS-volume maakt met behulp van Azure NetApp Files.
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
ms.date: 04/08/2021
ms.author: b-juche
ms.openlocfilehash: 9edf8c6eca223ece8728f9868ee9fe310c517ca9
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259707"
---
# <a name="configure-adds-ldap-with-extended-groups-for-nfs-volume-access"></a>Configureren voegt LDAP toe met uitgebreide groepen voor NFS-volume toegang

Wanneer u [een NFS-volume maakt](azure-netapp-files-create-volumes.md), hebt u de mogelijkheid om de functie LDAP met uitgebreide groepen (de **LDAP** -optie) in te scha kelen voor het volume. Met deze functie kunnen Active Directory LDAP-gebruikers en uitgebreide groepen (Maxi maal 1024 groepen) toegang krijgen tot het volume.  

In dit artikel worden de overwegingen en stappen beschreven voor het inschakelen van LDAP met uitgebreide groepen wanneer u een NFS-volume maakt.  

## <a name="considerations"></a>Overwegingen

* LDAP via TLS moet *niet* zijn ingeschakeld als u Azure Active Directory Domain Services (AADDS) gebruikt.  

* Als u de functie LDAP met uitgebreide groepen inschakelt, geeft [Kerberos-volumes](configure-kerberos-encryption.md) met LDAP-ondersteuning het bestands eigendom voor LDAP-gebruikers niet goed weer. Een bestand of map die door een LDAP-gebruiker is gemaakt, wordt standaard `root` als eigenaar ingesteld in plaats van de daad werkelijke LDAP-gebruiker. Het `root` account kan echter het eigendom van het bestand hand matig wijzigen met behulp van de opdracht `chown <username> <filename>` . 

* U kunt de instelling van de LDAP-optie (ingeschakeld of uitgeschakeld) niet wijzigen nadat u het volume hebt gemaakt.  

* De volgende tabel beschrijft de Time to Live (TTL)-instellingen voor de LDAP-cache. U moet wachten tot de cache is vernieuwd voordat u toegang tot een bestand of map via een client probeert te krijgen. Anders wordt een bericht over geweigerde toegang weer gegeven op de client. 

    |     Foutvoorwaarde    |     Oplossing    |
    |-|-|
    | Cache |  Standaardtime-out |
    | Lijst met groepslid maatschappen  | TTL van 24 uur  |
    | UNIX-groepen  | 24-uurs TTL, 1-minuut, negatieve TTL  |
    | UNIX-gebruikers  | 24-uurs TTL, 1-minuut, negatieve TTL  |

    Caches hebben een specifieke time-outperiode met de naam *time to Live*. Na de time-outperiode zijn de vermeldingen zo oud dat verouderde vermeldingen niet meer zijn. De *negatieve TTL* -waarde is waar een mislukte zoek actie zich voordoet om prestatie problemen te voor komen vanwege LDAP-query's voor objecten die mogelijk niet bestaan. "        

## <a name="steps"></a>Stappen

1. De functie LDAP met uitgebreide groepen is momenteel beschikbaar als preview-versie. Voordat u deze functie voor de eerste keer gebruikt, moet u de functie registreren:  

    1. De functie registreren:   

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapExtendedGroups
        ```

    2. Controleer de status van de functie registratie: 

        > [!NOTE]
        > Het **RegistrationState** kan `Registering` tot 60 minuten duren voordat de status wordt gewijzigd in `Registered` . Wacht tot de status is `Registered` voordat u doorgaat.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapExtendedGroups
        ```
        
    U kunt ook [Azure cli-opdrachten](/cli/azure/feature) gebruiken `az feature register` `az feature show` om de functie te registreren en de registratie status weer te geven. 

2. Voor LDAP-volumes is een Active Directory configuratie vereist voor LDAP-server instellingen. Volg de instructies in [vereisten voor Active Directory verbindingen](create-active-directory-connections.md#requirements-for-active-directory-connections) en [Maak een Active Directory verbinding](create-active-directory-connections.md#create-an-active-directory-connection) om Active Directory verbindingen te configureren op de Azure Portal.  

3. Zorg ervoor dat de Active Directory LDAP-server op de Active Directory actief is. 

4. LDAP NFS-gebruikers moeten bepaalde POSIX-kenmerken hebben op de LDAP-server. Stel de kenmerken voor LDAP-gebruikers en LDAP-groepen als volgt in: 

    * Vereiste kenmerken voor LDAP-gebruikers:   
        `uid: Alice`, `uidNumber: 139`, `gidNumber: 555`, `objectClass: user`
    * Vereiste kenmerken voor LDAP-groepen:   
        `objectClass: group`, `gidNumber: 555`

    U kunt POSIX-kenmerken beheren met de MMC-module Active Directory gebruikers en computers. In het volgende voor beeld wordt de Active Directory kenmerk editor weer gegeven:  

    ![Active Directory kenmerk editor](../media/azure-netapp-files/active-directory-attribute-editor.png) 

5. Als u een met LDAP geïntegreerde Linux-client wilt configureren, raadpleegt u [een NFS-client configureren voor Azure NetApp files](configure-nfs-clients.md).

6.  Volg de stappen in [een NFS-volume maken voor Azure NetApp files](azure-netapp-files-create-volumes.md) om een NFS-volume te maken. Schakel tijdens het proces voor het maken van het volume, onder het tabblad **protocol** , de optie **LDAP** in.   

    ![Scherm afbeelding met de optie een volume pagina maken met LDAP.](../media/azure-netapp-files/create-nfs-ldap.png)  

7. Optioneel: u kunt lokale NFS-client gebruikers die niet aanwezig zijn op de Windows LDAP-server inschakelen om toegang te krijgen tot een NFS-volume met LDAP waarvoor uitgebreide groepen zijn ingeschakeld. Hiervoor schakelt u de optie **lokale NFS-gebruikers met LDAP toestaan** als volgt in:
    1. Klik op **Active Directory verbindingen**.  Klik op een bestaande Active Directory verbinding op het context menu (de drie puntjes `…` ) en selecteer **bewerken**.  
    2. Selecteer in het venster **Active Directory instellingen bewerken** dat wordt weer gegeven, de optie **lokale NFS-gebruikers met LDAP toestaan** .  

    ![Scherm afbeelding met de optie lokale NFS-gebruikers met LDAP toestaan](../media/azure-netapp-files/allow-local-nfs-users-with-ldap.png)  

## <a name="next-steps"></a>Volgende stappen  

* [Een NFS-volume maken voor Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Active Directory verbindingen maken en beheren](create-active-directory-connections.md)
* [Problemen met LDAP-volumes oplossen](troubleshoot-ldap-volumes.md)
