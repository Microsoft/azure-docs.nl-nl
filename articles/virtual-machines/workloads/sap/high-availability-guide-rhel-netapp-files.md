---
title: Azure-Vm's hoge Beschik baarheid voor SAP NW op RHEL met Azure NetApp Files | Microsoft Docs
description: Stel hoge Beschik baarheid in voor SAP NW op Azure virtual machines (Vm's) RHEL met Azure NetApp Files.
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
ms.date: 01/11/2021
ms.author: radeltch
ms.openlocfilehash: 8fa51bec1918b8dce99c80a61da0160c9650d5e4
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98116089"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux-with-azure-netapp-files-for-sap-applications"></a>Azure Virtual Machines hoge Beschik baarheid voor SAP NetWeaver op Red Hat Enterprise Linux met Azure NetApp Files voor SAP-toepassingen

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

In dit artikel wordt beschreven hoe u de virtuele machines implementeert, de virtuele machines configureert, het cluster raamwerk installeert en een Maxi maal beschik bare SAP NetWeaver 7,50-systeem installeert met behulp van [Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-introduction.md).
In de voorbeeld configuraties, installatie opdrachten, enzovoort. Het ASCS-exemplaar is getal 00, het ERS-exemplaar is nummer 01, de primaire toepassings instantie (PAS) is 02 en het toepassings exemplaar (AAS) is 03. SAP-systeem-ID QAS wordt gebruikt. 

De laag van de data base wordt in dit artikel niet uitvoerig besproken.  

Lees eerst de volgende SAP-opmerkingen en-documenten:

* [Documentatie over Azure NetApp Files][anf-azure-doc] 
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
* [SAP NetWeaver in pacemaker-cluster](https://access.redhat.com/articles/3150081)
* Algemene documentatie voor RHEL
  * [Overzicht van Add-On met hoge Beschik baarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Add-On beheer met hoge Beschik baarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referentie voor Add-On met hoge Beschik baarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [ASCS/ERS configureren voor SAP NetWeaver met zelfstandige bronnen in RHEL 7,5](https://access.redhat.com/articles/3569681)
  * [SAP S/4HANA ASCS/ERS met zelfstandige server 2 (ENSA2) configureren in pacemaker op RHEL ](https://access.redhat.com/articles/3974941)
* Documentatie voor Azure-specifieke RHEL:
  * [Ondersteunings beleid voor RHEL-clusters met hoge Beschik baarheid-Microsoft Azure Virtual Machines als cluster leden](https://access.redhat.com/articles/3131341)
  * [Installeren en configureren van een Red Hat Enterprise Linux 7,4 (en hoger) High-Availability cluster op Microsoft Azure](https://access.redhat.com/articles/3252491)
* [NetApp SAP-toepassingen op Microsoft Azure met behulp van Azure NetApp Files][anf-sap-applications-azure]

## <a name="overview"></a>Overzicht

Voor hoge Beschik baarheid (HA) voor de SAP NetWeaver Central-Services is gedeelde opslag vereist.
Om ervoor te zorgen dat de Red Hat Linux tot nu toe een afzonderlijke Maxi maal beschik bare GlusterFS-cluster moet bouwen. 

Het is nu mogelijk om SAP NetWeaver HA te maken met behulp van gedeelde opslag, geïmplementeerd op Azure NetApp Files. Als u Azure NetApp Files voor de gedeelde opslag gebruikt, hoeft u geen extra [glusterfs-cluster](./high-availability-guide-rhel-glusterfs.md)meer te gebruiken. Pacemaker is nog steeds nodig voor HA van de SAP NetWeaver Central-Services (ASCS/SCS).

![Overzicht van de hoge Beschik baarheid van SAP netweave](./media/high-availability-guide-rhel/high-availability-guide-rhel-anf.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS en de SAP HANA-Data Base gebruiken virtuele hostnamen en virtuele IP-adressen. Op Azure is een load balancer vereist voor het gebruik van een virtueel IP-adres. U kunt het beste [standaard Load Balancer](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md)gebruiken. De volgende lijst bevat de configuratie van de load balancer met afzonderlijke front-end Ip's voor (A) SCS en ERS.

### <a name="ascs"></a>(A)SCS

* Front-end configuratie
  * IP-adres 192.168.14.9
* Test poort
  * Poort 620<strong> &lt; Nr &gt; </strong>
* Taakverdelingsregels
  * Als u Standard Load Balancer gebruikt, selecteert u **ha-poorten**
  * 32<strong> &lt; Nr &gt; </strong> TCP
  * 36<strong> &lt; Nr &gt; </strong> TCP
  * 39<strong> &lt; Nr &gt; </strong> TCP
  * 81<strong> &lt; Nr &gt; </strong> TCP
  * 5<strong> &lt; Nr &gt; </strong>. 13 TCP
  * 5<strong> &lt; Nr &gt; </strong>. 14 TCP
  * 5<strong> &lt; Nr &gt; </strong>. 16 TCP

### <a name="ers"></a>ERS

* Front-end configuratie
  * IP-adres 192.168.14.10
* Test poort
  * Poort 621<strong> &lt; Nr &gt; </strong>
* Taakverdelingsregels
  * Als u Standard Load Balancer gebruikt, selecteert u **ha-poorten**
  * 32<strong> &lt; Nr &gt; </strong> TCP
  * 33<strong> &lt; Nr &gt; </strong> TCP
  * 5<strong> &lt; Nr &gt; </strong>. 13 TCP
  * 5<strong> &lt; Nr &gt; </strong>. 14 TCP
  * 5<strong> &lt; Nr &gt; </strong>. 16 TCP

* Back-end-configuratie
  * Verbonden met primaire netwerk interfaces van alle virtuele machines die deel moeten uitmaken van het (A) SCS/ERS-cluster

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>De Azure NetApp Files-infra structuur instellen 

Voor SAP NetWeaver is gedeelde opslag vereist voor de map Trans Port en het profiel.  Voordat u doorgaat met de installatie van de Azure NetApp-infra structuur, moet u vertrouwd raken met de [Azure NetApp files-documentatie][anf-azure-doc]. Controleer of de geselecteerde Azure-regio Azure NetApp Files bevat. De volgende koppeling toont de beschik baarheid van Azure NetApp Files per Azure-regio: [Azure NetApp files Beschik baarheid per Azure-regio][anf-avail-matrix].

Azure NetApp-bestanden zijn beschikbaar in verschillende [Azure-regio's](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Voordat u Azure NetApp Files implementeert, moet u onboarding aanvragen bij Azure NetApp Files, volgens de [instructies registreren voor Azure NetApp files][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Azure NetApp Files-resources implementeren  

Bij de stappen wordt ervan uitgegaan dat u [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md)al hebt geïmplementeerd. De Azure NetApp Files resources en de virtuele machines, waarbij de Azure NetApp Files resources worden gekoppeld, moeten worden geïmplementeerd in dezelfde Azure-Virtual Network of in gepeerde Azure Virtual Networks.  

1. Als u dat nog niet hebt gedaan, vraagt u [om onboarding naar Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-register.md).  
2. Maak het NetApp-account in de geselecteerde Azure-regio en volg de [instructies voor het maken](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)van een NetApp-account.  
3. Stel Azure NetApp Files capaciteits groep in en volg de [instructies voor het instellen van Azure NetApp files capaciteits groep](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md).  
De SAP NetWeaver-architectuur die in dit artikel wordt gepresenteerd, maakt gebruik van een enkele Azure NetApp Files capaciteits groep, een Premium-SKU. U kunt het beste Azure NetApp Files Premium SKU voor de werk belasting van de SAP net-Weaver op Azure.  
4. Delegeer een subnet naar Azure NetApp-bestanden zoals beschreven in de [instructies een subnet overdragen aan Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).  

5. Implementeer Azure NetApp Files volumes, gevolgd door de instructies voor het [maken van een volume voor Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md). Implementeer de volumes in het aangewezen Azure NetApp Files [subnet](/rest/api/virtualnetwork/subnets). De IP-adressen van de Azure NetApp-volumes worden automatisch toegewezen. Houd er rekening mee dat de Azure NetApp Files resources en de virtuele Azure-machines zich in dezelfde Azure-Virtual Network moeten bevinden of in een Peerd Azure Virtual Network. In dit voor beeld gebruiken we twee Azure NetApp Files volumes: SAP<b>QAS</b> en transSAP. De bestands paden die zijn gekoppeld aan de bijbehorende koppel punten, zijn/usrsap<b>QAS</b>/sapmnt<b>QAS</b>,/usrsap<b>QAS</b>/usrsap<b>QAS</b>sys, enzovoort.  

   1. volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/sapmnt<b>QAS</b>)
   2. volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>ascs)
   3. volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>sys)
   4. volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>ers)
   5. volume transSAP (nfs://192.168.24.4/transSAP)
   6. volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>pas)
   7. volume SAP<b>QAS</b> (NFS://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>aas)
  
In dit voor beeld hebben we Azure NetApp Files voor alle SAP NetWeaver-bestands systemen gebruikt om te laten zien hoe Azure NetApp Files kunnen worden gebruikt. SAP-bestands systemen die niet via NFS moeten worden gekoppeld, kunnen ook worden geïmplementeerd als [Azure-schijf opslag](../../disks-types.md#premium-ssd) . In dit voor beeld moet <b>a-e</b> zich bevindt op Azure NetApp files en <b>f-g</b> (dat wil zeggen,/usr/sap/<b>QAS</b>/d<b>02</b>,/usr/sap/<b>QAS</b>/d<b>03</b>) kan worden geïmplementeerd als Azure-schijf opslag. 

### <a name="important-considerations"></a>Belangrijke overwegingen

Houd rekening met de volgende belang rijke overwegingen bij het overwegen van Azure NetApp Files voor de SAP net-Weaver op SUSE-architectuur met hoge Beschik baarheid:

- De minimale capaciteits pool is 4 TiB. De grootte van de capaciteits groep kan worden verhoogd in 1 TiB-stappen.
- Het minimale volume is 100 GiB
- Azure NetApp Files en alle virtuele machines, waarbij Azure NetApp Files volumes worden gekoppeld, moeten zich in dezelfde Azure-Virtual Network of in gekoppelde [virtuele netwerken](../../../virtual-network/virtual-network-peering-overview.md) in dezelfde regio bevinden. Azure NetApp Files toegang via VNET-peering in dezelfde regio wordt nu ondersteund. Toegang tot Azure NetApp via wereld wijde peering wordt nog niet ondersteund.
- Het geselecteerde virtuele netwerk moet een subnet hebben, gedelegeerd aan Azure NetApp Files.
- Azure NetApp Files biedt [export beleid](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md): u kunt de toegestane clients, het toegangs type (lezen&schrijven, alleen-lezen, enz.) beheren. 
- Er is nog geen zone bewust van Azure NetApp Files-functie. Momenteel wordt Azure NetApp Files-functie niet geïmplementeerd in alle beschikbaarheids zones in een Azure-regio. Houd rekening met de mogelijke latentie implicaties in sommige Azure-regio's. 
- Azure NetApp Files volumes kunnen worden geïmplementeerd als NFSv3-of NFSv 4.1-volumes. Beide protocollen worden ondersteund voor de SAP Application Layer (ASCS/ERS, SAP-toepassings servers). 

## <a name="setting-up-ascs"></a>Instellen van (A) SCS

In dit voor beeld zijn de resources hand matig geïmplementeerd via de [Azure Portal](https://portal.azure.com/#home).

### <a name="deploy-linux-manually-via-azure-portal"></a>Linux hand matig implementeren via Azure Portal

Eerst moet u de Azure NetApp Files volumes maken. Implementeer de Vm's. Daarna maakt u een load balancer en gebruikt u de virtuele machines in de back-end-pool.

1. Load balancer maken (intern, standaard):  
   1. De frontend-IP-adressen maken
      1. IP-adres 192.168.14.9 voor de ASCS
         1. Open de load balancer, selecteer de frontend-IP-adres groep en klik op toevoegen
         1. Voer de naam van de nieuwe front-end-IP-adres groep in (bijvoorbeeld front- **End. QAS. ASCS**)
         1. Stel de toewijzing in op statisch en voer het IP-adres in (bijvoorbeeld **192.168.14.9**)
         1. Klik op OK
      1. IP-adres 192.168.14.10 voor de ASCS ERS
         * Herhaal de bovenstaande stappen onder a om een IP-adres voor de ERS te maken (bijvoorbeeld **192.168.14.10** en front- **End. QAS. ERS**)
   1. Back-endpool maken
      1. Open de load balancer, selecteer back-endservers en klik op toevoegen
      1. Voer de naam van de nieuwe back-end-groep in (bijvoorbeeld **back-end. QAS**)
      1. Klik op een virtuele machine toevoegen.
      1. Virtuele machine selecteren. 
      1. Selecteer de virtuele machines van het (A) SCS-cluster en de bijbehorende IP-adressen.
      1. Klik op Add.
   1. De status tests maken
      1. Poort 620 **00** voor ASCS
         1. Open de load balancer, selecteer status controles en klik op toevoegen
         1. Voer de naam van de nieuwe status test in (bijvoorbeeld **status. QAS. ASCS**)
         1. TCP als protocol selecteren, poort 620 **00**, interval 5 en drempel waarde voor onjuiste status 2 gebruiken
         1. Klik op OK
      1. Poort 621 **01** voor ASCS ers
            * Herhaal de bovenstaande stappen onder ' c ' om een status test te maken voor de ERS (bijvoorbeeld 621 **01** en **status. QAS. ERS**)
   1. Taakverdelingsregels
      1. Taakverdelings regels voor ASCS
         1. Open de load balancer, selecteer regels voor taak verdeling en klik op toevoegen.
         1. Voer de naam in van de nieuwe load balancer regel (bijvoorbeeld **lb. QAS. ASCS**)
         1. Selecteer het frontend-IP-adres voor ASCS, back-endservers en de status test die u eerder hebt gemaakt (bijvoorbeeld front- **End. QAS. ASCS**, **back-end. QAS** en **status. QAS. ASCS**)
         1. **Ha-poorten** selecteren
         1. Time-out voor inactiviteit tot 30 minuten verhogen
         1. **Zorg ervoor dat zwevend IP-adressen zijn ingeschakeld**
         1. Klik op OK
         * Herhaal de bovenstaande stappen om taakverdelings regels voor ERS te maken (bijvoorbeeld **lb. QAS. ERS**)
1. U kunt ook de volgende stappen uitvoeren als voor uw scenario Basic-load balancer (intern) vereist is:  
   1. De frontend-IP-adressen maken
      1. IP-adres 192.168.14.9 voor de ASCS
         1. Open de load balancer, selecteer de frontend-IP-adres groep en klik op toevoegen
         1. Voer de naam van de nieuwe front-end-IP-adres groep in (bijvoorbeeld front- **End. QAS. ASCS**)
         1. Stel de toewijzing in op statisch en voer het IP-adres in (bijvoorbeeld **192.168.14.9**)
         1. Klik op OK
      1. IP-adres 192.168.14.10 voor de ASCS ERS
         * Herhaal de bovenstaande stappen onder a om een IP-adres voor de ERS te maken (bijvoorbeeld **192.168.14.10** en front- **End. QAS. ERS**)
   1. Back-endpool maken
      1. Open de load balancer, selecteer back-endservers en klik op toevoegen
      1. Voer de naam van de nieuwe back-end-groep in (bijvoorbeeld **back-end. QAS**)
      1. Klik op een virtuele machine toevoegen.
      1. Selecteer de Beschikbaarheidsset die u eerder hebt gemaakt voor ASCS 
      1. De virtuele machines van het (A) SCS-cluster selecteren
      1. Klik op OK
   1. De status tests maken
      1. Poort 620 **00** voor ASCS
         1. Open de load balancer, selecteer status controles en klik op toevoegen
         1. Voer de naam van de nieuwe status test in (bijvoorbeeld **status. QAS. ASCS**)
         1. TCP als protocol selecteren, poort 620 **00**, interval 5 en drempel waarde voor onjuiste status 2 gebruiken
         1. Klik op OK
      1. Poort 621 **01** voor ASCS ers
            * Herhaal de bovenstaande stappen onder ' c ' om een status test te maken voor de ERS (bijvoorbeeld 621 **01** en **status. QAS. ERS**)
   1. Taakverdelingsregels
      1. 32 **00** TCP voor ASCS
         1. Open de load balancer, selecteer regels voor taak verdeling en klik op toevoegen.
         1. Voer de naam in van de nieuwe load balancer regel (bijvoorbeeld **lb. QAS. ASCS. 3200**)
         1. Selecteer het frontend-IP-adres voor ASCS, back-endservers en de status test die u eerder hebt gemaakt (bijvoorbeeld front- **End. QAS. ASCS**)
         1. Behoud protocol **TCP**, voer poort **3200** in
         1. Time-out voor inactiviteit tot 30 minuten verhogen
         1. **Zorg ervoor dat zwevend IP-adressen zijn ingeschakeld**
         1. Klik op OK
      1. Aanvullende poorten voor de ASCS
         * Herhaal de bovenstaande stappen onder d voor poorten 36 **00**, 39 **00**, 81 **00**, 5 **00** 13, 5 **00** 14, 5 **00** 16 en TCP voor de ASCS
      1. Aanvullende poorten voor de ASCS ERS
         * Herhaal de bovenstaande stappen onder d voor de poorten 32 **01**, 33 **01**, 5 **01** 13, 5 **01** 14, 5 **01** en TCP voor de ASCS ers

      > [!IMPORTANT]
      > Zwevend IP wordt niet ondersteund voor een secundaire IP-configuratie in een NIC in scenario's voor taak verdeling. Zie beperkingen voor [Azure Load Balancer](../../../load-balancer/load-balancer-multivip-overview.md#limitations)voor meer informatie. Als u een extra IP-adres voor de virtuele machine nodig hebt, implementeert u een tweede NIC.  

      > [!Note]
      > Wanneer Vm's zonder open bare IP-adressen in de back-endadresgroep van intern (geen openbaar IP-adres load balancer) worden geplaatst, is er geen uitgaande Internet verbinding, tenzij er aanvullende configuratie wordt uitgevoerd om route ring naar open bare eind punten toe te staan. Zie [connectiviteit van open bare eind punten voor virtual machines met behulp van Azure Standard Load Balancer in scenario's met hoge Beschik baarheid voor SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md)voor meer informatie over het bezorgen van uitgaande verbindingen.  

      > [!IMPORTANT]
      > Schakel TCP-tijds tempels niet in op virtuele Azure-machines die achter Azure Load Balancer worden geplaatst. Door TCP-tijds tempels in te scha kelen, mislukken de status controles. Stel para meter **net.IPv4.tcp_timestamps** in op **0**. Zie [Load Balancer Health probe](../../../load-balancer/load-balancer-custom-probe-overview.md)(Engelstalig) voor meer informatie.

## <a name="disable-id-mapping-if-using-nfsv41"></a>ID-toewijzing uitschakelen (als u NFSv 4.1 gebruikt)

De instructies in deze sectie zijn alleen van toepassing op het gebruik van Azure NetApp Files volumes met NFSv 4.1-protocol. Voer de configuratie uit op alle Vm's, waarbij Azure NetApp Files NFSv 4.1-volumes worden gekoppeld.  

1. Controleer de instelling van het NFS-domein. Zorg ervoor dat het domein is geconfigureerd als standaard Azure NetApp Files domein, dat wil zeggen **`defaultv4iddomain.com`** en dat de toewijzing is ingesteld op **niemand**.  

    > [!IMPORTANT]
    > Zorg ervoor dat u het NFS-domein in `/etc/idmapd.conf` op de VM instelt op de standaard domein configuratie op Azure NetApp files: **`defaultv4iddomain.com`** . Als er een verschil is tussen de domein configuratie op de NFS-client (de virtuele machine) en de NFS-server, dat wil zeggen de Azure NetApp-configuratie, worden de machtigingen voor bestanden op Azure NetApp-volumes die zijn gekoppeld op de Vm's weer gegeven als `nobody` .  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** controleren `nfs4_disable_idmapping` . Deze moet worden ingesteld op **Y**. Als u de mapstructuur wilt maken waar `nfs4_disable_idmapping` zich bevindt, voert u de koppeling-opdracht uit. U kunt de map niet hand matig maken onder/sys/modules, omdat de toegang is gereserveerd voor de kernel/Stuur Programma's.  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 192.168.24.5:/sap<b>QAS</b>
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

   Zie voor meer informatie over het wijzigen van `nfs4_disable_idmapping` para meters https://access.redhat.com/solutions/1749883 .

### <a name="create-pacemaker-cluster"></a>Een pacemaker-cluster maken

Volg de stappen bij het [instellen van pacemaker op Red Hat Enterprise Linux in azure](high-availability-guide-rhel-pacemaker.md) voor het maken van een basis pacemaker-cluster voor deze (a) SCS-server.

### <a name="prepare-for-sap-netweaver-installation"></a>Voorbereiden op SAP NetWeaver-installatie

De volgende items worden voorafgegaan door **[A]** , van toepassing op alle knoop punten, **[1]** -alleen van toepassing op knoop punt 1 of **[2]** -alleen van toepassing op knoop punt 2.

1. **[A]** omzetting van hostnaam van installatie

   U kunt een DNS-server gebruiken of de bestand/etc/hosts wijzigen op alle knoop punten. In dit voor beeld ziet u hoe u het bestand/etc/hosts-bestand gebruikt.
   Vervang het IP-adres en de hostnaam in de volgende opdrachten:

    ```
    sudo vi /etc/hosts
    ```

   Voeg de volgende regels toe aan/etc/hosts. Wijzig het IP-adres en de hostnaam zodat deze overeenkomen met uw omgeving

    ```
    # IP address of cluster node 1
    192.168.14.5    anftstsapcl1
    # IP address of cluster node 2
    192.168.14.6     anftstsapcl2
    # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
    192.168.14.9    anftstsapvh
    # IP address of the load balancer frontend configuration for SAP Netweaver ERS
    192.168.14.10    anftstsapers
    ```

1. **[1]** SAP-mappen maken in het Azure NetApp files-volume.  
   Koppel tijdelijk het Azure NetApp Files volume op een van de virtuele machines en maak de SAP-directory's (bestands paden).  

    ```
     # mount temporarily the volume
     sudo mkdir -p /saptmp
     # If using NFSv3
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 192.168.24.5:/sapQAS /saptmp
     # If using NFSv4.1
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys,tcp 192.168.24.5:/sapQAS /saptmp
     # create the SAP directories
     sudo cd /saptmp
     sudo mkdir -p sapmntQAS
     sudo mkdir -p usrsapQASascs
     sudo mkdir -p usrsapQASers
     sudo mkdir -p usrsapQASsys
     sudo mkdir -p usrsapQASpas
     sudo mkdir -p usrsapQASaas
     # unmount the volume and delete the temporary directory
     sudo cd ..
     sudo umount /saptmp
     sudo rmdir /saptmp
    ``` 

1. **[A]** de gedeelde mappen maken

   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/QAS/SYS
   sudo mkdir -p /usr/sap/QAS/ASCS00
   sudo mkdir -p /usr/sap/QAS/ERS01
   
   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/QAS/SYS
   sudo chattr +i /usr/sap/QAS/ASCS00
   sudo chattr +i /usr/sap/QAS/ERS01
   ```

1. **[A]** NFS-client en andere vereisten installeren

   ```
   sudo yum -y install nfs-utils resource-agents resource-agents-sap
   ```

1. **[A] Controleer de** versie van de resource-agents-SAP

   Zorg ervoor dat de versie van de geïnstalleerde resource-agents SAP-pakket ten minste 3.9.5 -124. EL7
   ```
   sudo yum info resource-agents-sap
   
   # Loaded plugins: langpacks, product-id, search-disabled-repos
   # Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
   # Installed Packages
   # Name        : resource-agents-sap
   # Arch        : x86_64
   # Version     : 3.9.5
   # Release     : 124.el7
   # Size        : 100 k
   # Repo        : installed
   # From repo   : rhel-sap-for-rhel-7-server-rpms
   # Summary     : SAP cluster resource agents and connector script
   # URL         : https://github.com/ClusterLabs/resource-agents
   # License     : GPLv2+
   # Description : The SAP resource agents and connector script interface with
   #          : Pacemaker to allow SAP instances to be managed in a cluster
   #          : environment.
   ```


1. **[A]** koppelings vermeldingen toevoegen

   Als u NFSv3 gebruikt:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   Als u NFSv 4.1 gebruikt:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   > [!NOTE]
   > Zorg ervoor dat u voldoet aan de NFS-protocol versie van de Azure NetApp Files volumes bij het koppelen van de volumes. Als de Azure NetApp Files volumes worden gemaakt als NFSv3-volumes, gebruikt u de bijbehorende NFSv3-configuratie. Als de Azure NetApp Files volumes worden gemaakt als NFSv 4.1-volumes, volgt u de instructies om ID-toewijzing uit te scha kelen en ervoor te zorgen dat u de overeenkomstige configuratie van NFSv 4.1 gebruikt. In dit voor beeld zijn de Azure NetApp Files volumes gemaakt als NFSv3-volumes.  

   De nieuwe shares koppelen

   ```
   sudo mount -a  
   ```

1. **[A]** wissel bestand configureren

   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   De agent opnieuw starten om de wijziging te activeren

   ```
   sudo service waagent restart
   ```

1. **[A]** RHEL-configuratie

   RHEL configureren zoals beschreven in SAP Note [2002167]

### <a name="installing-sap-netweaver-ascsers"></a>SAP NetWeaver ASCS/ERS installeren

1. **[1]** Maak een virtuele IP-bron en een status test voor het ASCS-exemplaar

   ```
   sudo pcs node standby anftstsapcl2
   # If using NFSv3
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' force_unmount=safe \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-QAS_ASCS
   
   # If using NFSv4.1
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-QAS_ASCS
   
   sudo pcs resource create vip_QAS_ASCS IPaddr2 \
     ip=192.168.14.9 cidr_netmask=24 \
     --group g-QAS_ASCS
   
   sudo pcs resource create nc_QAS_ASCS azure-lb port=62000 \
     --group g-QAS_ASCS
   ```

   Zorg ervoor dat de cluster status OK is en dat alle resources worden gestart. Het is niet belang rijk op welk knoop punt de resources worden uitgevoerd.

   ```
   sudo pcs status
   
   # Node anftstsapcl2: standby
   # Online: [ anftstsapcl1 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
   ```

1. **[1]** SAP NetWeaver ASCS installeren  

   Installeer SAP NetWeaver ASCS als root op het eerste knoop punt met behulp van een virtuele hostnaam die verwijst naar het IP-adres van de load balancer frontend-configuratie voor de ASCS, bijvoorbeeld <b>anftstsapvh</b>, <b>192.168.14.9</b> en het exemplaar nummer dat u hebt gebruikt voor de test van de Load Balancer, bijvoorbeeld <b>00</b>.

   U kunt de para meter sapinst SAPINST_REMOTE_ACCESS_USER gebruiken om een niet-hoofd gebruiker verbinding te laten maken met sapinst.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   Als de installatie geen submap kan maken in/usr/sap/**QAS**/ASCS **00**, probeert u de eigenaar en groep van de ASCS **00** -map in te stellen en het opnieuw te proberen.

   ```
   sudo chown qasadm /usr/sap/QAS/ASCS00
   sudo chgrp sapsys /usr/sap/QAS/ASCS00
   ```

1. **[1]** Maak een virtuele IP-bron en een status test voor het ers-exemplaar

   ```
   sudo pcs node unstandby anftstsapcl2
   sudo pcs node standby anftstsapcl1
   
   # If using NFSv3
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' force_unmount=safe \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-QAS_AERS
   
   # If using NFSv4.1
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
     op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-QAS_AERS
   
   sudo pcs resource create vip_QAS_AERS IPaddr2 \
     ip=192.168.14.10 cidr_netmask=24 \
    --group g-QAS_AERS
   
   sudo pcs resource create nc_QAS_AERS azure-lb port=62101 \
    --group g-QAS_AERS
   ```
 
   Zorg ervoor dat de cluster status OK is en dat alle resources worden gestart. Het is niet belang rijk op welk knoop punt de resources worden uitgevoerd.

   ```
   sudo pcs status
   
   # Node anftstsapcl1: standby
   # Online: [ anftstsapcl2 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2<
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   #  Resource Group: g-QAS_AERS
   #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
   #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   ```

1. **[2]** SAP NetWeaver ers installeren  

   Installeer SAP NetWeaver ERS als root op het tweede knoop punt met behulp van een virtuele hostnaam die verwijst naar het IP-adres van de load balancer frontend-configuratie voor de ERS, bijvoorbeeld <b>anftstsapers</b>, <b>192.168.14.10</b> en het exemplaar nummer dat u hebt gebruikt voor de test van de Load Balancer, bijvoorbeeld <b>01</b>.

   U kunt de para meter sapinst SAPINST_REMOTE_ACCESS_USER gebruiken om een niet-hoofd gebruiker verbinding te laten maken met sapinst.

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   Als de installatie geen submap kan maken in/usr/sap/**QAS**/ers **01**, probeert u de eigenaar en groep van de map ers **01** in te stellen en het opnieuw te proberen.

   ```
   sudo chown qaadm /usr/sap/QAS/ERS01
   sudo chgrp sapsys /usr/sap/QAS/ERS01
   ```

1. **[1]** de ASCS/SCS-en ers-instantie profielen aanpassen

   * ASCS/SCS-profiel

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the keep alive parameter, if using ENSA1
   enque/encni/set_so_keepalive = true
   ```

   Voor zowel ENSA1 als ENSA2, moet u ervoor zorgen dat de `keepalive` OS-para meters zijn ingesteld zoals beschreven in SAP note [1410736](https://launchpad.support.sap.com/#/notes/1410736).  

   * ERS-profiel

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ERS01_anftstsapers
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   ```


1. **[A]** Keep Alive configureren

   De communicatie tussen de SAP NetWeaver-toepassings server en de ASCS/SCS wordt doorgestuurd via een software load balancer. De load balancer verbreekt inactieve verbindingen na een Configureer bare time-out. Als u dit wilt voor komen, moet u een para meter instellen in het SAP NetWeaver ASCS/SCS-profiel, als u ENSA1 gebruikt en de Linux `keepalive` -systeem instellingen op alle SAP-servers voor zowel ENSA1/ENSA2 wijzigen. Lees [SAP-opmerking 1410736][1410736] voor meer informatie.

   ```
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=300
   ```

1. **[A]** het/usr/sap/sapservices-bestand bijwerken

   Om het starten van de instanties door het opstart script van sapinit te voor komen, moeten alle instanties die worden beheerd door pacemaker, worden omgeleid vanuit het/usr/sap/sapservices-bestand. Voer geen commentaar uit op het SAP HANA-exemplaar als het wordt gebruikt met HANA SR.

   ```
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ASCS00/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ASCS00/exe/sapstartsrv pf=/usr/sap/QAS/SYS/profile/QAS_ASCS00_anftstsapvh -D -u qasadm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ERS01/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ERS01/exe/sapstartsrv pf=/usr/sap/QAS/ERS01/profile/QAS_ERS01_anftstsapers -D -u qasadm
   ```

1. **[1]** de SAP-cluster resources maken

   Als u Server 1-architectuur (in plaats van ENSA1) gebruikt, definieert u de resources als volgt:

   ```
   sudo pcs property set maintenance-mode=true
   
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint location rsc_sap_QAS_ASCS00 rule score=2000 runs_ers_QAS eq 1
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   SAP heeft ondersteuning geïntroduceerd voor het plaatsen van Server 2, inclusief replicatie, vanaf SAP NW 7,52. Vanaf ABAP platform 1809 wordt Server 2 in de wachtrij standaard geïnstalleerd. Zie SAP Note [2630416](https://launchpad.support.sap.com/#/notes/2630416) voor de ondersteuning van Server 2 in de wachtrij.
   Als u Server 2-architectuur ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)) gebruikt, installeert u resource agent resource-agents-SAP-4.1.1-12.EL7.x86_64 of nieuwer en definieert u de resources als volgt:

    ```
    sudo pcs property set maintenance-mode=true
    
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-QAS_ASCS then stop g-QAS_AERS symmetrical=false
   
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   Als u een upgrade uitvoert van een oudere versie en overschakelt naar Server 2 in wachtrij, raadpleegt u SAP Note [2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   > [!NOTE]
   > De time-outs in de bovenstaande configuratie zijn alleen voor beelden en moeten mogelijk worden aangepast aan de specifieke SAP-installatie. 

   Zorg ervoor dat de cluster status OK is en dat alle resources worden gestart. Het is niet belang rijk op welk knoop punt de resources worden uitgevoerd.

    ```
    sudo pcs status
    
    # Online: [ anftstsapcl1 anftstsapcl2 ]
    #
    # Full list of resources:
    #
    # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
    #  Resource Group: g-QAS_ASCS
    #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
    #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
    #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
    #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    #  Resource Group: g-QAS_AERS
    #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
    #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
    #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
    #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. **[A]** Voeg firewall regels voor ASCS en ers op beide knoop punten toe om de firewall regels voor ASCS en ers op beide knoop punten toe te voegen.
   ```
   # Probe Port of ASCS
   sudo firewall-cmd --zone=public --add-port=62000/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62000/tcp
   sudo firewall-cmd --zone=public --add-port=3200/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3200/tcp
   sudo firewall-cmd --zone=public --add-port=3600/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3600/tcp
   sudo firewall-cmd --zone=public --add-port=3900/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3900/tcp
   sudo firewall-cmd --zone=public --add-port=8100/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=8100/tcp
   sudo firewall-cmd --zone=public --add-port=50013/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50013/tcp
   sudo firewall-cmd --zone=public --add-port=50014/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50014/tcp
   sudo firewall-cmd --zone=public --add-port=50016/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50016/tcp
   # Probe Port of ERS
   sudo firewall-cmd --zone=public --add-port=62101/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62101/tcp
   sudo firewall-cmd --zone=public --add-port=3201/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3201/tcp
   sudo firewall-cmd --zone=public --add-port=3301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3301/tcp
   sudo firewall-cmd --zone=public --add-port=50113/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50113/tcp
   sudo firewall-cmd --zone=public --add-port=50114/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50114/tcp
   sudo firewall-cmd --zone=public --add-port=50116/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50116/tcp
   ```

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Voor bereiding van SAP NetWeaver-toepassings server

   Voor sommige data bases moet de installatie van het data base-exemplaar worden uitgevoerd op een toepassings server. Bereid de virtuele machines van de toepassings server voor om ze in deze gevallen te kunnen gebruiken.  

   In de stappen onderstaande wordt ervan uitgegaan dat u de toepassings server installeert op een andere server dan de ASCS/SCS-en HANA-servers. Anders zijn enkele van de onderstaande stappen (zoals het configureren van de omzetting van hostnamen) niet nodig.  

   De volgende items worden voorafgegaan door **[A]** , van toepassing op zowel de voor-en aas, **[P]** -alleen van toepassing op pas of **[S]** -alleen van toepassing op aas.  

1. **[A]** voor het instellen van de naam omzetting van de host kunt u een DNS-server gebruiken of de bestand/etc/hosts op alle knoop punten wijzigen. In dit voor beeld ziet u hoe u het bestand/etc/hosts-bestand gebruikt.
   Vervang het IP-adres en de hostnaam in de volgende opdrachten:  

   ```
   sudo vi /etc/hosts
   ```

   Voeg de volgende regels toe aan/etc/hosts. Wijzig het IP-adres en de hostnaam zodat deze overeenkomen met uw omgeving.

   ```
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   192.168.14.9 anftstsapvh
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   192.168.14.10 anftstsapers
   192.168.14.7 anftstsapa01
   192.168.14.8 anftstsapa02
   ```

1. **[A]** Maak de sapmnt-Directory maken de sapmnt-map.
   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   ```

1. **[A]** NFS-client en andere vereisten installeren  

   ```
   sudo yum -y install nfs-utils uuidd
   ```

1. **[A]** koppelings vermeldingen toevoegen  
   Als u NFSv3 gebruikt:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   Als u NFSv 4.1 gebruikt:
   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   ```

   De nieuwe shares koppelen

   ```
   sudo mount -a
   ```

1. **[P]** de pas-map maken en koppelen  
   Als u NFSv3 gebruikt:
   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

   Als u NFSv 4.1 gebruikt:
   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
   # Mount
   sudo mount -a
   ```

1. **[S]** de aas-map maken en koppelen  
   Als u NFSv3 gebruikt:
   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

   Als u NFSv 4.1 gebruikt:
   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys
   
   # Mount
   sudo mount -a
   ```

1. **[A]** wissel bestand configureren
 
   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   De agent opnieuw starten om de wijziging te activeren

   ```
   sudo service waagent restart
   ```

## <a name="install-database"></a>Database installeren

In dit voor beeld is SAP NetWeaver geïnstalleerd op SAP HANA. U kunt elke ondersteunde Data Base voor deze installatie gebruiken. Zie voor meer informatie over het installeren van SAP HANA in azure [hoge Beschik baarheid van SAP Hana op virtuele Azure-machines in Red Hat Enterprise Linux][sap-hana-ha] . For a list of supported databases, see [SAP Note 1928533][1928533] .

1. De installatie van het SAP-data base-exemplaar uitvoeren

   Installeer het SAP NetWeaver-data base-exemplaar als root met behulp van een virtuele hostnaam die wordt toegewezen aan het IP-adres van de load balancer front-end-configuratie voor de data base.

   U kunt de para meter sapinst SAPINST_REMOTE_ACCESS_USER gebruiken om een niet-hoofd gebruiker verbinding te laten maken met sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

## <a name="sap-netweaver-application-server-installation"></a>Installatie van de toepassings server van SAP net-Weaver

Volg deze stappen om een SAP-toepassings server te installeren.

1. Toepassings server voorbereiden

   Volg de stappen in het hoofd stuk [SAP NetWeaver Application Server](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) -voor bereiding hierboven om de toepassings server voor te bereiden.

1. De SAP NetWeaver-toepassings server installeren

   Installeer een primaire of extra SAP NetWeaver-toepassings server.

   U kunt de para meter sapinst SAPINST_REMOTE_ACCESS_USER gebruiken om een niet-hoofd gebruiker verbinding te laten maken met sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

1. SAP HANA Secure Store bijwerken

   Werk het beveiligde archief van SAP HANA bij zodat dit verwijst naar de virtuele naam van de installatie van de SAP HANA systeem replicatie.

   Voer de volgende opdracht uit om de vermeldingen weer te geven als \<sapsid> adm

   ```
   hdbuserstore List
   ```

   Hiermee wordt een lijst weer gegeven met alle vermeldingen en deze moeten er ongeveer als volgt uitzien
   ```
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 192.168.14.4:30313
     USER: SAPABAP1
     DATABASE: QAS
   ```

   In de uitvoer ziet u dat het IP-adres van de standaard vermelding verwijst naar de virtuele machine en niet naar het IP-adres van de load balancer. Dit item moet worden gewijzigd om te verwijzen naar de virtuele hostnaam van de load balancer. Zorg ervoor dat u dezelfde poort (**30313** in de bovenstaande uitvoer) en de naam van de data base (**QAS** in de bovenstaande uitvoer) gebruikt.

   ```
   su - qasadm
   hdbuserstore SET DEFAULT qasdb:30313@QAS SAPABAP1 <password of ABAP schema>
   ```

## <a name="test-the-cluster-setup"></a>De Cluster installatie testen

1. Het ASCS-exemplaar hand matig migreren

   Resource status voordat u begint met testen:

   ```
    rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Voer de volgende opdrachten uit als root om het ASCS-exemplaar te migreren.

   ```
   [root@anftstsapcl1 ~]# pcs resource move rsc_sap_QAS_ASCS00
   
   [root@anftstsapcl1 ~]# pcs resource clear rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resource status na de test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. Knoop punt crash simuleren

   Resource status voordat u begint met testen:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   Voer de volgende opdracht uit als root op het knoop punt waar het ASCS-exemplaar wordt uitgevoerd

   ```
   [root@anftstsapcl2 ~]# echo b > /proc/sysrq-trigger
   ```

   De status nadat het knoop punt opnieuw is gestart, moet er als volgt uitzien.

   ```
   Online: [ anftstsapcl1 anftstsapcl2 ]
   
   Full list of resources:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   
   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=45, status=complete, exitreason='',
   ```

   Gebruik de volgende opdracht om de mislukte resources op te schonen.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resource status na de test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Bericht Server proces beëindigen

   Resource status voordat u begint met testen:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```
   
   Voer de volgende opdrachten uit als root om het proces van de berichten server te identificeren en af te breken.

   ```
   [root@anftstsapcl1 ~]# pgrep -f ms.sapQAS | xargs kill -9
   ```

   Als u de berichten server alleen eenmaal beëindigt, wordt deze opnieuw opgestart door `sapstart` . Als u deze regel matig beëindigt, wordt de ASCS-instantie uiteindelijk door pacemaker naar het andere knoop punt verplaatst. Voer de volgende opdrachten uit als root om de resource status van het ASCS-en ERS-exemplaar na de test op te schonen.

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resource status na de test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. Server proces voor het plaatsen van de wachtrij beëindigen

   Resource status voordat u begint met testen:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   Voer de volgende opdrachten uit als root op het knoop punt waar het ASCS-exemplaar wordt uitgevoerd om de bewerkings server af te breken.

   ```
   #If using ENSA1
   [root@anftstsapcl2 ~]# pgrep -f en.sapQAS | xargs kill -9
   #If using ENSA2
   [root@anftstsapcl2 ~]# pgrep -f enq.sapQAS | xargs kill -9
   ```

   Het ASCS-exemplaar moet direct een failover uitvoeren naar het andere knoop punt. Na het starten van het ASCS-exemplaar moet ook een failover worden uitgevoerd voor het ERS-exemplaar. Voer de volgende opdrachten uit als root om de resource status van het ASCS-en ERS-exemplaar na de test op te schonen.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resource status na de test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Replicatie Server proces voor het plaatsen van de wachtrij beëindigen

   Resource status voordat u begint met testen:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Voer de volgende opdracht uit als hoofd server op het knoop punt waar het ERS-exemplaar wordt uitgevoerd om het proces voor het repliceren van de wachtrij te beëindigen.

   ```
   #If using ENSA1
   [root@anftstsapcl2 ~]# pgrep -f er.sapQAS | xargs kill -9
   #If using ENSA2
   [root@anftstsapcl2 ~]# pgrep -f enqr.sapQAS | xargs kill -9
   ```

   Als u de opdracht slechts eenmaal uitvoert, `sapstart` wordt het proces opnieuw gestart. Als u dit vaak genoeg uitvoert, `sapstart` wordt het proces niet opnieuw gestart en wordt de bron gestopt. Voer de volgende opdrachten uit als root om de resource status van het ERS-exemplaar na de test op te schonen.

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   Resource status na de test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. Sapstartsrv-proces voor het plaatsen van de wachtrij beëindigen

   Resource status voordat u begint met testen:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   Voer de volgende opdrachten uit als root op het knoop punt waar de ASCS wordt uitgevoerd.

   ```
   [root@anftstsapcl1 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@anftstsapcl1 ~]# kill -9 59545
   ```

   Het sapstartsrv-proces moet altijd opnieuw worden gestart door de bron agent van pacemaker als onderdeel van de bewaking. Resource status na de test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

## <a name="next-steps"></a>Volgende stappen

* [HA voor SAP NW op Azure Vm's op RHEL voor SAP-toepassingen multi-SID-hand leiding](./high-availability-guide-rhel-multi-sid.md)
* [Azure Virtual Machines planning en implementatie voor SAP][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP][dbms-guide]
* Zie [SAP Hana (grote instanties) hoge Beschik baarheid en herstel na nood gevallen op Azure](hana-overview-high-availability-disaster-recovery.md)voor meer informatie over het tot stand brengen van een hoge Beschik baarheid en het plannen van nood herstel van SAP Hana op Azure (grote exemplaren).
* Zie [hoge Beschik baarheid van SAP Hana op azure virtual machines (vm's)][sap-hana-ha] voor meer informatie over het opzetten van een hoge Beschik baarheid en het plannen van nood herstel van SAP Hana op Azure-vm's.