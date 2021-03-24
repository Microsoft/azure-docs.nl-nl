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
ms.date: 03/19/2021
ms.author: b-juche
ms.openlocfilehash: c673f7a9556193fb05e05ea372bfccd17cd3c5ed
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104868508"
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
    * Als u continue Beschik baarheid voor het SMB-volume wilt inschakelen, selecteert u **voortdurende Beschik baarheid inschakelen**.    

        > [!IMPORTANT]   
        > De functie voor het door lopen van SMB-Beschik baarheid is momenteel beschikbaar als open bare preview. U moet een Waitlist-aanvraag indienen om toegang te krijgen tot de functie via de **[Waitlist-inzendings pagina Azure NETAPP files SMB Continuous Availability shares Public Preview](https://aka.ms/anfsmbcasharespreviewsignup)**. Wacht op een officiële bevestigings-e-mail van het Azure NetApp Files team voordat u de functie voortdurende Beschik baarheid gebruikt.   
        > 
        > U moet continue Beschik baarheid alleen inschakelen voor SQL-workloads. Het gebruik van SMB continue beschikbaarheids shares voor andere werk belastingen dan SQL Server wordt *niet* ondersteund. Deze functie wordt momenteel ondersteund op Windows SQL Server. Linux-SQL Server wordt momenteel niet ondersteund. Als u een niet-beheerders account (domein) gebruikt om SQL Server te installeren, moet u ervoor zorgen dat aan het account de vereiste beveiligings bevoegdheid is toegewezen. Als het domein account niet de vereiste beveiligings bevoegdheid () heeft `SeSecurityPrivilege` en de bevoegdheid niet kan worden ingesteld op domein niveau, kunt u de bevoegdheid verlenen aan het account met behulp van het veld **gebruikers met beveiligings bevoegdheden** van Active Directory verbindingen. Zie [een Active Directory verbinding maken](create-active-directory-connections.md#create-an-active-directory-connection).

    <!-- [1/13/21] Commenting out command-based steps below, because the plan is to use form-based (URL) registration, similar to CRR feature registration -->
    <!-- 
        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBCAShare
        ```

        Check the status of the feature registration: 

        > [!NOTE]
        > The **RegistrationState** may be in the `Registering` state for up to 60 minutes before changing to`Registered`. Wait until the status is `Registered` before continuing.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBCAShare
        ```
        
        You can also use [Azure CLI commands](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` and `az feature show` to register the feature and display the registration status. 
    --> 

    ![Scherm afbeelding van het tabblad Protocol van het maken van een SMB-volume.](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

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
