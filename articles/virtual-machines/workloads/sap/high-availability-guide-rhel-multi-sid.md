---
title: Azure-Vm's hoge Beschik baarheid voor SAP NW op RHEL multi-SID-hand leiding | Microsoft Docs
description: Stel hoge Beschik baarheid in voor SAP NW op Azure virtual machines (Vm's) RHEL multi-SID.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: radeltch
ms.openlocfilehash: b6365e9488dc00ae1bec3217b52fefa534bb0671
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94956310"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-red-hat-enterprise-linux-for-sap-applications-multi-sid-guide"></a>Hoge Beschik baarheid voor SAP NetWeaver op Azure Vm's op Red Hat Enterprise Linux voor de multi-SID-hand leiding voor SAP-toepassingen

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

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

In dit artikel wordt beschreven hoe u meerdere SAP net-computers met hoge Beschik baarheid (dat wil zeggen multi-SID) kunt implementeren in een cluster met twee knoop punten op Azure-Vm's met Red Hat Enterprise Linux voor SAP-toepassingen.  

In de voorbeeld configuraties worden installatie opdrachten enz. drie SAP NetWeaver 7,50-systemen geïmplementeerd in een cluster met hoge Beschik baarheid met één knoop punt. De Sid's van het SAP-systeem zijn:
* **NW1**: het ASCS-exemplaar nummer **00** en de naam van de virtuele host **msnw1ascs**; Het ERS-exemplaar nummer **02** en de naam van de virtuele host **msnw1ers**.  
* **NW2**: ASCS-instantie nummer **10** en virtuele hostname **msnw2ascs**; Het ERS-exemplaar nummer is **12** en de naam van de virtuele host **msnw2ers**.  
* **NW3**: ASCS-instantie nummer **20** en virtuele hostname **msnw3ascs**; Het ERS-exemplaar nummer is **22** en de naam van de virtuele host **msnw3ers**.  

Het artikel heeft geen betrekking op de data base-laag en de implementatie van de SAP NFS-shares. In de voor beelden in dit artikel gebruiken we [Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)  volume **SAPMSID** voor de NFS-shares, ervan uitgaande dat het volume al is geïmplementeerd. Ook wordt ervan uitgegaan dat het Azure NetApp Files-volume wordt geïmplementeerd met het NFSv3-protocol en dat de volgende bestands paden bestaan voor de cluster bronnen voor de ASCS-en ERS-instanties van SAP Systems NW1, NW2 en NW3:  

* volume sapMSID (nfs://10.42.0.4/sapmnt<b>NW1</b>)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>ascs)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>sys)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW1</b>ers)
* volume sapMSID (nfs://10.42.0.4/sapmnt<b>NW2</b>)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>ascs)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>sys)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW2</b>ers)
* volume sapMSID (nfs://10.42.0.4/sapmnt<b>NW3</b>)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>ascs)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>sys)
* volume sapMSID (nfs://10.42.0.4/usrsap<b>NW3</b>ers)

Voordat u begint, raadpleegt u eerst de volgende SAP-opmerkingen en-documenten:

* SAP-opmerking [1928533], die:
  * Lijst met Azure-VM-grootten die worden ondersteund voor de implementatie van SAP-software
  * Informatie over belang rijke capaciteit voor Azure VM-grootten
  * Ondersteunde SAP-software en besturings systemen (OS) en database combinaties
  * Vereiste versie van de SAP-kernel voor Windows en Linux op Microsoft Azure
* [Documentatie over Azure NetApp Files][anf-azure-doc]
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
* [SAP NetWeaver in pacemaker-cluster](https://access.redhat.com/articles/3150081)
* Algemene documentatie voor RHEL
  * [Overzicht van Add-On met hoge Beschik baarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Add-On beheer met hoge Beschik baarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referentie voor Add-On met hoge Beschik baarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [ASCS/ERS configureren voor SAP NetWeaver met zelfstandige bronnen in RHEL 7,5](https://access.redhat.com/articles/3569681)
  * [SAP S/4HANA ASCS/ERS met zelfstandige server 2 (ENSA2) configureren in pacemaker op RHEL](https://access.redhat.com/articles/3974941)
* Documentatie voor Azure-specifieke RHEL:
  * [Ondersteunings beleid voor RHEL-clusters met hoge Beschik baarheid-Microsoft Azure Virtual Machines als cluster leden](https://access.redhat.com/articles/3131341)
  * [Installeren en configureren van een Red Hat Enterprise Linux 7,4 (en hoger) High-Availability cluster op Microsoft Azure](https://access.redhat.com/articles/3252491)
* [NetApp SAP-toepassingen op Microsoft Azure met behulp van Azure NetApp Files][anf-sap-applications-azure]

## <a name="overview"></a>Overzicht

De virtuele machines die deel uitmaken van het cluster, moeten de grootte hebben om alle resources te kunnen uitvoeren, voor het geval failover wordt uitgevoerd. Elke SAP-SID kan onafhankelijk van elkaar worden overgenomen in het cluster met hoge Beschik baarheid met meerdere SID'S.  

Voor een hoge Beschik baarheid zijn voor SAP net-Weave Maxi maal beschik bare shares vereist. In deze documentatie bieden we de voor beelden van de SAP-shares die zijn geïmplementeerd op [Azure NETAPP files NFS-volumes](../../../azure-netapp-files/azure-netapp-files-create-volumes.md). Het is ook mogelijk om de shares te hosten op Maxi maal beschik bare [glusterfs-cluster](./high-availability-guide-rhel-glusterfs.md), dat kan worden gebruikt door meerdere SAP-systemen.  

![Overzicht van de hoge Beschik baarheid van SAP netweave](./media/high-availability-guide-rhel/ha-rhel-multi-sid.png)

> [!IMPORTANT]
> De ondersteuning voor multi-SID clustering van SAP ASCS/ERS met Red Hat Linux als gast besturingssysteem in azure Vm's is beperkt tot **vijf** SAP-sid's op hetzelfde cluster. Elke nieuwe SID verhoogt de complexiteit. Een combi natie van SAP voor het in de wachtrij plaatsen van replicatie server 1 en in plaats van replicatie Server 2 op hetzelfde cluster wordt **niet ondersteund**. Met multi-SID clustering wordt de installatie van meerdere SAP ASCS/ERS-exemplaren met verschillende Sid's in één pacemaker-cluster beschreven. Momenteel wordt multi-SID-clustering alleen ondersteund voor ASCS/ERS.  

> [!TIP]
> De multi-SID clustering van SAP ASCS/ERS is een oplossing met een grotere complexiteit. Het is complexer om te implementeren. Het omvat ook meer administratieve inspanningen wanneer er onderhouds activiteiten worden uitgevoerd (zoals patches van besturings systemen). Voordat u met de daad werkelijke implementatie begint, moet u de implementatie zorgvuldig plannen en alle betrokken onderdelen, zoals Vm's, NFS-koppels, Vip's, load balancer configuraties, enzovoort.  

SAP NetWeaver ASCS, SAP NetWeaver SCS en SAP NetWeaver ERS gebruiken virtuele hostnamen en virtuele IP-adressen. Op Azure is een load balancer vereist voor het gebruik van een virtueel IP-adres. U kunt het beste [standaard Load Balancer](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md)gebruiken.  

De volgende lijst bevat de configuratie van de (A) SCS-en ERS-load balancer voor dit multi-SID-cluster voorbeeld met drie SAP-systemen. Voor elk van de Sid's hebt u afzonderlijke frontend-IP-, status controles en taakverdelings regels nodig voor elk ASCS-en ERS-exemplaar. Wijs alle virtuele machines, die deel uitmaken van het ASCS/ASCS-cluster, toe aan één back-end-pool van één ILB.  

### <a name="ascs"></a>(A)SCS

* Front-end configuratie
  * IP-adres voor NW1:10.3.1.50
  * IP-adres voor NW2:10.3.1.52
  * IP-adres voor NW3:10.3.1.54

* Poorten testen
  * Poort 620 <strong> &lt; Nr &gt;</strong>, daarom voor NW1-, NW2-en NW3-test poorten 620 **00**, 620 **10** en 620 **20**
* Regels voor taak verdeling: Maak een regel voor elk exemplaar, dat wil zeggen, NW1/ASCS, NW2/ASCS en NW3/ASCS.
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
  * IP-adres voor NW1 10.3.1.51
  * IP-adres voor NW2 10.3.1.53
  * IP-adres voor NW3 10.3.1.55

* Test poort
  * Poort 621 <strong> &lt; Nr &gt;</strong>, daarom voor NW1-, NW2-en N3-test poorten 621 **02**, 621 **12** en 621 **22**
* Regels voor taak verdeling: Maak een regel voor elk exemplaar, dat wil zeggen, NW1/ERS, NW2/ERS en NW3/ERS.
  * Als u Standard Load Balancer gebruikt, selecteert u **ha-poorten**
  * Als u basis Load Balancer gebruikt, maakt u regels voor taak verdeling voor de volgende poorten
    * 32<strong> &lt; Nr &gt; </strong> TCP
    * 33<strong> &lt; Nr &gt; </strong> TCP
    * 5<strong> &lt; Nr &gt; </strong>. 13 TCP
    * 5<strong> &lt; Nr &gt; </strong>. 14 TCP
    * 5<strong> &lt; Nr &gt; </strong>. 16 TCP

* Back-end-configuratie
  * Verbonden met primaire netwerk interfaces van alle virtuele machines die deel moeten uitmaken van het (A) SCS/ERS-cluster

> [!IMPORTANT]
> Zwevend IP wordt niet ondersteund voor een secundaire IP-configuratie in een NIC in scenario's voor taak verdeling. Zie beperkingen voor [Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-multivip-overview#limitations)voor meer informatie. Als u een extra IP-adres voor de virtuele machine nodig hebt, implementeert u een tweede NIC.  

> [!Note]
> Wanneer Vm's zonder open bare IP-adressen in de back-endadresgroep van intern (geen openbaar IP-adres load balancer) worden geplaatst, is er geen uitgaande Internet verbinding, tenzij er aanvullende configuratie wordt uitgevoerd om route ring naar open bare eind punten toe te staan. Zie [connectiviteit van open bare eind punten voor virtual machines met behulp van Azure Standard Load Balancer in scenario's met hoge Beschik baarheid voor SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md)voor meer informatie over het bezorgen van uitgaande verbindingen.  

> [!IMPORTANT]
> Schakel TCP-tijds tempels niet in op virtuele Azure-machines die achter Azure Load Balancer worden geplaatst. Door TCP-tijds tempels in te scha kelen, mislukken de status controles. Stel para meter **net.IPv4.tcp_timestamps** in op **0**. Zie [Load Balancer Health probe](../../../load-balancer/load-balancer-custom-probe-overview.md)(Engelstalig) voor meer informatie.

## <a name="sap-shares"></a>SAP-shares

Voor SAP NetWeaver is gedeelde opslag vereist voor het Trans Port, de profielmap, enzovoort. Voor Maxi maal beschik bare SAP-systemen is het belang rijk dat u beschikt over Maxi maal beschik bare shares. U moet beslissen over de architectuur van uw SAP-shares. Een optie is het implementeren van de shares op [Azure NETAPP files NFS-volumes](../../../azure-netapp-files/azure-netapp-files-create-volumes.md).  Met Azure NetApp Files krijgt u ingebouwde hoge Beschik baarheid voor de SAP NFS-shares.

Een andere mogelijkheid is om [glusterfs te bouwen op virtuele machines in azure op Red Hat Enterprise Linux voor SAP NetWeaver](./high-availability-guide-rhel-glusterfs.md), die kan worden gedeeld tussen meerdere SAP-systemen. 

## <a name="deploy-the-first-sap-system-in-the-cluster"></a>Het eerste SAP-systeem in het cluster implementeren

Nu u de architectuur van de SAP-shares hebt bepaald, implementeert u het eerste SAP-systeem in het cluster en volgt u de bijbehorende documentatie.

* Als u Azure NetApp Files NFS-volumes gebruikt, volgt u [Azure-vm's hoge Beschik baarheid voor SAP NetWeaver op Red Hat Enterprise Linux met Azure NetApp files voor SAP-toepassingen](./high-availability-guide-rhel-netapp-files.md)  
* Als u GlusterFS-cluster gebruikt, volgt u [glusterfs op virtuele machines van Azure op Red Hat Enterprise Linux voor SAP net-Weaver](./high-availability-guide-rhel-glusterfs.md).  

De documenten die hierboven worden weer gegeven, begeleiden u bij de stappen voor het voorbereiden van de benodigde infra structuur, het bouwen van het cluster, het voorbereiden van het besturings systeem voor het uitvoeren van de SAP-toepassing.  

> [!TIP]
> Test de failover-functionaliteit van het cluster altijd, nadat het eerste systeem is geïmplementeerd, voordat u de extra SAP-Sid's aan het cluster toevoegt. Op die manier weet u zeker dat de cluster functionaliteit werkt, voordat u de complexiteit van extra SAP-systemen toevoegt aan het cluster.   

## <a name="deploy-additional-sap-systems-in-the-cluster"></a>Extra SAP-systemen in het cluster implementeren

In dit voor beeld wordt ervan uitgegaan dat systeem **NW1** al is geïmplementeerd in het cluster. We laten u zien hoe u kunt implementeren in het cluster SAP Systems **NW2** en **NW3**. 

De volgende items worden voorafgegaan door **[A]** , van toepassing op alle knoop punten, **[1]** -alleen van toepassing op knoop punt 1 of **[2]** -alleen van toepassing op knoop punt 2.

### <a name="prerequisites"></a>Vereisten 

> [!IMPORTANT]
> Volg vóór het volgen van de instructies voor het implementeren van extra SAP-systemen in het cluster de instructies voor het implementeren van het eerste SAP-systeem in het cluster, omdat er stappen zijn die alleen nodig zijn tijdens de eerste systeem implementatie.  

In deze documentatie wordt ervan uitgegaan dat:
* Het pacemaker-cluster is al geconfigureerd en wordt uitgevoerd.  
* Ten minste één SAP-systeem (ASCS/ERS-exemplaar) is al geïmplementeerd en wordt uitgevoerd in het cluster.  
* De functionaliteit van de cluster-failover is getest.  
* De NFS-shares voor alle SAP-systemen worden geïmplementeerd.  

### <a name="prepare-for-sap-netweaver-installation"></a>Voorbereiden op SAP NetWeaver-installatie

1. Voeg configuratie toe voor het zojuist geïmplementeerde systeem ( **NW2**, **NW3**) aan de bestaande Azure Load Balancer, gevolgd door de instructies om [Azure Load Balancer hand matig te implementeren via Azure Portal](./high-availability-guide-rhel-netapp-files.md#deploy-linux-manually-via-azure-portal). Pas de IP-adressen, de status test poorten en de taakverdelings regels voor uw configuratie aan.  

2. **[A]** naam omzetting voor de extra SAP-systemen. U kunt de DNS-server gebruiken of wijzigen `/etc/hosts` op alle knoop punten. In dit voor beeld ziet u hoe u het `/etc/hosts` bestand gebruikt.  Pas de IP-adressen en de hostnamen aan uw omgeving aan. 

    ```
    sudo vi /etc/hosts
    # IP address of the load balancer frontend configuration for NW2 ASCS
    10.3.1.52 msnw2ascs
    # IP address of the load balancer frontend configuration for NW3 ASCS
    10.3.1.54 msnw3ascs
    # IP address of the load balancer frontend configuration for NW2 ERS
    10.3.1.53 msnw2ers
    # IP address of the load balancer frontend configuration for NW3 ERS
    10.3.1.55 msnw3ers
   ```

3. **[A]** Maak de gedeelde mappen voor de extra **NW2** -en **NW3** SAP-systemen die u op het cluster implementeert. 

    ```
    sudo mkdir -p /sapmnt/NW2
    sudo mkdir -p /usr/sap/NW2/SYS
    sudo mkdir -p /usr/sap/NW2/ASCS10
    sudo mkdir -p /usr/sap/NW2/ERS12
    sudo mkdir -p /sapmnt/NW3
    sudo mkdir -p /usr/sap/NW3/SYS
    sudo mkdir -p /usr/sap/NW3/ASCS20
    sudo mkdir -p /usr/sap/NW3/ERS22

    
    sudo chattr +i /sapmnt/NW2
    sudo chattr +i /usr/sap/NW2/SYS
    sudo chattr +i /usr/sap/NW2/ASCS10
    sudo chattr +i /usr/sap/NW2/ERS12
    sudo chattr +i /sapmnt/NW3
    sudo chattr +i /usr/sap/NW3/SYS
    sudo chattr +i /usr/sap/NW3/ASCS20
    sudo chattr +i /usr/sap/NW3/ERS22
   ```

4. **[A]** Voeg de koppelings vermeldingen voor de/sapmnt/sid-en/usr/sap/sid/sys-bestands systemen toe voor de extra SAP-systemen die u op het cluster implementeert. In dit voor beeld **NW2** en **NW3**.  

   Update bestand `/etc/fstab` met de bestands systemen voor de extra SAP-systemen die u naar het cluster implementeert.  

   * Als u Azure NetApp Files gebruikt, volgt u de instructies [hier](./high-availability-guide-rhel-netapp-files.md#prepare-for-sap-netweaver-installation)  
   * Als u GlusterFS-cluster gebruikt, volgt u de instructies [hier](./high-availability-guide-rhel.md#prepare-for-sap-netweaver-installation)  

### <a name="install-ascs--ers"></a>ASCS/ERS installeren

1. Maak de virtuele IP-en Health probe cluster resources voor de ASCS-exemplaren van de extra SAP-systemen die u naar het cluster implementeert. Het voor beeld dat hier wordt weer gegeven, is voor **NW2** en **NW3** ASCS, met behulp van NFS op Azure NetApp files volumes met NFSv3-protocol.  

    ```
    sudo pcs resource create fs_NW2_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ascs' \
    directory='/usr/sap/NW2/ASCS10' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_ASCS

    sudo pcs resource create vip_NW2_ASCS IPaddr2 \
    ip=10.3.1.52 cidr_netmask=24 \
     --group g-NW2_ASCS
    
    sudo pcs resource create nc_NW2_ASCS azure-lb port=62010 \
     --group g-NW2_ASCS

    sudo pcs resource create fs_NW3_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ascs' \
    directory='/usr/sap/NW3/ASCS20' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-NW3_ASCS

    sudo pcs resource create vip_NW3_ASCS IPaddr2 \
    ip=10.3.1.54 cidr_netmask=24 \
    --group g-NW3_ASCS

    sudo pcs resource create nc_NW3_ASCS azure-lb port=62020 \
    --group g-NW3_ASCS
    ```

   Zorg ervoor dat de cluster status OK is en dat alle resources worden gestart. Het is niet belang rijk op welk knoop punt de resources worden uitgevoerd.  

2. **[1]** SAP NetWeaver ASCS installeren  

   Installeer SAP NetWeaver ASCS als root, met behulp van een virtuele hostnaam die wordt toegewezen aan het IP-adres van de load balancer front-end-configuratie voor de ASCS. Bijvoorbeeld: voor System **NW2** is de virtuele hostnaam <b>msnw2ascs</b>, <b>10.3.1.52</b> en het exemplaar nummer dat u hebt gebruikt voor de test van de Load Balancer, bijvoorbeeld <b>10</b>. Voor System **NW3** is de virtuele hostnaam <b>msnw3ascs</b>, <b>10.3.1.54</b> en het exemplaar nummer dat u hebt gebruikt voor de test van de Load Balancer, bijvoorbeeld <b>20</b>. Noteer op het cluster knooppunt dat u ASCS hebt geïnstalleerd voor elke SAP-SID.  

   U kunt de para meter sapinst SAPINST_REMOTE_ACCESS_USER gebruiken om een niet-hoofd gebruiker verbinding te laten maken met sapinst. U kunt de para meter SAPINST_USE_HOSTNAME gebruiken om SAP te installeren met behulp van de naam van de virtuele host.  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   Als de installatie geen submap kan maken in/usr/sap/**sid**/ASCS **instance #**, probeert u de eigenaar in te stellen op **sid** adm en groep naar sapsys van het ASCS-**exemplaar #** en probeert u het opnieuw.

3. **[1]** Maak een virtuele IP-en status test cluster bronnen voor het ers-exemplaar van het extra SAP-systeem dat u naar het cluster implementeert. Het voor beeld dat hier wordt weer gegeven, is voor **NW2** en **NW3** ers, met behulp van NFS op Azure NetApp files volumes met NFSv3-protocol.  

    ```
    sudo pcs resource create fs_NW2_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ers' \
    directory='/usr/sap/NW2/ERS12' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_AERS

    sudo pcs resource create vip_NW2_AERS IPaddr2 \
    ip=10.3.1.53 cidr_netmask=24 \
     --group g-NW2_AERS

    sudo pcs resource create nc_NW2_AERS azure-lb port=62112 \
     --group g-NW2_AERS

    sudo pcs resource create fs_NW3_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ers' \
    directory='/usr/sap/NW3/ERS22' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW3_AERS

    sudo pcs resource create vip_NW3_AERS IPaddr2 \
    ip=10.3.1.55 cidr_netmask=24 \
     --group g-NW3_AERS

    sudo pcs resource create nc_NW3_AERS azure-lb port=62122 \
     --group g-NW3_AERS
   ```

   Zorg ervoor dat de cluster status OK is en dat alle resources worden gestart.  

   Zorg er vervolgens voor dat de resources van de zojuist gemaakte ERS-groep worden uitgevoerd op het cluster knooppunt, tegengesteld aan het cluster knooppunt waar het ASCS-exemplaar voor hetzelfde SAP-systeem is geïnstalleerd.  Als bijvoorbeeld NW2 ASCS is geïnstalleerd op `rhelmsscl1` , moet u ervoor zorgen dat de groep NW2 ers wordt uitgevoerd `rhelmsscl2` .  U kunt de NW2 ERS-groep migreren naar `rhelmsscl2` door de volgende opdracht uit te voeren voor een van de cluster bronnen in de groep: 

    ```
      pcs resource move fs_NW2_AERS rhelmsscl2
    ```

4. **[2]** SAP NetWeaver ers installeren

   Installeer SAP NetWeaver ERS als root op het andere knoop punt, met behulp van een virtuele hostnaam die wordt toegewezen aan het IP-adres van de load balancer front-end-configuratie voor de ERS. Bijvoorbeeld, de naam van de virtuele **NW2** is <b>msnw2ers</b>, <b>10.3.1.53</b> en het exemplaar nummer dat u hebt gebruikt voor de test van de Load Balancer, bijvoorbeeld <b>12</b>. Voor System **NW3**, de naam van de virtuele host <b>msnw3ers</b>, <b>10.3.1.55</b> en het exemplaar nummer dat u hebt gebruikt voor de test van de Load Balancer, bijvoorbeeld <b>22</b>. 

   U kunt de para meter sapinst SAPINST_REMOTE_ACCESS_USER gebruiken om een niet-hoofd gebruiker verbinding te laten maken met sapinst. U kunt de para meter SAPINST_USE_HOSTNAME gebruiken om SAP te installeren met behulp van de naam van de virtuele host.  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   > [!NOTE]
   > Gebruik SWPM SP 20 PL of hoger. Bij lagere versies worden de machtigingen niet correct ingesteld en de installatie mislukt.

   Als de installatie geen submap kan maken in/usr/sap/**NW2**/ers **instance #**, probeert u de eigenaar in te stellen op **sid** adm en de groep naar sapsys van de ers **instance #** map en probeert u het opnieuw.

   Als het nodig is om de ERS-groep van het zojuist geïmplementeerde SAP-systeem naar een ander cluster knooppunt te migreren, vergeet dan niet om de locatie beperking voor de groep ERS te verwijderen. U kunt de beperking verwijderen door de volgende opdracht uit te voeren (het voor beeld wordt gegeven voor SAP Systems **NW2** en **NW3**). Zorg ervoor dat u de tijdelijke beperkingen verwijdert voor dezelfde resource die u in de opdracht hebt gebruikt om de ERS-cluster groep te verplaatsen.

    ```
      pcs resource clear fs_NW2_AERS
      pcs resource clear fs_NW3_AERS
    ```

5. **[1]** de ASCS/SCS-en ers-instantie profielen aanpassen voor de ZOJUIST geïnstalleerde SAP-systemen. Het voor beeld dat hieronder wordt weer gegeven, is voor NW2. U moet de profielen ASCS/SCS en ERS aanpassen voor alle SAP-exemplaren die aan het cluster worden toegevoegd.  
 
   * ASCS/SCS-profiel

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs
   
      # Change the restart command to a start command
      #Restart_Program_01 = local $(_EN) pf=$(_PF)
      Start_Program_01 = local $(_EN) pf=$(_PF)

      # Add the keep alive parameter, if using ENSA1
      enque/encni/set_so_keepalive = true
      ```

     Voor zowel ENSA1 als ENSA2, moet u ervoor zorgen dat de `keepalive` OS-para meters zijn ingesteld zoals beschreven in SAP note [1410736](https://launchpad.support.sap.com/#/notes/1410736).    

   * ERS-profiel

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ERS12_msnw2ers
   
      # Change the restart command to a start command
      #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
      Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
      # remove Autostart from ERS profile
      # Autostart = 1
      ```

6. **[A]** het/usr/sap/sapservices-bestand bijwerken

   Om het starten van de instanties door het opstart script van sapinit te voor komen, moeten alle instanties die worden beheerd door pacemaker, een opmerking van het `/usr/sap/sapservices` bestand maken.  Het voor beeld hieronder wordt weer gegeven voor SAP-systemen **NW2** en **NW3**.  

   ```
    # On the node where ASCS was installed, comment out the line for the ASCS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ASCS10/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ASCS10/exe/sapstartsrv pf=/usr/sap/NW2/SYS/profile/NW2_ASCS10_msnw2ascs -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ASCS20/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ASCS20/exe/sapstartsrv pf=/usr/sap/NW3/SYS/profile/NW3_ASCS20_msnw3ascs -D -u nw3adm

    # On the node where ERS was installed, comment out the line for the ERS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ERS12/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ERS12/exe/sapstartsrv pf=/usr/sap/NW2/ERS12/profile/NW2_ERS12_msnw2ers -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ERS22/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ERS22/exe/sapstartsrv pf=/usr/sap/NW3/ERS22/profile/NW3_ERS22_msnw3ers -D -u nw3adm
   ```

7. **[1]** de SAP-cluster resources maken voor het ZOJUIST geïnstalleerde SAP-systeem.  

   Als u Server 1-architectuur (ENSA1) in de wachtrij plaatst, definieert u de resources voor SAP Systems **NW2** en **NW3** als volgt:

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint location rsc_sap_NW2_ASCS10 rule score=2000 runs_ers_NW2 eq 1
    sudo pcs constraint order g-NW2_ASCS then g-NW2_AERS kind=Optional symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint location rsc_sap_NW3_ASCS20 rule score=2000 runs_ers_NW3 eq 1
    sudo pcs constraint order g-NW3_ASCS then g-NW3_AERS kind=Optional symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   SAP heeft ondersteuning geïntroduceerd voor het plaatsen van Server 2, inclusief replicatie, vanaf SAP NW 7,52. Vanaf ABAP platform 1809 wordt Server 2 in de wachtrij standaard geïnstalleerd. Zie SAP Note [2630416](https://launchpad.support.sap.com/#/notes/2630416) voor de ondersteuning van Server 2 in de wachtrij.
   Als u Server 2-architectuur ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)) in de wachtrij plaatst, definieert u de resources voor SAP Systems **NW2** en **NW3** als volgt:

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint order g-NW2_ASCS then g-NW2_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW2_ASCS then stop g-NW2_AERS symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint order g-NW3_ASCS then g-NW3_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW3_ASCS then stop g-NW3_AERS symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   Als u een upgrade uitvoert van een oudere versie en overschakelt naar Server 2 in wachtrij, raadpleegt u SAP Note [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   > [!NOTE]
   > De time-outs in de bovenstaande configuratie zijn alleen voor beelden en moeten mogelijk worden aangepast aan de specifieke SAP-installatie. 

   Zorg ervoor dat de cluster status OK is en dat alle resources worden gestart. Het is niet belang rijk op welk knoop punt de resources worden uitgevoerd.
   In het volgende voor beeld ziet u de status van de cluster bronnen nadat SAP-systemen **NW2** en **NW3** aan het cluster zijn toegevoegd. 

    ```
     sudo pcs status

    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    ```

8. **[A]** Voeg firewall regels voor ASCS en ers op beide knoop punten toe.  In het onderstaande voor beeld ziet u de firewall regels voor zowel SAP Systems **NW2** als **NW3**.  

   ```
    # NW2 - ASCS
    sudo firewall-cmd --zone=public --add-port=62010/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62010/tcp
    sudo firewall-cmd --zone=public --add-port=3210/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3210/tcp
    sudo firewall-cmd --zone=public --add-port=3610/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3610/tcp
    sudo firewall-cmd --zone=public --add-port=3910/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3910/tcp
    sudo firewall-cmd --zone=public --add-port=8110/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8110/tcp
    sudo firewall-cmd --zone=public --add-port=51013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51013/tcp
    sudo firewall-cmd --zone=public --add-port=51014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51014/tcp
    sudo firewall-cmd --zone=public --add-port=51016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51016/tcp
    # NW2 - ERS
    sudo firewall-cmd --zone=public --add-port=62112/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62112/tcp
    sudo firewall-cmd --zone=public --add-port=3312/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3312/tcp
    sudo firewall-cmd --zone=public --add-port=51213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51213/tcp
    sudo firewall-cmd --zone=public --add-port=51214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51214/tcp
    sudo firewall-cmd --zone=public --add-port=51216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51216/tcp
    # NW3 - ASCS
    sudo firewall-cmd --zone=public --add-port=62020/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62020/tcp
    sudo firewall-cmd --zone=public --add-port=3220/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3220/tcp
    sudo firewall-cmd --zone=public --add-port=3620/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3620/tcp
    sudo firewall-cmd --zone=public --add-port=3920/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3920/tcp
    sudo firewall-cmd --zone=public --add-port=8120/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8120/tcp
    sudo firewall-cmd --zone=public --add-port=52013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52013/tcp
    sudo firewall-cmd --zone=public --add-port=52014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52014/tcp
    sudo firewall-cmd --zone=public --add-port=52016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52016/tcp
    # NW3 - ERS
    sudo firewall-cmd --zone=public --add-port=62122/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62122/tcp
    sudo firewall-cmd --zone=public --add-port=3322/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3322/tcp
    sudo firewall-cmd --zone=public --add-port=52213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52213/tcp
    sudo firewall-cmd --zone=public --add-port=52214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52214/tcp
    sudo firewall-cmd --zone=public --add-port=52216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52216/tcp
   ```

### <a name="proceed-with-the-sap-installation"></a>Door gaan met de SAP-installatie 

Voltooi uw SAP-installatie door:

* [Uw SAP NetWeaver-toepassings servers voorbereiden](./high-availability-guide-rhel-netapp-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7)
* [Een DBMS-exemplaar installeren](./high-availability-guide-rhel-netapp-files.md#install-database)
* [Een primaire SAP-toepassings server installeren](./high-availability-guide-rhel-netapp-files.md#sap-netweaver-application-server-installation)
* Een of meer extra SAP-toepassings exemplaren installeren

## <a name="test-the-multi-sid-cluster-setup"></a>De multi-SID-cluster installatie testen

De volgende tests zijn een subset van de test cases in de best practices-richt lijnen van Red Hat. Ze zijn opgenomen voor uw gemak. Raadpleeg de volgende documentatie voor een volledige lijst met cluster tests:

* Als u Azure NetApp Files NFS-volumes gebruikt, volgt u [Azure-vm's hoge Beschik baarheid voor SAP NetWeaver op RHEL met Azure NetApp files voor SAP-toepassingen](./high-availability-guide-rhel-netapp-files.md)
* Als u Maxi maal beschikbaar `GlusterFS` wilt gebruiken, volgt u [Azure-vm's hoge beschik BAARHEID voor SAP NetWeaver op RHEL voor SAP-toepassingen](./high-availability-guide-rhel.md).  

Lees altijd de hand leidingen voor de best practices voor Red Hat en voer alle extra tests uit die mogelijk zijn toegevoegd.  
De tests die worden weer gegeven, bevinden zich in een twee knoop punt, multi-SID-cluster waarop drie SAP-systemen zijn geïnstalleerd.  

1. Migreer het ASCS-exemplaar hand matig. In het voor beeld wordt het ASCS-exemplaar voor SAP System NW3 gemigreerd.

   Resource status voordat u begint met testen:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   Voer de volgende opdrachten uit als root om het NW3 ASCS-exemplaar te migreren.

   ```
    pcs resource move rsc_sap_NW3_ASCS200
    # Clear temporary migration constraints
    pcs resource clear rsc_sap_NW3_ASCS20

    # Remove failed actions for the ERS that occurred as part of the migration
    pcs resource cleanup rsc_sap_NW3_ERS22
   ```

   Resource status na de test:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

1. Knoop punt crash simuleren

   Resource status voordat u begint met testen:

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

   Voer de volgende opdracht uit als basis op een knoop punt, waarbij ten minste één ASCS-exemplaar wordt uitgevoerd. In dit voor beeld hebben we de opdracht uitgevoerd op `rhelmsscl1` , waar de ASCS-instanties voor NW1, NW2 en NW3 worden uitgevoerd.  

   ```
   echo c > /proc/sysrq-trigger
   ```

   De status na de test en na het vastlopen van het knoop punt is vastgelopen. dit moet er als volgt uitzien.

   ```
    Full list of resources:

    rsc_st_azure    (stonith:fence_azure_arm):      Started rhelmsscl2
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   Als er berichten voor mislukte resources zijn, reinigt u de status van de resources die zijn mislukt. Bijvoorbeeld:

   ```
   pcs resource cleanup rsc_sap_NW1_ERS02
   ```

## <a name="next-steps"></a>Volgende stappen

* [Azure Virtual Machines planning en implementatie voor SAP][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP][dbms-guide]
* Zie [hoge Beschik baarheid van SAP Hana op azure virtual machines (vm's)][sap-hana-ha] voor meer informatie over het opzetten van een hoge Beschik baarheid en het plannen van nood herstel van SAP Hana op Azure-vm's.
