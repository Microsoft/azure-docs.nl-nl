---
title: Azure NetApp Files-volumes koppelen voor virtuele machines
description: Meer informatie over het koppelen of ontkoppelen van een volume voor virtuele Windows-machines of virtuele Linux-machines in Azure.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 83d6e051f520737e750e6c46c192eb698e7bf0e3
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842254"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Een volume voor Windows- of Linux-VM's koppelen of ontkoppelen 

U kunt indien nodig een volume koppelen of ontkoppelen voor virtuele Windows-of Linux-machines.  De koppelings instructies voor virtuele Linux-machines zijn beschikbaar op Azure NetApp Files.  

## <a name="requirements"></a>Vereisten 

* U moet ten minste één export beleid hebben om toegang te kunnen krijgen tot een NFS-volume.
* Als u een NFS-volume met succes wilt koppelen, moet u ervoor zorgen dat de volgende NFS-poorten zijn geopend tussen de client en de NFS-volumes:
    * 111 TCP/UDP = `RPCBIND/Portmapper`
    * 635 TCP/UDP = `mountd`
    * 2049 TCP/UDP = `nfs`
    * 4045 TCP/UDP = `nlockmgr` (alleen NFSv3)
    * 4046 TCP/UDP = `status` (alleen NFSv3)

## <a name="steps"></a>Stappen

1. Klik op de Blade **volumes** en selecteer vervolgens het volume dat u wilt koppelen. 
2. Klik op **koppelings instructies** van het geselecteerde volume en volg de instructies om het volume te koppelen. 

    ![Koppelings instructies NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![Koppelings instructies SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)  
    * Als u een NFS-volume koppelt, moet u ervoor zorgen dat u de `vers` optie in de `mount` opdracht gebruikt om de NFS-protocol versie op te geven die overeenkomt met het volume dat u wilt koppelen. 
    * Als u NFSv 4.1 gebruikt, gebruikt u de volgende opdracht om het bestands systeem te koppelen:  `sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  
        > [!NOTE]
        > Als u NFSv 4.1 gebruikt, moet u ervoor zorgen dat alle Vm's die de export koppelen, unieke hostnamen gebruiken.

3. Als u een NFS-volume automatisch wilt koppelen wanneer een virtuele machine van Azure wordt gestart of opnieuw is opgestart, voegt u een vermelding toe aan het `/etc/fstab` bestand op de host. 

    Bijvoorbeeld: `$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP` is het IP-adres van het Azure NetApp Files volume dat op de Blade met volume-eigenschappen is gevonden.
    * `$FILEPATH` is het exportpad van het Azure NetApp Files volume.
    * `$MOUNTPOINT` is de map die is gemaakt op de Linux-host die wordt gebruikt om de NFS-export te koppelen.

4. Als u het volume aan Windows wilt koppelen met behulp van NFS:

    a. Koppel het volume eerst aan een UNIX-of Linux-VM.  
    b. Voer een `chmod 777` of- `chmod 775` opdracht uit op het volume.  
    c. Koppel het volume via de NFS-client in Windows.
    
5. Als u een NFS Kerberos-volume wilt koppelen, raadpleegt u [nfsv 4.1 Kerberos Encryption configureren](configure-kerberos-encryption.md) voor meer informatie. 

## <a name="next-steps"></a>Volgende stappen

* [NFSv4.1-standaarddomein configureren voor Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Veelgestelde vragen over NFS](./azure-netapp-files-faqs.md#nfs-faqs)
* [Overzicht Network File System](/windows-server/storage/nfs/nfs-overview)
* [Een NFS Kerberos-volume koppelen](configure-kerberos-encryption.md#kerberos_mount)
