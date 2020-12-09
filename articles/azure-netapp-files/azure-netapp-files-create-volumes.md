---
title: Een NFS-volume maken voor Azure NetApp Files | Microsoft Docs
description: In dit artikel wordt beschreven hoe u een NFS-volume maakt in Azure NetApp Files. Meer informatie over overwegingen, zoals welke versie moet worden gebruikt en aanbevolen procedures.
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
ms.date: 09/24/2020
ms.author: b-juche
ms.openlocfilehash: 2cc9d3e0fb711a0662852ce4f2c5a08dc626f246
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96854730"
---
# <a name="create-an-nfs-volume-for-azure-netapp-files"></a>Een NFS-volume maken voor Azure NetApp Files

Azure NetApp Files biedt ondersteuning voor het maken van volumes met behulp van NFS (NFSv3 en NFSv 4.1), SMB3 of het dubbele Protocol (NFSv3 en SMB). Capaciteitsgebruik van een volume wordt in mindering gebracht op de ingerichte capaciteit van de pool. In dit artikel wordt beschreven hoe u een NFS-volume maakt. 

## <a name="before-you-begin"></a>Voordat u begint 
* U dient al een capaciteitspool te hebben ingesteld.  
    Zie [een capaciteits pool instellen](azure-netapp-files-set-up-capacity-pool.md).   
* Er moet een subnet zijn gedelegeerd aan Azure NetApp Files.  
    Zie [een subnet delegeren aan Azure NetApp files](azure-netapp-files-delegate-subnet.md).

## <a name="considerations"></a>Overwegingen 

* Bepalen welke NFS-versie moet worden gebruikt  
  NFSv3 kan een groot aantal use-cases verwerken en wordt meestal geïmplementeerd in de meeste bedrijfs toepassingen. U dient te controleren welke versie (NFSv3 of NFSv 4.1) uw toepassing vereist en maakt u het volume met behulp van de juiste versie. Als u bijvoorbeeld [Apache ActiveMQ](https://activemq.apache.org/shared-file-system-master-slave)gebruikt, wordt het vergren delen van bestanden met nfsv 4.1 aanbevolen voor NFSv3. 

* Beveiliging  
  Ondersteuning voor UNIX-modus bits (lezen, schrijven en uitvoeren) is beschikbaar voor NFSv3 en NFSv 4.1. Toegang op hoofd niveau is vereist op de NFS-client om NFS-volumes te koppelen.

* Lokale gebruiker/groep en LDAP-ondersteuning voor NFSv 4.1  
  Op dit moment ondersteunt NFSv 4.1 alleen toegang tot de hoofdmap voor volumes. Zie [nfsv 4.1 standaard domein configureren voor Azure NetApp files](azure-netapp-files-configure-nfsv41-domain.md). 

## <a name="best-practice"></a>Best practice

* Zorg ervoor dat u de juiste koppelings instructies voor het volume gebruikt.  Zie [een volume koppelen of ontkoppelen voor virtuele Windows-of Linux-machines](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).

* De NFS-client moet zich in hetzelfde VNet of een gelijkwaardige VNet bezien als het Azure NetApp Files volume. Het maken van een verbinding van buiten het VNet wordt ondersteund; Er wordt echter extra latentie geïntroduceerd en de algehele prestaties worden gereduceerd.

* Controleer of de NFS-client up-to-date is en of de meest recente updates voor het besturings systeem worden uitgevoerd.

## <a name="create-an-nfs-volume"></a>Een NFS-volume maken

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
    * Selecteer **NFS** als protocoltype voor het volume.   
    * Geef het **bestandspad op dat wordt** gebruikt voor het maken van het exportpad voor het nieuwe volume. Het exportpad wordt gebruikt om het volume te koppelen en benaderen.

        Het bestandspad mag alleen letters, cijfers en afbreekstreepjes ('-') bevatten. Het bestandspad moet tussen de 16 en 40 tekens lang zijn. 

        Het bestandspad moet uniek zijn binnen elk abonnement en elke regio. 

    * Selecteer de NFS-versie (**NFSv3** of **NFSv4.1**) voor het volume.  

    * Als u NFSv 4.1 gebruikt, geeft u aan of u **Kerberos** -versleuteling wilt inschakelen voor het volume.  

        Aanvullende configuraties zijn vereist als u Kerberos gebruikt met NFSv 4.1. Volg de instructies in [Configure nfsv 4.1 Kerberos Encryption](configure-kerberos-encryption.md).

    * Optioneel kunt [u het export beleid configureren voor het NFS-volume](azure-netapp-files-configure-export-policy.md).

    ![NFS-protocol opgeven](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

4. Klik op **beoordeling + maken** om de details van het volume te controleren.  Klik vervolgens op **maken** om het volume te maken.

    Het volume dat u hebt gemaakt, wordt weer gegeven op de pagina volumes. 
 
    Een volume neemt het abonnement, de resourcegroep en de locatiekenmerken over van de bijbehorende capaciteitspool. U kunt de implementatiestatus van het volume controleren vanuit het tabblad Meldingen.


## <a name="next-steps"></a>Volgende stappen  

* [NFSv4.1-standaarddomein configureren voor Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [NFSv4.1 Kerberos-versleuteling configureren](configure-kerberos-encryption.md)
* [Een volume voor Windows- of Linux-VM's koppelen of ontkoppelen](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Exportbeleid voor een NFS-volume configureren](azure-netapp-files-configure-export-policy.md)
* [Resourcelimieten voor Azure NetApp Files](azure-netapp-files-resource-limits.md)
* Meer informatie over [Integratie van virtuele netwerken voor Azure-services](../virtual-network/virtual-network-for-azure-services.md)