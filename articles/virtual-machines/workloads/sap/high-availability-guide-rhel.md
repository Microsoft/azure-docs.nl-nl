---
title: Azure-Vm's hoge Beschik baarheid voor SAP NW op RHEL | Microsoft Docs
description: Azure Virtual Machines hoge Beschik baarheid voor SAP NetWeaver op Red Hat Enterprise Linux
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/12/2021
ms.author: radeltch
ms.openlocfilehash: cf71f180a1ce845df436391b98227ae173fe77f1
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303658"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux"></a>Azure Virtual Machines hoge Beschik baarheid voor SAP NetWeaver op Red Hat Enterprise Linux

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

In dit artikel wordt beschreven hoe u de virtuele machines implementeert, hoe u de virtuele machines configureert, het cluster raamwerk installeert en een Maxi maal beschikbaar SAP NetWeaver 7,50-systeem installeert.
In de voorbeeld configuraties, installatie opdrachten, enzovoort. Het ASCS-exemplaar nummer 00, het ERS-exemplaar nummer 02 en de SAP-systeem-ID NW1 wordt gebruikt. De namen van de bronnen (bijvoorbeeld virtuele machines, virtuele netwerken) in het voor beeld aannemen dat u de [ASCS/SCS-sjabloon][template-multisid-xscs] hebt gebruikt met resource voorvoegsel NW1 om de resources te maken.

Lees eerst de volgende SAP-opmerkingen en-documenten

* SAP-opmerking [1928533], die:
  * Lijst met Azure-VM-grootten die worden ondersteund voor de implementatie van SAP-software
  * Informatie over belang rijke capaciteit voor Azure VM-grootten
  * Ondersteunde SAP-software en besturings systemen (OS) en database combinaties
  * Vereiste versie van de SAP-kernel voor Windows en Linux op Microsoft Azure

* SAP-opmerking [2015553] bevat vereisten voor SAP-ondersteuning voor SAP-software-implementaties in Azure.
* SAP Note [2002167] heeft aanbevolen instellingen voor het besturings systeem voor Red Hat Enterprise Linux
* SAP Note [2009879] heeft SAP Hana richt lijnen voor Red Hat Enterprise Linux
* SAP Note [2178632] bevat gedetailleerde informatie over alle bewakings gegevens die zijn gerapporteerd voor SAP in Azure.
* SAP Note [2191498] heeft de vereiste SAP host agent-versie voor Linux in Azure.
* SAP Note [2243692] bevat informatie over SAP-licentie verlening op Linux in Azure.
* SAP Note [1999351] bevat extra informatie over probleem oplossing voor de uitgebreide bewakings extensie van Azure voor SAP.
* Op de [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) zijn alle vereiste SAP-notities voor Linux geïnstalleerd.
* [Azure Virtual Machines planning en implementatie voor SAP op Linux][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP op Linux][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP op Linux][dbms-guide]
* [Product documentatie voor Red Hat Gluster Storage](https://access.redhat.com/documentation/red_hat_gluster_storage/)
* [SAP NetWeaver in pacemaker-cluster](https://access.redhat.com/articles/3150081)
* Algemene documentatie voor RHEL
  * [Overzicht van Add-On met hoge Beschik baarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Add-On beheer met hoge Beschik baarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referentie voor Add-On met hoge Beschik baarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [ASCS/ERS configureren voor SAP NetWeaver met zelfstandige bronnen in RHEL 7,5](https://access.redhat.com/articles/3569681)
  * [SAP S/4HANA ASCS/ERS met zelfstandige server 2 (ENSA2) configureren in pacemaker op RHEL ](https://access.redhat.com/articles/3974941)
* Specifieke RHEL-documentatie voor Azure:
  * [Ondersteunings beleid voor RHEL-clusters met hoge Beschik baarheid-Microsoft Azure Virtual Machines als cluster leden](https://access.redhat.com/articles/3131341)
  * [Installeren en configureren van een Red Hat Enterprise Linux 7,4 (en hoger) High-Availability cluster op Microsoft Azure](https://access.redhat.com/articles/3252491)

## <a name="overview"></a>Overzicht

Voor een hoge Beschik baarheid is voor SAP net-Weave gedeelde opslag vereist. GlusterFS wordt geconfigureerd in een afzonderlijk cluster en kan worden gebruikt door meerdere SAP-systemen.

![Overzicht van de hoge Beschik baarheid van SAP netweave](./media/high-availability-guide-rhel/ha-rhel.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS en de SAP HANA-Data Base gebruiken virtuele hostnamen en virtuele IP-adressen. Op Azure is een load balancer vereist voor het gebruik van een virtueel IP-adres. U kunt het beste [standaard Load Balancer](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md)gebruiken. De volgende lijst bevat de configuratie van de (A) SCS-en ERS-load balancer.

### <a name="ascs"></a>(A)SCS

* Front-end configuratie
  * IP-adres 10.0.0.7
* Test poort
  * Poort 620<strong> &lt; Nr &gt; </strong>
* Taakverdelingsregels
  * Als u Standard Load Balancer gebruikt, selecteert u **ha-poorten**
  * Als u basis Load Balancer gebruikt, maakt u regels voor taak verdeling voor de volgende poorten
    * 32<strong> &lt; Nr &gt; </strong> TCP
    * 36<strong> &lt; Nr &gt; </strong> TCP
    * 39<strong> &lt; Nr &gt; </strong> TCP
    * 81<strong> &lt; Nr &gt; </strong> TCP
    * 5<strong> &lt; Nr &gt; </strong>. 13 TCP
    * 5<strong> &lt; Nr &gt; </strong>. 14 TCP
    * 5<strong> &lt; Nr &gt; </strong>. 16 TCP

### <a name="ers"></a>ERS

* Front-end configuratie
  * IP-adres 10.0.0.8
* Test poort
  * Poort 621<strong> &lt; Nr &gt; </strong>
* Taakverdelingsregels
  * Als u Standard Load Balancer gebruikt, selecteert u **ha-poorten**
  * Als u basis Load Balancer gebruikt, maakt u regels voor taak verdeling voor de volgende poorten
    * 32<strong> &lt; Nr &gt; </strong> TCP
    * 33<strong> &lt; Nr &gt; </strong> TCP
    * 5<strong> &lt; Nr &gt; </strong>. 13 TCP
    * 5<strong> &lt; Nr &gt; </strong>. 14 TCP
    * 5<strong> &lt; Nr &gt; </strong>. 16 TCP

* Back-end-configuratie
  * Verbonden met primaire netwerk interfaces van alle virtuele machines die deel moeten uitmaken van het (A) SCS/ERS-cluster

## <a name="setting-up-glusterfs"></a>GlusterFS instellen

Voor SAP NetWeaver is gedeelde opslag vereist voor de map Trans Port en het profiel. Lees [glusterfs op virtuele machines van Azure op Red Hat Enterprise Linux voor SAP NetWeaver][glusterfs-ha] voor het instellen van GLUSTERFS voor SAP NetWeaver.

## <a name="setting-up-ascs"></a>Instellen van (A) SCS

U kunt een Azure-sjabloon van GitHub gebruiken om alle vereiste Azure-resources te implementeren, met inbegrip van de virtuele machines, beschikbaarheidsset en load balancer, of u kunt de resources hand matig implementeren.

### <a name="deploy-linux-via-azure-template"></a>Linux implementeren via Azure-sjabloon

De Azure Marketplace bevat een installatie kopie voor Red Hat Enterprise Linux die u kunt gebruiken om nieuwe virtuele machines te implementeren. U kunt een van de Quick Start-sjablonen op GitHub gebruiken voor het implementeren van alle vereiste resources. De sjabloon implementeert de virtuele machines, de load balancer, de beschik baarheid, enzovoort. Volg deze stappen om de sjabloon te implementeren:

1. Open de [ASCS/SCS-sjabloon][template-multisid-xscs] op de Azure Portal  
1. Voer de volgende para meters in
   1. Resource voorvoegsel  
      Geef het voor voegsel op dat u wilt gebruiken. De waarde wordt gebruikt als een voor voegsel voor de resources die worden geïmplementeerd.
   1. Stack type  
      Het stack type SAP net-Weaver selecteren
   1. Type besturings systeem  
      Selecteer een van de Linux-distributies. Selecteer voor dit voor beeld RHEL 7
   1. Db-type  
      HANA selecteren
   1. Aantal SAP-systemen  
      Het aantal SAP-systemen dat in dit cluster wordt uitgevoerd. Selecteer 1.
   1. Systeem beschikbaarheid  
      HA selecteren
   1. Beheerders naam, beheerders wachtwoord of SSH-sleutel  
      Er wordt een nieuwe gebruiker gemaakt die kan worden gebruikt om u aan te melden bij de computer.
   1. Subnet-ID  
   Als u de virtuele machine wilt implementeren in een bestaand VNet waarvoor u een subnet hebt gedefinieerd, moet de virtuele machine worden toegewezen aan, de ID van het specifieke subnet benoemen. De ID ziet er meestal uit als/Subscriptions/**&lt; -abonnements &gt; -id**/resourceGroups/naam **&lt; &gt; van de resource groep**/providers/Microsoft.Network/virtualNetworks/naam **&lt; &gt; van het virtuele netwerk**/subnets/**&lt; subnet naam &gt;**

### <a name="deploy-linux-manually-via-azure-portal"></a>Linux hand matig implementeren via Azure Portal

U moet eerst de virtuele machines voor dit cluster maken. Daarna maakt u een load balancer en gebruikt u de virtuele machines in de back-end-pool.

1. Een resourcegroep maken
1. Een Virtual Network maken
1. Een beschikbaarheidsset maken  
   Maximum aantal update domeinen instellen
1. Virtuele machine 1 maken  
   Gebruik ten minste RHEL 7, in dit voor beeld de installatie kopie van Red Hat Enterprise Linux 7,4 <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   Selecteer een Beschikbaarheidsset die u eerder hebt gemaakt  
1. Virtuele machine 2 maken  
   Gebruik ten minste RHEL 7, in dit voor beeld de installatie kopie van Red Hat Enterprise Linux 7,4 <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux74-ARM>  
   Selecteer een Beschikbaarheidsset die u eerder hebt gemaakt  
1. Voeg ten minste één gegevens schijf toe aan beide virtuele machines  
   De gegevens schijven worden gebruikt voor de/usr/sap/- `<SAPSID`> Directory
1. Load balancer maken (intern, standaard):  
   1. De frontend-IP-adressen maken
      1. IP-adres 10.0.0.7 voor de ASCS
         1. Open de load balancer, selecteer de frontend-IP-adres groep en klik op toevoegen
         1. Voer de naam van de nieuwe front-end-IP-adres groep in (bijvoorbeeld **NW1-ascs-front-end**)
         1. Stel de toewijzing in op statisch en voer het IP-adres in (bijvoorbeeld **10.0.0.7**)
         1. Klik op OK
      1. IP-adres 10.0.0.8 voor de ASCS ERS
         * Herhaal de bovenstaande stappen voor het maken van een IP-adres voor de ERS (bijvoorbeeld **10.0.0.8** en **NW1-Aers-** front-end)
   1. Back-endpool maken
      1. Open de load balancer, selecteer back-endservers en klik op toevoegen
      1. Voer de naam van de nieuwe back-end-groep in (bijvoorbeeld **NW1-back-end**)
      1. Klik op een virtuele machine toevoegen.
      1. Virtuele machine selecteren.
      1. Selecteer de virtuele machines van het (A) SCS-cluster en de bijbehorende IP-adressen.
      1. Klik op Add.
   1. De status tests maken
      1. Poort 620 **00** voor ASCS
         1. Open de load balancer, selecteer status controles en klik op toevoegen
         1. Voer de naam van de nieuwe status test in (bijvoorbeeld **NW1-ascs-HP**)
         1. TCP als protocol selecteren, poort 620 **00**, interval 5 en drempel waarde voor onjuiste status 2 gebruiken
         1. Klik op OK
      1. Poort 621 **02** voor ASCS ers
         * Herhaal de bovenstaande stappen voor het maken van een status test voor de ERS (bijvoorbeeld 621 **02** en **NW1-Aers-HP**)
   1. Taakverdelingsregels
      1. Taakverdelings regels voor ASCS
         1. Open de load balancer, selecteer taakverdelings regels en klik op toevoegen
         1. Voer de naam in van de nieuwe load balancer regel (bijvoorbeeld **NW1-lb-ascs**)
         1. Selecteer het frontend-IP-adres, de back-endadresgroep en de status test die u eerder hebt gemaakt (bijvoorbeeld **NW1-ascs-frontend**, **NW1-back-end** en **NW1-ascs-HP**)
         1. **Ha-poorten** selecteren
         1. **Zorg ervoor dat zwevend IP-adressen zijn ingeschakeld**
         1. Klik op OK
         * Herhaal de bovenstaande stappen om taakverdelings regels voor ERS te maken (bijvoorbeeld **NW1-lb-ers**)
1. U kunt ook de volgende stappen uitvoeren als voor uw scenario Basic-load balancer (intern) vereist is:  
   1. De frontend-IP-adressen maken
      1. IP-adres 10.0.0.7 voor de ASCS
         1. Open de load balancer, selecteer de frontend-IP-adres groep en klik op toevoegen
         1. Voer de naam van de nieuwe front-end-IP-adres groep in (bijvoorbeeld **NW1-ascs-front-end**)
         1. Stel de toewijzing in op statisch en voer het IP-adres in (bijvoorbeeld **10.0.0.7**)
         1. Klik op OK
      1. IP-adres 10.0.0.8 voor de ASCS ERS
         * Herhaal de bovenstaande stappen voor het maken van een IP-adres voor de ERS (bijvoorbeeld **10.0.0.8** en **NW1-Aers-** front-end)
   1. Back-endpool maken
      1. Open de load balancer, selecteer back-endservers en klik op toevoegen
      1. Voer de naam van de nieuwe back-end-groep in (bijvoorbeeld **NW1-back-end**)
      1. Klik op een virtuele machine toevoegen.
      1. Selecteer de Beschikbaarheidsset die u eerder hebt gemaakt
      1. De virtuele machines van het (A) SCS-cluster selecteren
      1. Klik op OK
   1. De status tests maken
      1. Poort 620 **00** voor ASCS
         1. Open de load balancer, selecteer status controles en klik op toevoegen
         1. Voer de naam van de nieuwe status test in (bijvoorbeeld **NW1-ascs-HP**)
         1. TCP als protocol selecteren, poort 620 **00**, interval 5 en drempel waarde voor onjuiste status 2 gebruiken
         1. Klik op OK
      1. Poort 621 **02** voor ASCS ers
         * Herhaal de bovenstaande stappen voor het maken van een status test voor de ERS (bijvoorbeeld 621 **02** en **NW1-Aers-HP**)
   1. Taakverdelingsregels
      1. 32 **00** TCP voor ASCS
         1. Open de load balancer, selecteer taakverdelings regels en klik op toevoegen
         1. Voer de naam in van de nieuwe load balancer regel (bijvoorbeeld **NW1-lb-3200**)
         1. Selecteer het frontend-IP-adres, de back-end-pool en de status test die u eerder hebt gemaakt (bijvoorbeeld **NW1-ascs-front-end**)
         1. Behoud protocol **TCP**, voer poort **3200** in
         1. Time-out voor inactiviteit tot 30 minuten verhogen
         1. **Zorg ervoor dat zwevend IP-adressen zijn ingeschakeld**
         1. Klik op OK
      1. Aanvullende poorten voor de ASCS
         * Herhaal de bovenstaande stappen voor poorten 36 **00**, 39 **00**, 81 **00**, 5 **00** 13, 5 **00** 14, 5 **00** 16 en TCP voor de ASCS
      1. Aanvullende poorten voor de ASCS ERS
         * Herhaal de bovenstaande stappen voor poort 33 **02**, 5 **02** 13, 5 **02** 14, 5 **02** 16 en TCP voor de ASCS ers

> [!IMPORTANT]
> Zwevend IP wordt niet ondersteund voor een secundaire IP-configuratie in een NIC in scenario's voor taak verdeling. Zie beperkingen voor [Azure Load Balancer](../../../load-balancer/load-balancer-multivip-overview.md#limitations)voor meer informatie. Als u een extra IP-adres voor de virtuele machine nodig hebt, implementeert u een tweede NIC.  

> [!Note]
> Wanneer Vm's zonder open bare IP-adressen in de back-endadresgroep van intern (geen openbaar IP-adres load balancer) worden geplaatst, is er geen uitgaande Internet verbinding, tenzij er aanvullende configuratie wordt uitgevoerd om route ring naar open bare eind punten toe te staan. Zie [connectiviteit van open bare eind punten voor virtual machines met behulp van Azure Standard Load Balancer in scenario's met hoge Beschik baarheid voor SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md)voor meer informatie over het bezorgen van uitgaande verbindingen.  

> [!IMPORTANT]
> Schakel TCP-tijds tempels niet in op virtuele Azure-machines die achter Azure Load Balancer worden geplaatst. Door TCP-tijds tempels in te scha kelen, mislukken de status controles. Stel para meter **net.IPv4.tcp_timestamps** in op **0**. Zie [Load Balancer Health probe](../../../load-balancer/load-balancer-custom-probe-overview.md)(Engelstalig) voor meer informatie.

### <a name="create-pacemaker-cluster"></a>Een pacemaker-cluster maken

Volg de stappen bij het [instellen van pacemaker op Red Hat Enterprise Linux in azure](high-availability-guide-rhel-pacemaker.md) voor het maken van een basis pacemaker-cluster voor deze (a) SCS-server.

### <a name="prepare-for-sap-netweaver-installation"></a>Voorbereiden op SAP NetWeaver-installatie

De volgende items worden voorafgegaan door **[A]** , van toepassing op alle knoop punten, **[1]** -alleen van toepassing op knoop punt 1 of **[2]** -alleen van toepassing op knoop punt 2.

1. **[A]** omzetting van hostnaam van installatie

   U kunt een DNS-server gebruiken of de bestand/etc/hosts wijzigen op alle knoop punten. In dit voor beeld ziet u hoe u het bestand/etc/hosts-bestand gebruikt.
   Vervang het IP-adres en de hostnaam in de volgende opdrachten:

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Voeg de volgende regels toe aan/etc/hosts. Wijzig het IP-adres en de hostnaam zodat deze overeenkomen met uw omgeving

   <pre><code># IP addresses of the GlusterFS nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   </code></pre>

1. **[A]** de gedeelde mappen maken

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>NW1</b>/SYS
   sudo mkdir -p /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>NW1</b>/ERS<b>02</b>

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>NW1</b>/SYS
   sudo chattr +i /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[A]** glusterfs-client en andere vereisten installeren

   <pre><code>sudo yum -y install glusterfs-fuse resource-agents resource-agents-sap
   </code></pre>

1. **[A] Controleer de** versie van de resource-agents-SAP

   Zorg ervoor dat de versie van de geïnstalleerde resource-agents SAP-pakket ten minste 3.9.5 -124. EL7
   <pre><code>sudo yum info resource-agents-sap
   
   # Loaded plugins: langpacks, product-id, search-disabled-repos
   # Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
   # Installed Packages
   # Name        : resource-agents-sap
   # Arch        : x86_64
   # Version     : <b>3.9.5</b>
   # Release     : <b>124.el7</b>
   # Size        : 100 k
   # Repo        : installed
   # From repo   : rhel-sap-for-rhel-7-server-rpms
   # Summary     : SAP cluster resource agents and connector script
   # URL         : https://github.com/ClusterLabs/resource-agents
   # License     : GPLv2+
   # Description : The SAP resource agents and connector script interface with
   #          : Pacemaker to allow SAP instances to be managed in a cluster
   #          : environment.
   </code></pre>


1. **[A]** koppelings vermeldingen toevoegen

   <pre><code>sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   <b>glust-0</b>:/<b>NW1</b>-sapmnt /sapmnt/<b>NW1</b> glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-trans /usr/sap/trans glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-sys /usr/sap/<b>NW1</b>/SYS glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   </code></pre>

   De nieuwe shares koppelen

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** wissel bestand configureren

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   De agent opnieuw starten om de wijziging te activeren

   <pre><code>sudo service waagent restart
   </code></pre>

1. **[A]** RHEL-configuratie

   RHEL configureren zoals beschreven in SAP Note [2002167]

### <a name="installing-sap-netweaver-ascsers"></a>SAP NetWeaver ASCS/ERS installeren

1. **[1]** Maak een virtuele IP-bron en een status test voor het ASCS-exemplaar

   <pre><code>sudo pcs node standby <b>nw1-cl-1</b>
   
   sudo pcs resource create fs_<b>NW1</b>_ASCS Filesystem device='<b>glust-0</b>:/<b>NW1</b>-ascs' \
     directory='/usr/sap/<b>NW1</b>/ASCS<b>00</b>' fstype='glusterfs' \
     options='backup-volfile-servers=<b>glust-1:glust-2</b>' \
     --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create vip_<b>NW1</b>_ASCS IPaddr2 \
     ip=<b>10.0.0.7</b> cidr_netmask=<b>24</b> \
     --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create nc_<b>NW1</b>_ASCS azure-lb port=620<b>00</b> \
     --group g-<b>NW1</b>_ASCS
   </code></pre>

   Zorg ervoor dat de cluster status OK is en dat alle resources worden gestart. Het is niet belang rijk op welk knoop punt de resources worden uitgevoerd.

   <pre><code>sudo pcs status
   
   # Node <b>nw1-cl-1</b>: standby
   # Online: [ <b>nw1-cl-0</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-0</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-0</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-0</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-0</b>
   </code></pre>

1. **[1]** SAP NetWeaver ASCS installeren  

   Installeer SAP NetWeaver ASCS als root op het eerste knoop punt met behulp van een virtuele hostnaam die verwijst naar het IP-adres van de load balancer frontend-configuratie voor de ASCS, bijvoorbeeld <b>NW1-ASCS</b>, <b>10.0.0.7</b> en het exemplaar nummer dat u hebt gebruikt voor de test van de Load Balancer, bijvoorbeeld <b>00</b>.

   U kunt de para meter sapinst SAPINST_REMOTE_ACCESS_USER gebruiken om een niet-hoofd gebruiker verbinding te laten maken met sapinst.

   <pre><code># Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Als de installatie geen submap kan maken in/usr/sap/**NW1**/ASCS **00**, probeert u de eigenaar en groep van de ASCS **00** -map in te stellen en het opnieuw te proberen.

   <pre><code>sudo chown nw1adm /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   sudo chgrp sapsys /usr/sap/<b>NW1</b>/ASCS<b>00</b>
   </code></pre>

1. **[1]** Maak een virtuele IP-bron en een status test voor het ers-exemplaar

   <pre><code>sudo pcs node unstandby <b>nw1-cl-1</b>
   sudo pcs node standby <b>nw1-cl-0</b>
   
   sudo pcs resource create fs_<b>NW1</b>_AERS Filesystem device='<b>glust-0</b>:/<b>NW1</b>-aers' \
     directory='/usr/sap/<b>NW1</b>/ERS<b>02</b>' fstype='glusterfs' \
     options='backup-volfile-servers=<b>glust-1:glust-2</b>' \
    --group g-<b>NW1</b>_AERS
   
   sudo pcs resource create vip_<b>NW1</b>_AERS IPaddr2 \
     ip=<b>10.0.0.8</b> cidr_netmask=<b>24</b> \
    --group g-<b>NW1</b>_AERS
   
   sudo pcs resource create nc_<b>NW1</b>_AERS azure-lb port=621<b>02</b> \
    --group g-<b>NW1</b>_AERS
   </code></pre>
 
   Zorg ervoor dat de cluster status OK is en dat alle resources worden gestart. Het is niet belang rijk op welk knoop punt de resources worden uitgevoerd.

   <pre><code>sudo pcs status
   
   # Node <b>nw1-cl-0</b>: standby
   # Online: [ <b>nw1-cl-1</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_AERS
   #      fs_<b>NW1</b>_AERS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_AERS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_AERS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   </code></pre>

1. **[2]** SAP NetWeaver ers installeren  

   Installeer SAP NetWeaver ERS als root op het tweede knoop punt met behulp van een virtuele hostnaam die verwijst naar het IP-adres van de load balancer frontend-configuratie voor de ERS, bijvoorbeeld <b>NW1-Aers</b>, <b>10.0.0.8</b> en het exemplaar nummer dat u hebt gebruikt voor de test van de Load Balancer, bijvoorbeeld <b>02</b>.

   U kunt de para meter sapinst SAPINST_REMOTE_ACCESS_USER gebruiken om een niet-hoofd gebruiker verbinding te laten maken met sapinst.

   <pre><code># Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

   Als de installatie geen submap kan maken in/usr/sap/**NW1**/ers **02**, probeert u de eigenaar en groep van de map ers **02** in te stellen en het opnieuw te proberen.

   <pre><code>sudo chown nw1adm /usr/sap/<b>NW1</b>/ERS<b>02</b>
   sudo chgrp sapsys /usr/sap/<b>NW1</b>/ERS<b>02</b>
   </code></pre>

1. **[1]** de ASCS/SCS-en ers-instantie profielen aanpassen

   * ASCS/SCS-profiel

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_<b>ASCS00</b>_<b>nw1-ascs</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the keep alive parameter, if using ENSA1
   enque/encni/set_so_keepalive = true
   </code></pre>

   Voor zowel ENSA1 als ENSA2, moet u ervoor zorgen dat de `keepalive` OS-para meters zijn ingesteld zoals beschreven in SAP note [1410736](https://launchpad.support.sap.com/#/notes/1410736).    

   * ERS-profiel

   <pre><code>sudo vi /sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>


1. **[A]** Keep Alive configureren

   De communicatie tussen de SAP NetWeaver-toepassings server en de ASCS/SCS wordt doorgestuurd via een software load balancer. De load balancer verbreekt inactieve verbindingen na een Configureer bare time-out. Als u dit wilt voor komen, moet u een para meter instellen in het SAP NetWeaver ASCS/SCS-profiel, als u ENSA1 gebruikt en de Linux `keepalive` -systeem instellingen op alle SAP-servers voor zowel ENSA1/ENSA2 wijzigen. Lees [SAP-opmerking 1410736][1410736] voor meer informatie.

   <pre><code># Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=300
   </code></pre>

1. **[A]** het/usr/sap/sapservices-bestand bijwerken

   Om het starten van de instanties door het opstart script van sapinit te voor komen, moeten alle instanties die worden beheerd door pacemaker, worden omgeleid vanuit het/usr/sap/sapservices-bestand. Voer geen commentaar uit op het SAP HANA-exemplaar als het wordt gebruikt met HANA SR.

   <pre><code>
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/<b>NW1</b>/ASCS<b>00</b>/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/<b>NW1</b>/ASCS<b>00</b>/exe/sapstartsrv pf=/usr/sap/<b>NW1</b>/SYS/profile/<b>NW1</b>_ASCS<b>00</b>_<b>nw1-ascs</b> -D -u <b>nw1</b>adm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/<b>NW1</b>/ERS<b>02</b>/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/<b>NW1</b>/ERS<b>02</b>/exe/sapstartsrv pf=/usr/sap/<b>NW1</b>/ERS<b>02</b>/profile/<b>NW1</b>_ERS<b>02</b>_<b>nw1-aers</b> -D -u <b>nw1</b>adm
   </code></pre>

1. **[1]** de SAP-cluster resources maken

  Als u Server 1-architectuur (in plaats van ENSA1) gebruikt, definieert u de resources als volgt:

   <pre><code>sudo pcs property set maintenance-mode=true
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ASCS00 SAPInstance \
    InstanceName=<b>NW1</b>_ASCS00_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS00_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    InstanceName=<b>NW1</b>_ERS02_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS02_<b>nw1-aers</b>" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_AERS
      
   sudo pcs constraint colocation add g-<b>NW1</b>_AERS with g-<b>NW1</b>_ASCS -5000
   sudo pcs constraint location rsc_sap_<b>NW1</b>_ASCS<b>00</b> rule score=2000 runs_ers_<b>NW1</b> eq 1
   sudo pcs constraint order start g-<b>NW1</b>_ASCS then stop g-<b>NW1</b>_AERS kind=Optional symmetrical=false
   
   sudo pcs node unstandby <b>nw1-cl-0</b>
   sudo pcs property set maintenance-mode=false
   </code></pre>

   SAP heeft ondersteuning geïntroduceerd voor het plaatsen van Server 2, inclusief replicatie, vanaf SAP NW 7,52. Vanaf ABAP platform 1809 wordt Server 2 in de wachtrij standaard geïnstalleerd. Zie SAP Note [2630416](https://launchpad.support.sap.com/#/notes/2630416) voor de ondersteuning van Server 2 in de wachtrij.
   Als u Server 2-architectuur ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)) gebruikt, installeert u resource agent resource-agents-SAP-4.1.1-12.EL7.x86_64 of nieuwer en definieert u de resources als volgt:

<pre><code>sudo pcs property set maintenance-mode=true
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ASCS00 SAPInstance \
    InstanceName=<b>NW1</b>_ASCS00_<b>nw1-ascs</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ASCS00_<b>nw1-ascs</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_ASCS
   
   sudo pcs resource create rsc_sap_<b>NW1</b>_ERS<b>02</b> SAPInstance \
    InstanceName=<b>NW1</b>_ERS02_<b>nw1-aers</b> START_PROFILE="/sapmnt/<b>NW1</b>/profile/<b>NW1</b>_ERS02_<b>nw1-aers</b>" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-<b>NW1</b>_AERS
      
   sudo pcs constraint colocation add g-<b>NW1</b>_AERS with g-<b>NW1</b>_ASCS -5000
   sudo pcs constraint order g-<b>NW1</b>_ASCS then g-<b>NW1</b>_AERS kind=Optional symmetrical=false
   sudo pcs constraint order start g-<b>NW1</b>_ASCS then stop g-<b>NW1</b>_AERS kind=Optional symmetrical=false
   
   sudo pcs node unstandby <b>nw1-cl-0</b>
   sudo pcs property set maintenance-mode=false
   </code></pre>

   Als u een upgrade uitvoert van een oudere versie en overschakelt naar Server 2 in wachtrij, raadpleegt u SAP Note [2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   > [!NOTE]
   > De time-outs in de bovenstaande configuratie zijn alleen voor beelden en moeten mogelijk worden aangepast aan de specifieke SAP-installatie. 

   Zorg ervoor dat de cluster status OK is en dat alle resources worden gestart. Het is niet belang rijk op welk knoop punt de resources worden uitgevoerd.

   <pre><code>sudo pcs status
   
   # Online: [ <b>nw1-cl-0</b> <b>nw1-cl-1</b> ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started <b>nw1-cl-0</b>
   #  Resource Group: g-<b>NW1</b>_ASCS
   #      fs_<b>NW1</b>_ASCS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-1</b>
   #      nc_<b>NW1</b>_ASCS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-1</b>
   #      vip_<b>NW1</b>_ASCS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-1</b>
   #      rsc_sap_<b>NW1</b>_ASCS00 (ocf::heartbeat:SAPInstance):   Started <b>nw1-cl-1</b>
   #  Resource Group: g-<b>NW1</b>_AERS
   #      fs_<b>NW1</b>_AERS        (ocf::heartbeat:Filesystem):    Started <b>nw1-cl-0</b>
   #      nc_<b>NW1</b>_AERS        (ocf::heartbeat:azure-lb):      Started <b>nw1-cl-0</b>
   #      vip_<b>NW1</b>_AERS       (ocf::heartbeat:IPaddr2):       Started <b>nw1-cl-0</b>
   #      rsc_sap_<b>NW1</b>_ERS02  (ocf::heartbeat:SAPInstance):   Started <b>nw1-cl-0</b>
   </code></pre>

1. **[A]** firewall regels voor ASCS en ers op beide knoop punten toevoegen

   <pre><code># Probe Port of ASCS
   sudo firewall-cmd --zone=public --add-port=620<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=620<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=32<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=32<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=36<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=36<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=39<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=39<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=81<b>00</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=81<b>00</b>/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>13/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>13/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>14/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>14/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>16/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>00</b>16/tcp
   # Probe Port of ERS
   sudo firewall-cmd --zone=public --add-port=621<b>02</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=621<b>02</b>/tcp
   sudo firewall-cmd --zone=public --add-port=32<b>02</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=32<b>02</b>/tcp
   sudo firewall-cmd --zone=public --add-port=33<b>02</b>/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=33<b>02</b>/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>13/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>13/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>14/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>14/tcp
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>16/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=5<b>02</b>16/tcp
   </code></pre>

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Voor bereiding van SAP NetWeaver-toepassings server

Voor sommige data bases moet de installatie van het data base-exemplaar worden uitgevoerd op een toepassings server. Bereid de virtuele machines van de toepassings server voor om ze in deze gevallen te kunnen gebruiken.

In de stappen onderstaande wordt ervan uitgegaan dat u de toepassings server installeert op een andere server dan de ASCS/SCS-en HANA-servers. Anders zijn enkele van de onderstaande stappen (zoals het configureren van de omzetting van hostnamen) niet nodig.

1. Omzetting van hostnamen instellen

   U kunt een DNS-server gebruiken of de bestand/etc/hosts wijzigen op alle knoop punten. In dit voor beeld ziet u hoe u het bestand/etc/hosts-bestand gebruikt.
   Vervang het IP-adres en de hostnaam in de volgende opdrachten:

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Voeg de volgende regels toe aan/etc/hosts. Wijzig het IP-adres en de hostnaam zodat deze overeenkomen met uw omgeving

   <pre><code># IP addresses of the GlusterFS nodes
   <b>10.0.0.40 glust-0</b>
   <b>10.0.0.41 glust-1</b>
   <b>10.0.0.42 glust-2</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   <b>10.0.0.7 nw1-ascs</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   <b>10.0.0.8 nw1-aers</b>
   # IP address of the load balancer frontend configuration for database
   <b>10.0.0.13 nw1-db</b>
   </code></pre>

1. De sapmnt-Directory maken

   <pre><code>sudo mkdir -p /sapmnt/<b>NW1</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>NW1</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. GlusterFS-client en andere vereisten installeren

   <pre><code>sudo yum -y install glusterfs-fuse uuidd
   </code></pre>

1. Koppelings vermeldingen toevoegen

   <pre><code>sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   <b>glust-0</b>:/<b>NW1</b>-sapmnt /sapmnt/<b>NW1</b> glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   <b>glust-0</b>:/<b>NW1</b>-trans /usr/sap/trans glusterfs backup-volfile-servers=<b>glust-1:glust-2</b> 0 0
   </code></pre>

   De nieuwe shares koppelen

   <pre><code>sudo mount -a
   </code></pre>

1. WISSEL bestand configureren
 
   <pre><code>
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   De agent opnieuw starten om de wijziging te activeren

   <pre><code>
   sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Database installeren

In dit voor beeld is SAP NetWeaver geïnstalleerd op SAP HANA. U kunt elke ondersteunde Data Base voor deze installatie gebruiken. Zie voor meer informatie over het installeren van SAP HANA in azure [hoge Beschik baarheid van SAP Hana op virtuele Azure-machines in Red Hat Enterprise Linux][sap-hana-ha] . For a list of supported databases, see [SAP Note 1928533][1928533] .

1. De installatie van het SAP-data base-exemplaar uitvoeren

   Installeer het SAP NetWeaver-data base-exemplaar als root met behulp van een virtuele hostnaam die wordt toegewezen aan het IP-adres van de load balancer frontend-configuratie voor de data base, bijvoorbeeld <b>NW1-DB</b> en <b>10.0.0.13</b>.

   U kunt de para meter sapinst SAPINST_REMOTE_ACCESS_USER gebruiken om een niet-hoofd gebruiker verbinding te laten maken met sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>Installatie van de toepassings server van SAP net-Weaver

Volg deze stappen om een SAP-toepassings server te installeren.

1. Toepassings server voorbereiden

   Volg de stappen in het hoofd stuk [SAP NetWeaver Application Server](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) -voor bereiding hierboven om de toepassings server voor te bereiden.

1. De SAP NetWeaver-toepassings server installeren

   Installeer een primaire of extra SAP NetWeaver-toepassings server.

   U kunt de para meter sapinst SAPINST_REMOTE_ACCESS_USER gebruiken om een niet-hoofd gebruiker verbinding te laten maken met sapinst.

   <pre><code>
   sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

1. SAP HANA Secure Store bijwerken

   Werk het beveiligde archief van SAP HANA bij zodat dit verwijst naar de virtuele naam van de installatie van de SAP HANA systeem replicatie.

   Voer de volgende opdracht uit om de vermeldingen weer te geven als \<sapsid> adm

   <pre><code>hdbuserstore List
   </code></pre>

   Hiermee wordt een lijst weer gegeven met alle vermeldingen en deze moeten er ongeveer als volgt uitzien
   <pre><code>
   DATA FILE       : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.DAT
   KEY FILE        : /home/nw1adm/.hdb/nw1-di-0/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.0.0.14:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>NW1</b>
   </code></pre>

   In de uitvoer ziet u dat het IP-adres van de standaard vermelding verwijst naar de virtuele machine en niet naar het IP-adres van de load balancer. Dit item moet worden gewijzigd om te verwijzen naar de virtuele hostnaam van de load balancer. Zorg ervoor dat u dezelfde poort (**30313** in de bovenstaande uitvoer) en de naam van de data base (**HN1** in de bovenstaande uitvoer) gebruikt.

   <pre><code>su - <b>nw1</b>adm
   hdbuserstore SET DEFAULT <b>nw1-db</b>:<b>30313@NW1</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>De Cluster installatie testen

1. Het ASCS-exemplaar hand matig migreren

   Resource status voordat u begint met testen:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Voer de volgende opdrachten uit als root om het ASCS-exemplaar te migreren.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource move rsc_sap_NW1_ASCS00
   
   [root@nw1-cl-0 ~]# pcs resource clear rsc_sap_NW1_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Resource status na de test:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Knoop punt crash simuleren

   Resource status voordat u begint met testen:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Voer de volgende opdracht uit als root op het knoop punt waar het ASCS-exemplaar wordt uitgevoerd

   <pre><code>[root@nw1-cl-1 ~]# echo b > /proc/sysrq-trigger
   </code></pre>

   De status nadat het knoop punt opnieuw is gestart, moet er als volgt uitzien.

   <pre><code>Online: [ nw1-cl-0 nw1-cl-1 ]
   
   Full list of resources:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   
   Failed Actions:
   * rsc_sap_NW1_ERS02_monitor_11000 on nw1-cl-0 'not running' (7): call=45, status=complete, exitreason='',
       last-rc-change='Tue Aug 21 13:52:39 2018', queued=0ms, exec=0ms
   </code></pre>

   Gebruik de volgende opdracht om de mislukte resources op te schonen.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Resource status na de test:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Bericht Server proces beëindigen

   Resource status voordat u begint met testen:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Voer de volgende opdrachten uit als root om het proces van de berichten server te identificeren en af te breken.

   <pre><code>[root@nw1-cl-0 ~]# pgrep -f ms.sapNW1 | xargs kill -9
   </code></pre>

   Als u de berichten server alleen eenmaal beëindigt, wordt deze opnieuw opgestart door `sapstart` . Als u deze regel matig beëindigt, wordt de ASCS-instantie uiteindelijk door pacemaker naar het andere knoop punt verplaatst. Voer de volgende opdrachten uit als root om de resource status van het ASCS-en ERS-exemplaar na de test op te schonen.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ASCS00
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Resource status na de test:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

1. Server proces voor het plaatsen van de wachtrij beëindigen

   Resource status voordat u begint met testen:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
   </code></pre>

   Voer de volgende opdrachten uit als root op het knoop punt waar het ASCS-exemplaar wordt uitgevoerd om de bewerkings server af te breken.

   <pre><code>
    #If using ENSA1 
    [root@nw1-cl-1 ~]# pgrep -f en.sapNW1 | xargs kill -9
    #If using ENSA2
    [root@nw1-cl-1 ~]# pgrep -f enq.sapNW1 | xargs kill -9
   </code></pre>

   Het ASCS-exemplaar moet direct een failover uitvoeren naar het andere knoop punt. Na het starten van het ASCS-exemplaar moet ook een failover worden uitgevoerd voor het ERS-exemplaar. Voer de volgende opdrachten uit als root om de resource status van het ASCS-en ERS-exemplaar na de test op te schonen.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ASCS00
   [root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Resource status na de test:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Replicatie Server proces voor het plaatsen van de wachtrij beëindigen

   Resource status voordat u begint met testen:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Voer de volgende opdracht uit als hoofd server op het knoop punt waar het ERS-exemplaar wordt uitgevoerd om het proces voor het repliceren van de wachtrij te beëindigen.

   <pre><code>
    #If using ENSA1
    [root@nw1-cl-1 ~]# pgrep -f er.sapNW1 | xargs kill -9
    #If using ENSA2
    [root@nw1-cl-1 ~]# pgrep -f enqr.sapNW1 | xargs kill -9
   </code></pre>

   Als u de opdracht slechts eenmaal uitvoert, `sapstart` wordt het proces opnieuw gestart. Als u dit vaak genoeg uitvoert, `sapstart` wordt het proces niet opnieuw gestart en wordt de bron gestopt. Voer de volgende opdrachten uit als root om de resource status van het ERS-exemplaar na de test op te schonen.

   <pre><code>[root@nw1-cl-0 ~]# pcs resource cleanup rsc_sap_NW1_ERS02
   </code></pre>

   Resource status na de test:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

1. Sapstartsrv-proces voor het plaatsen van de wachtrij beëindigen

   Resource status voordat u begint met testen:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

   Voer de volgende opdrachten uit als root op het knoop punt waar de ASCS wordt uitgevoerd.

   <pre><code>[root@nw1-cl-0 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@nw1-cl-0 ~]# kill -9 59545
   </code></pre>

   Het sapstartsrv-proces moet altijd opnieuw worden gestart door de bron agent van pacemaker als onderdeel van de bewaking. Resource status na de test:

   <pre><code>rsc_st_azure    (stonith:fence_azure_arm):      Started nw1-cl-0
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started nw1-cl-0
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started nw1-cl-0
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-0
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started nw1-cl-0
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started nw1-cl-1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started nw1-cl-1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started nw1-cl-1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started nw1-cl-1
   </code></pre>

## <a name="next-steps"></a>Volgende stappen

* [HA voor SAP NW op Azure Vm's op RHEL voor SAP-toepassingen multi-SID-hand leiding](./high-availability-guide-rhel-multi-sid.md)
* [Azure Virtual Machines planning en implementatie voor SAP][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP][dbms-guide]
* Zie [SAP Hana (grote instanties) hoge Beschik baarheid en herstel na nood gevallen op Azure](hana-overview-high-availability-disaster-recovery.md)voor meer informatie over het tot stand brengen van een hoge Beschik baarheid en het plannen van nood herstel van SAP Hana op Azure (grote exemplaren).
* Zie [hoge Beschik baarheid van SAP Hana op azure virtual machines (vm's)][sap-hana-ha] voor meer informatie over het opzetten van een hoge Beschik baarheid en het plannen van nood herstel van SAP Hana op Azure-vm's.