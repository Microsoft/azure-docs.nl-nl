---
title: Implementatie van SAP BusinessObjects BI-platform op Azure voor Linux | Microsoft Docs
description: SAP BusinessObjects BI-platform implementeren en configureren op Azure voor Linux
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files,mysql
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/05/2020
ms.author: depadia
ms.openlocfilehash: b94e1f82409da3329eb6d978fa2ae0222928cd97
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102505933"
---
# <a name="sap-businessobjects-bi-platform-deployment-guide-for-linux-on-azure"></a>Handleiding voor SAP BusinessObjects BI-platformimplementatie voor Linux in Azure

In dit artikel wordt de strategie beschreven voor het implementeren van het SAP BOBI-platform op Azure voor Linux. In dit voor beeld worden twee virtuele machines met Premium-SSD Managed Disks als installatiemap zijn geconfigureerd. Azure Database for MySQL wordt gebruikt voor CMS-data base en Azure NetApp Files voor de bestands opslagplaats server wordt gedeeld op beide servers. De standaard Tomcat Java-webtoepassing en BI-platform toepassing worden samen op virtuele machines geïnstalleerd. Als u de gebruikers aanvraag wilt verdelen, wordt Application Gateway gebruikt dat systeem eigen TLS/SSL-offloading mogelijkheden heeft.

Dit type architectuur is effectief voor een kleine implementatie of een niet-productie omgeving. Voor productie-of grootschalige implementaties kunt u afzonderlijke hosts voor de webtoepassing hebben en ook meerdere BOBI-toepassingen hosten waarmee de server meer informatie kan verwerken.

![SAP BOBI-implementatie in azure voor Linux](media/businessobjects-deployment-guide/businessobjects-deployment-linux.png)

In dit voor beeld wordt onder de product versie en de indeling van het bestands systeem gebruikt.

- SAP BusinessObjects-platform 4,3
- SUSE Linux Enterprise Server 12 SP5
- Azure Database for MySQL (versie: 8.0.15)
- MySQL C API-connector-libmysqlclient (versie: 6.1.11)

| Bestandssysteem        | Description                                                                                                               | Grootte (GB)             | Eigenaar  | Groep  | Storage                    |
|--------------------|---------------------------------------------------------------------------------------------------------------------------|-----------------------|--------|--------|----------------------------|
| /usr/sap           | Het bestands systeem voor de installatie van het SAP BOBI-exemplaar, de standaard Tomcat-webtoepassing en database Stuur programma's (indien nodig) | SAP-formaat richtlijnen | bl1adm | sapsys | Beheerde Premium-schijf-SSD |
| /usr/sap/frsinput  | De koppelings Directory is voor de gedeelde bestanden op alle BOBI-hosts die worden gebruikt als opslag plaats Directory voor invoer bestanden  | Bedrijfs behoeften         | bl1adm | sapsys | Azure NetApp Files         |
| /usr/sap/frsoutput | De koppelings Directory is voor de gedeelde bestanden op alle BOBI-hosts die worden gebruikt als opslag plaats Directory voor uitvoer bestanden | Bedrijfs behoeften         | bl1adm | sapsys | Azure NetApp Files         |

## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>Virtuele Linux-machine implementeren via Azure Portal

In deze sectie maakt u twee virtuele machines (Vm's) met installatie kopie van het besturings systeem Linux voor het SAP BOBI-platform. De stappen op hoog niveau voor het maken van Virtual Machines zijn als volgt:

1. Een [resource groep](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) maken

2. Maak een [Virtual Network](../../../virtual-network/quick-create-portal.md#create-a-virtual-network).

   - Gebruik niet één subnet voor alle Azure-Services in de implementatie van het SAP BI-platform. Op basis van de SAP BI-platform architectuur moet u meerdere subnetten maken. In deze implementatie maken we drie subnetten: toepassings subnet, bestand opslagplaats opslag subnet en Application Gateway subnet.
   - In azure moeten Application Gateway en Azure NetApp Files altijd zich op een afzonderlijk subnet bevinden. Raadpleeg [Azure-toepassing gateway](../../../application-gateway/configuration-overview.md) en [richt lijnen voor Azure NetApp files-netwerk plannings](../../../azure-netapp-files/azure-netapp-files-network-topologies.md) artikel voor meer informatie.

3. Maak een Beschikbaarheidsset.

   - Plaats virtuele machines voor elke laag in een beschikbaarheidsset om redundantie te verzorgen voor elke laag in een implementatie met meerdere exemplaren. Zorg ervoor dat u de beschikbaarheids sets voor elke laag scheidt op basis van uw architectuur.

4. Maak een virtuele machine 1 **(azusbosl1).**

   - U kunt een aangepaste installatie kopie gebruiken of een installatie kopie kiezen uit Azure Marketplace. Raadpleeg [de implementatie van een virtuele machine vanuit Azure Marketplace voor SAP](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap) of [het implementeren van een VM met een aangepaste installatie kopie voor SAP](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-2-deploying-a-vm-with-a-custom-image-for-sap) op basis van uw behoeften.

5. Maak de virtuele machine 2 **(azusbosl2).**
6. Voeg één Premium-SSD schijf toe. De map wordt gebruikt als een SAP BOBI-installatiemap.

## <a name="provision-azure-netapp-files"></a>Azure NetApp Files inrichten

Raadpleeg de documentatie van Azure [NetApp files](../../../azure-netapp-files/azure-netapp-files-introduction.md)voordat u doorgaat met de installatie van Azure NetApp files.

Azure NetApp Files is beschikbaar in verschillende [Azure-regio's](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Controleer of de geselecteerde Azure-regio Azure NetApp Files bevat.

Gebruik de pagina [Beschik baarheid van Azure NetApp files per Azure-regio](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) om de beschik baarheid van Azure NetApp files per regio te controleren.

Vraag om onboarding naar Azure NetApp Files door u te [registreren voor Azure NetApp files instructies](../../../azure-netapp-files/azure-netapp-files-register.md) voordat u Azure NetApp files implementeert.

### <a name="deploy-azure-netapp-files-resources"></a>Azure NetApp Files-resources implementeren

Bij de volgende instructies wordt ervan uitgegaan dat u uw [virtuele Azure-netwerk](../../../virtual-network/virtual-networks-overview.md)al hebt geïmplementeerd. De Azure NetApp Files resources en virtuele machines, waarbij de Azure NetApp Files resources worden gekoppeld, moeten worden geïmplementeerd in hetzelfde virtuele Azure-netwerk of in peered virtuele netwerken van Azure.

1. Als u de resources nog niet hebt geïmplementeerd, vraagt u [om onboarding naar Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-register.md).

2. Maak een NetApp-account in uw geselecteerde Azure-regio door de instructies in [een NetApp-account maken](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)te volgen.

3. Stel een Azure NetApp Files capaciteits groep in door de instructies in [een Azure NetApp files-capaciteits groep instellen](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)te volgen.

   - De SAP BI-platform architectuur die in dit artikel wordt gepresenteerd, maakt gebruik van één Azure NetApp Files capaciteits groep op het niveau van de *Premium* -service. Voor de SAP BI File Repository-Server op Azure kunt u het beste een Azure NetApp Files *Premium* -of *Ultra* - [service niveau](../../../azure-netapp-files/azure-netapp-files-service-levels.md)gebruiken.

4. Delegeer een subnet aan Azure NetApp Files, zoals beschreven in de instructies in [een subnet overdragen aan Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).

5. Implementeer Azure NetApp Files volumes door de instructies in [een NFS-volume maken voor Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)te volgen.

   ANF-volume kan worden geïmplementeerd als NFSv3 en NFSv 4.1, aangezien beide protocollen worden ondersteund voor het SAP BOBI-platform. Implementeer de volumes in het respectieve Azure NetApp Files subnet. De IP-adressen van de Azure NetApp-volumes worden automatisch toegewezen.

Houd er rekening mee dat de Azure NetApp Files resources en de virtuele Azure-machines zich in hetzelfde virtuele Azure-netwerk moeten bevinden of in een Peerd Azure Virtual Network. Bijvoorbeeld, azusbobi-frsinput, azusbobi-frsoutput zijn de volume namen en nfs://10.31.2.4/azusbobi-frsinput, nfs://10.31.2.4/azusbobi-frsoutput de bestands paden zijn voor de Azure NetApp Files volumes.

- Volume azusbobi-frsinput (nfs://10.31.2.4/azusbobi-frsinput)
- Volume azusbobi-frsoutput (nfs://10.31.2.4/azusbobi-frsoutput)

### <a name="important-considerations"></a>Belangrijke overwegingen

Houd bij het maken van uw Azure NetApp Files voor de SAP BOBI platform File Repository Server rekening met de volgende overweging:

1. De minimale capaciteits pool is 4 tebibytes (TiB).
2. De minimale volume grootte is 100 gibibytes (GiB).
3. Azure NetApp Files en alle virtuele machines waar de Azure NetApp Files volumes worden gekoppeld, moeten zich in hetzelfde virtuele Azure-netwerk of in gekoppelde [virtuele netwerken](../../../virtual-network/virtual-network-peering-overview.md) in dezelfde regio bevinden. Azure NetApp Files toegang via VNET-peering in dezelfde regio wordt nu ondersteund. Toegang tot Azure NetApp via wereld wijde peering wordt nog niet ondersteund.
4. Het geselecteerde virtuele netwerk moet een subnet hebben dat is overgedragen aan Azure NetApp Files.
5. Met de Azure NetApp Files [export beleid](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)kunt u de toegestane clients, het toegangs type (lezen-schrijven, alleen-lezen, enzovoort) beheren.
6. De functie Azure NetApp Files is nog geen zone-bewust. Op dit moment wordt de functie niet geïmplementeerd in alle beschikbaarheids zones in een Azure-regio. Houd rekening met de mogelijke latentie implicaties in sommige Azure-regio's.
7. Azure NetApp Files volumes kunnen worden geïmplementeerd als NFSv3-of NFSv 4.1-volumes. Beide protocollen worden ondersteund voor de SAP BI-platform toepassingen.

## <a name="configure-file-systems-on-linux-servers"></a>Bestands systemen op Linux-servers configureren

Voor de stappen in deze sectie worden de volgende voor voegsels gebruikt:

**[A]**: de stap is van toepassing op alle hosts

### <a name="format-and-mount-sap-file-system"></a>SAP-bestands systeem Format teren en koppelen

1. **[A]** alle gekoppelde schijven weer geven

    ```bash
    sudo lsblk
    NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
    sda      8:0    0   30G  0 disk
    ├─sda1   8:1    0    2M  0 part
    ├─sda2   8:2    0  512M  0 part /boot/efi
    ├─sda3   8:3    0    1G  0 part /boot
    └─sda4   8:4    0 28.5G  0 part /
    sdb      8:16   0   32G  0 disk
    └─sdb1   8:17   0   32G  0 part /mnt
    sdc      8:32   0  128G  0 disk
    sr0     11:0    1  628K  0 rom  
    # Premium SSD of 128 GB is attached to Virtual Machine, whose device name is sdc
    ```

2. **[A]** indelings blok apparaat voor/usr/sap

    ```bash
    sudo mkfs.xfs /dev/sdc
    ```

3. **[A]** koppelings Directory maken

    ```bash
    sudo mkdir -p /usr/sap
    ```

4. **[A]** uuid van het apparaat ophalen

    ```bash
    sudo blkid

    #It will display information about block device. Copy UUID of the formatted block device

    /dev/sdc: UUID="0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b" TYPE="xfs"
    ```

5. **[A]** vermelding bestands systeem koppeling in bestand/etc/fstab onderhouden

    ```bash
    sudo echo "UUID=0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b /usr/sap xfs defaults,nofail 0 2" >> /etc/fstab
    ```

6. **[A]** koppel bestands systeem

    ```bash
    sudo mount -a

    sudo df -h

    Filesystem                     Size  Used Avail Use% Mounted on
    devtmpfs                       7.9G  8.0K  7.9G   1% /dev
    tmpfs                          7.9G   82M  7.8G   2% /run
    tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
    /dev/sda4                       29G  1.8G   27G   6% /
    tmpfs                          1.6G     0  1.6G   0% /run/user/1000
    /dev/sda3                     1014M   87M  928M   9% /boot
    /dev/sda2                      512M  1.1M  511M   1% /boot/efi
    /dev/sdb1                       32G   49M   30G   1% /mnt
    /dev/sdc                       128G   29G  100G  23% /usr/sap
    ```

### <a name="mount-azure-netapp-files-volume"></a>Azure NetApp Files volume koppelen

1. **[A]** koppel mappen maken

   ```bash
   sudo mkdir -p /usr/sap/frsinput
   sudo mkdir -p /usr/sap/frsoutput
   ```

2. **[A]** client besturingssysteem configureren ter ondersteuning van de nfsv 4.1-koppeling **(alleen van toepassing als u nfsv 4.1 gebruikt)**

   Als u Azure NetApp Files volumes gebruikt met NFSv 4.1-protocol, voert u de volgende configuratie uit op alle virtuele machines, waarbij Azure NetApp Files NFSv 4.1-volumes moeten worden gekoppeld.

   **NFS-domein instellingen verifiëren**

   Zorg ervoor dat het domein is geconfigureerd als de standaard Azure NetApp Files domein  **defaultv4iddomain.com** en de toewijzing is ingesteld op **niemand**.

   ```bash
   sudo cat /etc/idmapd.conf
   # Example
   [General]
   Domain = defaultv4iddomain.com
   [Mapping]
   Nobody-User = nobody
   Nobody-Group = nobody
   ```

   > [!Important]
   >
   > Zorg ervoor dat u het NFS-domein in/etc/idmapd.conf op de virtuele machine zo instelt dat deze overeenkomt met de standaard domein configuratie op Azure NetApp Files: **defaultv4iddomain.com**. Als er een verschil is tussen de domein configuratie op de NFS-client (de virtuele machine) en de NFS-server, dat wil zeggen de Azure NetApp-configuratie, worden de machtigingen voor bestanden op Azure NetApp-volumes die zijn gekoppeld op de Vm's weer gegeven als niemand.

   Verifiëren `nfs4_disable_idmapping` . Deze moet worden ingesteld op **Y**. Als u de mapstructuur wilt maken waar `nfs4_disable_idmapping` zich bevindt, voert u de koppeling-opdracht uit. U kunt de map niet hand matig maken onder/sys/modules, omdat de toegang is gereserveerd voor de kernel/Stuur Programma's.

   ```bash
   # Check nfs4_disable_idmapping
   cat /sys/module/nfs/parameters/nfs4_disable_idmapping

   # If you need to set nfs4_disable_idmapping to Y
   mkdir /mnt/tmp
   mount -t nfs -o sec=sys,vers=4.1 10.31.2.4:/azusbobi-frsinput /mnt/tmp
   umount /mnt/tmp

   echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping

   # Make the configuration permanent
   echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
   ```

3. **[A]** koppelings vermeldingen toevoegen

   Als NFSv3 wordt gebruikt

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   ```

   Als u NFSv 4.1 gebruikt

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   ```

4. **[A]** NFS-volumes koppelen

   ```bash
   sudo mount -a

   sudo df -h

   Filesystem                     Size  Used Avail Use% Mounted on
   devtmpfs                       7.9G  8.0K  7.9G   1% /dev
   tmpfs                          7.9G   82M  7.8G   2% /run
   tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
   /dev/sda4                       29G  1.8G   27G   6% /
   tmpfs                          1.6G     0  1.6G   0% /run/user/1000
   /dev/sda3                     1014M   87M  928M   9% /boot
   /dev/sda2                      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1                       32G   49M   30G   1% /mnt
   /dev/sdc                       128G   29G  100G  23% /usr/sap
   10.31.2.4:/azusbobi-frsinput   101T   18G  100T   1% /usr/sap/frsinput
   10.31.2.4:/azusbobi-frsoutput  100T  512K  100T   1% /usr/sap/frsoutput
   ```

## <a name="configure-cms-database---azure-database-for-mysql"></a>CMS-data base configureren-Azure data base for MySQL

Deze sectie bevat informatie over het inrichten van Azure Database for MySQL met behulp van Azure Portal. Ook vindt u hier instructies voor het maken van CMS-en audit databases voor het SAP BOBI-platform en een gebruikers account voor toegang tot de data base.

De richt lijnen zijn alleen van toepassing als u Azure DB voor MySQL gebruikt. Raadpleeg voor meer informatie over andere data base (s) SAP of data base-specifieke documentatie voor instructies.

### <a name="create-an-azure-database-for-mysql"></a>Een Azure-Data Base voor MySQL maken

Meld u aan bij Azure Portal en volg de stappen in deze [Snelstartgids van Azure database for MySQL](../../../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Enkele punten om te noteren tijdens het inrichten van Azure Database for MySQL-

1. Selecteer dezelfde regio voor Azure Database for MySQL waar uw SAP BI-platform toepassings servers worden uitgevoerd.

2. Kies een ondersteunde DB-versie op basis van de [product beschikbaarheids matrix (PAM) voor SAP BI](https://support.sap.com/pam) specifiek voor uw SAP BOBI-versie. Volg dezelfde compatibiliteits richtlijnen als geadresseerd voor MySQL AB in SAP PAM

3. Selecteer in "Compute + Storage" **server configureren** en selecteer de juiste prijs categorie op basis van de grootte van de uitvoer.

4. Automatische **groei van opslag** is standaard ingeschakeld. Houd er rekening mee dat [opslag](../../../mysql/concepts-pricing-tiers.md#storage) alleen omhoog kan worden geschaald.

5. Standaard is **een back-up van de Bewaar periode** zeven dagen, maar u kunt deze [optioneel configureren](../../../mysql/howto-restore-server-portal.md#set-backup-configuration) tot 35 dagen.

6. Back-ups van Azure Database for MySQL zijn standaard lokaal redundant, dus als u Server back-ups in geografisch redundante opslag wilt maken, selecteert u **geografisch redundante** **Opties voor back-** redundantie.

> [!NOTE]
> Het wijzigen van de [Opties voor back-upredundantie](../../../mysql/concepts-backup.md#backup-redundancy-options) bij het maken van een server wordt niet ondersteund.

### <a name="configure-connection-security"></a>Verbindingsbeveiliging configureren

De server die is gemaakt, is standaard beveiligd met een firewall en is niet openbaar toegankelijk. Volg de onderstaande stappen om toegang te bieden tot het virtuele netwerk waarin de SAP BI-platform toepassings servers worden uitgevoerd:  

1. Ga naar Server bronnen in het Azure Portal en selecteer **verbindings beveiliging** in het menu links voor uw server resource.
2. Selecteer **Ja** om **toegang tot Azure-Services toe te staan**.
3. Onder VNET-regels selecteert u **bestaand virtueel netwerk toevoegen**. Selecteer het virtuele netwerk en subnet van de SAP BI-platform toepassings server. U moet ook toegang bieden tot Jump box of andere servers van waaruit u [MySQL Workbench](../../../mysql/connect-workbench.md) kunt verbinden met Azure database for MySQL. MySQL Workbench wordt gebruikt voor het maken van een CMS-en audit database
4. Wanneer de virtuele netwerken zijn toegevoegd, selecteert u **Opslaan**.

### <a name="create-cms-and-audit-database"></a>Een CMS-en audit database maken

1. De MySQL-Workbench downloaden en installeren vanaf de [MySQL-website](https://dev.mysql.com/downloads/workbench/). Zorg ervoor dat u MySQL Workbench installeert op de server die toegang heeft tot Azure Database for MySQL.

2. Verbinding maken met de server met behulp van MySQL Workbench. Volg de instructies in dit [artikel](../../../mysql/connect-workbench.md#get-connection-information). Als de verbindings test is geslaagd, wordt het volgende bericht weer gegeven:

   ![SQL Workbench-verbinding](media/businessobjects-deployment-guide/businessobjects-sql-workbench.png)

3. Voer in het tabblad SQL query de volgende query uit om een schema te maken voor CMS-en audit data base.

   ```sql
   # Here cmsbl1 is the database name of CMS database. You can provide the name you want for CMS database.
   CREATE SCHEMA `cmsbl1` DEFAULT CHARACTER SET utf8;

   # auditbl1 is the database name of Audit database. You can provide the name you want for CMS database.
   CREATE SCHEMA `auditbl1` DEFAULT CHARACTER SET utf8;
   ```
   
4. Maak een gebruikers account om verbinding te maken met het schema

   ```sql
   # Create a user that can connect from any host, use the '%' wildcard as a host part
   CREATE USER 'cmsadmin'@'%' IDENTIFIED BY 'password';
   CREATE USER 'auditadmin'@'%' IDENTIFIED BY 'password';

   # Grant all privileges to a user account over a specific database:
   GRANT ALL PRIVILEGES ON cmsbl1.* TO 'cmsadmin'@'%' WITH GRANT OPTION;
   GRANT ALL PRIVILEGES ON auditbl1.* TO 'auditadmin'@'%' WITH GRANT OPTION;

   # Following any updates to the user privileges, be sure to save the changes by issuing the FLUSH PRIVILEGES
   FLUSH PRIVILEGES;
   ```

5. De bevoegdheden en rollen van het MySQL-gebruikers account controleren

   ```sql
   USE sys;
   SHOW GRANTS for 'cmsadmin'@'%';
   +------------------------------------------------------------------------+
   | Grants for cmsadmin@%                                                  |
   +------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `cmsadmin`@`%`                                   |
   | GRANT ALL PRIVILEGES ON `cmsbl1`.* TO `cmsadmin`@`%` WITH GRANT OPTION |
   +------------------------------------------------------------------------+

   USE sys;
   SHOW GRANTS FOR 'auditadmin'@'%';
   +----------------------------------------------------------------------------+
   | Grants for auditadmin@%                                                    |
   +----------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `auditadmin`@`%`                                     |
   | GRANT ALL PRIVILEGES ON `auditbl1`.* TO `auditadmin`@`%` WITH GRANT OPTION |
   +----------------------------------------------------------------------------+
   ```

### <a name="install-mysql-c-api-connector-libmysqlclient-on-linux-server"></a>MySQL C API connector (libmysqlclient) installeren op Linux-server

Voor SAP BOBI-toepassings server voor toegang tot de data base is data base client/drivers vereist. MySQL C API-connector voor Linux moet worden gebruikt voor toegang tot CMS-en audit databases. ODBC-verbinding met CMS-data base wordt niet ondersteund. In deze sectie vindt u instructies voor het instellen van MySQL C API connector op Linux.

1. Raadpleeg de [MySQL-Stuur Programma's en-beheer hulpprogramma's die compatibel zijn met Azure database for MySQL](../../../mysql/concepts-compatibility.md) artikel, waarin de Stuur Programma's worden beschreven die compatibel zijn met Azure database for MySQL. Controleer op het stuur programma **mysql connector/C (libmysqlclient)** in het artikel.

2. Raadpleeg deze [koppeling](https://downloads.mysql.com/archives/c-c/) voor het downloaden van Stuur Programma's.

3. Selecteer het besturings systeem en down load het pakket shared component rpm van MySQL Connector. In dit voor beeld wordt mysql-connector-c-Shared-6.1.11-connector versie gebruikt.

4. Installeer de connector in alle SAP BOBI-toepassings exemplaren.

   ```bash
   # Install rpm package
   SLES: sudo zypper install <package>.rpm
   RHEL: sudo yum install <package>.rpm
   ```

5. Controleer het pad van de libmysqlclient.so

   ```bash
   # Find the location of libmysqlclient.so file
   whereis libmysqlclient

   # sample output
   libmysqlclient: /usr/lib64/libmysqlclient.so
   ```

6. Stel LD_LIBRARY_PATH in om naar `/usr/lib64` map te verwijzen voor gebruikers account dat wordt gebruikt voor installatie.

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LD_LIBRARY_PATH=/usr/lib64
   ```

## <a name="server-preparation"></a>Server voorbereiding

Voor de stappen in deze sectie worden de volgende voor voegsels gebruikt:

**[A]**: de stap is van toepassing op alle hosts.

1. **[A]** op basis van de kern van Linux (SLES of RHEL), moet u kernel-para meters instellen en de vereiste bibliotheken installeren. Raadpleeg de sectie **systeem vereisten** in de [installatie handleiding voor Business Intelligence platform voor UNIX](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US).

2. **[A]** Controleer of de tijd zone op de computer correct is ingesteld. Raadpleeg de [sectie Aanvullende vereisten voor UNIX en Linux](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US/46b143336e041014910aba7db0e91070.html) in de installatie handleiding.

3. **[A] Maak een** gebruikers account (**BL1** adm) en groep (sapsys) waarmee de achtergrond processen van de software kunnen worden uitgevoerd. Gebruik dit account om de installatie uit te voeren en de software uit te voeren. Voor het account zijn geen hoofd bevoegdheden vereist.

4. **[A]** stel het gebruikers account (**BL1** adm) in om een ondersteunde UTF-8-land instelling te gebruiken en zorg ervoor dat uw console software UTF-8-teken sets ondersteunt. Om ervoor te zorgen dat uw besturings systeem gebruikmaakt van de juiste land instelling, stelt u de omgevings variabelen LC_ALL en LANG in op uw voorkeurs land instellingen in uw **BL1** adm-gebruikers omgeving.

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LANG=en_US.utf8
   export LC_ALL=en_US.utf8
   ```

5. **[A]** gebruikers account (**BL1** adm) configureren.

   ```bash
   # Set ulimit for bl1adm to unlimited
   root@azusbosl1:~> ulimit -f unlimited bl1adm
   root@azusbosl1:~> ulimit -u unlimited bl1adm

   root@azusbosl1:~> su - bl1adm
   bl1adm@azusbosl1:~> ulimit -a

   core file size          (blocks, -c) unlimited
   data seg size           (kbytes, -d) unlimited
   scheduling priority             (-e) 0
   file size               (blocks, -f) unlimited
   pending signals                 (-i) 63936
   max locked memory       (kbytes, -l) 64
   max memory size         (kbytes, -m) unlimited
   open files                      (-n) 1024
   pipe size            (512 bytes, -p) 8
   POSIX message queues     (bytes, -q) 819200
   real-time priority              (-r) 0
   stack size              (kbytes, -s) 8192
   cpu time               (seconds, -t) unlimited
   max user processes              (-u) unlimited
   virtual memory          (kbytes, -v) unlimited
   file locks                      (-x) unlimited
   ```

6. Down load en pak media uit voor SAP BusinessObjects BI-platform van SAP Service Marketplace.

## <a name="installation"></a>Installatie

De land instelling controleren van het gebruikers account **BL1** adm op de server

```bash
bl1adm@azusbosl1:~> locale
LANG=en_US.utf8
LC_ALL=en_US.utf8
```

Navigeer naar media van het SAP BusinessObjects BI-platform en voer de volgende opdracht uit met **BL1** adm-gebruiker-

```bash
./setup.sh -InstallDir /usr/sap/BL1
```

Volg de installatie handleiding voor het [SAP BOBI-platform](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM) voor UNIX, specifiek voor uw versie. Enkele punten om te noteren tijdens het installeren van het SAP BOBI-platform.

- In het scherm **product registratie configureren** kunt u de tijdelijke licentie sleutel voor SAP BusinessObjects-oplossingen van sap Note [1288121](https://launchpad.support.sap.com/#/notes/1288121) gebruiken of kunt u een licentie code genereren in SAP Service Marketplace

- Selecteer in het scherm **installatie type selecteren** de optie **volledige** installatie op de eerste server (azusbosl1), voor een andere server (Azusbosl2), selecteer **aangepast/uitvouwen** , waarmee de bestaande BOBI-installatie wordt uitgevouwen.

- Selecteer in het venster **standaard of bestaande data base** selecteren **de optie een bestaande data base configureren**, waarin u wordt gevraagd om CMS-en audit database te selecteren. Selecteer **MySQL** voor het CMS-database type en het type controle database.

  U kunt ook geen controle database selecteren als u geen controle wilt configureren tijdens de installatie.

- Selecteer de gewenste opties op het **scherm Java Web Application Server selecteren** op basis van uw SAP BOBI-architectuur. In dit voor beeld hebben we optie 1 geselecteerd, waarmee Tomcat server op hetzelfde SAP BOBI-platform wordt geïnstalleerd.

- Voer CMS-database gegevens in in de **CMS-opslagplaats database configureren-MySQL**. Voor beeld van invoer voor CMS-database gegevens voor Linux-installatie. Azure Database for MySQL wordt gebruikt op standaard poort 3306
  
  ![SAP BOBI-implementatie in Linux-CMS-data base](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cms.png)

- Beschrijving Voer de gegevens van de audit-data base in de **audit opslagplaats configureren-MySQL** in. Voor beeld van invoer voor de controle van database gegevens voor Linux-installatie.

  ![SAP BOBI-implementatie in Linux-Audit data base](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-audit.png)

- Volg de instructies en voer de vereiste invoer in om de installatie te volt ooien.

Voor implementatie met meerdere exemplaren voert u de installatie-instellingen op de tweede host (azusbosl2) uit. Selecteer in het scherm **installatie type selecteren** de optie **aangepast/uitvouwen** , waarmee de bestaande BOBI-instellingen worden uitgebreid.

In azure data base for MySQL wordt een gateway gebruikt om de verbindingen met Server exemplaren om te leiden. Nadat de verbinding tot stand is gebracht, wordt in de MySQL-client de versie van MySQL weergegeven die in de gateway is ingesteld, niet de feitelijke versie die wordt uitgevoerd op de MySQL-serverinstantie. Als u de versie van uw MySQL-serverinstantie wilt vaststellen, gebruikt u de `SELECT VERSION();`-opdracht bij de MySQL-prompt. In de Central Management-Console (CMC) vindt u een andere database versie die in principe de versie heeft die is ingesteld op de gateway. Controleer de [ondersteunde Azure database for mysql server versies](../../../mysql/concepts-supported-versions.md) voor meer informatie.

![SAP BOBI-implementatie op Linux-CMC-instellingen](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cmc.png)

```sql
# Run direct query to the database using MySQL Workbench

select version();

+-----------+
| version() |
+-----------+
| 8.0.15    |
+-----------+
```

## <a name="post-installation"></a>Na de installatie

### <a name="tomcat-clustering---session-replication"></a>Tomcat clustering-sessie replicatie

Tomcat ondersteunt het clusteren van twee of meer toepassings servers voor sessie replicatie en failover. SAP BOBI-platform sessies worden geserialiseerd, een gebruikers sessie kan naadloos worden uitgevoerd naar een ander exemplaar van Tomcat, zelfs wanneer een toepassings server uitvalt.

Als een gebruiker bijvoorbeeld is verbonden met een webserver die mislukt terwijl de gebruiker in de SAP BI-toepassing naar een mappen hiërarchie navigeert. Met een correct geconfigureerd cluster kan de gebruiker door gaan met de navigatie van de mappen hiërarchie zonder dat deze wordt omgeleid naar de aanmeldings pagina.

In SAP Note [2808640](https://launchpad.support.sap.com/#/notes/2808640)worden de stappen voor het configureren van Tomcat-clustering aangeboden via multi cast. In azure wordt multi cast echter niet ondersteund. Om ervoor te zorgen dat Tomcat-cluster in azure werkt, moet u [StaticMembershipInterceptor](https://tomcat.apache.org/tomcat-8.0-doc/config/cluster-interceptor.html#Static_Membership) (SAP-notitie [2764907](https://launchpad.support.sap.com/#/notes/2764907)) gebruiken. Controleer [Tomcat clustering met statische lidmaatschap voor het SAP BUSINESSOBJECTS BI-platform](https://blogs.sap.com/2020/09/04/sap-on-azure-tomcat-clustering-using-static-membership-for-sap-businessobjects-bi-platform/) op SAP-Blog om Tomcat-cluster in te stellen in Azure.

### <a name="load-balancing-web-tier-of-sap-bi-platform"></a>Weblaag voor taak verdeling van het SAP BI-platform

In de SAP BOBI-implementatie met meerdere instanties worden Java Web Application servers (weblaag) uitgevoerd op twee of meer hosts. Als u de gebruikers taken gelijkmatig over webservers wilt verdelen, kunt u een load balancer tussen eind gebruikers en webservers gebruiken. In azure kunt u Azure Load Balancer of Azure-toepassing gateway gebruiken voor het beheren van verkeer naar uw Web Application servers. Meer informatie over elke aanbieding wordt beschreven in de volgende sectie.

#### <a name="azure-load-balancer-network-based-load-balancer"></a>Azure load balancer (load balancer op basis van het netwerk)

[Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) is een hoge prestatie, lage latentie laag 4 (TCP, UDP) Load Balancer die verkeer distribueert over gezonde virtual machines. Een load balancer Health prob bewaakt een bepaalde poort op elke VM en distribueert alleen verkeer naar een of meer operationele virtuele machine (s). U kunt een open bare load balancer of een interne load balancer kiezen, afhankelijk van of u het SAP BI-platform toegankelijk wilt maken vanaf internet of niet. De zone is redundant, waardoor er hoge Beschik baarheid over Beschikbaarheidszones.

Raadpleeg de sectie interne Load Balancer in onderstaande afbeelding waar Web Application Server wordt uitgevoerd op poort 8080, standaard Tomcat HTTP-poort, die wordt bewaakt door de status test. Alle inkomende aanvragen die afkomstig zijn van eind gebruikers worden omgeleid naar de Web Application servers (azusbosl1 of azusbosl2) in de back-endserver. De Load Balancer biedt geen ondersteuning voor het beëindigen van TLS/SSL (ook wel bekend als TLS/SSL-offloading). Als u Azure load balancer gebruikt voor het distribueren van verkeer via webservers, raden we u aan Standard Load Balancer te gebruiken.

> [!NOTE]
> Wanneer Vm's zonder open bare IP-adressen in de back-endadresgroep van intern (geen openbaar IP-adres load balancer) worden geplaatst, is er geen uitgaande Internet verbinding, tenzij er aanvullende configuratie wordt uitgevoerd om route ring naar open bare eind punten toe te staan. Zie [connectiviteit van open bare eind punten voor virtual machines met behulp van Azure Standard Load Balancer in scenario's met hoge Beschik baarheid voor SAP](high-availability-guide-standard-load-balancer-outbound-connections.md)voor meer informatie over het bezorgen van uitgaande verbindingen.

![Azure Load Balancer het verkeer te verdelen over webservers](media/businessobjects-deployment-guide/businessobjects-deployment-load-balancer.png)

#### <a name="azure-application-gateway-web-application-load-balancer"></a>Azure Application Gateway (webtoepassing load balancer)

[Azure-toepassing gateway (AGW)](../../../application-gateway/overview.md) biedt ADC (Application Delivery controller) als een service, die wordt gebruikt om gebruikers verkeer naar een of meer Web Application servers te sturen. Het biedt diverse Layer 7-functies voor taak verdeling, zoals TLS/SSL-offload, Web Application firewall (WAF), sessie affiniteit op basis van cookies en anderen voor uw toepassingen.

In SAP BI-platform stuurt Application Gateway het webverkeer van de toepassing naar de opgegeven bronnen in een back-end-pool-azusbosl1 of azusbos2. U wijst een listener aan poort toe, maakt regels en voegt resources toe aan een back-end-groep. In de onderstaande afbeelding fungeren toepassings gateway met het privé-frontend-IP-adres (10.31.3.20) als ingangs punt voor de gebruikers, verwerkt binnenkomende TLS/SSL-verbindingen (HTTPS-TCP/443), ontsleutelt de TLS/SSL en wordt de niet-versleutelde aanvraag (HTTP-TCP/8080) door gegeven aan de servers in de back-end-pool. Met de ingebouwde functie voor het beëindigen van TLS/SSL moeten we een TLS/SSL-certificaat op Application Gateway onderhouden, waardoor de bewerkingen worden vereenvoudigd.

![Application Gateway het verkeer te verdelen over webservers](media/businessobjects-deployment-guide/businessobjects-deployment-application-gateway.png)

Als u Application Gateway wilt configureren voor de SAP BOBI-webserver, kunt u verwijzen naar [Load Balancing SAP BOBI webservers met behulp van Azure-toepassing gateway](https://blogs.sap.com/2020/09/17/sap-on-azure-load-balancing-web-application-servers-for-sap-bobi-using-azure-application-gateway/) op SAP blog.

> [!NOTE]
> U wordt aangeraden Azure-toepassing gateway te gebruiken om het verkeer naar de webserver te verdelen terwijl het functionaliteits niveau van de functie biedt als SSL-offload, het SSL-beheer centraliseren om de overhead van versleuteling en ontsleuteling op de server te verminderen, Round-Robin algoritme voor het distribueren van verkeer, WAF-mogelijkheden (Web Application firewall), hoge Beschik baarheid, enzovoort.

### <a name="sap-businessobjects-bi-platform---back-up-and-restore"></a>SAP BusinessObjects BI-platform: back-up en herstel

Backup en Restore is het proces van het maken van periodieke kopieën van gegevens en toepassingen naar een afzonderlijke locatie. Het kan dus worden hersteld of hersteld naar een eerdere status als de oorspronkelijke gegevens of toepassingen verloren zijn gegaan of beschadigd raken. Het is ook een essentieel onderdeel van een bedrijfs strategie voor nood herstel.

Voor het ontwikkelen van een uitgebreide strategie voor back-up en herstel voor het SAP BOBI-platform, identificeert u de onderdelen die leiden tot uitval tijd of onderbreking van de toepassing. In het SAP BOBI-platform zijn back-ups van de volgende onderdelen cruciaal om de toepassing te beveiligen.

- SAP BOBI-installatiemap (beheerde Premium-schijven)
- Bestands opslagplaats server (Azure NetApp Files of Azure Premium-bestanden)
- CMS-data base (Azure Database for MySQL of Data Base op Azure VM)

In de volgende sectie wordt beschreven hoe u een strategie voor back-up en herstel implementeert voor elk onderdeel op het SAP BOBI-platform.

#### <a name="backup--restore-for-sap-bobi-installation-directory"></a>Back-up & herstellen voor de SAP BOBI-installatiemap

In Azure is de eenvoudigste manier om back-ups te maken van toepassings servers en alle gekoppelde schijven is door gebruik te maken van [Azure backup](../../../backup/backup-overview.md) -service. Het biedt onafhankelijke en geïsoleerde back-ups om onbedoelde vernietiging van de gegevens op uw Vm's te bewaken. Back-ups worden opgeslagen in een Recovery Services-kluis met ingebouwd beheer van herstelpunten. Configuratie en schalen zijn eenvoudig, back-ups worden geoptimaliseerd en kunnen eenvoudig worden hersteld wanneer dat nodig is.

Als onderdeel van het back-upproces wordt er een moment opname gemaakt en worden de gegevens overgedragen naar de Recovery service-kluis zonder dat dit van invloed is op de werk belasting van de productie. De moment opname biedt een verschillend consistentie niveau, zoals beschreven in [consistentie artikel over moment opnamen](../../../backup/backup-azure-vms-introduction.md#snapshot-consistency) . U kunt er ook voor kiezen om een back-up te maken van de subset van de gegevens schijven in de virtuele machine met behulp van de functie voor back-up en herstel Zie back-updocument van [Azure VM](../../../backup/backup-azure-vms-introduction.md) en [Veelgestelde vragen over back-ups van virtuele machines van Azure](../../../backup/backup-azure-vm-backup-faq.yml)voor meer informatie.

#### <a name="backup--restore-for-file-repository-server"></a>Back-up & herstellen voor de bestands opslagplaats server

U kunt voor **Azure NetApp files** een moment opnamen op aanvraag maken en automatische moment opnamen plannen met behulp van een momentopname beleid. Momentopname kopieën bieden een tijdgebonden kopie van uw ANF-volume. Zie [moment opnamen beheren met Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-manage-snapshots.md)voor meer informatie.

**Azure files** back-up is geïntegreerd met de systeem eigen [Azure backup](../../../backup/backup-overview.md) -service, waarmee de functie back-up en herstellen wordt gecentraliseerd, samen met vm's back-ups en vereenvoudigt het werk. Zie back-ups van [Azure-bestands share](../../../backup/azure-file-share-backup-overview.md) en [Veelgestelde vragen-back-ups maken Azure files](../../../backup/backup-azure-files-faq.md)voor meer informatie.

#### <a name="backup--restore-for-cms-database"></a>Back-up & herstellen voor CMS-data base

Azure data base van MySQL is een DBaaS-aanbieding in azure, die automatisch server back-ups maakt en opslaat in een door de gebruiker geconfigureerde lokaal redundante of geografisch redundante opslag. Azure data base van MySQL maakt back-ups van de gegevens bestanden en het transactie logboek. Afhankelijk van de ondersteunde maximale opslag grootte, neemt deze volledige en differentiële back-ups (Maxi maal 4 TB opslag servers) of back-up van moment opname (Maxi maal 16 TB aan opslag servers). Met deze back-ups kunt u op elk moment binnen de geconfigureerde back-upperiode een server herstellen. De standaard retentie periode voor back-ups is zeven dagen, die u [Optioneel kunt configureren](../../../mysql/howto-restore-server-portal.md#set-backup-configuration) tot drie dagen. Alle back-ups worden versleuteld met AES 256-bits versleuteling.

Deze back-upbestanden zijn niet zichtbaar voor gebruikers en kunnen niet worden geëxporteerd. Deze back-ups kunnen alleen worden gebruikt voor herstel bewerkingen in Azure Database for MySQL. U kunt [mysqldump](../../../mysql/concepts-migrate-dump-restore.md) gebruiken om een Data Base te kopiëren. Zie [back-up en herstellen in azure database for MySQL](../../../mysql/concepts-backup.md)voor meer informatie.

Voor de data base die op Virtual Machines is geïnstalleerd, kunt u standaard back-uphulpprogramma's of [Azure backup](../../../backup/sap-hana-db-about.md) voor Hana-Data Base gebruiken. Als de Azure-Services en-hulpprogram ma's niet voldoen aan uw vereisten, kunt u ook andere back-upprogramma's of script gebruiken om back-ups van schijven te maken.

## <a name="sap-businessobjects-bi-platform-reliability"></a>Betrouw baarheid van SAP BusinessObjects BI-platform

Het SAP BusinessObjects BI-platform bevat verschillende lagen die zijn geoptimaliseerd voor specifieke taken en bewerkingen. Wanneer een onderdeel van een wille keurige laag niet meer beschikbaar is, wordt SAP BOBI-toepassing niet meer toegankelijk of wordt bepaalde functionaliteit van de toepassing niet meer gebruikt. U moet er dus voor zorgen dat elke laag betrouwbaar is om toepassings operationeel te houden zonder dat er bedrijfs onderbrekingen zijn.

Deze sectie is gericht op de volgende opties voor het SAP BOBI-platform-

- **Hoge Beschik baarheid:** Een platform met hoge Beschik baarheid heeft ten minste twee van alles binnen de Azure-regio, zodat de toepassing operationeel blijft als een van de servers niet meer beschikbaar is.
- **Herstel na nood geval:** Het is een proces waarbij de functionaliteit van uw toepassing wordt hersteld als er sprake is van een onherstelbaar verlies, zoals de volledige Azure-regio niet meer beschikbaar is vanwege een natuur ramp.

De implementatie van deze oplossing varieert afhankelijk van de aard van de systeem installatie in Azure. Klanten moeten dus een oplossing voor hoge Beschik baarheid en herstel na nood gevallen aanpassen op basis van hun zakelijke vereisten.

### <a name="high-availability"></a>Hoge beschikbaarheid

Hoge Beschik baarheid verwijst naar een set technologieën die IT-onderbrekingen kan minimaliseren door de bedrijfs continuïteit van toepassingen/services te bieden via redundante, fout tolerante of met failover beveiligde onderdelen binnen hetzelfde Data Center. In ons geval zijn de data centers binnen één Azure-regio. De [architectuur en scenario's met hoge Beschik baarheid voor SAP](sap-high-availability-architecture-scenarios.md) bieden een eerste inzicht in de verschillende technieken voor hoge Beschik baarheid en aanbevelingen die worden aangeboden op Azure voor SAP-toepassingen. Dit geldt voor de instructies in deze sectie.

Op basis van het schaal resultaat van het SAP BOBI-platform moet u het landschap ontwerpen en de distributie van BI-onderdelen in azure-Virtual Machines en-subnetten bepalen. Het niveau van redundantie in de gedistribueerde architectuur is afhankelijk van de bedrijfs vereiste herstel tijd van de onderneming (RTO) en het beoogde herstel punt (RPO). Het SAP BOBI-platform bevat verschillende lagen en onderdelen op elke laag, die zijn ontworpen om redundantie te garanderen. Als er een fout optreedt in een onderdeel, is er weinig te voor komen dat uw SAP BOBI-toepassing wordt onderbroken. Bijvoorbeeld:

- Redundante toepassings servers als BI-toepassings servers en webserver
- Unieke onderdelen, zoals CMS-data base, bestands opslagplaats server, Load Balancer

In de volgende sectie wordt beschreven hoe u hoge Beschik baarheid kunt bezorgen voor elk onderdeel van het SAP BOBI-platform.

#### <a name="high-availability-for-application-servers"></a>Hoge Beschik baarheid voor toepassings servers

Voor BI-en Web Application-servers, ongeacht of deze afzonderlijk of samen worden geïnstalleerd, is geen specifieke oplossing voor hoge Beschik baarheid nodig. U kunt hoge Beschik baarheid bezorgen door redundantie, dat wil zeggen door meerdere exemplaren van BI-en webservers in verschillende Azure-Virtual Machines te configureren.

Om de impact van uitval tijd te verminderen vanwege een of meer gebeurtenissen, is het raadzaam om de volgende procedure te volgen met hoge Beschik baarheid voor de toepassings servers die worden uitgevoerd op meerdere virtuele machines.

- Gebruik Beschikbaarheidszones om datacenter fouten te beveiligen.
- Configureer meerdere Virtual Machines in een Beschikbaarheidsset voor redundantie.
- Gebruik Managed Disks voor Vm's in een Beschikbaarheidsset.
- Configureer elke toepassingslaag in afzonderlijke beschikbaarheids sets.

Schakel [de beschik baarheid van virtuele Linux-machines beheren](../../availability.md) in voor meer informatie.

#### <a name="high-availability-for-cms-database"></a>Hoge Beschik baarheid voor CMS-data base

Als u de service Azure data base as a Service (DBaaS) voor CMS-data base gebruikt, wordt standaard een Framework met hoge Beschik baarheid beschikbaar gesteld. U hoeft alleen maar de regio en de service in te stellen op basis van hoge Beschik baarheid, redundantie en tolerantie, zonder dat u extra onderdelen hoeft te configureren. Voor meer informatie over de SLA van ondersteunde DBaaS-aanbiedingen op Azure, controleert u [hoge Beschik baarheid in azure database for MySQL](../../../mysql/concepts-high-availability.md) en [hoge Beschik baarheid voor Azure SQL database](../../../azure-sql/database/high-availability-sla.md)

Raadpleeg de [DBMS-implementatie handleidingen voor de SAP-werk belasting](dbms_guide_general.md)voor een andere DBMS-implementatie voor de CMS-data base. deze biedt inzicht in de verschillende DBMS-implementatie en de aanpak ervan om hoge Beschik baarheid te bereiken.

#### <a name="high-availability-for-file-repository-server"></a>Hoge Beschik baarheid voor de bestands opslagplaats server

File Repository Server (FRS) verwijst naar de schijf mappen waarin inhoud zoals rapporten, universums en verbindingen worden opgeslagen. Het wordt gedeeld op alle toepassings servers van dat systeem. U moet er dus voor zorgen dat deze Maxi maal beschikbaar is.

In azure kunt u kiezen voor [Azure Premium-bestanden](../../../storage/files/storage-files-introduction.md) of [Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-introduction.md) voor bestands shares die zijn ontworpen om Maxi maal beschikbaar te zijn en zeer duurzaam zijn. Zie de sectie [Redundantie](../../../storage/files/storage-files-planning.md#redundancy) voor Azure files voor meer informatie.

> [!NOTE]
> Het SMB-protocol voor Azure Files is algemeen beschikbaar, maar de NFS-protocol ondersteuning voor Azure Files is momenteel in de preview-versie. Zie voor meer informatie [NFS 4,1-ondersteuning voor Azure files is nu](https://azure.microsoft.com/en-us/blog/nfs-41-support-for-azure-files-is-now-in-preview/) beschikbaar als preview-versie

Omdat deze bestands share service niet beschikbaar is in alle regio's, moet u ervoor zorgen dat u verwijst naar [producten die beschikbaar zijn per regio](https://azure.microsoft.com/en-us/global-infrastructure/services/) -site om actuele informatie te vinden. Als de service niet beschikbaar is in uw regio, kunt u een NFS-server maken van waaruit u het bestands systeem kunt delen met de SAP BOBI-toepassing. Maar u moet ook rekening houden met de hoge Beschik baarheid.

#### <a name="high-availability-for-load-balancer"></a>Hoge Beschik baarheid voor load balancer

Als u verkeer wilt distribueren via een webserver, kunt u Azure Load Balancer of Azure-toepassing gateway gebruiken. De redundantie voor een van de load balancer kan worden bereikt op basis van de SKU die u voor de implementatie kiest.

- Voor Azure Load Balancer kan redundantie worden bereikt door Standard Load Balancer frontend te configureren als zone-redundant. Zie [Standard Load Balancer en Beschikbaarheidszones](../../../load-balancer/load-balancer-standard-availability-zones.md) voor meer informatie.
- Voor Application Gateway kan hoge Beschik baarheid worden bereikt op basis van het type laag dat tijdens de implementatie is geselecteerd.
  - v1 SKU ondersteunt scenario's met hoge Beschik baarheid wanneer u twee of meer exemplaren hebt geïmplementeerd. Azure distribueert deze instanties over update-en fout domeinen om ervoor te zorgen dat de exemplaren niet allemaal op hetzelfde moment worden uitgevoerd. Met deze SKU kan redundantie worden bereikt binnen de zone
  - v2 SKU zorgt er automatisch voor dat nieuwe instanties worden verspreid over fout domeinen en update domeinen. Als u zone redundantie kiest, worden de nieuwste instanties ook verspreid over de beschik bare zones om zonegebonden-fout tolerantie aan te bieden. Raadpleeg voor meer informatie automatisch [schalen en zone-redundante Application Gateway v2](../../../application-gateway/application-gateway-autoscaling-zone-redundant.md)

#### <a name="reference-high-availability-architecture-for-sap-businessobjects-bi-platform"></a>Naslag informatie over de architectuur met hoge Beschik baarheid voor het SAP BusinessObjects BI-platform

Onder referentie architectuur wordt de installatie van het SAP BOBI-platform beschreven met behulp van de beschikbaarheidsset, die virtuele machines in de zone redundantie en beschik baarheid biedt. De architectuur bevat een demonstratie van het gebruik van verschillende Azure-Services, zoals Azure-toepassing gateway, Azure NetApp Files en Azure Database for MySQL voor het SAP BOBI-platform dat ingebouwde redundantie biedt, waardoor de complexiteit van het beheer van verschillende oplossingen met hoge Beschik baarheid wordt beperkt.

In de onderstaande afbeelding wordt het binnenkomende verkeer (HTTPS-TCP/443) gelijkmatig verdeeld met behulp van Azure-toepassing gateway v1 SKU, die Maxi maal beschikbaar is wanneer deze wordt geïmplementeerd op twee of meer exemplaren. Meerdere exemplaren van webserver, beheerser vers en verwerkings servers worden geïmplementeerd in afzonderlijke Virtual Machines om redundantie te verzorgen en elke laag wordt geïmplementeerd in afzonderlijke beschikbaarheids sets. Azure NetApp Files heeft ingebouwde redundantie binnen Data Center, zodat uw ANF-volumes voor de bestands opslagplaats server Maxi maal beschikbaar zijn. CMS-data base is ingericht op Azure Database for MySQL (DBaaS) met inherente hoge Beschik baarheid. Zie voor meer informatie [hoge Beschik baarheid in azure database for MySQL](../../../mysql/concepts-high-availability.md) Guide.

![SAP BusinessObjects BI-platform redundantie met beschikbaarheids sets](media/businessobjects-deployment-guide/businessobjects-deployment-high-availability.png)

De bovenstaande architectuur biedt inzicht in de manier waarop SAP BOBI-implementatie in azure kan worden uitgevoerd. Maar dit geldt niet voor alle mogelijke configuratie opties voor het SAP BOBI-platform op Azure. De klant kan hun implementatie aanpassen op basis van hun bedrijfs behoeften door verschillende producten/services te kiezen voor verschillende onderdelen, zoals Load Balancer, bestands opslagplaats server en DBMS.

In verschillende Azure-regio's worden Beschikbaarheidszones aangeboden, wat betekent dat het onafhankelijke aanbod van voedings bron, koeling en netwerk is. Hiermee kunnen klanten toepassingen implementeren in twee of drie beschik bare zones. Voor klanten die hoge Beschik baarheid willen hebben over AZs, kan het SAP BOBI-platform implementeren in verschillende beschikbaarheids zones, zodat elk onderdeel in de toepassing zone redundant is.

### <a name="disaster-recovery"></a>Herstel na noodgeval

De instructie in dit gedeelte beschrijft de strategie voor het bieden van herstel na nood gevallen voor het SAP BOBI-platform. Het is een aanvulling op het [nood herstel voor SAP](../../../site-recovery/site-recovery-sap.md) -document, dat de primaire bronnen voor de algemene SAP-nood herstel methode vertegenwoordigt.

#### <a name="reference-disaster-recovery-architecture-for-sap-businessobjects-bi-platform"></a>Naslag informatie over herstel na nood gevallen voor het SAP BusinessObjects BI-platform

Deze referentie architectuur voert een implementatie met meerdere exemplaren van het SAP BOBI-platform uit met redundante toepassings servers. Voor herstel na nood gevallen moet u een failover van alle lagen naar een secundaire regio uitvoeren. Voor elke laag wordt een andere strategie gebruikt voor herstel na nood gevallen.

![Herstel na nood gevallen voor SAP BusinessObjects BI-platform](media/businessobjects-deployment-guide/businessobjects-deployment-disaster-recovery.png)

#### <a name="load-balancer"></a>Load balancer

Load Balancer wordt gebruikt voor het distribueren van verkeer via Web Application servers van het SAP BOBI-platform. Voor het realiseren van DR voor Azure-toepassing gateway, implementeert u parallelle installatie van Application Gateway op de secundaire regio.

#### <a name="virtual-machines-running-web-and-bi-application-servers"></a>Virtuele machines met web-en BI-toepassings servers

Azure Site Recovery-service kan worden gebruikt om Virtual Machines met web-en BI-toepassings servers te repliceren op de secundaire regio. De servers in de secundaire regio worden gerepliceerd, zodat u, wanneer er storingen en storingen optreden, u eenvoudig een failover naar uw gerepliceerde omgeving kunt uitvoeren en met werken

#### <a name="file-repository-servers"></a>Bestands opslagplaats servers

- **Azure NetApp files** biedt NFS-en SMB-volumes, dus elk hulp programma voor het kopiëren van bestanden kan worden gebruikt om gegevens tussen Azure-regio's te repliceren. Zie [Veelgestelde vragen over Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-faqs.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region) voor meer informatie over het kopiëren van een ANF-volume in een andere regio.

  U kunt Azure NetApp Files replicatie voor meerdere regio's gebruiken. Dit is momenteel als [Preview-versie](https://azure.microsoft.com/en-us/blog/azure-netapp-files-cross-region-replication-and-new-enhancements-in-preview/) die gebruikmaakt van de NetApp SnapMirror®-technologie. Daarom worden alleen gewijzigde blokken via het netwerk verzonden met een gecomprimeerde, efficiënte indeling. Deze bedrijfs technologie minimaliseert de hoeveelheid gegevens die nodig zijn voor replicatie over de regio's, waardoor de kosten voor gegevens overdracht worden bespaard. Ook wordt de replicatie tijd verkort, zodat u een kleinere herstel punt doelstelling (RPO) kunt krijgen. Raadpleeg de [vereisten en overwegingen voor het gebruik van replicatie tussen regio's](../../../azure-netapp-files/cross-region-replication-requirements-considerations.md) voor meer informatie.

- **Azure Premium-bestanden** ondersteunen alleen lokaal redundante (LRS) en zone redundante opslag (ZRS). Voor de DR-strategie van Azure Premium-bestanden kunt u [AzCopy](../../../storage/common/storage-use-azcopy-v10.md) of [Azure PowerShell](/powershell/module/az.storage/) gebruiken om uw bestanden te kopiëren naar een ander opslag account in een andere regio. Zie [nood herstel en failover van het opslag account](../../../storage/common/storage-disaster-recovery-guidance.md) voor meer informatie.

#### <a name="cms-database"></a>CMS-data base

Azure Database for MySQL biedt meerdere opties voor het herstellen van de Data Base als er sprake is van een nood geval. Kies de juiste optie voor uw bedrijf.

- Schakel replica's met meerdere regio's in om uw bedrijfs continuïteit en planning voor herstel na nood gevallen te verbeteren. U kunt repliceren van de bron server naar Maxi maal vijf replica's. Lees replica's worden asynchroon bijgewerkt met de binaire logboek replicatie technologie van MySQL. Replica's zijn nieuwe servers die u op dezelfde manier beheert als gewone Azure Database for MySQL servers. Meer informatie over het lezen van replica's, beschik bare regio's, beperkingen en het uitvoeren van failover vanuit het [artikel concepten van replica's lezen](../../../mysql/concepts-read-replicas.md).

- Gebruik de functie geo-Restore van Azure Database for MySQL waarmee de server wordt hersteld met behulp van geo-redundante back-ups. Deze back-ups zijn toegankelijk, zelfs wanneer de regio waarop uw server wordt gehost, offline is. U kunt van deze back-ups naar een andere regio herstellen en uw server weer online brengen.

  > [!NOTE]
  > Geo-herstel is alleen mogelijk als u de server hebt ingericht met geografisch redundante back-upopslag. Het wijzigen van de **Opties voor back-upredundantie** bij het maken van een server wordt niet ondersteund. Zie [back-upredundantie](../../../mysql/concepts-backup.md#backup-redundancy-options) artikel voor meer informatie.

Hier volgt een aanbeveling voor herstel na nood gevallen van elke laag die in dit voor beeld wordt gebruikt.

| SAP BOBI-platform lagen   | Aanbeveling                                                                                           |
|---------------------------|----------------------------------------------------------------------------------------------------------|
| Azure Application Gateway | Parallelle installatie van Application Gateway op de secundaire regio                                                |
| Web Application servers   | Repliceren met behulp van Site Recovery                                                                         |
| BI-toepassings servers    | Repliceren met behulp van Site Recovery                                                                         |
| Azure NetApp Files        | Op bestanden gebaseerd hulp programma voor het repliceren van gegevens naar een secundaire regio **of** ANF Kruis regio replicatie (preview-versie) |
| Azure Database for MySQL  | U hebt replica's gelezen **of** u herstelt back-ups van geo-redundante back-ups.                             |

## <a name="next-steps"></a>Volgende stappen

- [Herstel na nood geval instellen voor een implementatie van SAP-apps met meerdere lagen](../../../site-recovery/site-recovery-sap.md)
- [Azure Virtual Machines planning en implementatie voor SAP](planning-guide.md)
- [Azure Virtual Machines-implementatie voor SAP](deployment-guide.md)
- [Azure Virtual Machines DBMS-implementatie voor SAP](./dbms_guide_general.md)
