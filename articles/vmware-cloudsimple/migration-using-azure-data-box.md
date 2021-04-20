---
title: 'Azure VMware Solution: migratie met behulp van Azure Data Box'
description: Gegevens bulksgewijs Azure Data Box naar een Azure VMware Solution.
author: sharaths-cs
ms.author: dikamath
ms.date: 09/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ab772bd9cb415045ef70cb4cf9a518791befb192
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741751"
---
# <a name="migrating-data-to-azure-vmware-solution-by-using-azure-data-box"></a>Gegevens migreren naar Azure VMware Solution met behulp van Azure Data Box

Met Microsoft Azure Data Box cloudoplossing kunt u terabytes (TB's) aan gegevens snel, goedkoop en betrouwbaar naar Azure verzenden. De veilige gegevensoverdracht wordt versneld door u een systeemeigen Data Box-opslagapparaat toe te sturen. Elk opslagapparaat heeft een maximaal gebruiksbare opslagcapaciteit van 80 TB en wordt door een regionale vervoerder naar uw datacenter getransporteerd. Dit apparaat heeft een stevige behuizing om uw gegevens tijdens het verzenden te beschermen.

Met behulp Data Box kunt u uw VMware-gegevens bulksgewijs migreren naar uw privécloud. Gegevens uit uw on-premises VMware vSphere worden gekopieerd naar Data Box via het NFS-protocol (Network File System). Bulkgegevensmigratie omvat het opslaan van een point-in-time kopie van virtuele machines, configuratie en gekoppelde gegevens naar Data Box en vervolgens handmatig verzenden naar Azure.

In dit artikel leert u het volgende:

* Het instellen van Data Box.
* Gegevens kopiëren van de on-premises VMware-omgeving naar de Data Box via NFS.
* Voorbereiden op het retourneren van Data Box.
* Blobgegevens voorbereiden voor kopiëren naar Azure VMware Solution.
* De gegevens kopiëren van Azure naar uw privécloud.

## <a name="scenarios"></a>Scenario's

Gebruik Data Box in de volgende scenario's voor bulkgegevensmigratie:

* Voor het migreren van een grote hoeveelheid gegevens van on-premises naar Azure VMware Solution. Met deze methode wordt een basislijn vastgesteld en worden verschillen via het netwerk gesynchroniseerd.
* Voor het migreren van een groot aantal virtuele machines die zijn uitgeschakeld (koude virtuele machines).
* Gegevens van virtuele machines migreren voor het instellen van ontwikkel- en testomgevingen.
* Voor het migreren van een groot aantal virtuele-machinesjablonen, ISO-bestanden en schijven van virtuele machines.

## <a name="before-you-begin"></a>Voordat u begint

* Controleer de vereisten en [volgorde Data Box](../databox/data-box-deploy-ordered.md) uw Azure Portal. Tijdens het bestelproces moet u een opslagaccount selecteren waarmee Blob Storage kan worden gebruikt. Nadat u het Data Box apparaat hebt ontvangen, verbindt u [](../databox/data-box-deploy-set-up.md) het met uw on-premises netwerk en stelt u het apparaat in met een IP-adres dat bereikbaar is vanuit uw vSphere-beheernetwerk.

* Maak een virtueel netwerk en een opslagaccount in dezelfde regio waar uw Azure VMware Solution is ingericht.

* Maak een virtuele Azure-netwerkverbinding vanuit uw privécloud naar het virtuele netwerk waar het opslagaccount wordt gemaakt door de stappen te volgen in [Connect Azure virtual network to CloudSimple using ExpressRoute](virtual-network-connection.md)(Virtueel [Azure-netwerk](cloudsimple-azure-network-connection.md) verbinden met CloudSimple met behulp van ExpressRoute).

## <a name="set-up-data-box-for-nfs"></a>Een Data Box voor NFS instellen

Maak verbinding met Data Box lokale webinterface door de stappen te volgen in de sectie Verbinding maken met uw apparaat in Zelfstudie: Bekabelen en verbinding maken [met uw Azure Data Box](../databox/data-box-deploy-set-up.md).  Configureer Data Box toegang tot NFS-clients toe te staan:

1. Ga in de lokale gebruikersinterface naar de pagina **Verbinding maken en kopiëren**. Selecteer **onder NFS-instellingen** **de optie NFS-clienttoegang.** 

    ![NFS-clienttoegang configureren 1](media/nfs-client-access.png)

2. Voer het IP-adres van de VMware ESXi hosts in en selecteer **Toevoegen.** U kunt de toegang voor alle hosts in uw vSphere-cluster configureren door deze stap te herhalen. Selecteer **OK**.

    ![NFS-clienttoegang configureren 2](media/nfs-client-access2.png)
> [!IMPORTANT]
> **Maak altijd een map voor de bestanden die u van plan bent te kopiëren in de bestandsshare en kopieer de bestanden vervolgens naar die map**. De map gemaakt onder shares met blok-blobs en pagina-blobs vertegenwoordigt een container waarnaar gegevens als blobs worden geüpload. U kunt bestanden niet rechtstreeks naar de *hoofdmap* in het opslagaccount kopiëren.

Onder blok-blob- en pagina-blob-shares zijn entiteiten op het eerste niveau containers en entiteiten op het tweede niveau blobs. Onder shares voor Azure Files zijn entiteiten op het eerste niveau shares en entiteiten op het tweede niveau bestanden.

In de volgende tabel ziet u het UNC-pad naar de shares op uw Data Box en de URL van het Azure Storage-pad waarnaar de gegevens worden geüpload. De uiteindelijke URL van het Azure Storage-pad kan worden afgeleid van het UNC-pad naar de shares.
 
| Blobs en bestanden | Pad en URL |
|---------------- | ------------ |
| Azure-blok-blobs | <li>UNC-pad naar shares: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Azure Storage-URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Azure-pagina-blobs  | <li>UNC-pad naar shares: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Azure Storage-URL: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>UNC-pad naar shares: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Azure Storage-URL: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

> [!NOTE]
> Gebruik Azure-blok-blobs voor het kopiëren van VMware-gegevens.

## <a name="mount-the-nfs-share-as-a-datastore-on-your-on-premises-vcenter-cluster-and-copy-the-data"></a>De NFS-share als een gegevensstore aan uw on-premises vCenter-cluster monteren en de gegevens kopiëren

De NFS-share van uw Data Box moet worden bevestigd als een gegevensstore op uw on-premises vCenter-cluster of VMware ESXi-host om de gegevens te kunnen kopiëren naar de NFS-gegevensstore:

1. Meld u aan bij uw on-premises vCenter-server.

2. Klik met de rechtermuisknop **op Datacenter,** **selecteer Opslag,** **selecteer Nieuwe gegevensopslag** en selecteer **vervolgens Volgende.**

   ![Nieuwe gegevensstore toevoegen](media/databox-migration-add-datastore.png)

3. In stap 1 van de wizard Gegevens opslaan toevoegen selecteert **u NFS** onder **Type**.

   ![Nieuwe gegevensstore toevoegen - type](media/databox-migration-add-datastore-type.png)

4. Selecteer in stap 2 van de wizard **NFS 3** als NFS-versie en selecteer vervolgens **Volgende.**

   ![Nieuwe gegevensstore toevoegen - NFS-versie](media/databox-migration-add-datastore-nfs-version.png)

5. Geef in stap 3 van de wizard de naam op voor het gegevensbeheer, het pad en de server. U kunt het IP-adres van uw Data Box voor de server. Het pad naar de map heeft de `/<StorageAccountName_BlockBlob>/<ContainerName>/` indeling .

   ![Nieuwe gegevensstore toevoegen - NFS-configuratie](media/databox-migration-add-datastore-nfs-configuration.png)

6. Selecteer in stap 4 van de wizard de ESXi-hosts waar u het gegevensstore wilt monteren en selecteer **vervolgens Volgende.**  Selecteer in een cluster alle hosts om de migratie van de virtuele machines te garanderen.

   ![Nieuwe gegevensstore toevoegen - Hosts selecteren](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. Controleer in stap 5 van de wizard de samenvatting en selecteer **Voltooien.**

## <a name="copy-data-to-the-data-box-nfs-datastore"></a>Gegevens kopiëren naar Data Box NFS-gegevensstore

Virtuele machines kunnen worden gemigreerd of gekloond naar de nieuwe gegevensstore.  Ongebruikte virtuele machines die u wilt migreren, kunnen worden gemigreerd naar de Data Box NFS-gegevensopslag met behulp van de **opslagoptie vMotion.** Actieve virtuele machines kunnen worden gekloond naar Data Box NFS-gegevensstore.

* Identificeer en vermeld de virtuele machines die kunnen worden **verplaatst.**
* Identificeer en vermeld de virtuele machines die moeten **worden gekloond.**

### <a name="move-a-virtual-machine-to-a-data-box-datastore"></a>Een virtuele machine naar een Data Box verplaatsen

1. Klik met de rechtermuisknop op de virtuele machine die u wilt verplaatsen naar Data Box gegevensstore en selecteer **vervolgens Migreren.**

    ![Virtuele machine migreren](media/databox-migration-vm-migrate.png)

2. Selecteer **Alleen opslag wijzigen** voor het migratietype en selecteer vervolgens **Volgende.**

    ![Virtuele machine migreren - alleen opslag](media/databox-migration-vm-migrate-change-storage.png)

3. Selecteer **Databox-Datastore** als het doel en selecteer vervolgens **Volgende.**

    ![Virtuele machine migreren - gegevens opslaan selecteren](media/databox-migration-vm-migrate-change-storage-select-datastore.png)

4. Controleer de informatie en selecteer **Voltooien.**

5. Herhaal stap 1 tot en met 4 voor extra virtuele machines.

> [!TIP]
> U kunt meerdere virtuele machines met dezelfde energietoestand selecteren (in- of uitgeschakeld) en deze bulksgewijs migreren.

De virtuele machine wordt gemigreerd naar de NFS-gegevensstore van Data Box. Nadat alle virtuele machines zijn gemigreerd, kunt u de actieve virtuele machines uitschakelen (afsluiten) ter voorbereiding op de migratie van gegevens naar Azure VMware Solution.

### <a name="clone-a-virtual-machine-or-a-virtual-machine-template-to-the-data-box-datastore"></a>Een virtuele machine of een virtuele-machinesjabloon klonen naar Data Box gegevensstore

1. Klik met de rechtermuisknop op een virtuele machine of een virtuele-machinesjabloon die u wilt klonen. Selecteer **Kloon**  >  **klonen naar virtuele machine.**

    ![Kloon van virtuele machine](media/databox-migration-vm-clone.png)

2. Selecteer een naam voor de gekloonde virtuele machine of de virtuele-machinesjabloon.

3. Selecteer de map waarin u het gekloonde object wilt plaatsen en selecteer vervolgens **Volgende.**

4. Selecteer het cluster of de resourcegroep waarin u het gekloonde object wilt plaatsen en selecteer **vervolgens Volgende.**

5. Selecteer **Databox-Datastore** als opslaglocatie en selecteer vervolgens **Volgende.**

    ![Kloon van virtuele machine - gegevens opslaan selecteren](media/databox-migration-vm-clone-select-datastore.png)

6. Als u opties voor het gekloonde object wilt aanpassen, selecteert u de aanpassingsopties en selecteert u **vervolgens Volgende.**

7. Controleer de configuraties en selecteer **Voltooien.**

8. Herhaal stap 1 tot en met 7 voor extra virtuele machines of virtuele-machinesjablonen.

Virtuele machines worden gekloond en opgeslagen in het NFS-gegevensopslag van Data Box. Nadat de virtuele machines zijn gekloond, moet u ervoor zorgen dat ze zijn afgesloten ter voorbereiding op de migratie van gegevens naar Azure VMware Solution.

### <a name="copy-iso-files-to-the-data-box-datastore"></a>ISO-bestanden kopiëren naar Data Box gegevensstore

1. Ga vanuit uw on-premises vCenter-webinterface naar **Opslag.**  Selecteer **Databox-Datastore en** selecteer vervolgens **Bestanden**. Maak een nieuwe map voor het opslaan van ISO-bestanden.

    ![ISO kopiëren - nieuwe map maken](media/databox-migration-create-folder.png)

2. Geef een naam op voor de map waarin ISO-bestanden worden opgeslagen.

3. Dubbelklik op de zojuist gemaakte map om deze te openen.

4. Selecteer **Bestanden uploaden** en selecteer vervolgens de ISO-bestanden die u wilt uploaden.
    
    ![ISO kopiëren - bestanden uploaden](media/databox-migration-upload-iso.png)

> [!TIP]
> Als u al ISO-bestanden in uw on-premises gegevensstore  hebt, kunt u de bestanden selecteren en Kopiëren naar om de bestanden te kopiëren naar Data Box NFS-gegevensstore.


## <a name="prepare-data-box-for-return"></a>Voorbereiden Data Box retourneren

Nadat alle VFS-gegevens, sjabloongegevens van virtuele machines en ISO-bestanden zijn gekopieerd naar het NFS-gegevens archief van Data Box, kunt u de verbinding met het gegevens archief verbreken met uw vCenter. Alle virtuele machines en virtuele-machinesjablonen moeten uit de inventaris worden verwijderd voordat u de verbinding met het gegevensbeheer verbreekt.

### <a name="remove-objects-from-inventory"></a>Objecten verwijderen uit inventaris

1. Ga vanuit uw on-premises vCenter-webinterface naar **Opslag.** Selecteer **Databox-Datastore en** selecteer vervolgens **VM's.**

    ![Virtuele machines uit inventaris verwijderen - uitgeschakeld](media/databox-migration-select-databox-vm.png)

2. Zorg ervoor dat alle virtuele machines zijn afgesloten.

3. Selecteer alle virtuele machines, klik met de rechtermuisknop en selecteer vervolgens **Verwijderen uit inventaris**.

    ![Virtuele machines uit inventaris verwijderen](media/databox-migration-remove-vm-from-inventory.png)

4. Selecteer **VM-sjablonen in Mappen** en herhaal vervolgens stap 3.

### <a name="remove-the-data-box-nfs-datastore-from-vcenter"></a>De Data Box NFS-gegevensstore verwijderen uit vCenter

De Data Box NFS-gegevensstore moet worden losgekoppeld van VMware ESXi hosts voordat u de terugkomst voorbereidt.

1. Ga vanuit uw on-premises vCenter-webinterface naar **Opslag.**

2. Klik met de rechtermuisknop **op Databox-Datastore en** selecteer **Ontkoppelen van gegevensstore.**

    ![Een Data Box ontkoppelen](media/databox-migration-unmount-datastore.png)

3. Selecteer alle ESXi-hosts waarop het gegevensstore is bevestigd en selecteer **OK.**

    ![Een Data Box ontkoppelen - hosts selecteren](media/databox-migration-unmount-datastore-select-hosts.png)

4. Lees en accepteer eventuele waarschuwingen en selecteer **OK.**

### <a name="prepare-data-box-for-return-and-then-return-it"></a>Voorbereiden Data Box retourneren en vervolgens retourneren

Volg de stappen in het artikel [Return Azure Data Box and verify data upload to Azure](../databox/data-box-deploy-picked-up.md) (Gegevens uploaden naar Azure controleren om de gegevens te retourneren) om de Data Box. Controleer de status van de gegevenskopie naar uw Azure-opslagaccount. Nadat de status is voltooid, kunt u de gegevens in uw Azure-opslagaccount controleren.

## <a name="copy-data-from-azure-storage-to-azure-vmware-solution"></a>Gegevens kopiëren van Azure Storage naar Azure VMware Solution

Gegevens die naar uw Data Box zijn gekopieerd, zijn beschikbaar in uw Azure-opslagaccount nadat de orderstatus van uw Data Box wordt weergeven als voltooid. De gegevens kunnen nu worden gekopieerd naar uw Azure VMware Solution. Gegevens in het opslagaccount moeten worden gekopieerd naar het vSAN-gegevensopslag van uw privécloud met behulp van het NFS-protocol. 

Kopieer eerst Blob Storage-gegevens naar een beheerde schijf op een virtuele Linux-machine in Azure met behulp van **AzCopy**. Maak de beheerde schijf beschikbaar via NFS, bevestig de NFS-share als een gegevensopslag in uw privécloud en kopieer de gegevens. Met deze methode kunt u de gegevens sneller naar uw privécloud kopiëren.

### <a name="copy-data-to-your-private-cloud-using-a-linux-virtual-machine-and-managed-disks-and-then-export-as-nfs-share"></a>Gegevens kopiëren naar uw privécloud met behulp van een virtuele Linux-machine en beheerde schijven, en vervolgens exporteren als NFS-share

1. Maak een [virtuele Linux-machine](../virtual-machines/linux/quick-create-portal.md) in Azure in dezelfde regio waar uw opslagaccount wordt gemaakt en heeft een virtuele Azure-netwerkverbinding met uw privécloud.

2. Maak een beheerde schijf waarvan de opslagcapaciteit groter is dan de hoeveelheid blobgegevens en [koppel deze aan uw virtuele Linux-machine.](../virtual-machines/linux/attach-disk-portal.md)  Als de hoeveelheid blobgegevens groter is dan de capaciteit van de grootste beheerde schijf die beschikbaar is, moeten de gegevens in meerdere stappen of met behulp van meerdere beheerde schijven worden gekopieerd.

3. Maak verbinding met de virtuele Linux-machine en koppel de beheerde schijf.

4. Installeer [AzCopy op uw virtuele Linux-machine.](../storage/common/storage-use-azcopy-v10.md)

5. Download de gegevens van uw Azure Blob-opslag naar de beheerde schijf met behulp van AzCopy.  Opdrachtsyntaxis: `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"` .  Vervang `<storage-account-name>` door de naam van uw Azure-opslagaccount en door de container die de gegevens bevat die via de `<container-name>` Data Box.

6. Installeer de NFS-server op uw virtuele Linux-machine:

    - Op een Ubuntu/Debian-distributie: `sudo apt install nfs-kernel-server` .
    - Op een Enterprise Linux-distributie: `sudo yum install nfs-utils` .

7. Wijzig de machtiging van de map op de beheerde schijf waar gegevens uit Azure Blob Storage zijn gekopieerd.  Wijzig de machtigingen voor alle mappen die u wilt exporteren als een NFS-share.

    ```bash
    chmod -R 755 /<folder>/<subfolder>
    chown nfsnobody:nfsnobody /<folder>/<subfolder>
    ```

8. Wijs machtigingen voor client-IP-adressen toe voor toegang tot de NFS-share door het bestand te `/etc/exports` bewerken.

    ```bash
    sudo vi /etc/exports
    ```
    
    Voer de volgende regels in het bestand in voor elk ESXi-host-IP-adres van uw privécloud.  Als u shares voor meerdere mappen maakt, voegt u alle mappen toe.

    ```bash
    /<folder>/<subfolder> <ESXiNode1IP>(rw,sync,no_root_squash,no_subtree_check)
    /<folder>/<subfolder> <ESXiNode2IP>(rw,sync,no_root_squash,no_subtree_check)
    .
    .
    ```

9. Exporteert u de NFS-shares met behulp van de `sudo exportfs -a` opdracht .

10. Start de NFS-kernelserver opnieuw met behulp van de `sudo systemctl restart nfs-kernel-server` opdracht .


### <a name="mount-the-linux-virtual-machine-nfs-share-as-a-datastore-on-a-private-cloud-vcenter-cluster-and-then-copy-data"></a>De NFS-share van de virtuele Linux-machine als een gegevensopslag aan een vCenter-cluster in de privécloud en vervolgens gegevens kopiëren

De NFS-share van uw virtuele Linux-machine moet worden bevestigd als een gegevensopslag in uw vCenter-cluster in de privécloud. Nadat de gegevens zijn bevestigd, kunnen gegevens worden gekopieerd van het NFS-gegevensopslag naar de vSAN-gegevensopslag in de privécloud.

1. Meld u aan bij uw vCenter-server in de privécloud.

2. Klik met de rechtermuisknop **op Datacenter,** **selecteer Opslag,** **selecteer Nieuwe gegevensopslag** en selecteer **vervolgens Volgende.**

   ![Nieuwe gegevensstore toevoegen](media/databox-migration-add-datastore.png)

3. Selecteer in stap 1 van de wizard Gegevens opslaan toevoegen het **NFS-type.**

   ![Nieuwe gegevensstore toevoegen - type](media/databox-migration-add-datastore-type.png)

4. Selecteer in stap 2 van de wizard **NFS 3** als de NFS-versie en selecteer vervolgens **Volgende.**

   ![Nieuwe gegevensstore toevoegen - NFS-versie](media/databox-migration-add-datastore-nfs-version.png)

5. Geef in stap 3 van de wizard de naam op voor de gegevensstore, het pad en de server.  U kunt het IP-adres van uw virtuele Linux-machine voor de server gebruiken.  Het pad naar de map heeft de `/<folder>/<subfolder>/` indeling .

   ![Nieuwe gegevensstore toevoegen - NFS-configuratie](media/databox-migration-add-datastore-nfs-configuration.png)

6. Selecteer in stap 4 van de wizard de ESXi-hosts waarop u het gegevensstore wilt monteren en selecteer **vervolgens Volgende.**  Selecteer in een cluster alle hosts om de migratie van de virtuele machines te garanderen.

   ![Nieuwe gegevensstore toevoegen - Hosts selecteren](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. Controleer in stap 5 van de wizard de samenvatting en selecteer vervolgens **Voltooien.**

### <a name="add-virtual-machines-and-virtual-machine-templates-from-an-nfs-datastore-to-the-inventory"></a>Virtuele machines en virtuele-machinesjablonen uit een NFS-gegevensstore toevoegen aan de inventaris

1. Ga vanuit de vCenter-webinterface van uw privécloud naar **Opslag.**  Selecteer een NFS-gegevensstore voor een virtuele Linux-machine en selecteer vervolgens **Bestanden.**

    ![Bestanden selecteren uit NFS-gegevensstore](media/databox-migration-datastore-select-files.png)

2. Selecteer een map die een virtuele machine of een virtuele-machinesjabloon bevat.  Selecteer in het detailvenster een VMX-bestand voor een virtuele machine of een VMTX-bestand voor een virtuele-machinesjabloon.

3. Selecteer **VM registreren om** de virtuele machine te registreren in uw privécloud vCenter.

    ![Virtuele machine registreren](media/databox-migration-datastore-register-vm.png)

4. Selecteer het datacenter, de map en de cluster-/resourcegroep waar u de virtuele machine wilt registreren.

4. Herhaal stap 3 en 4 voor alle virtuele machines en virtuele-machinesjablonen.

5. Ga naar de map die de ISO-bestanden bevat.  Selecteer de ISO-bestanden en selecteer vervolgens **Kopiëren naar om** de bestanden te kopiëren naar een map in uw vSAN-gegevensstore.

De virtuele machines en virtuele-machinesjablonen zijn nu beschikbaar in uw privécloud vCenter. Deze virtuele machines moeten worden verplaatst van de NFS-gegevensstore naar de vSAN-gegevensstore voordat u ze in kunt zetten. U kunt de optie **storage vMotion gebruiken** en het vSAN-gegevensopslag selecteren als het doel voor de virtuele machines.

De virtuele-machinesjablonen moeten worden gekloond van de NFS-gegevensstore van uw virtuele Linux-machine naar uw vSAN-gegevensstore.

### <a name="clean-up-your-linux-virtual-machine"></a>Uw virtuele Linux-machine ops schonen

Nadat alle gegevens naar uw privécloud zijn gekopieerd, kunt u de NFS-gegevensopslag verwijderen uit uw privécloud:

1. Zorg ervoor dat alle virtuele machines en sjablonen worden verplaatst en gekloond naar uw vSAN-gegevensstore.

2. Verwijder alle sjablonen voor virtuele machines uit de NFS-gegevensstore uit de inventaris.

3. Ontkoppel de gegevensopslag van de virtuele Linux-machine uit uw privécloud-vCenter.

4. Verwijder de virtuele machine en de beheerde schijf uit Azure.

5. Als u de gegevens die door gebruikers zijn overgedragen niet in uw opslagaccount wilt Data Box, verwijdert u het Azure-opslagaccount.  
    


## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Data Box](../databox/data-box-overview.md).
* Meer informatie over de verschillende opties [voor het migreren van workloads naar uw privécloud.](migrate-workloads.md)
