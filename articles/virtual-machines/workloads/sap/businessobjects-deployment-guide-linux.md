---
title: SAP BusinessObjects BI Platform Deployment on Azure for Linux | Microsoft Docs
description: SAP BusinessObjects BI Platform implementeren en configureren in Azure voor Linux
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
ms.openlocfilehash: b16a2d9f779232e59eb883f6a254be22990f5c78
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520017"
---
# <a name="sap-businessobjects-bi-platform-deployment-guide-for-linux-on-azure"></a>Handleiding voor SAP BusinessObjects BI-platformimplementatie voor Linux in Azure

In dit artikel wordt de strategie beschreven voor het implementeren van SAP BOBI Platform in Azure voor Linux. In dit voorbeeld worden twee virtuele machines Premium - SSD Managed Disks als installatiemap zijn geconfigureerd. Azure Database for MySQL wordt gebruikt voor de CMS-database en Azure NetApp Files server voor bestandsopslagplaats wordt gedeeld op beide servers. De standaard Tomcat Java-webtoepassing en BI Platform-toepassing worden samen geïnstalleerd op beide virtuele machines. Voor het laden van de gebruikersaanvraag wordt Application Gateway gebruikt met native TLS/SSL-offloadingmogelijkheden.

Dit type architectuur is effectief voor een kleine implementatie- of niet-productieomgeving. Voor productie- of grootschalige implementaties kunt u afzonderlijke hosts voor webtoepassing hebben en ook meerdere BOBI-toepassingen hebben, zodat de server meer informatie kan verwerken.

![SAP BOBI-implementatie in Azure voor Linux](media/businessobjects-deployment-guide/businessobjects-deployment-linux.png)

In dit voorbeeld worden de onderstaande productversie en bestandssysteemindeling gebruikt

- SAP BusinessObjects Platform 4.3
- SUSE Linux Enterprise Server 12 SP5
- Azure Database for MySQL (versie: 8.0.15)
- MySQL C API Connector - libmysqlclient (versie: 6.1.11)

| Bestandssysteem        | Description                                                                                                               | Grootte (GB)             | Eigenaar  | Groep  | Storage                    |
|--------------------|---------------------------------------------------------------------------------------------------------------------------|-----------------------|--------|--------|----------------------------|
| /usr/sap           | Het bestandssysteem voor de installatie van het SAP BOBI-exemplaar, de standaard Tomcat-webtoepassing en databasest stuurprogramma's (indien nodig) | Richtlijnen voor SAP-formaat | bl1adm | sapsys | Beheerde Premium-schijf - SSD |
| /usr/sap/frsinput  | De map voor de mount is voor de gedeelde bestanden op alle BOBI-hosts die worden gebruikt als opslagplaatsmap voor invoerbestanden  | Zakelijke behoefte         | bl1adm | sapsys | Azure NetApp Files         |
| /usr/sap/frsoutput | De map mount is voor de gedeelde bestanden op alle BOBI-hosts die worden gebruikt als map opslagplaats voor uitvoerbestanden | Zakelijke behoefte         | bl1adm | sapsys | Azure NetApp Files         |

## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>Virtuele Linux-machine implementeren via Azure Portal

In deze sectie maken we twee virtuele machines (VM's) met een installatie installatie afbeelding van het Linux-besturingssysteem (OS) voor SAP BOBI Platform. De stappen op hoog niveau voor het maken Virtual Machines zijn als volgt:

1. Een [resourcegroep maken](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)

2. Maak een [Virtual Network](../../../virtual-network/quick-create-portal.md#create-a-virtual-network).

   - Gebruik niet één subnet voor alle Azure-services in sap bi-platformimplementatie. Op basis van de SAP BI Platform-architectuur moet u meerdere subnetten maken. In deze implementatie maken we drie subnetten: Toepassingssubnet, Subnet bestandsopslag voor bestandsopslag en Application Gateway subnet.
   - In Azure moeten Application Gateway en Azure NetApp Files zich altijd op een afzonderlijk subnet. Raadpleeg [Azure Application Gateway](../../../application-gateway/configuration-overview.md) en [richtlijnen voor Azure NetApp Files netwerkplanning](../../../azure-netapp-files/azure-netapp-files-network-topologies.md) voor meer informatie.

3. Maak een beschikbaarheidsset.

   - Plaats virtuele machines voor elke laag in een beschikbaarheidsset om redundantie te bereiken voor elke laag in de implementatie van meerdere exemplaren. Zorg ervoor dat u de beschikbaarheidssets voor elke laag scheidt op basis van uw architectuur.

4. Maak virtuele machine 1 **(azusbosl1).**

   - U kunt een aangepaste afbeelding gebruiken of een afbeelding kiezen uit Azure Marketplace. Raadpleeg [Deploying a VM from the Azure Marketplace for SAP](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap) (Een VM implementeren vanuit de Azure Marketplace voor SAP) of [Deploying a VM with a custom image for SAP](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-2-deploying-a-vm-with-a-custom-image-for-sap) (Een VM implementeren met een aangepaste afbeelding voor SAP op basis van uw behoefte).

5. Maak virtuele machine 2 **(azusbosl2).**
6. Voeg één Premium - SSD toe. Deze wordt gebruikt als SAP BOBI-installatiemap.

## <a name="provision-azure-netapp-files"></a>Inrichting Azure NetApp Files

Voordat u verder gaat met de installatie van Azure NetApp Files, moet u zich vertrouwd maken met de Documentatie voor Azure [NetApp Files.](../../../azure-netapp-files/azure-netapp-files-introduction.md)

Azure NetApp Files is beschikbaar in verschillende [Azure-regio's.](https://azure.microsoft.com/global-infrastructure/services/?products=netapp) Controleer of uw geselecteerde Azure-regio een Azure NetApp Files.

Gebruik [Azure NetApp Files beschikbaarheid per Azure-regio](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all) om de beschikbaarheid van Azure NetApp Files per regio te controleren.

Vraag onboarding aan Azure NetApp Files door naar Registreren voor Azure NetApp Files [te gaan](../../../azure-netapp-files/azure-netapp-files-register.md) voordat u de Azure NetApp Files.

### <a name="deploy-azure-netapp-files-resources"></a>Uw Azure NetApp Files implementeren

In de volgende instructies wordt ervan uit gegaan dat u uw virtuele [Azure-netwerk al hebt geïmplementeerd.](../../../virtual-network/virtual-networks-overview.md) De Azure NetApp Files-resources en VM's, waar de Azure NetApp Files-resources worden aangesloten, moeten worden geïmplementeerd in hetzelfde virtuele Azure-netwerk of in virtuele Azure-peernetwerken.

1. Als u de resources nog niet hebt geïmplementeerd, vraagt u [onboarding aan bij Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-register.md).

2. Maak een NetApp-account in de geselecteerde Azure-regio door de instructies in [Een NetApp-account maken te volgen.](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)

3. Stel een Azure NetApp Files-capaciteitspool in door de instructies te volgen in [Set up an Azure NetApp Files capacity pool (Een Azure NetApp Files-capaciteitspool instellen).](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)

   - De SAP BI Platform-architectuur die in dit artikel wordt gepresenteerd, maakt gebruik van één Azure NetApp Files capaciteitspool op *het niveau van* de Premium-service. Voor de SAP BI-bestandsopslagplaatsserver in Azure wordt u aangeraden een Azure NetApp Files *Premium-* of [Ultra-serviceniveau te gebruiken.](../../../azure-netapp-files/azure-netapp-files-service-levels.md) 

4. Delegeer een subnet aan Azure NetApp Files, zoals beschreven in [Delegeren van een subnet aan Azure NetApp Files.](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)

5. Implementeer Azure NetApp Files volumes door de instructies te volgen in [Een NFS-volume voor Azure NetApp Files.](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)

   ANF-volume kan worden geïmplementeerd als NFSv3 en NFSv4.1, omdat beide protocollen worden ondersteund voor SAP BOBI Platform. Implementeer de volumes in Azure NetApp Files subnet. De IP-adressen van de Azure NetApp-volumes worden automatisch toegewezen.

Houd er rekening mee dat de Azure NetApp Files en de Azure-VM's zich in hetzelfde virtuele Azure-netwerk of in virtuele Azure-peernetwerken moeten zijn. azusbobi-frsinput, azusbobi-frsoutput zijn bijvoorbeeld de volumenamen en nfs://10.31.2.4/azusbobi-frsinput, nfs://10.31.2.4/azusbobi-frsoutput zijn de bestandspaden voor de Azure NetApp Files Volumes.

- Volume azusbobi-frsinput (nfs://10.31.2.4/azusbobi-frsinput)
- Volume azusbobi-frsoutput (nfs://10.31.2.4/azusbobi-frsoutput)

### <a name="important-considerations"></a>Belangrijke overwegingen

Bij het maken van uw Azure NetApp Files voor SAP BOBI Platform-bestandsopslagplaatsserver, moet u rekening houden met het volgende:

1. De minimale capaciteitspool is 4 tebibyte (TiB).
2. De minimale volumegrootte is 100 gibibyte (GiB).
3. Azure NetApp Files en alle virtuele machines waarop de Azure NetApp Files-volumes worden aangesloten, moeten zich in hetzelfde virtuele Azure-netwerk of in [peered](../../../virtual-network/virtual-network-peering-overview.md) virtuele netwerken in dezelfde regio. Azure NetApp Files toegang via VNET-peering in dezelfde regio wordt nu ondersteund. Azure NetApp-toegang via wereldwijde peering wordt nog niet ondersteund.
4. Het geselecteerde virtuele netwerk moet een subnet hebben dat wordt gedelegeerd aan Azure NetApp Files.
5. Met het Azure NetApp Files [exportbeleid](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)kunt u de toegestane clients, het toegangstype (lezen/schrijven, alleen-lezen, etc.) bepalen.
6. De Azure NetApp Files is nog niet zonebewust. Op dit moment is de functie niet geïmplementeerd in alle beschikbaarheidszones in een Azure-regio. Let op de mogelijke gevolgen voor latentie in sommige Azure-regio's.
7. Azure NetApp Files kunnen worden geïmplementeerd als NFSv3- of NFSv4.1-volumes. Beide protocollen worden ondersteund voor de SAP BI-platformtoepassingen.

## <a name="configure-file-systems-on-linux-servers"></a>Bestandssystemen configureren op Linux-servers

In de stappen in deze sectie worden de volgende voorvoegsels gebruikt:

**[A]**: De stap is van toepassing op alle hosts

### <a name="format-and-mount-sap-file-system"></a>SAP-bestandssysteem opmaken en mounten

1. **[A]** Alle gekoppelde schijven op een lijst zetten

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

2. **[A]** Blokapparaat opmaken voor /usr/sap

    ```bash
    sudo mkfs.xfs /dev/sdc
    ```

3. **[A]** Map voor het maken van een mount

    ```bash
    sudo mkdir -p /usr/sap
    ```

4. **[A]** UUID van blokapparaat op halen

    ```bash
    sudo blkid

    #It will display information about block device. Copy UUID of the formatted block device

    /dev/sdc: UUID="0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b" TYPE="xfs"
    ```

5. **[A]** Bestandssysteem mount vermelding onderhouden in /etc/fstab

    ```bash
    sudo echo "UUID=0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b /usr/sap xfs defaults,nofail 0 2" >> /etc/fstab
    ```

6. **[A] Bestandssysteem** toevoegen

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

### <a name="mount-azure-netapp-files-volume"></a>Een Azure NetApp Files volume

1. **[A]** Een mount-directories maken

   ```bash
   sudo mkdir -p /usr/sap/frsinput
   sudo mkdir -p /usr/sap/frsoutput
   ```

2. **[A]** Configureer het client-besturingssysteem voor de ondersteuning van NFSv4.1-mount (alleen van toepassing **als NFSv4.1 wordt gebruikt)**

   Als u Azure NetApp Files volumes met het NFSv4.1-protocol gebruikt, voert u de volgende configuratie uit op alle VM's, waarbij Azure NetApp Files NFSv4.1-volumes moeten worden bevestigd.

   **NFS-domeininstellingen controleren**

   Zorg ervoor dat het domein is geconfigureerd als het standaarddomein Azure NetApp Files dat wil zeggen, defaultv4iddomain.com de toewijzing is ingesteld op **niemand.** 

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
   > Zorg ervoor dat u het NFS-domein in /etc/idmapd.conf op de VM in stelt op de standaarddomeinconfiguratie op Azure NetApp Files: **defaultv4iddomain.com**. Als er een verschil is tussen de domeinconfiguratie op de NFS-client (dat wil zeggen de VM) en de NFS-server, dat wil zeggen de Azure NetApp-configuratie, worden de machtigingen voor bestanden op Azure NetApp-volumes die zijn bevestigd op de VM's weergegeven als niemand.

   Controleer `nfs4_disable_idmapping` of . Deze moet worden ingesteld op **Y**. Voer de opdracht voor het maken van de mapstructuur waar `nfs4_disable_idmapping` zich bevindt. U kunt de map niet handmatig maken onder /sys/modules, omdat toegang is gereserveerd voor de kernel/stuurprogramma's.

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

3. **[A]** Voeg bevestigingsgegevens toe

   Als u NFSv3 gebruikt

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   ```

   Als u NFSv4.1 gebruikt

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   ```

4. **[A]** NFS-volumes monteren

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

## <a name="configure-cms-database---azure-database-for-mysql"></a>CMS-database configureren - Azure Database for MySQL

In deze sectie vindt u meer informatie over het inrichten Azure Database for MySQL met Azure Portal. Het bevat ook instructies voor het maken van CMS- en auditdatabases voor SAP BOBI Platform en een gebruikersaccount voor toegang tot de database.

De richtlijnen zijn alleen van toepassing als u Azure DB for MySQL. Raadpleeg voor andere database(s) SAP- of databasespecifieke documentatie voor instructies.

### <a name="create-an-azure-database-for-mysql"></a>Een Azure-database voor MySQL maken

Meld u aan bij Azure Portal en volg de stappen in deze [Snelstartgids van Azure Database for MySQL](../../../mysql/quickstart-create-mysql-server-database-using-azure-portal.md). Enkele punten om op te merken tijdens het inrichten Azure Database for MySQL -

1. Selecteer dezelfde regio voor Azure Database for MySQL waar uw SAP BI Platform-toepassingsservers worden uitgevoerd.

2. Kies een ondersteunde DB-versie op basis [van PAM (Product Availability Matrix)](https://support.sap.com/pam) voor SAP BI die specifiek is voor uw SAP BOBI-versie. Volg dezelfde compatibiliteitsrichtlijnen als voor MySQL AB in SAP PAM

3. Selecteer in 'compute+storage' de optie **Server configureren** en selecteer de juiste prijscategorie op basis van de uitvoer van uw formaat.

4. **Automatische groei van opslag** is standaard ingeschakeld. Houd er rekening mee [dat Opslag](../../../mysql/concepts-pricing-tiers.md#storage) alleen omhoog en niet omlaag kan worden geschaald.

5. Standaard is de [](../../../mysql/howto-restore-server-portal.md#set-backup-configuration) **retentieperiode van een back-up** zeven dagen, maar u kunt deze eventueel maximaal 35 dagen configureren.

6. Back-ups van Azure Database for MySQL zijn standaard lokaal redundant. Als u serverback-ups in  geografisch redundante opslag wilt, selecteert u Geografisch redundant in Redundantieopties voor **back-up.**

> [!NOTE]
> Het wijzigen van [de opties voor back-up redundantie](../../../mysql/concepts-backup.md#backup-redundancy-options) na het maken van de server wordt niet ondersteund.

### <a name="configure-connection-security"></a>Verbindingsbeveiliging configureren

De gemaakte server is standaard beveiligd met een firewall en is niet openbaar toegankelijk. Volg de onderstaande stappen om toegang te bieden tot het virtuele netwerk waarop SAP BI Platform-toepassingsservers worden uitgevoerd:  

1. Ga naar serverresources in Azure Portal selecteer **Verbindingsbeveiliging** in het menu aan de linkerkant voor uw serverresource.
2. Selecteer **Ja om** toegang tot **Azure-services toe te staan.**
3. Selecteer onder VNET-regels de optie **Bestaand virtueel netwerk toevoegen.** Selecteer het virtuele netwerk en subnet van de SAP BI Platform-toepassingsserver. U moet ook toegang bieden tot Jump Box of andere servers van waar u [MySQL Workbench](../../../mysql/connect-workbench.md) kunt verbinden met Azure Database for MySQL. MySQL Workbench wordt gebruikt om een CMS- en auditdatabase te maken
4. Zodra virtuele netwerken zijn toegevoegd, selecteert u **Opslaan.**

### <a name="create-cms-and-audit-database"></a>CMS- en auditdatabase maken

1. Download en installeer MySQL Workbench vanaf de [MySQL-website.](https://dev.mysql.com/downloads/workbench/) Zorg ervoor dat u MySQL Workbench installeert op de server die toegang heeft tot Azure Database for MySQL.

2. Maak verbinding met de server met behulp van MySQL Workbench. Volg de instructies in dit [artikel](../../../mysql/connect-workbench.md#get-connection-information). Als de verbindingstest is geslaagd, krijgt u het volgende bericht:

   ![VERBINDING MET SQL Workbench](media/businessobjects-deployment-guide/businessobjects-sql-workbench.png)

3. Voer op het tabblad SQL-query de onderstaande query uit om een schema te maken voor de CMS- en Audit-database.

   ```sql
   # Here cmsbl1 is the database name of CMS database. You can provide the name you want for CMS database.
   CREATE SCHEMA `cmsbl1` DEFAULT CHARACTER SET utf8;

   # auditbl1 is the database name of Audit database. You can provide the name you want for CMS database.
   CREATE SCHEMA `auditbl1` DEFAULT CHARACTER SET utf8;
   ```
   
4. Gebruikersaccount maken om verbinding te maken met schema

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

5. De bevoegdheden en rollen van het MySQL-gebruikersaccount controleren

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

### <a name="install-mysql-c-api-connector-libmysqlclient-on-linux-server"></a>MySQL C API-connector (libmysqlclient) installeren op linux-server

Voor toegang van de SAP BOBI-toepassingsserver tot de database zijn databaseclient/stuurprogramma's vereist. MySQL C API Connector voor Linux moet worden gebruikt voor toegang tot CMS- en auditdatabases. ODBC-verbinding met CMS-database wordt niet ondersteund. In deze sectie vindt u instructies voor het instellen van de MySQL C API-connector in Linux.

1. Raadpleeg het [artikel MySQL drivers and management tools compatible with Azure Database for MySQL (MySQL-stuurprogramma's](../../../mysql/concepts-compatibility.md) en beheerhulpprogramma's die compatibel zijn met Azure Database for MySQL, waarin de stuurprogramma's worden beschreven die compatibel zijn met Azure Database for MySQL. Controleer in het artikel op het stuurprogramma **MySQL Connector/C (libmysqlclient).**

2. Raadpleeg deze koppeling [om stuurprogramma's](https://downloads.mysql.com/archives/c-c/) te downloaden.

3. Selecteer het besturingssysteem en download het rpm-pakket met gedeelde onderdelen van MySQL Connector. In dit voorbeeld wordt de connectorversie mysql-connector-c-shared-6.1.11 gebruikt.

4. Installeer de connector in alle exemplaren van de SAP BOBI-toepassing.

   ```bash
   # Install rpm package
   SLES: sudo zypper install <package>.rpm
   RHEL: sudo yum install <package>.rpm
   ```

5. Controleer het pad van libmysqlclient.so

   ```bash
   # Find the location of libmysqlclient.so file
   whereis libmysqlclient

   # sample output
   libmysqlclient: /usr/lib64/libmysqlclient.so
   ```

6. Stel LD_LIBRARY_PATH in op de `/usr/lib64` map voor het gebruikersaccount dat wordt gebruikt voor installatie.

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LD_LIBRARY_PATH=/usr/lib64
   ```

## <a name="server-preparation"></a>Servervoorbereiding

In de stappen in deze sectie worden de volgende voorvoegsels gebruikt:

**[A]**: De stap is van toepassing op alle hosts.

1. **[A]** Op basis van de smaak van Linux (SLES of RHEL) moet u kernelparameters instellen en vereiste bibliotheken installeren. Raadpleeg de **sectie Systeemvereisten** in [de Installatiehandleiding voor het Business Intelligence-platform voor Unix.](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US)

2. **[A]** Zorg ervoor dat de tijdzone op uw computer correct is ingesteld. Raadpleeg de [sectie Aanvullende UNIX- en Linux-vereisten](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US/46b143336e041014910aba7db0e91070.html) in De installatiehandleiding.

3. **[A]** Maak een gebruikersaccount **(bl1** adm) en groep (sapsys) waarmee de achtergrondprocessen van de software kunnen worden uitgevoerd. Gebruik dit account om de installatie uit te voeren en de software uit te voeren. Voor het account zijn geen hoofdbevoegdheden vereist.

4. **[A]** Stel de omgeving gebruikersaccount **(bl1** adm) in op het gebruik van een ondersteunde UTF-8-locale en zorg ervoor dat uw consolesoftware UTF-8-tekensets ondersteunt. Om ervoor te zorgen dat uw besturingssysteem de juiste taal gebruikt, stelt u de omgevingsvariabelen LC_ALL en LANG in op de gewenste taal in uw **(bl1** adm)-gebruikersomgeving.

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LANG=en_US.utf8
   export LC_ALL=en_US.utf8
   ```

5. **[A]** Configureer het gebruikersaccount (**bl1** adm).

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

6. Download en extraheert media voor SAP BusinessObjects BI Platform van SAP Service Marketplace.

## <a name="installation"></a>Installatie

Controleer de locale voor gebruikersaccount **bl1** adm op de server

```bash
bl1adm@azusbosl1:~> locale
LANG=en_US.utf8
LC_ALL=en_US.utf8
```

Navigeer naar de media van SAP BusinessObjects BI Platform en voer de onderstaande opdracht uit **met bl1** adm-gebruiker -

```bash
./setup.sh -InstallDir /usr/sap/BL1
```

Volg [de SAP BOBI](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM) Platform-installatiehandleiding voor Unix, specifiek voor uw versie. Enkele punten om op te merken tijdens het installeren van SAP BOBI Platform.

- Op **het scherm Productregistratie** configureren kunt u een tijdelijke licentiesleutel gebruiken voor SAP BusinessObjects Solutions van SAP Note [1288121](https://launchpad.support.sap.com/#/notes/1288121) of een licentiesleutel genereren in SAP Service Marketplace

- Selecteer **in het** scherm  Type installatie selecteren de optie Volledige installatie op de eerste server (azusbosl1) en selecteer voor een andere server (azusbosl2) **Aangepast/Uitbreiden** om de bestaande BOBI-installatie uit te vouwen.

- Selecteer **in het scherm Standaard of bestaande database** selecteren de optie Een bestaande **database** configureren, waarin u wordt gevraagd cms- en controledatabase te selecteren. Selecteer **MySQL** als CMS-databasetype en Auditdatabasetype.

  U kunt ook Geen controledatabase selecteren als u de controle niet wilt configureren tijdens de installatie.

- Selecteer de juiste opties op **het scherm Java Web Application Server selecteren** op basis van uw SAP BOBI-architectuur. In dit voorbeeld hebben we optie 1 geselecteerd, waarmee tomcat-server op hetzelfde SAP BOBI-platform wordt geïnstalleerd.

- Voer CMS-databasegegevens in Database **voor CMS-opslagplaats configureren - MySQL in.** Voorbeeldinvoer voor CMS-databasegegevens voor Linux-installatie. Azure Database for MySQL wordt gebruikt op standaardpoort 3306
  
  ![SAP BOBI-implementatie in Linux - CMS-database](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cms.png)

- (Optioneel) Voer Databasegegevens controleren in **Database voor auditopslagplaats configureren - MySQL in.** Voorbeeldinvoer voor Databasegegevens controleren voor Linux-installatie.

  ![SAP BOBI-implementatie in Linux - Database controleren](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-audit.png)

- Volg de instructies en voer de vereiste invoer in om de installatie te voltooien.

Voor implementatie met meerdere exemplaren moet u de installatie-installatie uitvoeren op de tweede host (azusbosl2). Selecteer **in het scherm Type** installatie selecteren de optie **Aangepast/uitbreiden** om de bestaande BOBI-installatie uit te vouwen.

In Azure Database for MySQL wordt een gateway gebruikt om de verbindingen om te leiden naar server-exemplaren. Nadat de verbinding tot stand is gebracht, wordt in de MySQL-client de versie van MySQL weergegeven die in de gateway is ingesteld, niet de feitelijke versie die wordt uitgevoerd op de MySQL-serverinstantie. Als u de versie van uw MySQL-serverinstantie wilt vaststellen, gebruikt u de `SELECT VERSION();`-opdracht bij de MySQL-prompt. In De Central Management Console (CMC) vindt u dus een andere databaseversie die in feite de versie is die is ingesteld op de gateway. Raadpleeg [Ondersteunde Azure Database for MySQL serverversies](../../../mysql/concepts-supported-versions.md) voor meer informatie.

![SAP BOBI-implementatie in Linux - CMC-instellingen](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cmc.png)

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

### <a name="tomcat-clustering---session-replication"></a>Tomcat-clustering - sessiereplicatie

Tomcat ondersteunt clustering van twee of meer toepassingsservers voor sessiereplicatie en failover. SAP BOBI-platformsessies worden geseraliseerd. Een gebruikerssessie kan naadloos worden overgeslagen naar een ander exemplaar van tomcat, zelfs wanneer een toepassingsserver uitvalt.

Bijvoorbeeld als een gebruiker is verbonden met een webserver die uitvalt terwijl de gebruiker door een maphiërarchie in de SAP BI-toepassing navigeert. Met een correct geconfigureerd cluster kan de gebruiker door de maphiërarchie navigeren zonder dat deze wordt omgeleid naar de aanmeldingspagina.

In SAP Note [2808640](https://launchpad.support.sap.com/#/notes/2808640)worden de stappen voor het configureren van tomcat-clustering geleverd met behulp van multicast. Maar in Azure wordt multicast niet ondersteund. Als u het Tomcat-cluster in Azure wilt laten werken, moet u [StaticMembershipInterceptor](https://tomcat.apache.org/tomcat-8.0-doc/config/cluster-interceptor.html#Static_Membership) (SAP Note [2764907) gebruiken.](https://launchpad.support.sap.com/#/notes/2764907) Controleer [tomcat-clustering met behulp van statisch lidmaatschap voor SAP BusinessObjects BI Platform](https://blogs.sap.com/2020/09/04/sap-on-azure-tomcat-clustering-using-static-membership-for-sap-businessobjects-bi-platform/) in de SAP-blog om een tomcat-cluster in Te stellen in Azure.

### <a name="load-balancing-web-tier-of-sap-bi-platform"></a>Taakverdelingsweblaag van SAP BI-platform

In SAP BOBI-implementatie met meerdere exemplaren worden Java-webtoepassingsservers (weblaag) uitgevoerd op twee of meer hosts. Als u de gebruikersbelasting gelijkmatig wilt verdelen over webservers, kunt u een load balancer tussen eindgebruikers en webservers. In Azure kunt u een Azure Load Balancer of Azure Application Gateway om verkeer naar uw webtoepassingsservers te beheren. Meer informatie over elke aanbieding wordt uitgelegd in de volgende sectie.

#### <a name="azure-load-balancer-network-based-load-balancer"></a>Azure load balancer (netwerkgebaseerde load balancer)

[Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) is een laag 4-laag met hoge prestaties (TCP, UDP) load balancer die verkeer verdeelt over gezonde Virtual Machines. Een load balancer controleert een bepaalde poort op elke virtuele machine en distribueert alleen verkeer naar een operationele virtuele machine(s). U kunt een openbaar load balancer of een intern load balancer, afhankelijk van of u SAP BI Platform toegankelijk wilt maken via internet of niet. Zone-redundant, waardoor hoge beschikbaarheid in Beschikbaarheidszones.

Raadpleeg de sectie Interne Load Balancer in onderstaande afbeelding waar webtoepassingsserver wordt uitgevoerd op poort 8080, de standaard Tomcat HTTP-poort, die wordt bewaakt door de statustest. Dus elke binnenkomende aanvraag die afkomstig is van eindgebruikers, wordt omgeleid naar de webtoepassingsservers (azusbosl1 of azusbosl2) in de back-endpool. Load balancer biedt geen ondersteuning voor TLS/SSL-beëindiging (ook wel bekend als TLS/SSL-offloading). Als u Azure Load balancer gebruikt om verkeer over webservers te distribueren, raden we u aan om Standard Load Balancer.

> [!NOTE]
> Wanneer VM's zonder openbare IP-adressen in de back-endpool van interne (geen openbaar IP-adres) Standard Azure load balancer worden geplaatst, is er geen uitgaande internetverbinding, tenzij er aanvullende configuratie wordt uitgevoerd om routering naar openbare eindpunten toe te staan. Zie Public [endpoint connectivity for Virtual Machines using Azure Standard Load Balancer in SAP high-availability scenarios](high-availability-guide-standard-load-balancer-outbound-connections.md)(Openbare eindpuntconnectiviteit voor Virtual Machines met behulp van Azure Standard Load Balancer in SAP-scenario's met hoge beschikbaarheid) voor meer informatie over het bereiken van uitgaande connectiviteit.

![Azure Load Balancer verkeer over webservers te balanceren](media/businessobjects-deployment-guide/businessobjects-deployment-load-balancer.png)

#### <a name="azure-application-gateway-web-application-load-balancer"></a>Azure Application Gateway (web application load balancer)

[Azure Application Gateway (AGW)](../../../application-gateway/overview.md) leveren Application Delivery Controller (ADC) als een service, die wordt gebruikt om de toepassing te helpen gebruikersverkeer om te leiden naar een of meer webtoepassingsservers. Het biedt verschillende taakverdelingsmogelijkheden van laag 7, zoals TLS/SSL Offloading, Web Application Firewall (WAF), sessieaffiniteit op basis van cookies en andere functies voor uw toepassingen.

In SAP BI Platform stuurt Application Gateway webverkeer van toepassingen door naar de opgegeven resources in een back-endpool: azusbosl1 of azusbos2. U wijst een listener toe aan de poort, maakt regels en voegt resources toe aan een back-endpool. In de onderstaande afbeelding fungeren toepassingsgateways met een privé-front-end-IP-adres (10.31.3.20) als toegangspunt voor de gebruikers, worden binnenkomende TLS/SSL-verbindingen (HTTPS - TCP/443) verwerkt, wordt de TLS/SSL ontsleuteld en wordt de niet-versleutelde aanvraag (HTTP - TCP/8080) door gegeven aan de servers in de back-endpool. Met de ingebouwde functie voor TLS/SSL-beëindiging hoeven we slechts één TLS/SSL-certificaat op de toepassingsgateway te onderhouden, waardoor bewerkingen worden vereenvoudigd.

![Application Gateway verkeer over webservers te balanceren](media/businessobjects-deployment-guide/businessobjects-deployment-application-gateway.png)

Als u Application Gateway voor SAP BOBI-webserver wilt configureren, raadpleegt u de blog Load Balancing SAP BOBI Web Servers using Azure Application Gateway on SAP (Taakverdeling van [SAP BOBI-webservers](https://blogs.sap.com/2020/09/17/sap-on-azure-load-balancing-web-application-servers-for-sap-bobi-using-azure-application-gateway/) met behulp van Azure Application Gateway op SAP).

> [!NOTE]
> We raden u aan om Azure Application Gateway te gebruiken om het verkeer te verdelen over de webserver, omdat het functies biedt zoals SSL-offloading, centraliseren van SSL-beheer om de overhead voor versleuteling en ontsleuteling op de server te verminderen, Round-Robin-algoritme voor het distribueren van verkeer, Web Application Firewall(WAF)-mogelijkheden, hoge beschikbaarheid, en meer.

### <a name="sap-businessobjects-bi-platform---back-up-and-restore"></a>SAP BusinessObjects BI Platform - back-up en herstel

Back-up en herstel is een proces waarbij periodieke kopieën van gegevens en toepassingen worden gemaakt om de locatie te scheiden. Deze kan dus worden hersteld of hersteld naar een eerdere status als de oorspronkelijke gegevens of toepassingen verloren gaan of beschadigd zijn. Het is ook een essentieel onderdeel van elke strategie voor herstel na noodherstel voor bedrijven.

Als u een uitgebreide back-up- en herstelstrategie wilt ontwikkelen voor SAP BOBI Platform, identificeert u de onderdelen die leiden tot systeemuitvaltijd of onderbreking van de toepassing. In SAP BOBI Platform zijn back-ups van de volgende onderdelen essentieel om de toepassing te beveiligen.

- SAP BOBI-installatiemap (beheerde Premium-schijven)
- Server voor bestandsopslagplaats (Azure NetApp Files of Azure Premium Files)
- CMS-database (Azure Database for MySQL of Database op Azure VM)

In de volgende sectie wordt beschreven hoe u een back-up- en herstelstrategie implementeert voor elk onderdeel op het SAP BOBI-platform.

#### <a name="backup--restore-for-sap-bobi-installation-directory"></a>Back-& herstellen voor SAP BOBI-installatiemap

In Azure is het gebruik van Azure Backup Service de eenvoudigste manier om een back-up te maken van toepassingsservers [en alle gekoppelde](../../../backup/backup-overview.md) schijven. Het biedt onafhankelijke en geïsoleerde back-ups om onbedoelde vernietigende gegevens op uw VM's te beschermen. Back-ups worden opgeslagen in een Recovery Services-kluis met ingebouwd beheer van herstelpunten. Configuratie en schaalbaarheid zijn eenvoudig, back-ups zijn geoptimaliseerd en kunnen eenvoudig worden hersteld wanneer dat nodig is.

Als onderdeel van het back-upproces wordt er een momentopname gemaakt en worden de gegevens overgebracht naar de Recovery Service-kluis zonder dat dit van invloed is op productieworkloads. De momentopname biedt een ander consistentieniveau, zoals beschreven in het artikel [Consistentie van momentopnamen.](../../../backup/backup-azure-vms-introduction.md#snapshot-consistency) U kunt er ook voor kiezen om een back-up te maken van een subset van de gegevensschijven in de VM met behulp van back-up- en herstelfunctionaliteit voor selectieve schijven. Zie het document [Azure VM Backup (Azure VM Backup)](../../../backup/backup-azure-vms-introduction.md) en veelgestelde vragen [- Back-ups maken van Azure-VM's voor meer informatie.](../../../backup/backup-azure-vm-backup-faq.yml)

#### <a name="backup--restore-for-file-repository-server"></a>Back-& voor bestandsopslagplaatsserver

Voor **Azure NetApp Files** kunt u een momentopname op aanvraag maken en automatische momentopname plannen met behulp van beleid voor momentopnamen. Kopieën van momentopnamen bieden een kopie op een bepaald tijdstip van uw ANF-volume. Zie Momentopnamen beheren met behulp van Azure NetApp Files voor [meer Azure NetApp Files.](../../../azure-netapp-files/azure-netapp-files-manage-snapshots.md)

**Azure Files** back-up is geïntegreerd met de [systeemeigen](../../../backup/backup-overview.md) Azure Backup-service, waarmee de back-up- en herstelfunctie samen met de back-up van VM's wordt gecentreerd en het werk wordt vereenvoudigd. Zie Back-up van [Azure-bestands share en](../../../backup/azure-file-share-backup-overview.md) Veelgestelde vragen [- Back-up](../../../backup/backup-azure-files-faq.yml)maken Azure Files .

#### <a name="backup--restore-for-cms-database"></a>Back-& herstellen voor CMS-database

Azure Database of MySQL is een DBaaS-aanbieding in Azure, waarmee automatisch serverback-ups worden gemaakt en opgeslagen in door de gebruiker geconfigureerde lokaal redundante of geografisch redundante opslag. Azure Database of MySQL maakt back-ups van de gegevensbestanden en het transactielogboek. Afhankelijk van de ondersteunde maximale opslaggrootte, worden volledige en differentiële back-ups (maximaal 4 TB aan opslagservers) of een momentopnameback-up gemaakt (maximaal 16 TB aan opslagservers). Met deze back-ups kunt u een server op elk moment binnen de geconfigureerde bewaarperiode voor back-ups herstellen. De standaardretentieperiode voor back-ups is zeven dagen. U kunt deze eventueel [tot](../../../mysql/howto-restore-server-portal.md#set-backup-configuration) drie dagen configureren. Alle back-ups worden versleuteld met AES 256-bits versleuteling.

Deze back-upbestanden zijn niet zichtbaar voor gebruikers en kunnen niet worden geëxporteerd. Deze back-ups kunnen alleen worden gebruikt voor herstelbewerkingen in Azure Database for MySQL. U kunt [mysqldump gebruiken om](../../../mysql/concepts-migrate-dump-restore.md) een database te kopiëren. Zie Back-up maken en herstellen [in Azure Database for MySQL.](../../../mysql/concepts-backup.md)

Voor een database die is geïnstalleerd Virtual Machines, kunt u standaardhulpprogramma's voor [back-ups](../../../backup/sap-hana-db-about.md) of Azure Backup HANA Database gebruiken. Als de Azure-services en -hulpprogramma's niet voldoen aan uw behoeften, kunt u ook andere back-uphulpprogramma's of scripts gebruiken om back-ups van schijven te maken.

## <a name="sap-businessobjects-bi-platform-reliability"></a>Betrouwbaarheid van SAP BusinessObjects BI-platform

SAP BusinessObjects BI Platform bevat verschillende lagen die zijn geoptimaliseerd voor specifieke taken en bewerkingen. Wanneer een onderdeel van een van de lagen niet meer beschikbaar is, wordt de SAP BOBI-toepassing ontoegankelijk of werkt bepaalde functionaliteit van de toepassing niet. Daarom moet u ervoor zorgen dat elke laag is ontworpen om betrouwbaar te zijn om de toepassing operationeel te houden zonder bedrijfsonderbrekingen.

Deze sectie richt zich op de volgende opties voor SAP BOBI Platform -

- **Hoge beschikbaarheid:** Een hoog beschikbaar platform heeft ten minste twee van alles in de Azure-regio om de toepassing operationeel te houden als een van de servers niet meer beschikbaar is.
- **Herstel na noodherstel:** Het is een proces van het herstellen van de functionaliteit van uw toepassing als er sprake is van onherstelbaar verlies, omdat de hele Azure-regio niet meer beschikbaar is vanwege een natuurramp.

De implementatie van deze oplossing is afhankelijk van de aard van de systeeminstallatie in Azure. De klant moet dus een oplossing voor hoge beschikbaarheid en herstel na noodherstel aanpassen op basis van hun bedrijfsvereiste.

### <a name="high-availability"></a>Hoge beschikbaarheid

Hoge beschikbaarheid verwijst naar een set technologieën die IT-onderbrekingen kunnen minimaliseren door bedrijfscontinuïteit te bieden voor toepassingen/services via redundante, fouttolerante of met failover beveiligde onderdelen binnen hetzelfde datacenter. In ons geval zijn de datacenters binnen één Azure-regio. Het artikel Architectuur voor hoge beschikbaarheid en scenario's voor SAP bieden een eerste inzicht in verschillende technieken voor hoge beschikbaarheid en aanbevelingen die worden aangeboden in Azure voor [SAP-toepassingen.](sap-high-availability-architecture-scenarios.md) Dit is een aanvulling op de instructies in deze sectie.

Op basis van het formaatresultaat van SAP BOBI Platform moet u het landschap ontwerpen en de distributie van BI-onderdelen bepalen over Azure-Virtual Machines en subnetten. Het redundantieniveau in de gedistribueerde architectuur is afhankelijk van het vereiste hersteltijddoel (RTO) en de RPO (Recovery Point Objective). SAP BOBI Platform bevat verschillende lagen en onderdelen op elke laag moeten worden ontworpen om redundantie te bereiken. Als er dus één onderdeel uitvalt, is er weinig tot geen onderbreking van uw SAP BOBI-toepassing. Bijvoorbeeld:

- Redundante toepassingsservers zoals BI-toepassingsservers en webservers
- Unieke onderdelen, zoals CMS-database, server voor bestandsopslagplaats, Load Balancer

In de volgende sectie wordt beschreven hoe u hoge beschikbaarheid kunt bereiken voor elk onderdeel van SAP BOBI Platform.

#### <a name="high-availability-for-application-servers"></a>Hoge beschikbaarheid voor toepassingsservers

Voor BI- en webtoepassingsservers is geen specifieke oplossing met hoge beschikbaarheid nodig, ongeacht of ze afzonderlijk of samen zijn geïnstalleerd. U kunt hoge beschikbaarheid bereiken door redundantie, dat wil zeggen door meerdere exemplaren van BI- en webservers in verschillende Azure-Virtual Machines.

Om de gevolgen van downtime door een of meer gebeurtenissen te beperken, is het raadzaam om onderstaande hoge beschikbaarheidsprocedure te volgen voor de toepassingsservers die op meerdere virtuele machines worden uitgevoerd.

- Gebruik Beschikbaarheidszones om datacentrumfouten te beveiligen.
- Configureer meerdere Virtual Machines in een beschikbaarheidsset voor redundantie.
- Gebruik Managed Disks voor VM's in een beschikbaarheidsset.
- Configureer elke toepassingslaag in afzonderlijke beschikbaarheidssets.

Zie Manage [the availability of Linux virtual machines (De beschikbaarheid van virtuele Linux-machines beheren) voor meer informatie](../../availability.md)

#### <a name="high-availability-for-cms-database"></a>Hoge beschikbaarheid voor CMS-database

Als u Azure Database as a Service (DBaaS) gebruikt voor CMS-database, wordt standaard een framework voor hoge beschikbaarheid geboden. U hoeft alleen de regio en service te selecteren die inherent zijn aan hoge beschikbaarheid, redundantie en tolerantiemogelijkheden zonder dat u extra onderdelen hoeft te configureren. Voor meer informatie over de SLA van ondersteunde DBaaS-aanbiedingen in Azure, raadpleegt u Hoge beschikbaarheid [in](../../../mysql/concepts-high-availability.md) Azure Database for MySQL [en Hoge beschikbaarheid voor Azure SQL Database](../../../azure-sql/database/high-availability-sla.md)

Voor andere DBMS-implementaties voor CMS-databases raadpleegt u DBMS-implementatiehandleidingen voor [SAP-workload,](dbms_guide_general.md)die inzicht bieden in verschillende DBMS-implementaties en de benadering om hoge beschikbaarheid te bereiken.

#### <a name="high-availability-for-file-repository-server"></a>Hoge beschikbaarheid voor bestandsopslagplaatsserver

File Repository Server (FRS) verwijst naar de schijfdirecties waar inhoud, zoals rapporten, universums en verbindingen, worden opgeslagen. Het wordt gedeeld met alle toepassingsservers van dat systeem. U moet er dus voor zorgen dat deze zeer beschikbaar is.

In Azure kunt u Kiezen voor [Azure Premium Files](../../../storage/files/storage-files-introduction.md) of [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) voor bestands delen die zijn ontworpen om zeer beschikbaar en uiterst duurzaam van aard te zijn. Zie de sectie [Redundantie voor](../../../storage/files/storage-files-planning.md#redundancy) meer informatie Azure Files.

> [!NOTE]
> SMB Protocol voor Azure Files is algemeen beschikbaar, maar NFS Protocol-ondersteuning voor Azure Files is momenteel in preview. Zie [NFS 4.1 support for Azure Files is now in preview (NFS 4.1-ondersteuning](https://azure.microsoft.com/en-us/blog/nfs-41-support-for-azure-files-is-now-in-preview/) voor Azure Files is nu in preview) voor meer informatie

Omdat deze service voor bestands delen niet in alle [](https://azure.microsoft.com/en-us/global-infrastructure/services/) regio's beschikbaar is, raadpleegt u De beschikbare producten per regio-site voor actuele informatie. Als de service niet beschikbaar is in uw regio, kunt u een NFS-server maken van waaruit u het bestandssysteem kunt delen met de SAP BOBI-toepassing. Maar u moet ook rekening houden met de hoge beschikbaarheid.

#### <a name="high-availability-for-load-balancer"></a>Hoge beschikbaarheid voor load balancer

Als u verkeer wilt distribueren over de webserver, kunt u Azure Load Balancer of Azure Application Gateway. De redundantie voor een van de load balancer kan worden bereikt op basis van de SKU die u voor de implementatie kiest.

- Voor Azure Load Balancer kan redundantie worden bereikt door een front-Standard Load Balancer te configureren als zone-redundant. Zie voor meer informatie [Standard Load Balancer en Beschikbaarheidszones](../../../load-balancer/load-balancer-standard-availability-zones.md)
- Voor Application Gateway kunt u hoge beschikbaarheid bereiken op basis van het type laag dat tijdens de implementatie is geselecteerd.
  - v1 SKU ondersteunt scenario's met hoge beschikbaarheid wanneer u twee of meer exemplaren hebt geïmplementeerd. Azure distribueert deze exemplaren over update- en foutdomeinen om ervoor te zorgen dat exemplaren niet allemaal tegelijk mislukken. Met deze SKU kan redundantie dus worden bereikt binnen de zone
  - V2 SKU zorgt er automatisch voor dat nieuwe exemplaren worden verdeeld over foutdomeinen en updatedomeinen. Als u zone-redundantie kiest, worden de nieuwste exemplaren ook verdeeld over beschikbaarheidszones om zone-uitval tolerantie te bieden. Zie Automatisch schalen [en zone-redundante](../../../application-gateway/application-gateway-autoscaling-zone-redundant.md) Application Gateway v2 voor meer informatie

#### <a name="reference-high-availability-architecture-for-sap-businessobjects-bi-platform"></a>Referentiearchitectuur met hoge beschikbaarheid voor SAP BusinessObjects BI-platform

In de onderstaande referentiearchitectuur wordt beschreven hoe SAP BOBI Platform is ingesteld met behulp van een beschikbaarheidsset, die redundantie en beschikbaarheid van VM's binnen de zone biedt. De architectuur demonstreert het gebruik van verschillende Azure-services, zoals Azure Application Gateway, Azure NetApp Files en Azure Database for MySQL voor SAP BOBI Platform, dat ingebouwde redundantie biedt, waardoor het beheer van verschillende oplossingen met hoge beschikbaarheid minder complex wordt.

In de onderstaande afbeelding wordt het binnenkomende verkeer (HTTPS - TCP/443) verdeeld met behulp van Azure Application Gateway v1 SKU, die zeer beschikbaar is wanneer het wordt geïmplementeerd op twee of meer exemplaren. Er worden meerdere exemplaren van webservers, beheerservers en verwerkingsservers geïmplementeerd in afzonderlijke Virtual Machines om redundantie te bereiken en elke laag wordt geïmplementeerd in afzonderlijke beschikbaarheidssets. Azure NetApp Files heeft ingebouwde redundantie in het datacenter, zodat uw ANF-volumes voor bestandsopslagplaatsserver zeer beschikbaar zijn. CMS Database wordt ingericht op Azure Database for MySQL (DBaaS) die inherent hoge beschikbaarheid heeft. Zie Hoge beschikbaarheid in Azure Database for MySQL handleiding [voor meer](../../../mysql/concepts-high-availability.md) informatie.

![REDUNDANTie van SAP BusinessObjects BI-platform met behulp van beschikbaarheidssets](media/businessobjects-deployment-guide/businessobjects-deployment-high-availability.png)

De bovenstaande architectuur biedt inzicht in hoe SAP BOBI-implementatie in Azure kan worden uitgevoerd. Maar niet alle mogelijke configuratieopties voor SAP BOBI Platform in Azure komen aan de hand van deze optie. Klanten kunnen hun implementatie aanpassen op basis van hun zakelijke behoeften door verschillende producten/services te kiezen voor verschillende onderdelen, zoals Load Balancer, server voor bestandsopslagplaats en DBMS.

In verschillende Azure-regio'Beschikbaarheidszones aangeboden, wat betekent dat de stroomvoorziening, koeling en het netwerk onafhankelijk zijn. Hiermee kan de klant toepassingen implementeren in twee of drie beschikbaarheidszones. Klanten die hoge beschikbaarheid willen bereiken in meerdere AZ's, kunnen SAP BOBI Platform implementeren in beschikbaarheidszones en ervoor zorgen dat elk onderdeel in de toepassing zone-redundant is.

### <a name="disaster-recovery"></a>Herstel na noodgeval

In de instructie in deze sectie wordt uitgelegd wat de strategie is om bescherming tegen noodherstel te bieden voor SAP BOBI Platform. Het vormt een aanvulling op het document [Disaster Recovery for SAP,](../../../site-recovery/site-recovery-sap.md) dat de primaire resources vertegenwoordigt voor de algehele SAP-noodherstelbenadering.

#### <a name="reference-disaster-recovery-architecture-for-sap-businessobjects-bi-platform"></a>Referentiearchitectuur voor herstel na noodherstel voor SAP BusinessObjects BI-platform

In deze referentiearchitectuur wordt de implementatie van SAP BOBI Platform met meerdere exemplaren uitgevoerd met redundante toepassingsservers. Voor herstel na noodherstel moet u een fail over alle lagen naar een secundaire regio maken. Elke laag gebruikt een andere strategie om bescherming tegen noodherstel te bieden.

![SAP BusinessObjects BI Platform Disaster Recovery](media/businessobjects-deployment-guide/businessobjects-deployment-disaster-recovery.png)

#### <a name="load-balancer"></a>Load balancer

Load Balancer wordt gebruikt om verkeer te distribueren over webtoepassingsservers van SAP BOBI Platform. Implementeert parallelle installatie van Azure Application Gateway toepassingsgateway in de secundaire regio om dr-Azure Application Gateway te realiseren.

#### <a name="virtual-machines-running-web-and-bi-application-servers"></a>Virtuele machines waarop web- en BI-toepassingsservers worden uitgevoerd

Azure Site Recovery-service kan worden gebruikt voor het repliceren van Virtual Machines web- en BI-toepassingsservers in de secundaire regio. De servers in de secundaire regio worden gerepliceerd, zodat u bij nood en uitval eenvoudig een fail over kunt zetten naar uw gerepliceerde omgeving en kunt blijven werken

#### <a name="file-repository-servers"></a>Servers voor bestandsopslagplaats

- **Azure NetApp Files** biedt NFS- en SMB-volumes, zodat elk op bestanden gebaseerd hulpprogramma voor kopiëren kan worden gebruikt om gegevens tussen Azure-regio's te repliceren. Zie Veelgestelde vragen over het kopiëren van een ANF-volume in een andere [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-faqs.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region)

  U kunt Azure NetApp Files replicatie tussen regio's gebruiken. Deze is momenteel in [preview](https://azure.microsoft.com/en-us/blog/azure-netapp-files-cross-region-replication-and-new-enhancements-in-preview/) en maakt gebruik van netApp SnapMirror®technologie. Daarom worden alleen gewijzigde blokken via het netwerk verzonden in een gecomprimeerde, efficiënte indeling. Deze eigen technologie minimaliseert de hoeveelheid gegevens die nodig is om te repliceren tussen de regio's, waardoor kosten voor gegevensoverdracht worden bespaard. Het verkort ook de replicatietijd, zodat u een kleinere RPO (Restore Point Objective) kunt bereiken. Raadpleeg Vereisten [en overwegingen voor het gebruik van replicatie tussen regio's](../../../azure-netapp-files/cross-region-replication-requirements-considerations.md) voor meer informatie.

- **Azure Premium-bestanden** ondersteunen alleen lokaal redundante (LRS) en zone-redundante opslag (ZRS). Voor een dr-strategie voor Azure Premium Files kunt u [AzCopy](../../../storage/common/storage-use-azcopy-v10.md) of [Azure PowerShell](/powershell/module/az.storage/) bestanden kopiëren naar een ander opslagaccount in een andere regio. Zie Herstel na noodherstel en failover van [opslagaccount](../../../storage/common/storage-disaster-recovery-guidance.md) voor meer informatie

#### <a name="cms-database"></a>CMS-database

Azure Database for MySQL biedt meerdere opties voor het herstellen van de database in geval van nood. Kies de juiste optie die geschikt is voor uw bedrijf.

- Leesreplica's tussen regio's inschakelen om uw planning voor bedrijfscontinuïteit en herstel na noodherstel te verbeteren. U kunt vanaf de bronserver repliceren naar maximaal vijf replica's. Leesreplica's worden asynchroon bijgewerkt met behulp van de replicatietechnologie voor binaire logboeken van MySQL. Replica's zijn nieuwe servers die u beheert, vergelijkbaar met Azure Database for MySQL servers. Lees meer over leesreplica's, beschikbare regio's, beperkingen en hoe u een fail over kunt zetten in het artikel Over concepten van [leesreplica's.](../../../mysql/concepts-read-replicas.md)

- Gebruik Azure Database for MySQL functie voor geo-herstel waarmee de server wordt hersteld met behulp van geografisch redundante back-ups. Deze back-ups zijn zelfs toegankelijk wanneer de regio waarop uw server wordt gehost offline is. U kunt deze back-ups herstellen naar een andere regio en uw server weer online brengen.

  > [!NOTE]
  > Geo-herstel is alleen mogelijk als u de server hebt ingericht met geografisch redundante back-upopslag. Het wijzigen van **de opties voor back-up redundantie** na het maken van de server wordt niet ondersteund. Zie het artikel [Backup Redundancy (Redundantie van back-ups) voor](../../../mysql/concepts-backup.md#backup-redundancy-options) meer informatie.

Hieronder volgt de aanbeveling voor herstel na noodherstel van elke laag die in dit voorbeeld wordt gebruikt.

| SAP BOBI-platformlagen   | Aanbeveling                                                                                           |
|---------------------------|----------------------------------------------------------------------------------------------------------|
| Azure Application Gateway | Parallelle installatie van Application Gateway in secundaire regio                                                |
| Webtoepassingsservers   | Repliceren met behulp van Site Recovery                                                                         |
| BI-toepassingsservers    | Repliceren met behulp van Site Recovery                                                                         |
| Azure NetApp Files        | Hulpprogramma voor kopiëren op basis van bestanden voor het repliceren van gegevens naar de secundaire **regio of** ANF-replicatie tussen regio's (preview) |
| Azure Database for MySQL  | Leesreplica's in verschillende **regio's of** Back-up terugzetten vanuit geografisch redundante back-ups.                             |

## <a name="next-steps"></a>Volgende stappen

- [Herstel na noodherstel instellen voor een SAP-app-implementatie met meerdere lagen](../../../site-recovery/site-recovery-sap.md)
- [Azure Virtual Machines en implementatie voor SAP](planning-guide.md)
- [Azure Virtual Machines-implementatie voor SAP](deployment-guide.md)
- [Azure Virtual Machines DBMS-implementatie voor SAP](./dbms_guide_general.md)
