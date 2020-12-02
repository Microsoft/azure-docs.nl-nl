---
title: Azure-Vm's hoge Beschik baarheid voor SAP NetWeaver op SLES multi-SID-hand leiding | Microsoft Docs
description: Multi-SID-hand leiding voor hoge Beschik baarheid voor SAP NetWeaver op SUSE Linux Enterprise Server voor SAP-toepassingen
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: radeltch
ms.openlocfilehash: 2ce57911434aa0fdf1a5e624090633e75d98a5ad
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96484240"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-for-sap-applications-multi-sid-guide"></a>Hoge Beschik baarheid voor SAP NetWeaver op Azure Vm's op SUSE Linux Enterprise Server voor de multi-SID-hand leiding voor SAP-toepassingen

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

In dit artikel wordt beschreven hoe u meerdere SAP NetWeaver of S4HANA Maxi maal beschik bare systemen (dat wil zeggen multi-SID) kunt implementeren in een cluster met twee knoop punten op Azure-Vm's met SUSE Linux Enterprise Server voor SAP-toepassingen.  

In de voorbeeld configuraties worden installatie opdrachten enz. drie SAP NetWeaver 7,50-systemen geïmplementeerd in een cluster met hoge Beschik baarheid met één knoop punt. De Sid's van het SAP-systeem zijn:
* **NW1**: het ASCS-exemplaar nummer **00** en de naam van de virtuele host **msnw1ascs**; Het ERS-exemplaar nummer **02** en de naam van de virtuele host **msnw1ers**.  
* **NW2**: ASCS-instantie nummer **10** en virtuele hostname **msnw2ascs**; Het ERS-exemplaar nummer is **12** en de naam van de virtuele host **msnw2ers**.  
* **NW3**: ASCS-instantie nummer **20** en virtuele hostname **msnw3ascs**; Het ERS-exemplaar nummer is **22** en de naam van de virtuele host **msnw3ers**.  

Het artikel heeft geen betrekking op de data base-laag en de implementatie van de SAP NFS-shares. In de voor beelden in dit artikel gebruiken we virtuele namen NW2-NFS voor de NW2 NFS-shares en NW3-NFS voor de NW3 NFS-shares, ervan uitgaande dat het NFS-cluster is geïmplementeerd.  

Voordat u begint, raadpleegt u eerst de volgende SAP-opmerkingen en-documenten:

* SAP-opmerking [1928533][1928533], die:
  * Lijst met Azure-VM-grootten die worden ondersteund voor de implementatie van SAP-software
  * Informatie over belang rijke capaciteit voor Azure VM-grootten
  * Ondersteunde SAP-software en besturings systemen (OS) en database combinaties
  * Vereiste versie van de SAP-kernel voor Windows en Linux op Microsoft Azure

* SAP-opmerking [2015553][2015553] bevat vereisten voor SAP-ondersteuning voor SAP-software-implementaties in Azure.
* SAP Note [2205917][2205917] heeft aanbevolen instellingen voor het besturings systeem voor SuSE Linux Enterprise Server voor SAP-toepassingen
* SAP Note [1944799][1944799] heeft SAP Hana richt lijnen voor SuSE Linux Enterprise Server voor SAP-toepassingen
* SAP Note [2178632][2178632] bevat gedetailleerde informatie over alle bewakings gegevens die zijn gerapporteerd voor SAP in Azure.
* SAP Note [2191498][2191498] heeft de vereiste SAP host agent-versie voor Linux in Azure.
* SAP Note [2243692][2243692] bevat informatie over SAP-licentie verlening op Linux in Azure.
* SAP Note [1984787][1984787] bevat algemene informatie over SuSE Linux Enterprise Server 12.
* SAP Note [1999351][1999351] bevat extra informatie over probleem oplossing voor de uitgebreide bewakings extensie van Azure voor SAP.
* Op de [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) zijn alle vereiste SAP-notities voor Linux geïnstalleerd.
* [Azure Virtual Machines planning en implementatie voor SAP op Linux][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP op Linux][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP op Linux][dbms-guide]
* [Best Practice-hand leidingen voor SuSE SAP ha][suse-ha-guide] De gidsen bevatten alle vereiste informatie voor het instellen van netweave HA en SAP HANA systeem replicatie on-premises. Gebruik deze hand leidingen als algemene basis lijn. Ze bieden veel meer gedetailleerde informatie.
* [Release opmerkingen voor de SUSE-extensie voor hoge Beschik baarheid van 12 SP3][suse-ha-12sp3-relnotes]
* [SUSE multi-SID cluster Guide voor SLES 12 en SLES 15](https://documentation.suse.com/sbp/all/html/SBP-SAP-MULTI-SID/index.html)
* [NetApp SAP-toepassingen op Microsoft Azure met behulp van Azure NetApp Files][anf-sap-applications-azure]
## <a name="overview"></a>Overzicht

De grootte van de virtuele machines die deel uitmaken van het cluster, moet kunnen worden uitgevoerd voor alle resources, voor het geval failover plaatsvindt. Elke SAP-SID kan onafhankelijk van elkaar worden overgenomen in het cluster met hoge Beschik baarheid met meerdere SID'S.  Als u gebruikmaakt van een SBD-omheining, kunnen de SBD-apparaten worden gedeeld tussen meerdere clusters.  

Voor een hoge Beschik baarheid zijn voor SAP net-Weave Maxi maal beschik bare NFS-shares vereist. In dit voor beeld gaan we ervan uit dat de SAP NFS-shares worden gehost op een Maxi maal beschik bare [NFS-Bestands server](./high-availability-guide-suse-nfs.md), die door meerdere SAP-systemen kan worden gebruikt. Of de shares worden geïmplementeerd op [Azure NETAPP files NFS-volumes](../../../azure-netapp-files/azure-netapp-files-create-volumes.md).  

![Het pacemaker-cluster bevat gedetailleerde informatie over twee multi-SID clusters, msidcl1 en msidcl2.](./media/high-availability-guide-suse/ha-suse-multi-sid.png)

> [!IMPORTANT]
> De ondersteuning voor multi-SID clustering van SAP ASCS/ERS met SUSE Linux als gast besturingssysteem in azure Vm's is beperkt tot **vijf** SAP-sid's op hetzelfde cluster. Elke nieuwe SID verhoogt de complexiteit. Een combi natie van SAP voor het in de wachtrij plaatsen van replicatie server 1 en in plaats van replicatie Server 2 op hetzelfde cluster wordt **niet ondersteund**. Met multi-SID clustering wordt de installatie van meerdere SAP ASCS/ERS-exemplaren met verschillende Sid's in één pacemaker-cluster beschreven. Momenteel wordt multi-SID-clustering alleen ondersteund voor ASCS/ERS.  

> [!TIP]
> De multi-SID clustering van SAP ASCS/ERS is een oplossing met een grotere complexiteit. Het is complexer om te implementeren. Het omvat ook meer administratieve inspanningen wanneer er onderhouds activiteiten worden uitgevoerd (zoals patches van besturings systemen). Voordat u met de daad werkelijke implementatie begint, moet u de implementatie zorgvuldig plannen en alle betrokken onderdelen, zoals Vm's, NFS-koppels, Vip's, load balancer configuraties, enzovoort.  

De NFS-server, SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS en de SAP HANA Data Base gebruiken virtuele hostnamen en virtuele IP-adressen. Op Azure is een load balancer vereist voor het gebruik van een virtueel IP-adres. U kunt het beste [standaard Load Balancer](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md)gebruiken.  

De volgende lijst bevat de configuratie van de (A) SCS-en ERS-load balancer voor dit multi-SID-cluster voorbeeld met drie SAP-systemen. Voor elk van de Sid's hebt u afzonderlijke frontend-IP-, status controles en taakverdelings regels nodig voor elk ASCS-en ERS-exemplaar. Wijs alle virtuele machines, die deel uitmaken van het ASCS/ASCS-cluster, toe aan één back-end-pool.  

### <a name="ascs"></a>(A)SCS

* Front-end configuratie
  * IP-adres voor NW1:10.3.1.14
  * IP-adres voor NW2:10.3.1.16
  * IP-adres voor NW3:10.3.1.13
* Poorten testen
  * Poort 620 <strong> &lt; Nr &gt;</strong>, daarom voor NW1-, NW2-en NW3-test poorten 620 **00**, 620 **10** en 620 **20**
* Taakverdelings regels- 
* Maak een voor elk exemplaar, dat wil zeggen, NW1/ASCS, NW2/ASCS en NW3/ASCS.
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
  * IP-adres voor NW1 10.3.1.15
  * IP-adres voor NW2 10.3.1.17
  * IP-adres voor NW3 10.3.1.19
* Test poort
  * Poort 621 <strong> &lt; Nr &gt;</strong>, daarom voor NW1, NW2 en N # test poorten 621 **02**, 621 **12** en 621 **22**
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
> Zwevend IP wordt niet ondersteund voor een secundaire IP-configuratie in een NIC in scenario's voor taak verdeling. Zie beperkingen voor [Azure Load Balancer](../../../load-balancer/load-balancer-multivip-overview.md#limitations)voor meer informatie. Als u een extra IP-adres voor de virtuele machine nodig hebt, implementeert u een tweede NIC.  

> [!Note]
> Wanneer Vm's zonder open bare IP-adressen in de back-endadresgroep van intern (geen openbaar IP-adres load balancer) worden geplaatst, is er geen uitgaande Internet verbinding, tenzij er aanvullende configuratie wordt uitgevoerd om route ring naar open bare eind punten toe te staan. Zie [connectiviteit van open bare eind punten voor virtual machines met behulp van Azure Standard Load Balancer in scenario's met hoge Beschik baarheid voor SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md)voor meer informatie over het bezorgen van uitgaande verbindingen.  

> [!IMPORTANT]
> Schakel TCP-tijds tempels niet in op virtuele Azure-machines die achter Azure Load Balancer worden geplaatst. Door TCP-tijds tempels in te scha kelen, mislukken de status controles. Stel para meter **net.IPv4.tcp_timestamps** in op **0**. Zie [Load Balancer Health probe](../../../load-balancer/load-balancer-custom-probe-overview.md)(Engelstalig) voor meer informatie.

## <a name="sap-nfs-shares"></a>SAP NFS-shares

Voor SAP NetWeaver is gedeelde opslag vereist voor het Trans Port, de profielmap, enzovoort. Voor Maxi maal beschik bare SAP-systemen is het belang rijk dat u beschikt over Maxi maal beschik bare NFS-shares. U moet beslissen over de architectuur voor uw SAP NFS-shares. Een van de opties is het bouwen van een [Maxi maal beschik bare NFS-cluster op virtuele Azure-machines op SuSE Linux Enterprise Server][nfs-ha], dat kan worden gedeeld tussen meerdere SAP-systemen. 

Een andere optie is het implementeren van de shares op [Azure NETAPP files NFS-volumes](../../../azure-netapp-files/azure-netapp-files-create-volumes.md).  Met Azure NetApp Files krijgt u ingebouwde hoge Beschik baarheid voor de SAP NFS-shares.

## <a name="deploy-the-first-sap-system-in-the-cluster"></a>Het eerste SAP-systeem in het cluster implementeren

Nu u hebt besloten over de architectuur van de SAP NFS-shares, implementeert u het eerste SAP-systeem in het cluster en volgt u de bijbehorende documentatie.

* Als u een Maxi maal beschik bare NFS-server gebruikt, volgt u [hoge Beschik baarheid voor SAP NetWeaver op Azure-vm's op SuSE Linux Enterprise Server voor SAP-toepassingen](./high-availability-guide-suse.md).  
* Als u Azure NetApp Files NFS-volumes gebruikt, volgt u [hoge Beschik baarheid voor SAP NetWeaver op Azure-vm's op SuSE Linux Enterprise Server met Azure NetApp files voor SAP-toepassingen](./high-availability-guide-suse-netapp-files.md)

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
* De functionaliteit van het cluster failover is getest.  
* De NFS-shares voor alle SAP-systemen worden geïmplementeerd.  

### <a name="prepare-for-sap-netweaver-installation"></a>Voorbereiden op SAP NetWeaver-installatie

1. Voeg configuratie toe voor het zojuist geïmplementeerde systeem ( **NW2**, **NW3**) aan de bestaande Azure Load Balancer, gevolgd door de instructies om [Azure Load Balancer hand matig te implementeren via Azure Portal](./high-availability-guide-suse-netapp-files.md#deploy-azure-load-balancer-manually-via-azure-portal). Pas de IP-adressen, de status test poorten en de taakverdelings regels voor uw configuratie aan.  

2. **[A]** naam omzetting instellen voor de extra SAP-systemen. U kunt de DNS-server gebruiken of wijzigen `/etc/hosts` op alle knoop punten. In dit voor beeld ziet u hoe u het `/etc/hosts` bestand gebruikt.  Pas de IP-adressen en de hostnamen aan uw omgeving aan. 

    ```
    sudo vi /etc/hosts
    # IP address of the load balancer frontend configuration for NW2 ASCS
    10.3.1.16 msnw2ascs
    # IP address of the load balancer frontend configuration for NW3 ASCS
    10.3.1.13 msnw3ascs
    # IP address of the load balancer frontend configuration for NW2 ERS
    10.3.1.17 msnw2ers
    # IP address of the load balancer frontend configuration for NW3 ERS
    10.3.1.19 msnw3ers
    # IP address for virtual host name for the NFS server for NW2
    10.3.1.31 nw2-nfs
    # IP address for virtual host name for the NFS server for NW3
    10.3.1.32 nw3-nfs
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

4. **[A]** configureren `autofs` om de/Sapmnt/sid-en/usr/sap/sid/sys-bestands systemen te koppelen voor de extra SAP-systemen die u op het cluster implementeert. In dit voor beeld **NW2** en **NW3**.  

   Update bestand `/etc/auto.direct` met de bestands systemen voor de extra SAP-systemen die u naar het cluster implementeert.  

   * Als u NFS-Bestands server gebruikt, volgt u de instructies [hier](./high-availability-guide-suse.md#prepare-for-sap-netweaver-installation)
   * Als u Azure NetApp Files gebruikt, volgt u de instructies [hier](./high-availability-guide-suse-netapp-files.md#prepare-for-sap-netweaver-installation) 

   U moet de service opnieuw starten `autofs` om de zojuist toegevoegde shares te koppelen.  

### <a name="install-ascs--ers"></a>ASCS/ERS installeren

1. Maak de virtuele IP-en Health probe-cluster resources voor het ASCS-exemplaar van het extra SAP-systeem dat u naar het cluster implementeert. Het voor beeld dat hier wordt weer gegeven, is voor **NW2** en **NW3** ASCS met behulp van Maxi maal beschik bare NFS-server.  

   > [!IMPORTANT]
   > Recente tests hebben getoonde situaties, waarbij netcat niet meer reageert op aanvragen als gevolg van achterstand en de beperking van het verwerken van slechts één verbinding. De netcat-resource stopt met Luis teren naar de Azure Load Balancer-aanvragen en het zwevende IP-adres is niet meer beschikbaar.  
   > Voor bestaande pacemaker-clusters wordt geadviseerd in de laatste Vervang netcat met socat. Momenteel raden we u aan Azure-lb resource agent te gebruiken, die deel uitmaakt van pakket resource-agents, met de volgende pakket versie vereisten:
   > - Voor SLES 12 SP4/SP5 moet de versie ten minste resource-agents-4.3.018. a7fb5035-3.30.1 zijn.  
   > - Voor SLES 15/15 SP1 moet de versie ten minste resource-agents-4.3.0184.6 ee15eb2-4.13.1 zijn.  
   >
   > Houd er rekening mee dat voor de wijziging korte uitval tijd nodig is.  
   > Voor bestaande pacemaker-clusters geldt dat als de configuratie al is gewijzigd in het gebruik van socat, zoals beschreven in [Azure Load-Balancer-detectie beveiliging](https://www.suse.com/support/kb/doc/?id=7024128), u niet onmiddellijk over de resource-agent van Azure lb hoeft te scha kelen.

    ```
      sudo crm configure primitive fs_NW2_ASCS Filesystem device='nw2-nfs:/NW2/ASCS' directory='/usr/sap/NW2/ASCS10' fstype='nfs4' \
       op start timeout=60s interval=0 \
       op stop timeout=60s interval=0 \
       op monitor interval=20s timeout=40s
   
      sudo crm configure primitive vip_NW2_ASCS IPaddr2 \
        params ip=10.3.1.16 cidr_netmask=24 \
        op monitor interval=10 timeout=20
   
      sudo crm configure primitive nc_NW2_ASCS azure-lb port=62010
   
      sudo crm configure group g-NW2_ASCS fs_NW2_ASCS nc_NW2_ASCS vip_NW2_ASCS \
         meta resource-stickiness=3000

      sudo crm configure primitive fs_NW3_ASCS Filesystem device='nw3-nfs:/NW3/ASCS' directory='/usr/sap/NW3/ASCS20' fstype='nfs4' \
        op start timeout=60s interval=0 \
        op stop timeout=60s interval=0 \
        op monitor interval=20s timeout=40s
   
      sudo crm configure primitive vip_NW3_ASCS IPaddr2 \
       params ip=10.3.1.13 cidr_netmask=24 \
       op monitor interval=10 timeout=20
   
      sudo crm configure primitive nc_NW3_ASCS azure-lb port=62020
   
      sudo crm configure group g-NW3_ASCS fs_NW3_ASCS nc_NW3_ASCS vip_NW3_ASCS \
        meta resource-stickiness=3000
    ```

   Bij het maken van de resources kunnen ze worden toegewezen aan verschillende cluster resources. Wanneer u deze groepeert, worden deze gemigreerd naar een van de cluster knooppunten. Zorg ervoor dat de cluster status OK is en dat alle resources worden gestart. Het is niet belang rijk op welk knoop punt de resources worden uitgevoerd.

2. **[1]** SAP NetWeaver ASCS installeren  

   Installeer SAP NetWeaver ASCS als root, met behulp van een virtuele hostnaam die wordt toegewezen aan het IP-adres van de load balancer front-end-configuratie voor de ASCS. Bijvoorbeeld: voor System **NW2** is de virtuele hostnaam <b>msnw2ascs</b>, <b>10.3.1.16</b> en het exemplaar nummer dat u hebt gebruikt voor de test van de Load Balancer, bijvoorbeeld <b>10</b>. voor System **NW3** is de virtuele hostnaam <b>msnw3ascs</b>, <b>10.3.1.13</b> en het exemplaar nummer dat u hebt gebruikt voor de test van de Load Balancer, bijvoorbeeld <b>20</b>.

   U kunt de para meter sapinst SAPINST_REMOTE_ACCESS_USER gebruiken om een niet-hoofd gebruiker verbinding te laten maken met sapinst. U kunt de para meter SAPINST_USE_HOSTNAME gebruiken om SAP te installeren met behulp van de naam van de virtuele host.  

     ```
      sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
     ```

   Als de installatie geen submap kan maken in/usr/sap/**sid**/ASCS **instance #**, probeert u de eigenaar in te stellen op **sid** adm en groep naar sapsys van het ASCS-**exemplaar #** en probeert u het opnieuw.

3. **[1]** Maak een virtuele IP-en status test cluster bronnen voor het ers-exemplaar van het extra SAP-systeem dat u naar het cluster implementeert. Het voor beeld dat hier wordt weer gegeven, is voor **NW2** en **NW3** ers met behulp van Maxi maal beschik bare NFS-server. 

   ```
    sudo crm configure primitive fs_NW2_ERS Filesystem device='nw2-nfs:/NW2/ASCSERS' directory='/usr/sap/NW2/ERS12' fstype='nfs4' \
      op start timeout=60s interval=0 \
      op stop timeout=60s interval=0 \
      op monitor interval=20s timeout=40s
   
    sudo crm configure primitive vip_NW2_ERS IPaddr2 \
      params ip=10.3.1.17 cidr_netmask=24 \
      op monitor interval=10 timeout=20
   
    sudo crm configure primitive nc_NW2_ERS azure-lb port=62112
   
    sudo crm configure group g-NW2_ERS fs_NW2_ERS nc_NW2_ERS vip_NW2_ERS

    sudo crm configure primitive fs_NW3_ERS Filesystem device='nw3-nfs:/NW3/ASCSERS' directory='/usr/sap/NW3/ERS22' fstype='nfs4' \
      op start timeout=60s interval=0 \
      op stop timeout=60s interval=0 \
      op monitor interval=20s timeout=40s
   
    sudo crm configure primitive vip_NW3_ERS IPaddr2 \
      params ip=10.3.1.19 cidr_netmask=24 \
      op monitor interval=10 timeout=20
   
    sudo crm configure primitive nc_NW3_ERS azure-lb port=62122
   
    sudo crm configure group g-NW3_ERS fs_NW3_ERS nc_NW3_ERS vip_NW3_ERS
   ```

   Bij het maken van de resources kunnen ze worden toegewezen aan verschillende cluster knooppunten. Wanneer u deze groepeert, worden deze gemigreerd naar een van de cluster knooppunten. Zorg ervoor dat de cluster status OK is en dat alle resources worden gestart.  

   Zorg er vervolgens voor dat de resources van de zojuist gemaakte ERS-groep worden uitgevoerd op het cluster knooppunt, tegengesteld aan het cluster knooppunt waar het ASCS-exemplaar voor hetzelfde SAP-systeem is geïnstalleerd.  Als bijvoorbeeld NW2 ASCS is geïnstalleerd op `slesmsscl1` , moet u ervoor zorgen dat de groep NW2 ers wordt uitgevoerd `slesmsscl2` .  U kunt de groep NW2 ERS migreren naar `slesmsscl2` door de volgende opdracht uit te voeren: 

    ```
      crm resource migrate g-NW2_ERS slesmsscl2 force
    ```

4. **[2]** SAP NetWeaver ers installeren

   Installeer SAP NetWeaver ERS als root op het andere knoop punt, met behulp van een virtuele hostnaam die wordt toegewezen aan het IP-adres van de load balancer front-end-configuratie voor de ERS. Bijvoorbeeld, de naam van de virtuele **NW2** is <b>msnw2ers</b>, <b>10.3.1.17</b> en het exemplaar nummer dat u hebt gebruikt voor de test van de Load Balancer, bijvoorbeeld <b>12</b>. Voor System **NW3**, de naam van de virtuele host <b>msnw3ers</b>, <b>10.3.1.19</b> en het exemplaar nummer dat u hebt gebruikt voor de test van de Load Balancer, bijvoorbeeld <b>22</b>. 

   U kunt de para meter sapinst SAPINST_REMOTE_ACCESS_USER gebruiken om een niet-hoofd gebruiker verbinding te laten maken met sapinst. U kunt de para meter SAPINST_USE_HOSTNAME gebruiken om SAP te installeren met behulp van de naam van de virtuele host.  

    ```
     sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   > [!NOTE]
   > Gebruik SWPM SP 20 PL of hoger. Bij lagere versies worden de machtigingen niet correct ingesteld en de installatie mislukt.

   Als de installatie geen submap kan maken in/usr/sap/**NW2**/ers **instance #**, probeert u de eigenaar in te stellen op **sid** adm en de groep naar sapsys van de ers **instance #** map en probeert u het opnieuw.

   Als het nodig is om de ERS-groep van het zojuist geïmplementeerde SAP-systeem naar een ander cluster knooppunt te migreren, vergeet dan niet om de locatie beperking voor de groep ERS te verwijderen. U kunt de beperking verwijderen door de volgende opdracht uit te voeren (het voor beeld wordt gegeven voor SAP Systems **NW2** en **NW3**).  

    ```
      crm resource unmigrate g-NW2_ERS
      crm resource unmigrate g-NW3_ERS
    ```

5. **[1]** de ASCS/SCS-en ers-instantie profielen aanpassen voor de ZOJUIST geïnstalleerde SAP-systemen. Het voor beeld dat hieronder wordt weer gegeven, is voor NW2. U moet de profielen ASCS/SCS en ERS aanpassen voor alle SAP-exemplaren die aan het cluster worden toegevoegd.  
 
 * ASCS/SCS-profiel

   ```
   sudo vi /sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
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
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   ```

6. **[A]** CONFIGUREER de SAP-gebruikers voor het zojuist geïmplementeerde SAP-systeem, in dit voor beeld **NW2** en **NW3**. 

   ```
   # Add sidadm to the haclient group
   sudo usermod -aG haclient nw2adm
   sudo usermod -aG haclient nw3adm
   ```

7. Voeg de ASCS-en ERS SAP-services voor het zojuist geïnstalleerde SAP-systeem toe aan het `sapservice` bestand. Het voor beeld hieronder wordt weer gegeven voor SAP-systemen **NW2** en **NW3**.  

   Voeg de ASCS-service vermelding toe aan het tweede knoop punt en kopieer de vermelding van de ERS-service naar het eerste knoop punt. Voer de opdrachten voor elk SAP-systeem uit op het knoop punt, waar het ASCS-exemplaar voor het SAP-systeem is geïnstalleerd.  

    ```
     # Execute the following commands on slesmsscl1,assuming the NW2 ASCS instance was installed on slesmsscl1
     cat /usr/sap/sapservices | grep ASCS10 | sudo ssh slesmsscl2 "cat >>/usr/sap/sapservices"
     sudo ssh slesmsscl2 "cat /usr/sap/sapservices" | grep ERS12 | sudo tee -a /usr/sap/sapservices
     # Execute the following commands on slesmsscl2, assuming the NW3 ASCS instance was installed on slesmsscl2
     cat /usr/sap/sapservices | grep ASCS20 | sudo ssh slesmsscl1 "cat >>/usr/sap/sapservices"
     sudo ssh slesmsscl1 "cat /usr/sap/sapservices" | grep ERS22 | sudo tee -a /usr/sap/sapservices
    ```

8. **[1]** de SAP-cluster resources maken voor het ZOJUIST geïnstalleerde SAP-systeem. 

   Als u Server 1-architectuur (ENSA1) in de wachtrij plaatst, definieert u de resources voor SAP Systems **NW2** en **NW3** als volgt:

    ```
     sudo crm configure property maintenance-mode="true"
    
     sudo crm configure primitive rsc_sap_NW2_ASCS10 SAPInstance \
      operations \$id=rsc_sap_NW2_ASCS10-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
    
     sudo crm configure primitive rsc_sap_NW2_ERS12 SAPInstance \
      operations \$id=rsc_sap_NW2_ERS12-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true \
      meta priority=1000
    
     sudo crm configure modgroup g-NW2_ASCS add rsc_sap_NW2_ASCS10
     sudo crm configure modgroup g-NW2_ERS add rsc_sap_NW2_ERS12
    
     sudo crm configure colocation col_sap_NW2_no_both -5000: g-NW2_ERS g-NW2_ASCS
     sudo crm configure location loc_sap_NW2_failover_to_ers rsc_sap_NW2_ASCS10 rule 2000: runs_ers_NW2 eq 1
     sudo crm configure order ord_sap_NW2_first_start_ascs Optional: rsc_sap_NW2_ASCS10:start rsc_sap_NW2_ERS12:stop symmetrical=false
   
     sudo crm configure primitive rsc_sap_NW3_ASCS20 SAPInstance \
      operations \$id=rsc_sap_NW3_ASCS20-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ASCS10_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
    
     sudo crm configure primitive rsc_sap_NW3_ERS22 SAPInstance \
      operations \$id=rsc_sap_NW3_ERS22-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW3_ERS22_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true \
      meta priority=1000
    
     sudo crm configure modgroup g-NW3_ASCS add rsc_sap_NW3_ASCS20
     sudo crm configure modgroup g-NW3_ERS add rsc_sap_NW3_ERS22
    
     sudo crm configure colocation col_sap_NW3_no_both -5000: g-NW3_ERS g-NW3_ASCS
     sudo crm configure location loc_sap_NW3_failover_to_ers rsc_sap_NW3_ASCS10 rule 2000: runs_ers_NW3 eq 1
     sudo crm configure order ord_sap_NW3_first_start_ascs Optional: rsc_sap_NW3_ASCS20:start rsc_sap_NW3_ERS22:stop symmetrical=false
     sudo crm configure property maintenance-mode="false"
    ```

   SAP heeft ondersteuning geïntroduceerd voor het plaatsen van Server 2, inclusief replicatie, vanaf SAP NW 7,52. Vanaf ABAP platform 1809 wordt Server 2 in de wachtrij standaard geïnstalleerd. Zie SAP Note [2630416](https://launchpad.support.sap.com/#/notes/2630416) voor de ondersteuning van Server 2 in de wachtrij.
   Als u Server 2-architectuur ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)) in de wachtrij plaatst, definieert u de resources voor SAP Systems **NW2** en **NW3** als volgt:

    ```
     sudo crm configure property maintenance-mode="true"
    
     sudo crm configure primitive rsc_sap_NW2_ASCS10 SAPInstance \
      operations \$id=rsc_sap_NW2_ASCS10-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000 
    
     sudo crm configure primitive rsc_sap_NW2_ERS12 SAPInstance \
      operations \$id=rsc_sap_NW2_ERS12-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true 
    
     sudo crm configure modgroup g-NW2_ASCS add rsc_sap_NW2_ASCS10
     sudo crm configure modgroup g-NW2_ERS add rsc_sap_NW2_ERS12
    
     sudo crm configure colocation col_sap_NW2_no_both -5000: g-NW2_ERS g-NW2_ASCS
     sudo crm configure order ord_sap_NW2_first_start_ascs Optional: rsc_sap_NW2_ASCS10:start rsc_sap_NW2_ERS12:stop symmetrical=false
   
     sudo crm configure primitive rsc_sap_NW3_ASCS20 SAPInstance \
      operations \$id=rsc_sap_NW3_ASCS20-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ASCS10_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000
    
     sudo crm configure primitive rsc_sap_NW3_ERS22 SAPInstance \
      operations \$id=rsc_sap_NW3_ERS22-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW3_ERS22_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true
    
     sudo crm configure modgroup g-NW3_ASCS add rsc_sap_NW3_ASCS20
     sudo crm configure modgroup g-NW3_ERS add rsc_sap_NW3_ERS22
    
     sudo crm configure colocation col_sap_NW3_no_both -5000: g-NW3_ERS g-NW3_ASCS
     sudo crm configure order ord_sap_NW3_first_start_ascs Optional: rsc_sap_NW3_ASCS20:start rsc_sap_NW3_ERS22:stop symmetrical=false
     sudo crm configure property maintenance-mode="false"
    ```

   Als u een upgrade uitvoert van een oudere versie en overschakelt naar Server 2 in wachtrij, raadpleegt u SAP Note [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   Zorg ervoor dat de cluster status OK is en dat alle resources worden gestart. Het is niet belang rijk op welk knoop punt de resources worden uitgevoerd.
   In het volgende voor beeld ziet u de status van de cluster bronnen nadat SAP-systemen **NW2** en **NW3** aan het cluster zijn toegevoegd. 

    ```
     sudo crm_mon -r
    
    # Online: [ slesmsscl1 slesmsscl2 ]
    
    #Full list of resources:
    
    #stonith-sbd     (stonith:external/sbd): Started slesmsscl1
    # Resource Group: g-NW1_ASCS
    #     fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
    #     nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
    #     vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
    #     rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
    # Resource Group: g-NW1_ERS
    #     fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
    #     nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
    #     vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
    #     rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    # Resource Group: g-NW2_ASCS
    #     fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
    #     nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
    #     vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
    #     rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    # Resource Group: g-NW2_ERS
    #     fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
    #     nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
    #     vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
    #     rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
    # Resource Group: g-NW3_ASCS
    #     fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
    #     nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
    #     vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
    #     rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    # Resource Group: g-NW3_ERS
    #     fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
    #     nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
    #     vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
    #     rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
    ```

   In de volgende afbeelding ziet u hoe de resources eruitzien in de HA Web konsole (Hawk), waarbij de resources voor SAP System **NW2** zijn uitgevouwen.  

   [![Overzicht van de hoge Beschik baarheid van SAP netweave](./media/high-availability-guide-suse/ha-suse-multi-sid-hawk.png)](./media/high-availability-guide-suse/ha-suse-multi-sid-hawk-detail.png#lightbox)

### <a name="proceed-with-the-sap-installation"></a>Door gaan met de SAP-installatie 

Voltooi uw SAP-installatie door:

* [Uw SAP NetWeaver-toepassings servers voorbereiden](./high-availability-guide-suse.md#2d6008b0-685d-426c-b59e-6cd281fd45d7)
* [Een DBMS-exemplaar installeren](./high-availability-guide-suse.md#install-database)
* [Een primaire SAP-toepassings server installeren](./high-availability-guide-suse.md#sap-netweaver-application-server-installation)
* Een of meer extra SAP-toepassings exemplaren installeren

## <a name="test-the-multi-sid-cluster-setup"></a>De multi-SID-cluster installatie testen

De volgende tests zijn een subset van de test cases in de best practices-gidsen van SUSE. Ze zijn opgenomen voor uw gemak. Raadpleeg de volgende documentatie voor een volledige lijst met cluster tests:

* Als u een Maxi maal beschik bare NFS-server gebruikt, volgt u [hoge Beschik baarheid voor SAP NetWeaver op Azure-vm's op SuSE Linux Enterprise Server voor SAP-toepassingen](./high-availability-guide-suse.md).  
* Als u Azure NetApp Files NFS-volumes gebruikt, volgt u [hoge Beschik baarheid voor SAP NetWeaver op Azure-vm's op SuSE Linux Enterprise Server met Azure NetApp files voor SAP-toepassingen](./high-availability-guide-suse-netapp-files.md)

Lees altijd de hand leidingen voor de best practices van SUSE en voer alle extra tests uit die mogelijk zijn toegevoegd.  
De tests die worden weer gegeven, bevinden zich in een twee knoop punt, multi-SID-cluster waarop drie SAP-systemen zijn geïnstalleerd.  

1. HAGetFailoverConfig en HACheckFailoverConfig testen

   Voer de volgende opdrachten uit als <sapsid> adm op het knoop punt waar het ASCS-exemplaar op dit moment wordt uitgevoerd. Als de opdrachten mislukken met een fout: onvoldoende geheugen, kan dit worden veroorzaakt door streepjes in uw hostnaam. Dit is een bekend probleem en wordt opgelost door SUSE in het pakket SAP-SuSE-cluster-connector.

   ```
    slesmsscl1:nw1adm 57> sapcontrol -nr 00 -function HAGetFailoverConfig

   # 10.12.2019 21:33:08
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4
   # HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4 (sap_suse_cluster_connector 3.1.0)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode: slesmsscl1
   # HANodes: slesmsscl1, slesmsscl2

    slesmsscl1:nw1adm 53> sapcontrol -nr 00 -function HACheckFailoverConfig

    # 19.12.2019 21:19:58
    # HACheckFailoverConfig
    # OK
    # state, category, description, comment
    # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch

    slesmsscl2:nw2adm 35> sapcontrol -nr 10 -function HAGetFailoverConfig

   # 10.12.2019 21:37:09
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4
   # HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4 (sap_suse_cluster_connector 3.1.0)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode: slesmsscl2
   # HANodes: slesmsscl2, slesmsscl1

    slesmsscl2:nw2adm 52> sapcontrol -nr 10 -function HACheckFailoverConfig

    # 19.12.2019 21:17:39
    # HACheckFailoverConfig
    # OK
    # state, category, description, comment
    # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch

    slesmsscl1:nw3adm 49> sapcontrol -nr 20 -function HAGetFailoverConfig

   # 10.12.2019 23:35:36
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4
   # HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4 (sap_suse_cluster_connector 3.1.0)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode: slesmsscl1
   # HANodes: slesmsscl1, slesmsscl2

    slesmsscl1:nw3adm 52> sapcontrol -nr 20 -function HACheckFailoverConfig

    # 19.12.2019 21:10:42
    # HACheckFailoverConfig
    # OK
    # state, category, description, comment
    # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   ```

2. Migreer het ASCS-exemplaar hand matig. In het voor beeld wordt het ASCS-exemplaar voor SAP System NW2 gemigreerd.  
   Resource status voordat u begint met testen:
   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

   Voer de volgende opdrachten uit als root om het NW2 ASCS-exemplaar te migreren.

   ```
    crm resource migrate rsc_sap_NW2_ASCS10 force
    # INFO: Move constraint created for rsc_sap_NW2_ASCS10
    
    crm resource unmigrate rsc_sap_NW2_ASCS10
   # INFO: Removed migration constraints for rsc_sap_NW2_ASCS10
   
   # Remove failed actions for the ERS that occurred as part of the migration
    crm resource cleanup rsc_sap_NW2_ERS12
   ```

   Resource status na de test:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

1. HAFailoverToNode testen. De test die hier wordt weer gegeven, toont het migreren van het ASCS-exemplaar voor SAP System NW2.  

   Resource status voordat u begint met testen:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

   Voer de volgende opdrachten uit als **NW2** adm voor het migreren van het NW2 ASCS-exemplaar.

   ```
    slesmsscl2:nw2adm 53> sapcontrol -nr 10 -host msnw2ascs -user nw2adm password -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   crm resource cleanup rsc_sap_NW2_ERS12
   # Remove migration constraints
   crm resource clear rsc_sap_NW2_ASCS10
   #INFO: Removed migration constraints for rsc_sap_NW2_ASCS10
   ```

   Resource status na de test:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

1. Knoop punt crash simuleren

   Resource status voordat u begint met testen:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

   Voer de volgende opdracht uit als root op het knoop punt waarop ten minste één ASCS-exemplaar wordt uitgevoerd. In dit voor beeld hebben we de opdracht uitgevoerd op `slesmsscl2` , waar de ASCS-instanties voor NW1 en NW3 worden uitgevoerd.  

   ```
    slesmsscl2:~ # echo b > /proc/sysrq-trigger
   ```

   Als u SBD gebruikt, mag pacemaker niet automatisch worden gestart op het gedode knoop punt. De status nadat het knoop punt opnieuw is gestart, moet er als volgt uitzien.

   ```
    Online: [ slesmsscl1 ]
    OFFLINE: [ slesmsscl2 ]
    Full list of resources:

    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    
    Failed Resource Actions:
    * rsc_sap_NW1_ERS02_monitor_11000 on slesmsscl1 'not running' (7): call=125, status=complete, exitreason='',
        last-rc-change='Fri Dec 13 19:32:10 2019', queued=0ms, exec=0ms
    * rsc_sap_NW2_ERS12_monitor_11000 on slesmsscl1 'not running' (7): call=126, status=complete, exitreason='',
        last-rc-change='Fri Dec 13 19:32:10 2019', queued=0ms, exec=0ms
    * rsc_sap_NW3_ERS22_monitor_11000 on slesmsscl1 'not running' (7): call=127, status=complete, exitreason='',
        last-rc-change='Fri Dec 13 19:32:10 2019', queued=0ms, exec=0ms
   ```

   Gebruik de volgende opdrachten om pacemaker op het verwerkte knoop punt te starten, de SBD-berichten op te schonen en de mislukte resources op te schonen.

   ```bash
   # run as root
   # list the SBD device(s)
   cat /etc/sysconfig/sbd | grep SBD_DEVICE=

   # output is like:
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message slesmsscl2 clear
   
   systemctl start pacemaker
   crm resource cleanup rsc_sap_NW1_ERS02
   crm resource cleanup rsc_sap_NW2_ERS12
   crm resource cleanup rsc_sap_NW3_ERS22
   ```

   Resource status na de test:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
   ```

## <a name="next-steps"></a>Volgende stappen

* [Azure Virtual Machines planning en implementatie voor SAP][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP][dbms-guide]
* Zie [hoge Beschik baarheid van SAP Hana op azure virtual machines (vm's)][sap-hana-ha] voor meer informatie over het opzetten van een hoge Beschik baarheid en het plannen van nood herstel van SAP Hana op Azure-vm's.