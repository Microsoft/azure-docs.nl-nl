---
title: Het standaard domein NFSv 4.1 configureren voor Azure NetApp Files | Microsoft Docs
description: Hierin wordt beschreven hoe u de NFS-client configureert voor het gebruik van NFSv 4.1 met Azure NetApp Files.
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
ms.date: 10/14/2020
ms.author: b-juche
ms.openlocfilehash: c3c853190d5f63bbe9012727d8b7b7ac91da135f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92072149"
---
# <a name="configure-nfsv41-default-domain-for-azure-netapp-files"></a>NFSv4.1-standaarddomein configureren voor Azure NetApp Files

NFSv4 introduceert het concept van een verificatie domein. Azure NetApp Files ondersteunt momenteel alleen basis gebruikers toewijzingen van de service aan de NFS-client. Als u de NFSv 4.1-functionaliteit met Azure NetApp Files wilt gebruiken, moet u de NFS-client bijwerken.

## <a name="default-behavior-of-usergroup-mapping"></a>Standaard gedrag van de toewijzing van gebruikers/groepen

Basis toewijzing wordt standaard ingesteld op de `nobody` gebruiker omdat het NFSv4-domein `localdomain` standaard is ingeschakeld. Wanneer u een Azure NetApp Files NFSv 4.1-volume als root koppelt, ziet u bestands machtigingen als volgt:  

![Standaard gedrag van de toewijzing van gebruikers/groepen voor NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-default-behavior-user-group-mapping.png)

Zoals in het bovenstaande voor beeld wordt weer gegeven, is de gebruiker voor de `file1` `root` standaard instelling, maar wordt het toegewezen `nobody` .  In dit artikel wordt beschreven hoe u de `file1` gebruiker instelt op `root` door de `idmap Domain` instelling te wijzigen in `defaultv4iddomain.com` .  

## <a name="steps"></a>Stappen 

1. Bewerk het `/etc/idmapd.conf` bestand op de NFS-client.   
    Verwijder de opmerking bij de regel `#Domain` (dat wil zeggen de `#` van de regel verwijderen) en wijzig de waarde `localdomain` in `defaultv4iddomain.com` . 

    Initiële configuratie: 
    
    ![Initiële configuratie voor NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-initial-config.png)

    Bijgewerkte configuratie:
    
    ![De configuratie voor NFSv 4.1 is bijgewerkt](../media/azure-netapp-files/azure-netapp-files-nfsv41-updated-config.png)

2. Ontkoppel alle momenteel gekoppelde NFS-volumes.
3. Werk het `/etc/idmapd.conf` bestand bij.
4. Start de `rpcbind` service op uw host ( `service rpcbind restart` ) opnieuw op of start de host gewoon opnieuw op.
5. Koppel de NFS-volumes waar nodig aan.   

    Zie [een volume koppelen of ontkoppelen voor virtuele Windows-of Linux-machines](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md). 

In het volgende voor beeld ziet u de resulterende gebruikers-/groeps wijziging: 

![Scherm afbeelding met een voor beeld van de resulterende wijziging van de gebruiker/groep.](../media/azure-netapp-files/azure-netapp-files-nfsv41-resulting-config.png)

Zoals in het voor beeld wordt getoond, is de gebruiker/groep nu gewijzigd van `nobody` in `root` .

## <a name="behavior-of-other-non-root-users-and-groups"></a>Gedrag van andere (niet-basis) gebruikers en-groepen

Azure NetApp Files ondersteunt lokale gebruikers (gebruikers die lokaal op een host zijn gemaakt) die machtigingen hebben die zijn gekoppeld aan bestanden of mappen in NFSv 4.1-volumes. De service biedt momenteel echter geen ondersteuning voor het toewijzen van de gebruikers/groepen op meerdere knoop punten. Gebruikers die zijn gemaakt op een host, kunnen daarom niet standaard worden toegewezen aan gebruikers die zijn gemaakt op een andere host. 

In het volgende voor beeld `Host1` heeft drie bestaande test gebruikers accounts ( `testuser01` , `testuser02` , `testuser03` ): 

![Scherm opname die laat zien dat host1 drie bestaande test gebruikers accounts heeft.](../media/azure-netapp-files/azure-netapp-files-nfsv41-host1-users.png)

`Host2`De test gebruikers accounts zijn niet gemaakt, maar hetzelfde volume is op beide hosts gekoppeld:

![Resulterende configuratie voor NFSv 4.1](../media/azure-netapp-files/azure-netapp-files-nfsv41-host2-users.png)

## <a name="next-step"></a>Volgende stap 

[Een volume voor Windows- of Linux-VM's koppelen of ontkoppelen](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)

