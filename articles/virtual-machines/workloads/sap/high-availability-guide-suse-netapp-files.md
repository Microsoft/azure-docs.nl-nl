---
title: Azure-Vm's hoge Beschik baarheid voor SAP NW op SLES met Azure NetApp Files | Microsoft Docs
description: Hand leiding voor hoge Beschik baarheid voor SAP NetWeaver op SUSE Linux Enterprise Server met Azure NetApp Files voor SAP-toepassingen
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
ms.date: 10/22/2020
ms.author: radeltch
ms.openlocfilehash: b8f2054296c5b4e567699924305b7f3385f30835
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96486212"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-with-azure-netapp-files-for-sap-applications"></a>Hoge Beschik baarheid voor SAP NetWeaver op Azure Vm's op SUSE Linux Enterprise Server met Azure NetApp Files voor SAP-toepassingen

[dbms-guide]:dbms_guide_general.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
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

In dit artikel wordt beschreven hoe u de virtuele machines implementeert, de virtuele machines configureert, het cluster raamwerk installeert en een Maxi maal beschik bare SAP NetWeaver 7,50-systeem installeert met behulp van [Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-introduction.md).
In de voorbeeld configuraties, installatie opdrachten, enzovoort, is het ASCS-exemplaar nummer 00, het ERS-exemplaar nummer 01, de primaire toepassings instantie (PAS) 02 en het toepassings exemplaar (AAS) 03. SAP-systeem-ID QAS wordt gebruikt. 

In dit artikel wordt uitgelegd hoe u hoge Beschik baarheid kunt beleggen voor SAP NetWeaver-toepassing met Azure NetApp Files. De laag van de data base wordt in dit artikel niet uitvoerig besproken.

Lees eerst de volgende SAP-opmerkingen en-documenten:

* [Documentatie over Azure NetApp Files][anf-azure-doc] 
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
* SAP Community-WIKI] ( https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) heeft alle vereiste SAP-notities voor Linux.
* [Azure Virtual Machines planning en implementatie voor SAP op Linux][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP op Linux][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP op Linux][dbms-guide]
* [Best Practice-hand leidingen voor SuSE SAP ha][suse-ha-guide] De gidsen bevatten alle vereiste informatie voor het instellen van netweave HA en SAP HANA systeem replicatie on-premises. Gebruik deze hand leidingen als algemene basis lijn. Ze bieden veel meer gedetailleerde informatie.
* [Release opmerkingen voor de SUSE-extensie voor hoge Beschik baarheid van 12 SP3][suse-ha-12sp3-relnotes]
* [NetApp SAP-toepassingen op Microsoft Azure met behulp van Azure NetApp Files][anf-sap-applications-azure]

## <a name="overview"></a>Overzicht

Voor hoge Beschik baarheid (HA) voor de SAP NetWeaver Central-Services is gedeelde opslag vereist.
Om ervoor te zorgen dat de SUSE Linux tot dusver een afzonderlijke Maxi maal beschik bare NFS-cluster moet bouwen. 

Het is nu mogelijk om SAP NetWeaver HA te maken met behulp van gedeelde opslag, geïmplementeerd op Azure NetApp Files. Als u Azure NetApp Files voor de gedeelde opslag gebruikt, hoeft u geen extra [NFS-cluster](./high-availability-guide-suse-nfs.md)meer te gebruiken. Pacemaker is nog steeds nodig voor HA van de SAP NetWeaver Central-Services (ASCS/SCS).


![Overzicht van de hoge Beschik baarheid van SAP netweave](./media/high-availability-guide-suse-anf/high-availability-guide-suse-anf.png)

SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS en de SAP HANA-Data Base gebruiken virtuele hostnamen en virtuele IP-adressen. Op Azure is een [Load Balancer](../../../load-balancer/load-balancer-overview.md) vereist voor het gebruik van een virtueel IP-adres. U kunt het beste [standaard Load Balancer](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md)gebruiken. De volgende lijst bevat de configuratie van de (A) SCS-en ERS-load balancer.

### <a name="ascs"></a>(A)SCS

* Front-end configuratie
  * IP-adres 10.1.1.20
* Test poort
  * Poort 620<strong> &lt; Nr &gt; </strong>
* Taakverdelings regels
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
  * IP-adres 10.1.1.21
* Test poort
  * Poort 621<strong> &lt; Nr &gt; </strong>
* Taakverdelings regels
  * Als u Standard Load Balancer gebruikt, selecteert u **ha-poorten**
  * Als u basis Load Balancer gebruikt, maakt u regels voor taak verdeling voor de volgende poorten
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

5. Implementeer Azure NetApp Files volumes, gevolgd door de instructies voor het [maken van een volume voor Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md). Implementeer de volumes in het aangewezen Azure NetApp Files [subnet](/rest/api/virtualnetwork/subnets). De IP-adressen van de Azure NetApp-volumes worden automatisch toegewezen. Houd er rekening mee dat de Azure NetApp Files resources en de virtuele Azure-machines zich in dezelfde Azure-Virtual Network moeten bevinden of in een Peerd Azure Virtual Network. In dit voor beeld gebruiken we twee Azure NetApp Files volumes: SAP<b>QAS</b> and trans. De bestands paden die zijn gekoppeld aan de bijbehorende koppel punten, zijn/usrsap<b>QAS</b>/sapmnt<b>QAS</b>,/usrsap<b>QAS</b>/usrsap<b>QAS</b>sys, enzovoort.  

   1. volume SAP<b>QAS</b> (NFS://10.1.0.4/usrsap<b>QAS</b>/sapmnt<b>QAS</b>)
   2. volume SAP<b>QAS</b> (NFS://10.1.0.4/usrsap<b>QAS</b>/usrsap<b>QAS</b>ascs)
   3. volume SAP<b>QAS</b> (NFS://10.1.0.4/usrsap<b>QAS</b>/usrsap<b>QAS</b>sys)
   4. volume SAP<b>QAS</b> (NFS://10.1.0.4/usrsap<b>QAS</b>/usrsap<b>QAS</b>ers)
   5. volume trans (nfs://10.1.0.4/trans)
   6. volume SAP<b>QAS</b> (NFS://10.1.0.4/usrsap<b>QAS</b>/usrsap<b>QAS</b>pas)
   7. volume SAP<b>QAS</b> (NFS://10.1.0.4/usrsap<b>QAS</b>/usrsap<b>QAS</b>aas)

   
In dit voor beeld hebben we Azure NetApp Files voor alle SAP NetWeaver-bestands systemen gebruikt om te laten zien hoe Azure NetApp Files kunnen worden gebruikt. SAP-bestands systemen die niet via NFS moeten worden gekoppeld, kunnen ook worden geïmplementeerd als [Azure-schijf opslag](../../disks-types.md#premium-ssd) . In dit voor beeld moet <b>a-e</b> zich bevindt op Azure NetApp files en <b>f-g</b> (dat wil zeggen,/usr/sap/<b>QAS</b>/d<b>02</b>,/usr/sap/<b>QAS</b>/d<b>03</b>) kan worden geïmplementeerd als Azure-schijf opslag. 

### <a name="important-considerations"></a>Belangrijke overwegingen

Houd rekening met de volgende belang rijke overwegingen bij het overwegen van Azure NetApp Files voor de SAP net-Weaver op SUSE-architectuur met hoge Beschik baarheid:

- De minimale capaciteits pool is 4 TiB. De grootte van de capaciteits groep kan worden verhoogd met 1 TiB-stappen.
- Het minimale volume is 100 GiB
- Azure NetApp Files en alle virtuele machines, waarbij Azure NetApp Files volumes worden gekoppeld, moeten zich in dezelfde Azure-Virtual Network of in gekoppelde [virtuele netwerken](../../../virtual-network/virtual-network-peering-overview.md) in dezelfde regio bevinden. Azure NetApp Files toegang via VNET-peering in dezelfde regio wordt nu ondersteund. Toegang tot Azure NetApp via wereld wijde peering wordt nog niet ondersteund.
- Het geselecteerde virtuele netwerk moet een subnet hebben, gedelegeerd aan Azure NetApp Files.
- Azure NetApp Files biedt [export beleid](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md): u kunt de toegestane clients, het toegangs type (lezen&schrijven, alleen-lezen, enz.) beheren. 
- Er is nog geen zone bewust van Azure NetApp Files-functie. Momenteel wordt Azure NetApp Files-functie niet geïmplementeerd in alle beschikbaarheids zones in een Azure-regio. Houd rekening met de mogelijke latentie implicaties in sommige Azure-regio's. 
- Azure NetApp Files volumes kunnen worden geïmplementeerd als NFSv3-of NFSv 4.1-volumes. Beide protocollen worden ondersteund voor de SAP Application Layer (ASCS/ERS, SAP-toepassings servers). 

## <a name="deploy-linux-vms-manually-via-azure-portal"></a>Linux-Vm's hand matig implementeren via Azure Portal

Eerst moet u de Azure NetApp Files volumes maken. Implementeer de Vm's. Daarna maakt u een load balancer en gebruikt u de virtuele machines in de back-endservers.

1. Een resourcegroep maken
1. Een Virtual Network maken
1. Een Beschikbaarheidsset maken voor ASCS  
   Maximum aantal update domeinen instellen
1. Virtuele machine 1 maken  
   Gebruik ten minste SLES4SAP 12 SP3, in dit voor beeld wordt de installatie kopie van SLES4SAP 12 SP3 gebruikt  
   Beschikbaarheidsset selecteren die eerder is gemaakt voor ASCS  
1. Virtuele machine 2 maken  
   Gebruik ten minste SLES4SAP 12 SP3, in dit voor beeld wordt de installatie kopie van SLES4SAP 12 SP3 gebruikt  
   Beschikbaarheidsset selecteren die eerder is gemaakt voor ASCS  
1. Een Beschikbaarheidsset maken voor de SAP-toepassings exemplaren (PAS, AAS)    
   Maximum aantal update domeinen instellen
1. Virtuele machine 3 maken  
   Gebruik ten minste SLES4SAP 12 SP3, in dit voor beeld wordt de installatie kopie van SLES4SAP 12 SP3 gebruikt  
   Beschikbaarheidsset selecteren die eerder is gemaakt voor PAS-AAS   
1. Virtuele machine 4 maken  
   Gebruik ten minste SLES4SAP 12 SP3, in dit voor beeld wordt de installatie kopie van SLES4SAP 12 SP3 gebruikt  
   Beschikbaarheidsset selecteren die eerder is gemaakt voor PAS-AAS  

## <a name="disable-id-mapping-if-using-nfsv41"></a>ID-toewijzing uitschakelen (als u NFSv 4.1 gebruikt)

De instructies in deze sectie zijn alleen van toepassing op het gebruik van Azure NetApp Files volumes met NFSv 4.1-protocol. Voer de configuratie uit op alle Vm's, waarbij Azure NetApp Files NFSv 4.1-volumes worden gekoppeld.  

1. Controleer de instelling van het NFS-domein. Zorg ervoor dat het domein is geconfigureerd als standaard Azure NetApp Files domein, dat wil zeggen **`defaultv4iddomain.com`** en dat de toewijzing is ingesteld op **niemand**.  

    > [!IMPORTANT]
    > Zorg ervoor dat u het NFS-domein in `/etc/idmapd.conf` op de VM instelt op de standaard domein configuratie op Azure NetApp files: **`defaultv4iddomain.com`** . Als er een verschil is tussen de domein configuratie op de NFS-client (de virtuele machine) en de NFS-server, dat wil zeggen de Azure NetApp-configuratie, worden de machtigingen voor bestanden op Azure NetApp-volumes die zijn gekoppeld op de Vm's weer gegeven als `nobody` .  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Verbosity = 0
    Pipefs-Directory = /var/lib/nfs/rpc_pipefs
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
    mount 10.1.0.4:/sapmnt/<b>qas</b> /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>


## <a name="setting-up-ascs"></a>Instellen van (A) SCS

In dit voor beeld zijn de resources hand matig geïmplementeerd via de [Azure Portal](https://portal.azure.com/#home) .

### <a name="deploy-azure-load-balancer-manually-via-azure-portal"></a>Azure Load Balancer hand matig implementeren via Azure Portal

Eerst moet u de Azure NetApp Files volumes maken. Implementeer de Vm's. Daarna maakt u een load balancer en gebruikt u de virtuele machines in de back-end-pool.

1. Load balancer maken (intern, standaard):  
   1. De frontend-IP-adressen maken
      1. IP-adres 10.1.1.20 voor de ASCS
         1. Open de load balancer, selecteer de frontend-IP-adres groep en klik op toevoegen
         1. Voer de naam van de nieuwe front-end-IP-adres groep in (bijvoorbeeld front- **End. QAS. ASCS**)
         1. Stel de toewijzing in op statisch en voer het IP-adres in (bijvoorbeeld **10.1.1.20**)
         1. Klik op OK
      1. IP-adres 10.1.1.21 voor de ASCS ERS
         * Herhaal de bovenstaande stappen onder a om een IP-adres voor de ERS te maken (bijvoorbeeld **10.1.1.21** en front- **End. QAS. ERS**)
   1. Back-endpool maken
      1. Open de load balancer, selecteer back-endservers en klik op toevoegen
      1. Voer de naam van de nieuwe back-end-groep in (bijvoorbeeld **back-end. QAS**)
      1. Klik op een virtuele machine toevoegen.
      1. Virtuele machine selecteren
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
   1. Taakverdelings regels
      1. Een back-end-pool maken voor de ASCS
         1. Open de load balancer, selecteer taakverdelings regels en klik op toevoegen
         1. Voer de naam in van de nieuwe load balancer regel (bijvoorbeeld **lb. QAS. ASCS**)
         1. Selecteer het frontend-IP-adres voor ASCS, back-endservers en de status test die u eerder hebt gemaakt (bijvoorbeeld front- **End. QAS. ASCS**, **back-end. QAS** en **status. QAS. ASCS**)
         1. **Ha-poorten** selecteren
         1. Time-out voor inactiviteit tot 30 minuten verhogen
         1. **Zorg ervoor dat zwevend IP-adressen zijn ingeschakeld**
         1. Klik op OK
         * Herhaal de bovenstaande stappen om taakverdelings regels voor ERS te maken (bijvoorbeeld **lb. QAS. ERS**)
1. U kunt ook de volgende stappen uitvoeren als voor uw scenario Basic-load balancer (intern) vereist is:  
   1. De frontend-IP-adressen maken
      1. IP-adres 10.1.1.20 voor de ASCS
         1. Open de load balancer, selecteer de frontend-IP-adres groep en klik op toevoegen
         1. Voer de naam van de nieuwe front-end-IP-adres groep in (bijvoorbeeld front- **End. QAS. ASCS**)
         1. Stel de toewijzing in op statisch en voer het IP-adres in (bijvoorbeeld **10.1.1.20**)
         1. Klik op OK
      1. IP-adres 10.1.1.21 voor de ASCS ERS
         * Herhaal de bovenstaande stappen onder a om een IP-adres voor de ERS te maken (bijvoorbeeld **10.1.1.21** en front- **End. QAS. ERS**)
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
   1. Taakverdelings regels
      1. 32 **00** TCP voor ASCS
         1. Open de load balancer, selecteer taakverdelings regels en klik op toevoegen
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

### <a name="create-pacemaker-cluster"></a>Een pacemaker-cluster maken

Volg de stappen bij het [instellen van pacemaker op SuSE Linux Enterprise Server in azure](high-availability-guide-suse-pacemaker.md) voor het maken van een basis pacemaker-cluster voor deze (a) SCS-server.

### <a name="installation"></a>Installatie

De volgende items worden voorafgegaan door **[A]** , van toepassing op alle knoop punten, **[1]** -alleen van toepassing op knoop punt 1 of **[2]** -alleen van toepassing op knoop punt 2.

1. **[A]** SuSE-connector installeren

   <pre><code>sudo zypper install sap-suse-cluster-connector
   </code></pre>

   > [!NOTE]
   > Het bekende probleem met het gebruik van een streepje in hostnamen is opgelost met versie **3.1.1** van het pakket **SAP-SuSE-cluster-connector**. Zorg ervoor dat u ten minste versie 3.1.1 van package SAP-SuSE-cluster-connector gebruikt, als cluster knooppunten worden gebruikt met een streepje in de hostnaam. Als dat niet het geval is, werkt het cluster niet. 

   Zorg ervoor dat u de nieuwe versie van de SAP SUSE-cluster connector hebt geïnstalleerd. Het oude werd sap_suse_cluster_connector genoemd en de nieuwe heet **SAP-SuSE-cluster-connector**.

   <pre><code>sudo zypper info sap-suse-cluster-connector
   
    # Information for package sap-suse-cluster-connector:
    # ---------------------------------------------------
    # Repository     : SLE-12-SP3-SAP-Updates
    # Name           : sap-suse-cluster-connector
    # Version        : 3.1.0-8.1
    # Arch           : noarch
    # Vendor         : SUSE LLC &lt;https://www.suse.com/&gt;
    # Support Level  : Level 3
    # Installed Size : 45.6 KiB
    # Installed      : Yes
    # Status         : up-to-date
    # Source package : sap-suse-cluster-connector-3.1.0-8.1.src
    # Summary        : SUSE High Availability Setup for SAP Products
   </code></pre>

2. **[A]** SAP-Resource agenten bijwerken  
   
   Een patch voor het pakket met bron agenten is vereist voor het gebruik van de nieuwe configuratie, die wordt beschreven in dit artikel. U kunt controleren of de patch al is geïnstalleerd met de volgende opdracht:

   <pre><code>sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
   </code></pre>

   De uitvoer moet er ongeveer als volgt uitzien

   <pre><code>&lt;parameter name="IS_ERS" unique="0" required="0"&gt;
   </code></pre>

   Als de grep-opdracht de para meter IS_ERS niet vindt, moet u de patch installeren die wordt vermeld op [de SuSE-download pagina](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents)

   <pre><code># example for patch for SLES 12 SP1
   sudo zypper in -t patch SUSE-SLE-HA-12-SP1-2017-885=1
   # example for patch for SLES 12 SP2
   sudo zypper in -t patch SUSE-SLE-HA-12-SP2-2017-886=1
   </code></pre>

3. **[A]** omzetting van hostnaam van installatie

   U kunt een DNS-server gebruiken of de bestand/etc/hosts wijzigen op alle knoop punten. In dit voor beeld ziet u hoe u het bestand/etc/hosts-bestand gebruikt.
   Vervang het IP-adres en de hostnaam in de volgende opdrachten:

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Voeg de volgende regels toe aan/etc/hosts. Wijzig het IP-adres en de hostnaam zodat deze overeenkomen met uw omgeving   

   <pre><code>
   # IP address of cluster node 1
   <b>10.1.1.18    anftstsapcl1</b>
   # IP address of cluster node 2
   <b>10.1.1.6     anftstsapcl2</b>
   # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
   <b>10.1.1.20    anftstsapvh</b>
   # IP address of the load balancer frontend configuration for SAP Netweaver ERS
   <b>10.1.1.21    anftstsapers</b>
   </code></pre>

4. **[1]** SAP-mappen maken in het Azure NetApp files-volume.  
   Koppel tijdelijk het Azure NetApp Files volume op een van de virtuele machines en maak de SAP-directory's (bestands paden).  

   ```
    # mount temporarily the volume
    sudo mkdir -p /saptmp
    # If using NFSv3
    sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 10.1.0.4:/sapQAS /saptmp
    # If using NFSv4.1
    sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys,tcp 10.1.0.4:/sapQAS /saptmp
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

## <a name="prepare-for-sap-netweaver-installation"></a>Voorbereiden op SAP NetWeaver-installatie

1. **[A]** de gedeelde mappen maken

   <pre><code>sudo mkdir -p /sapmnt/<b>QAS</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>QAS</b>/SYS
   sudo mkdir -p /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>QAS</b>/ERS<b>01</b>
   
   sudo chattr +i /sapmnt/<b>QAS</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>QAS</b>/SYS
   sudo chattr +i /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/ERS<b>01</b>
   </code></pre>

2. **[A]** configureren `autofs`

   <pre><code>
   sudo vi /etc/auto.master
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Als u NFSv3 gebruikt, maakt u een bestand met:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind 10.1.0.4:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=3,nobind 10.1.0.4:/trans
   /usr/sap/<b>QAS</b>/SYS -nfsvers=3,nobind 10.1.0.4:/usrsap<b>qas</b>/usrsap<b>QAS</b>sys
   </code></pre>
   
   Als u NFSv 4.1 gebruikt, maakt u een bestand met:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=4.1,nobind,sec=sys 10.1.0.4:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=4.1,nobind,sec=sys 10.1.0.4:/trans
   /usr/sap/<b>QAS</b>/SYS -nfsvers=4.1,nobind,sec=sys 10.1.0.4:/usrsap<b>qas</b>/usrsap<b>QAS</b>sys
   </code></pre>
   
   > [!NOTE]
   > Zorg ervoor dat u voldoet aan de NFS-protocol versie van de Azure NetApp Files volumes bij het koppelen van de volumes. Als de Azure NetApp Files volumes worden gemaakt als NFSv3-volumes, gebruikt u de bijbehorende NFSv3-configuratie. Als de Azure NetApp Files volumes worden gemaakt als NFSv 4.1-volumes, volgt u de instructies om ID-toewijzing uit te scha kelen en ervoor te zorgen dat u de overeenkomstige configuratie van NFSv 4.1 gebruikt. In dit voor beeld zijn de Azure NetApp Files volumes gemaakt als NFSv3-volumes.  
   
   Opnieuw opstarten `autofs` om de nieuwe shares te koppelen
    <pre><code>
      sudo systemctl enable autofs
      sudo service autofs restart
     </code></pre>

3. **[A]** wissel bestand configureren

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

### <a name="installing-sap-netweaver-ascsers"></a>SAP NetWeaver ASCS/ERS installeren

1. **[1]** Maak een virtuele IP-bron en een status test voor het ASCS-exemplaar

   > [!IMPORTANT]
   > Recente tests hebben getoonde situaties, waarbij netcat niet meer reageert op aanvragen als gevolg van achterstand en de beperking van het verwerken van slechts één verbinding. De netcat-resource stopt met Luis teren naar de Azure Load Balancer-aanvragen en het zwevende IP-adres is niet meer beschikbaar.  
   > Voor bestaande pacemaker-clusters wordt geadviseerd in de laatste Vervang netcat met socat. Momenteel raden we u aan Azure-lb resource agent te gebruiken, die deel uitmaakt van pakket resource-agents, met de volgende pakket versie vereisten:
   > - Voor SLES 12 SP4/SP5 moet de versie ten minste resource-agents-4.3.018. a7fb5035-3.30.1 zijn.  
   > - Voor SLES 15/15 SP1 moet de versie ten minste resource-agents-4.3.0184.6 ee15eb2-4.13.1 zijn.  
   >
   > Houd er rekening mee dat voor de wijziging korte uitval tijd nodig is.  
   > Voor bestaande pacemaker-clusters geldt dat als de configuratie al is gewijzigd in het gebruik van socat, zoals beschreven in [Azure Load-Balancer-detectie beveiliging](https://www.suse.com/support/kb/doc/?id=7024128), u niet onmiddellijk over de resource-agent van Azure lb hoeft te scha kelen.

   <pre><code>sudo crm node standby <b>anftstsapcl2</b>
   # If using NFSv3
   sudo crm configure primitive fs_<b>QAS</b>_ASCS Filesystem device='<b>10.1.0.4</b>/usrsap<b>qas</b>/usrsap<b>QAS</b>ascs' directory='/usr/sap/<b>QAS</b>/ASCS<b>00</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   # If using NFSv4.1
   sudo crm configure primitive fs_<b>QAS</b>_ASCS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>ascs' directory='/usr/sap/<b>QAS</b>/ASCS<b>00</b>' fstype='nfs' options='sec=sys,vers=4.1' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ASCS IPaddr2 \
     params ip=<b>10.1.1.20</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ASCS azure-lb port=620<b>00</b>
   
   sudo crm configure group g-<b>QAS</b>_ASCS fs_<b>QAS</b>_ASCS nc_<b>QAS</b>_ASCS vip_<b>QAS</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   Zorg ervoor dat de cluster status OK is en dat alle resources worden gestart. Het is niet belang rijk op welk knoop punt de resources worden uitgevoerd.

   <pre><code>sudo crm_mon -r
   
   # Node anftstsapcl2: standby
   # <b>Online: [ anftstsapcl1 ]</b>
   # 
   # Full list of resources:
   #
   # Resource Group: g-QAS_ASCS
   #     fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl1</b>
   #     nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      <b>Started anftstsapcl1</b>
   #     vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl1</b>
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   </code></pre>
  
2. **[1]** SAP NetWeaver ASCS installeren  

   Installeer SAP NetWeaver ASCS als root op het eerste knoop punt met behulp van een virtuele hostnaam die verwijst naar het IP-adres van de load balancer frontend-configuratie voor de ASCS, bijvoorbeeld <b>anftstsapvh</b>, <b>10.1.1.20</b> en het exemplaar nummer dat u hebt gebruikt voor de test van de Load Balancer, bijvoorbeeld <b>00</b>.

   U kunt de para meter sapinst SAPINST_REMOTE_ACCESS_USER gebruiken om een niet-hoofd gebruiker verbinding te laten maken met sapinst. U kunt de para meter SAPINST_USE_HOSTNAME gebruiken om SAP te installeren met behulp van een virtuele hostnaam.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   Als de installatie geen submap kan maken in/usr/sap/**QAS**/ASCS **00**, probeert u de eigenaar en groep van de ASCS **00**  -map in te stellen en het opnieuw te proberen. 

   <pre><code>
   chown <b>qas</b>adm /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   </code></pre>

3. **[1]** Maak een virtuele IP-bron en een status test voor het ers-exemplaar

   <pre><code>
   sudo crm node online <b>anftstsapcl2</b>
   sudo crm node standby <b>anftstsapcl1</b>
   # If using NFSv3
   sudo crm configure primitive fs_<b>QAS</b>_ERS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>ers' directory='/usr/sap/<b>QAS</b>/ERS<b>01</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   # If using NFSv4.1
   sudo crm configure primitive fs_<b>QAS</b>_ERS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>ers' directory='/usr/sap/<b>QAS</b>/ERS<b>01</b>' fstype='nfs' options='sec=sys,vers=4.1' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ERS IPaddr2 \
     params ip=<b>10.1.1.21</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ERS azure-lb port=621<b>01</b>
   
   sudo crm configure group g-<b>QAS</b>_ERS fs_<b>QAS</b>_ERS nc_<b>QAS</b>_ERS vip_<b>QAS</b>_ERS
   </code></pre>

   Zorg ervoor dat de cluster status OK is en dat alle resources worden gestart. Het is niet belang rijk op welk knoop punt de resources worden uitgevoerd.

   <pre><code>sudo crm_mon -r
   
   # Node <b>anftstsapcl1: standby</b>
   # <b>Online: [ anftstsapcl2 ]</b>
   # 
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ERS
   #      fs_QAS_ERS (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ERS (ocf::heartbeat:azure-lb):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ERS  (ocf::heartbeat:IPaddr2):     <b>Started anftstsapcl2</b>
   </code></pre>

4. **[2]** SAP NetWeaver ers installeren

   Installeer SAP NetWeaver ERS als root op het tweede knoop punt met behulp van een virtuele hostnaam die verwijst naar het IP-adres van de load balancer frontend-configuratie voor de ERS, bijvoorbeeld <b>anftstsapers</b>, <b>10.1.1.21</b> en het exemplaar nummer dat u hebt gebruikt voor de test van de Load Balancer, bijvoorbeeld <b>01</b>.

   U kunt de para meter sapinst SAPINST_REMOTE_ACCESS_USER gebruiken om een niet-hoofd gebruiker verbinding te laten maken met sapinst. U kunt de para meter SAPINST_USE_HOSTNAME gebruiken om SAP te installeren met behulp van een virtuele hostnaam.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   > [!NOTE]
   > Gebruik SWPM SP 20 PL of hoger. Bij lagere versies worden de machtigingen niet correct ingesteld en de installatie mislukt.

   Als de installatie geen submap kan maken in/usr/sap/**QAS**/ers **01**, probeert u de eigenaar en groep van de map ers **01** in te stellen en het opnieuw te proberen.

   <pre><code>
   chown qasadm /usr/sap/<b>QAS</b>/ERS<b>01</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ERS<b>01</b>
   </code></pre>


5. **[1]** de ASCS/SCS-en ers-instantie profielen aanpassen
 
   * ASCS/SCS-profiel

   <pre><code>
   sudo vi /sapmnt/<b>QAS</b>/profile/<b>QAS</b>_<b>ASCS00</b>_<b>anftstsapvh</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # Add the keep alive parameter, if using ENSA1
   enque/encni/set_so_keepalive = true
   </code></pre>

   Voor zowel ENSA1 als ENSA2, moet u ervoor zorgen dat de `keepalive` OS-para meters zijn ingesteld zoals beschreven in SAP note [1410736](https://launchpad.support.sap.com/#/notes/1410736).  

   * ERS-profiel

   <pre><code>
   sudo vi /sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>

6. **[A]** Keep Alive configureren

   De communicatie tussen de SAP NetWeaver-toepassings server en de ASCS/SCS wordt doorgestuurd via een software load balancer. De load balancer verbreekt inactieve verbindingen na een Configureer bare time-out. Als u dit wilt voor komen, moet u een para meter instellen in het SAP NetWeaver ASCS/SCS-profiel, als u ENSA1 gebruikt en de Linux `keepalive` -systeem instellingen op alle SAP-servers voor zowel ENSA1/ENSA2 wijzigt. Lees [SAP-opmerking 1410736][1410736] voor meer informatie.

   <pre><code>
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=300
   </code></pre>

7. **[A]** de SAP-gebruikers na de installatie configureren

   <pre><code>
   # Add sidadm to the haclient group
   sudo usermod -aG haclient <b>qas</b>adm
   </code></pre>

8. **[1]** Voeg de ASCS-en ers SAP-services toe aan het `sapservice` bestand

   Voeg de ASCS-service vermelding toe aan het tweede knoop punt en kopieer de vermelding van de ERS-service naar het eerste knoop punt.

   <pre><code>
   cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>anftstsapcl2</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>anftstsapcl2</b> "cat /usr/sap/sapservices" | grep ERS<b>01</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

9. **[1]** de SAP-cluster resources maken

Als u Server 1-architectuur (in plaats van ENSA1) gebruikt, definieert u de resources als volgt:

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ERS<b>01</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ERS<b>01</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>" AUTOMATIC_RECOVER=false IS_ERS=true \
    meta priority=1000
   
   sudo crm configure modgroup g-<b>QAS</b>_ASCS add rsc_sap_<b>QAS</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>QAS</b>_ERS add rsc_sap_<b>QAS</b>_ERS<b>01</b>
   
   sudo crm configure colocation col_sap_<b>QAS</b>_no_both -5000: g-<b>QAS</b>_ERS g-<b>QAS</b>_ASCS
   sudo crm configure location loc_sap_<b>QAS</b>_failover_to_ers rsc_sap_<b>QAS</b>_ASCS<b>00</b> rule 2000: runs_ers_<b>QAS</b> eq 1
   sudo crm configure order ord_sap_<b>QAS</b>_first_start_ascs Optional: rsc_sap_<b>QAS</b>_ASCS<b>00</b>:start rsc_sap_<b>QAS</b>_ERS<b>01</b>:stop symmetrical=false
   
   sudo crm node online <b>anftstsapcl1</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   SAP heeft ondersteuning geïntroduceerd voor het plaatsen van Server 2, inclusief replicatie, vanaf SAP NW 7,52. Vanaf ABAP platform 1809 wordt Server 2 in de wachtrij standaard geïnstalleerd. Zie SAP Note [2630416](https://launchpad.support.sap.com/#/notes/2630416) voor de ondersteuning van Server 2 in de wachtrij.
Als u Server 2-architectuur ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)) in de wachtrij plaatst, definieert u de resources als volgt:

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ERS<b>01</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ERS<b>01</b>-operations \
    op monitor interval=11 timeout=60 on-fail=restart \
    params InstanceName=<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>" AUTOMATIC_RECOVER=false IS_ERS=true
   
   sudo crm configure modgroup g-<b>QAS</b>_ASCS add rsc_sap_<b>QAS</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>QAS</b>_ERS add rsc_sap_<b>QAS</b>_ERS<b>01</b>
   
   sudo crm configure colocation col_sap_<b>QAS</b>_no_both -5000: g-<b>QAS</b>_ERS g-<b>QAS</b>_ASCS
   sudo crm configure order ord_sap_<b>QAS</b>_first_start_ascs Optional: rsc_sap_<b>QAS</b>_ASCS<b>00</b>:start rsc_sap_<b>QAS</b>_ERS<b>01</b>:stop symmetrical=false
   
   sudo crm node online <b>anftstsapcl1</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   Als u een upgrade uitvoert van een oudere versie en overschakelt naar Server 2 in wachtrij, raadpleegt u SAP Note [2641019](https://launchpad.support.sap.com/#/notes/2641019). 

   Zorg ervoor dat de cluster status OK is en dat alle resources worden gestart. Het is niet belang rijk op welk knoop punt de resources worden uitgevoerd.

   <pre><code>sudo crm_mon -r
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl1</b>
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      <b>Started anftstsapcl1</b>
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl1</b>
   #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl1</b>
   #  Resource Group: g-QAS_ERS
   #      fs_QAS_ERS (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ERS (ocf::heartbeat:azure-lb):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl2</b>
   #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl2</b>
   </code></pre>

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>Voor bereiding van SAP NetWeaver-toepassings server 

Voor sommige data bases moet de installatie van het data base-exemplaar worden uitgevoerd op een toepassings server. Bereid de virtuele machines van de toepassings server voor om ze in deze gevallen te kunnen gebruiken.

In de stappen onderstaande wordt ervan uitgegaan dat u de toepassings server installeert op een andere server dan de ASCS/SCS-en HANA-servers. Anders zijn enkele van de onderstaande stappen (zoals het configureren van de omzetting van hostnamen) niet nodig.

De volgende items worden voorafgegaan door **[A]** , van toepassing op zowel de voor-en aas, **[P]** -alleen van toepassing op pas of **[S]** -alleen van toepassing op aas.


1. **[A]** besturings systeem configureren

   Verklein de omvang van de gewijzigde cache. Zie [lage schrijf prestaties op SLES 11/12-servers met grote hoeveel heden RAM-geheugen](https://www.suse.com/support/kb/doc/?id=7010287)voor meer informatie.

   <pre><code>
   sudo vi /etc/sysctl.conf
   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. **[A]** omzetting van hostnaam van installatie

   U kunt een DNS-server gebruiken of de bestand/etc/hosts wijzigen op alle knoop punten. In dit voor beeld ziet u hoe u het bestand/etc/hosts-bestand gebruikt.
   Vervang het IP-adres en de hostnaam in de volgende opdrachten:

   ```bash
   sudo vi /etc/hosts
   ```

   Voeg de volgende regels toe aan/etc/hosts. Wijzig het IP-adres en de hostnaam zodat deze overeenkomen met uw omgeving

   <pre><code>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.1.1.20 anftstsapvh</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.1.1.21 anftstsapers</b>
   # IP address of all application servers
   <b>10.1.1.15 anftstsapa01</b>
   <b>10.1.1.16 anftstsapa02</b>
   </code></pre>

1. **[A]** de sapmnt-Directory maken

   <pre><code>
   sudo mkdir -p /sapmnt/<b>QAS</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>QAS</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. **[P]** de pas-map maken

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>02</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>02</b>
   </code></pre>

1. **[S]** de aas-map maken

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>03</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>03</b>
   </code></pre>

1. **[P]** configureren `autofs` op pas

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Als u NFSv3 gebruikt, maakt u een nieuw bestand met:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind <b>10.1.0.4</b>:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=3,nobind <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>02</b> -nfsvers=3,nobind <b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>pas
   </code></pre>

   Als u NFSv 4.1 gebruikt, maakt u een nieuw bestand met:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>02</b> -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>pas
   </code></pre>

   Opnieuw opstarten `autofs` om de nieuwe shares te koppelen

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[P]** configureren `autofs` op aas

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   Als u NFSv3 gebruikt, maakt u een nieuw bestand met:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind <b>10.1.0.4</b>:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=3,nobind <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>03</b> -nfsvers=3,nobind <b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>aas
   </code></pre>

   Als u NFSv 4.1 gebruikt, maakt u een nieuw bestand met:

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/usrsap<b>qas</b>/sapmnt<b>QAS</b>
   /usr/sap/trans -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>03</b> -nfsvers=4.1,nobind,sec=sys <b>10.1.0.4</b>:/usrsap<b>qas</b>/usrsap<b>QAS</b>aas
   </code></pre>

   Opnieuw opstarten `autofs` om de nieuwe shares te koppelen

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[A]** wissel bestand configureren

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

   <pre><code>sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>Database installeren

In dit voor beeld is SAP NetWeaver geïnstalleerd op SAP HANA. U kunt elke ondersteunde Data Base voor deze installatie gebruiken. Zie voor meer informatie over het installeren van SAP HANA in azure [hoge Beschik baarheid van SAP Hana op Azure virtual machines (vm's)][sap-hana-ha]. Zie [SAP Note 1928533][1928533]voor een lijst met ondersteunde data bases.

* De installatie van het SAP-data base-exemplaar uitvoeren

   Installeer het SAP NetWeaver-data base-exemplaar als root met behulp van een virtuele hostnaam die wordt toegewezen aan het IP-adres van de load balancer front-end-configuratie voor de data base.

   U kunt de para meter sapinst SAPINST_REMOTE_ACCESS_USER gebruiken om een niet-hoofd gebruiker verbinding te laten maken met sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>Installatie van de toepassings server van SAP net-Weaver

Volg deze stappen om een SAP-toepassings server te installeren.

1. **[A]** toepassings server voorbereiden Volg de stappen in het hoofd stuk [SAP NetWeaver Application Server voorbereiding](high-availability-guide-suse-netapp-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) hierboven om de toepassings server voor te bereiden.

2. **[A]** SAP NetWeaver-toepassings server installeren een primaire of extra SAP NetWeaver-toepassingen server installeren.

   U kunt de para meter sapinst SAPINST_REMOTE_ACCESS_USER gebruiken om een niet-hoofd gebruiker verbinding te laten maken met sapinst.

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

3. **[A]** update SAP Hana Secure Store

   Werk het beveiligde archief van SAP HANA bij zodat dit verwijst naar de virtuele naam van de installatie van de SAP HANA systeem replicatie.

   Voer de volgende opdracht uit om de vermeldingen weer te geven
   <pre><code>
   hdbuserstore List
   </code></pre>

   Hiermee wordt een lijst weer gegeven met alle vermeldingen en deze moeten er ongeveer als volgt uitzien
   <pre><code>
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.1.1.5:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>QAS</b>
   </code></pre>

   In de uitvoer ziet u dat het IP-adres van de standaard vermelding verwijst naar de virtuele machine en niet naar het IP-adres van de load balancer. Dit item moet worden gewijzigd om te verwijzen naar de virtuele hostnaam van de load balancer. Zorg ervoor dat u dezelfde poort (**30313** in de bovenstaande uitvoer) en de naam van de data base (**QAS** in de bovenstaande uitvoer) gebruikt.

   <pre><code>
   su - <b>qas</b>adm
   hdbuserstore SET DEFAULT <b>qasdb:30313@QAS</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>De Cluster installatie testen

De volgende tests zijn een kopie van de test cases in de [Best practices-gidsen van SuSE][suse-ha-guide]. Ze worden voor uw gemak gekopieerd. Lees altijd ook de best practices-hand leidingen en voer alle extra tests uit die mogelijk zijn toegevoegd.

1. HAGetFailoverConfig, HACheckConfig en HACheckFailoverConfig testen

   Voer de volgende opdrachten uit als \<sapsid> adm op het knoop punt waar het ASCS-exemplaar op dit moment wordt uitgevoerd. Als de opdrachten mislukken met een fout: onvoldoende geheugen, kan dit worden veroorzaakt door streepjes in uw hostnaam. Dit is een bekend probleem en wordt opgelost door SUSE in het pakket SAP-SuSE-cluster-connector.

   <pre><code>
   anftstsapcl1:qasadm 52> sapcontrol -nr 00 -function HAGetFailoverConfig
   07.03.2019 20:08:59
   HAGetFailoverConfig
   OK
   HAActive: TRUE
   HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP3
   HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP3 (sap_suse_cluster_connector 3.1.0)
   HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   HAActiveNode: anftstsapcl1
   HANodes: anftstsapcl1, anftstsapcl2

   anftstsapcl1:qasadm 54> sapcontrol -nr 00 -function HACheckConfig
   07.03.2019 23:28:29
   HACheckConfig
   OK
   state, category, description, comment
   SUCCESS, SAP CONFIGURATION, Redundant ABAP instance configuration, 2 ABAP instances detected
   SUCCESS, SAP CONFIGURATION, Redundant Java instance configuration, 0 Java instances detected
   SUCCESS, SAP CONFIGURATION, Enqueue separation, All Enqueue server separated from application server
   SUCCESS, SAP CONFIGURATION, MessageServer separation, All MessageServer separated from application server
   SUCCESS, SAP CONFIGURATION, ABAP instances on multiple hosts, ABAP instances on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP SPOOL service configuration, 2 ABAP instances with SPOOL service detected
   SUCCESS, SAP STATE, Redundant ABAP SPOOL service state, 2 ABAP instances with active SPOOL service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP SPOOL service on multiple hosts, ABAP instances with active ABAP SPOOL service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP BATCH service configuration, 2 ABAP instances with BATCH service detected
   SUCCESS, SAP STATE, Redundant ABAP BATCH service state, 2 ABAP instances with active BATCH service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP BATCH service on multiple hosts, ABAP instances with active ABAP BATCH service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP DIALOG service configuration, 2 ABAP instances with DIALOG service detected
   SUCCESS, SAP STATE, Redundant ABAP DIALOG service state, 2 ABAP instances with active DIALOG service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP DIALOG service on multiple hosts, ABAP instances with active ABAP DIALOG service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP UPDATE service configuration, 2 ABAP instances with UPDATE service detected
   SUCCESS, SAP STATE, Redundant ABAP UPDATE service state, 2 ABAP instances with active UPDATE service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP UPDATE service on multiple hosts, ABAP instances with active ABAP UPDATE service on multiple hosts detected
   SUCCESS, SAP STATE, SCS instance running, SCS instance status ok
   SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version (anftstsapvh_QAS_00), SAPInstance includes is-ers patch
   SUCCESS, SAP CONFIGURATION, Enqueue replication (anftstsapvh_QAS_00), Enqueue replication enabled
   SUCCESS, SAP STATE, Enqueue replication state (anftstsapvh_QAS_00), Enqueue replication active
   
   anftstsapcl1:qasadm 55> sapcontrol -nr 00 -function HACheckFailoverConfig
   07.03.2019 23:30:48
   HACheckFailoverConfig
   OK
   state, category, description, comment
   SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   </code></pre>

2. Het ASCS-exemplaar hand matig migreren

   Resource status voordat u begint met testen:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rscsap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Starting anftstsapcl1
   </code></pre>

   Voer de volgende opdrachten uit als root om het ASCS-exemplaar te migreren.

   <pre><code>
   anftstsapcl1:~ # crm resource migrate rsc_sap_QAS_ASCS00 force
   INFO: Move constraint created for rsc_sap_QAS_ASCS00
   
   anftstsapcl1:~ # crm resource unmigrate rsc_sap_QAS_ASCS00
   INFO: Removed migration constraints for rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Resource status na de test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

3. HAFailoverToNode testen

   Resource status voordat u begint met testen:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

   Voer de volgende opdrachten uit als \<sapsid> adm om het ASCS-exemplaar te migreren.

   <pre><code>
   anftstsapcl1:qasadm 53> sapcontrol -nr 00 -host anftstsapvh -user <b>qas</b>adm &lt;password&gt; -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   # Remove migration constraints
   anftstsapcl1:~ # crm resource clear rsc_sap_QAS_ASCS00
   #INFO: Removed migration constraints for rsc_sap_QAS_ASCS00
   </code></pre>

   Resource status na de test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

4. Knoop punt crash simuleren 

   Resource status voordat u begint met testen:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Voer de volgende opdracht uit als root op het knoop punt waar het ASCS-exemplaar wordt uitgevoerd

   <pre><code>anftstsapcl2:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   Als u SBD gebruikt, mag pacemaker niet automatisch worden gestart op het gedode knoop punt. De status nadat het knoop punt opnieuw is gestart, moet er als volgt uitzien.

   <pre><code>Online:
   Online: [ anftstsapcl1 ]
   OFFLINE: [ anftstsapcl2 ]

   Full list of resources:

    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1

   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=166, status=complete, exitreason='',
    last-rc-change='Fri Mar  8 18:26:10 2019', queued=0ms, exec=0ms
   </code></pre>

   Gebruik de volgende opdrachten om pacemaker op het verwerkte knoop punt te starten, de SBD-berichten op te schonen en de mislukte resources op te schonen.

   <pre><code>
   # run as root
   # list the SBD device(s)
   anftstsapcl2:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405b730e31e7d5a4516a2a697dcf;/dev/disk/by-id/scsi-36001405f69d7ed91ef54461a442c676e;/dev/disk/by-id/scsi-360014058e5f335f2567488882f3a2c3a"

   anftstsapcl2:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e11 -d /dev/disk/by-id/scsi-36001405f69d7ed91ef54461a442c676e -d /dev/disk/by-id/scsi-360014058e5f335f2567488882f3a2c3a message anftstsapcl2 clear

   anftstsapcl2:~ # systemctl start pacemaker
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Resource status na de test:

   <pre><code>
   Full list of resources:
   
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

5. Hand matig opnieuw starten van ASCS-exemplaar testen

   Resource status voordat u begint met testen:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Maak een vergren deling in de wachtrij door bijvoorbeeld een gebruiker in trans actie su01 te bewerken. Voer de volgende opdrachten uit als <sapsid \> adm op het knoop punt waarop het ASCS-exemplaar wordt uitgevoerd. Met de opdrachten wordt het ASCS-exemplaar gestopt en opnieuw gestart. Als u de architectuur voor de bewaarde server 1 gebruikt, wordt de beplaatsings vergrendeling naar verwachting verloren gegaan tijdens deze test. Als u de architectuur van Server 2 in plaats van gebruikt, wordt de in de wachtrij bewaard. 

   <pre><code>anftstsapcl2:qasadm 51> sapcontrol -nr 00 -function StopWait 600 2
   </code></pre>

   Het ASCS-exemplaar moet nu worden uitgeschakeld in pacemaker

   <pre><code>  rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Stopped (disabled)
   </code></pre>

   Start het ASCS-exemplaar opnieuw op hetzelfde knoop punt.

   <pre><code>anftstsapcl2:qasadm 52> sapcontrol -nr 00 -function StartWait 600 2
   </code></pre>

   De vergren deling van trans actie su01 moet verloren gaan als u de architectuur voor Server replicatie 1 in de wachtrij wilt gebruiken en de back-end moet zijn ingesteld. Resource status na de test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

6. Bericht Server proces beëindigen

   Resource status voordat u begint met testen:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Voer de volgende opdrachten uit als root om het proces van de berichten server te identificeren en af te breken.

   <pre><code>anftstsapcl2:~ # pgrep ms.sapQAS | xargs kill -9
   </code></pre>

   Als u de berichten server alleen eenmaal beëindigt, wordt deze opnieuw opgestart door `sapstart` . Als u deze regel matig beëindigt, wordt de ASCS-instantie uiteindelijk door pacemaker naar het andere knoop punt verplaatst. Voer de volgende opdrachten uit als root om de resource status van het ASCS-en ERS-exemplaar na de test op te schonen.

   <pre><code>
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Resource status na de test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

7. Server proces voor het plaatsen van de wachtrij beëindigen

   Resource status voordat u begint met testen:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

   Voer de volgende opdrachten uit als root op het knoop punt waar het ASCS-exemplaar wordt uitgevoerd om de bewerkings server af te breken.

   <pre><code>anftstsapcl1:~ # pgrep en.sapQAS | xargs kill -9
   </code></pre>

   Het ASCS-exemplaar moet direct een failover uitvoeren naar het andere knoop punt. Na het starten van het ASCS-exemplaar moet ook een failover worden uitgevoerd voor het ERS-exemplaar. Voer de volgende opdrachten uit als root om de resource status van het ASCS-en ERS-exemplaar na de test op te schonen.

   <pre><code>
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Resource status na de test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

8. Replicatie Server proces voor het plaatsen van de wachtrij beëindigen

   Resource status voordat u begint met testen:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Voer de volgende opdracht uit als hoofd server op het knoop punt waar het ERS-exemplaar wordt uitgevoerd om het proces voor het repliceren van de wachtrij te beëindigen.

   <pre><code>anftstsapcl1:~ # pgrep er.sapQAS | xargs kill -9
   </code></pre>

   Als u de opdracht slechts eenmaal uitvoert, `sapstart` wordt het proces opnieuw gestart. Als u dit vaak genoeg uitvoert, `sapstart` wordt het proces niet opnieuw gestart en wordt de bron gestopt. Voer de volgende opdrachten uit als root om de resource status van het ERS-exemplaar na de test op te schonen.

   <pre><code>anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   Resource status na de test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

9. Sapstartsrv-proces voor het plaatsen van de wachtrij beëindigen

   Resource status voordat u begint met testen:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   Voer de volgende opdrachten uit als root op het knoop punt waar de ASCS wordt uitgevoerd.

   <pre><code>
   anftstsapcl2:~ # pgrep -fl ASCS00.*sapstartsrv
   #67625 sapstartsrv
   
   anftstsapcl2:~ # kill -9 67625
   </code></pre>

   Het sapstartsrv-proces moet altijd opnieuw worden gestart door de resource agent van pacemaker. Resource status na de test:

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

## <a name="next-steps"></a>Volgende stappen

* [HA voor SAP NW op Azure Vm's op SLES voor SAP-toepassingen multi-SID-hand leiding](./high-availability-guide-suse-multi-sid.md)
* [Azure Virtual Machines planning en implementatie voor SAP][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP][dbms-guide]
* Zie [hoge Beschik baarheid van SAP Hana op azure virtual machines (vm's)][sap-hana-ha] voor meer informatie over het opzetten van een hoge Beschik baarheid en het plannen van nood herstel van SAP Hana op Azure-vm's.