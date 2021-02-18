---
title: Een SMB-volume maken voor Azure NetApp Files | Microsoft Docs
description: In dit artikel wordt beschreven hoe u een SMB3-volume maakt in Azure NetApp Files. Meer informatie over de vereisten voor Active Directory verbindingen en Domain Services.
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
ms.date: 02/16/2021
ms.author: b-juche
ms.openlocfilehash: 91f4f90658281282cdcb01b091bd9c9647d8d702
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100635483"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Een SMB-volume maken voor Azure NetApp Files

Azure NetApp Files biedt ondersteuning voor het maken van volumes met behulp van NFS (NFSv3 en NFSv 4.1), SMB3 of het dubbele Protocol (NFSv3 en SMB). Capaciteitsgebruik van een volume wordt in mindering gebracht op de ingerichte capaciteit van de pool. In dit artikel wordt beschreven hoe u een SMB3-volume maakt.

## <a name="before-you-begin"></a>Voordat u begint 

* U dient al een capaciteitspool te hebben ingesteld. Zie [een capaciteits pool instellen](azure-netapp-files-set-up-capacity-pool.md).     
* Er moet een subnet zijn gedelegeerd aan Azure NetApp Files. Zie [een subnet delegeren aan Azure NetApp files](azure-netapp-files-delegate-subnet.md).

## <a name="configure-active-directory-connections"></a>Active Directory verbindingen configureren 

Voordat u een SMB-volume maakt, moet u een Active Directory verbinding maken. Als u Active Directory verbindingen voor Azure NetApp-bestanden nog niet hebt geconfigureerd, volgt u de instructies in [Active Directory verbindingen maken en beheren](create-active-directory-connections.md).

## <a name="add-an-smb-volume"></a>Een SMB-volume toevoegen

1. Klik op de Blade **volumes** op de Blade capaciteits groepen. 

    ![Navigeren naar volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Klik op **+ Volume toevoegen** om een volume te maken.  
    Het venster een volume maken wordt weer gegeven.

3. Klik in het venster een volume maken op **maken** en geef informatie op voor de volgende velden op het tabblad basis beginselen:   
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

        Het VNet dat u opgeeft, moet een subnet hebben gedelegeerd aan Azure NetApp Files. De Azure NetApp Files-service kan alleen worden geopend vanuit hetzelfde VNet of vanuit een VNet dat zich in dezelfde regio bevindt als het volume via VNet-peering. U kunt het volume ook openen vanuit uw on-premises netwerk via een snelle route.   

    * **Subnet**  
        Geef het subnet op dat u wilt gebruiken voor het volume.  
        Het opgegeven subnet moet zijn gedelegeerd aan Azure NetApp Files. 
        
        Als u nog geen subnet hebt gedelegeerd, kunt u op de pagina een volume maken klikken op **Nieuw maken** . Geef vervolgens op de pagina Subnet maken de subnetgegevens op, en selecteer **Microsoft.NetApp/volumes** om het subnet te delegeren aan Azure NetApp Files. In elk VNet kan slechts één subnet worden gedelegeerd aan Azure NetApp Files.   
 
        ![ Een volume maken](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Subnet maken](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Als u een bestaand momentopname beleid wilt Toep assen op het volume, klikt u op de **sectie Geavanceerd weer geven** om deze uit te vouwen, geeft u op of u het pad naar de moment opname wilt verbergen en selecteert u een momentopname beleid in de vervolg keuzelijst. 

        Zie voor meer informatie over het maken van een momentopname beleid [beleid voor moment opnamen beheren](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

        ![Geavanceerde selectie weer geven](../media/azure-netapp-files/volume-create-advanced-selection.png)

4. Klik op **protocol** en voer de volgende informatie uit:  
    * Selecteer **SMB** als het protocol type voor het volume. 
    * Selecteer uw **Active Directory** verbinding in de vervolg keuzelijst.
    * Geef de naam op van het gedeelde volume in  **share naam**.

    ![SMB-protocol opgeven](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Klik op **beoordeling + maken** om de details van het volume te controleren.  Klik vervolgens op **maken** om het SMB-volume te maken.

    Het volume dat u hebt gemaakt, wordt weer gegeven op de pagina volumes. 
 
    Een volume neemt het abonnement, de resourcegroep en de locatiekenmerken over van de bijbehorende capaciteitspool. U kunt de implementatiestatus van het volume controleren vanuit het tabblad Meldingen.

## <a name="control-access-to-an-smb-volume"></a>Toegang tot een SMB-volume beheren  

Toegang tot een SMB-volume wordt beheerd via machtigingen.  

### <a name="share-permissions"></a>Share machtigingen  

Een nieuw volume heeft standaard de machtigingen delen **iedereen/volledig beheer** . Leden van de groep domein Administrators kunnen de share machtigingen wijzigen door gebruik te maken van computer beheer op het computer account dat wordt gebruikt voor het Azure NetApp Files volume.

![SMB-koppel pad ](../media/azure-netapp-files/smb-mount-path.png) 
 ![ machtigingen voor delen instellen](../media/azure-netapp-files/set-share-permissions.png) 

### <a name="ntfs-file-and-folder-permissions"></a>NTFS-bestands-en mapmachtigingen  

U kunt machtigingen voor een bestand of map instellen met behulp van het tabblad **beveiliging** van de eigenschappen van het object in de Windows SMB-client.
 
![Machtigingen voor bestanden en mappen instellen](../media/azure-netapp-files/set-file-folder-permissions.png) 

## <a name="next-steps"></a>Volgende stappen  

* [Een volume voor Windows- of Linux-VM's koppelen of ontkoppelen](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Resourcelimieten voor Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Veelgestelde vragen over SMB](./azure-netapp-files-faqs.md#smb-faqs)
* [Problemen met SMB-of Dual-protocol volumes oplossen](troubleshoot-dual-protocol-volumes.md)
* Meer informatie over [Integratie van virtuele netwerken voor Azure-services](../virtual-network/virtual-network-for-azure-services.md)
* [Een nieuw Active Directory-forest installeren met behulp van Azure CLI](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
