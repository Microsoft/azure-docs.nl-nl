---
title: Een SMB-volume maken voor Azure NetApp Files | Microsoft Docs
description: In dit artikel wordt beschreven hoe u een SMB3-volume maakt in Azure NetApp Files. Meer informatie over vereisten voor Active Directory-verbindingen en Domain Services.
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
ms.openlocfilehash: 9bb995e5e3038d7a4cd24f0db2608461c8848497
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726286"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Een SMB-volume maken voor Azure NetApp Files

Azure NetApp Files biedt ondersteuning voor het maken van volumes met NFS (NFSv3 en NFSv4.1), SMB3 of dual-protocol (NFSv3 en SMB). Capaciteitsgebruik van een volume wordt in mindering gebracht op de ingerichte capaciteit van de pool. 

In dit artikel wordt beschreven hoe u een SMB3-volume maakt. Zie een NFS-volume maken [voor NFS-volumes.](azure-netapp-files-create-volumes.md) Zie Create [a dual-protocol volume (Een dual-protocol volume](create-volumes-dual-protocol.md)maken) voor volumes met dubbele protocollen.

## <a name="before-you-begin"></a>Voordat u begint 

* U dient al een capaciteitspool te hebben ingesteld. Zie [Een capaciteitspool instellen.](azure-netapp-files-set-up-capacity-pool.md)     
* Er moet een subnet zijn gedelegeerd aan Azure NetApp Files. Zie [Een subnet delegeren aan Azure NetApp Files.](azure-netapp-files-delegate-subnet.md)

## <a name="configure-active-directory-connections"></a>Active Directory-verbindingen configureren 

Voordat u een SMB-volume maakt, moet u een Active Directory-verbinding maken. Als u nog geen Active Directory-verbindingen voor Azure NetApp-bestanden hebt geconfigureerd, volgt u de instructies in Active Directory-verbindingen maken [en beheren.](create-active-directory-connections.md)

## <a name="add-an-smb-volume"></a>Een SMB-volume toevoegen

1. Klik op de blade **Volumes** op de blade Capaciteitspools. 

    ![Navigeer naar Volumes](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Klik op **+ Volume toevoegen** om een volume te maken.  
    Het venster Een volume maken wordt weergegeven.

3. Klik in het venster Een volume maken op **Maken en** geef informatie op voor de volgende velden op het tabblad Basisinformatie:   
    * **Volumenaam**      
        Geef de naam op voor het volume dat u wilt maken.   

        Een volumenaam moet uniek zijn binnen elke capaciteitspool. De naam moet minstens drie tekens bevatten. U kunt alle alfanumerieke tekens gebruiken.   

        U kunt of niet gebruiken `default` `bin` als de volumenaam.

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

        Het VNet dat u opgeeft, moet een subnet hebben dat is gedelegeerd Azure NetApp Files. De Azure NetApp Files-service is alleen toegankelijk vanaf hetzelfde VNet of vanuit een VNet dat zich in dezelfde regio als het volume via VNet-peering. U kunt het volume ook openen vanuit uw on-premises netwerk via Express Route.   

    * **Subnet**  
        Geef het subnet op dat u wilt gebruiken voor het volume.  
        Het opgegeven subnet moet zijn gedelegeerd aan Azure NetApp Files. 
        
        Als u nog geen subnet hebt gedelegeerd, kunt u op **Nieuwe maken klikken** op de pagina Een volume maken. Geef vervolgens op de pagina Subnet maken de subnetgegevens op, en selecteer **Microsoft.NetApp/volumes** om het subnet te delegeren aan Azure NetApp Files. In elk VNet kan slechts één subnet worden gedelegeerd aan Azure NetApp Files.   
 
        ![ Een volume maken](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Subnet maken](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Als u een bestaand momentopnamebeleid wilt  toepassen op het volume, klikt u op de sectie Geavanceerd weergeven om het uit te vouwen, geeft u op of u het momentopnamepad wilt verbergen en selecteert u een momentopnamebeleid in het pull-downmenu. 

        Zie Beleid voor momentopnamen beheren voor meer informatie over het maken [van een beleid voor momentopnamen.](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)

        ![Geavanceerde selectie tonen](../media/azure-netapp-files/volume-create-advanced-selection.png)

4. Klik **op Protocol** en vul de volgende gegevens in:  
    * Selecteer **SMB** als het protocoltype voor het volume. 
    * Selecteer uw **Active Directory-verbinding** in de vervolgkeuzelijst.
    * Geef de naam van het gedeelde volume op in **Sharenaam.**
    * Als u versleuteling voor SMB3 wilt inschakelen, selecteert u **SMB3-protocolversleuteling inschakelen.**   
        Met deze functie schakelt u versleuteling in voor in-flight SMB3-gegevens. SMB-clients die geen SMB3-versleuteling gebruiken, hebben geen toegang tot dit volume.  Data-at-rest wordt versleuteld, ongeacht deze instelling.  
        Zie [Veelgestelde vragen over SMB-versleuteling](azure-netapp-files-faqs.md#smb-encryption-faqs) voor meer informatie. 

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
    * Als u Continue beschikbaarheid voor het SMB-volume wilt inschakelen, selecteert **u Continue beschikbaarheid inschakelen.**    

        > [!IMPORTANT]   
        > De functie Continue beschikbaarheid van SMB is momenteel beschikbaar als openbare preview. U moet een aanvraag voor een wachtlijst indienen voor toegang tot de functie via de pagina voor het indienen **[Azure NetApp Files SMB Continuous Availability Shares Public Preview.](https://aka.ms/anfsmbcasharespreviewsignup)** Wacht op een officiële bevestigingsmail van het Azure NetApp Files voordat u de functie Continue beschikbaarheid gebruikt.   
        > 
        > U moet continue beschikbaarheid alleen inschakelen voor SQL-workloads. Het gebruik van SMB-shares voor continue beschikbaarheid voor andere workloads dan SQL Server *wordt niet* ondersteund. Deze functie wordt momenteel ondersteund op Windows SQL Server. Linux SQL Server wordt momenteel niet ondersteund. Als u een niet-beheerdersaccount (domein) gebruikt voor het installeren van SQL Server, moet u ervoor zorgen dat aan het account de vereiste beveiligingsrechten zijn toegewezen. Als het domeinaccount niet over de vereiste beveiligingsrechten ( ) en de bevoegdheid kan niet worden ingesteld op domeinniveau, kunt u de bevoegdheid verlenen aan het account met behulp van de beveiligingsrechten gebruikers veld van `SeSecurityPrivilege` Active Directory-verbindingen.  Zie [Een Active Directory-verbinding maken.](create-active-directory-connections.md#create-an-active-directory-connection)

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

    ![Schermopname van het tabblad Protocol voor het maken van een SMB-volume.](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Klik **op Controleren en maken om** de volumedetails te controleren.  Klik vervolgens **op Maken** om het SMB-volume te maken.

    Het volume dat u hebt gemaakt, wordt weergegeven op de pagina Volumes. 
 
    Een volume neemt het abonnement, de resourcegroep en de locatiekenmerken over van de bijbehorende capaciteitspool. U kunt de implementatiestatus van het volume controleren vanuit het tabblad Meldingen.

## <a name="control-access-to-an-smb-volume"></a>Toegang tot een SMB-volume regelen  

Toegang tot een SMB-volume wordt beheerd via machtigingen.  

### <a name="share-permissions"></a>Machtigingen voor delen  

Een nieuw volume heeft standaard de sharemachtigingen **Iedereen/Volledig** beheer. Leden van de groep Domeinad administrators kunnen de machtigingen voor delen als volgt wijzigen:  

1. Wijs de share toe aan een station.  
2. Klik met de rechtermuisknop op het station, **selecteer Eigenschappen** en ga vervolgens naar het **tabblad** Beveiliging.

[![Sharemachtigingen instellen](../media/azure-netapp-files/set-share-permissions.png)](../media/azure-netapp-files/set-share-permissions.png#lightbox)

### <a name="ntfs-file-and-folder-permissions"></a>NTFS-bestands- en mapmachtigingen  

U kunt machtigingen voor een bestand  of map instellen met behulp van het tabblad Beveiliging van de eigenschappen van het object in de Windows SMB-client.
 
![Bestands- en mapmachtigingen instellen](../media/azure-netapp-files/set-file-folder-permissions.png) 

## <a name="next-steps"></a>Volgende stappen  

* [Een volume voor Windows- of Linux-VM's koppelen of ontkoppelen](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Resourcelimieten voor Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Veelgestelde vragen over SMB](./azure-netapp-files-faqs.md#smb-faqs)
* [Problemen met SMB- of dual-protocolvolumes oplossen](troubleshoot-dual-protocol-volumes.md)
* Meer informatie over [Integratie van virtuele netwerken voor Azure-services](../virtual-network/virtual-network-for-azure-services.md)
* [Een nieuw Active Directory-forest installeren met behulp van Azure CLI](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
