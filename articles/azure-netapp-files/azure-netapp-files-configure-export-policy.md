---
title: Export beleid configureren voor NFS-volume-Azure NetApp Files
description: Hierin wordt beschreven hoe u het beleid voor exporteren configureert om de toegang tot een NFS-volume te beheren met behulp van Azure NetApp Files
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: d3f268a4ce2660350055367770e987a06828e2d7
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740116"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Exportbeleid voor een NFS-volume configureren

U kunt beleid voor exporteren configureren om de toegang tot een Azure NetApp Files volume te beheren. Azure NetApp Files export beleid ondersteunt volumes die gebruikmaken van het NFS-protocol (zowel NFSv3 als NFSv 4.1) als het dubbele Protocol (NFSv3 en SMB). 

U kunt Maxi maal vijf export beleids regels maken.

## <a name="configure-the-policy"></a>Het beleid configureren 

1.  Selecteer op de pagina **volumes** het volume waarvoor u het export beleid wilt configureren en selecteer vervolgens **beleid exporteren**. U kunt het export beleid ook configureren tijdens het maken van het volume.

2.  Als u een beleids regel voor exporteren wilt maken, geeft u de volgende informatie op:   
    * **Index**: Geef het index nummer voor de regel op.  
      
      Een exportbeleid kan uit maximaal vijf regels bestaan. Regels worden geëvalueerd volgens hun volgorde in de lijst met indexnummers. Regels met lagere indexnummers worden als eerste geëvalueerd. De regel met indexnummer 1 wordt bijvoorbeeld geëvalueerd vóór de regel met indexnummer 2. 

    * **Toegestane clients**: Geef de waarde op in een van de volgende indelingen:  
      * IPv4-adres. Voorbeeld: `10.1.12.24`
      * IPv4-adres met een subnetmasker uitgedrukt als een aantal bits. Voorbeeld: `10.1.12.10/4`
      * Door komma's gescheiden IP-adressen. U kunt meerdere host-Ip's opgeven in één regel door deze te scheiden met komma's. Voorbeeld: `10.1.12.25,10.1.12.28,10.1.12.29`

    * **Toegang**: Selecteer een van de volgende toegangs typen:  
      * Geen toegang 
      * Lezen en schrijven
      * Alleen-lezen

    * **Alleen-lezen** en **lezen/schrijven**: als u Kerberos-versleuteling met nfsv 4.1 gebruikt, volgt u de instructies in [Configure nfsv 4.1 Kerberos Encryption](configure-kerberos-encryption.md).  Zie [prestaties van Kerberos op nfsv 4.1-volumes](performance-impact-kerberos.md)voor prestatie-impact van Kerberos. 

      ![Kerberos-beveiligings opties](../media/azure-netapp-files/kerberos-security-options.png) 

    * **Hoofd toegang**: Hiermee geeft u op of het `root` account toegang heeft tot het volume.  Hoofd toegang is standaard ingesteld **op aan** en het `root` account heeft toegang tot het volume.

      ![Exportbeleid](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 

## <a name="next-steps"></a>Volgende stappen 
* [Een volume voor virtuele machines koppelen of ontkoppelen](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Momentopnamen beheren](azure-netapp-files-manage-snapshots.md)
