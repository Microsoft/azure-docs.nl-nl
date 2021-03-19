---
title: Hoge Beschik baarheid van SAP HANA op virtuele machines van Azure op RHEL | Microsoft Docs
description: Breng hoge Beschik baarheid van SAP HANA op virtuele machines van Azure (Vm's).
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/16/2021
ms.author: radeltch
ms.openlocfilehash: daa0a6b15d4c187efdea96fd8067b08c89fa0e82
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599864"
---
# <a name="high-availability-of-sap-hana-on-azure-vms-on-red-hat-enterprise-linux"></a>Hoge Beschik baarheid van SAP HANA op virtuele machines van Azure op Red Hat Enterprise Linux

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2388694]:https://launchpad.support.sap.com/#/notes/2388694
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://launchpad.support.sap.com/#/softwarecenter
[template-multisid-db]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json

Voor on-premises ontwikkeling kunt u gebruikmaken van de HANA-systeem replicatie of het gebruik van gedeelde opslag om hoge Beschik baarheid voor SAP HANA tot stand te brengen.
Op virtuele machines van Azure (Vm's) is HANA-systeem replicatie op Azure momenteel de enige ondersteunde functie voor hoge Beschik baarheid.
SAP HANA replicatie bestaat uit één primair knoop punt en ten minste één secundair knoop punt. Wijzigingen in de gegevens op het primaire knoop punt worden synchroon of asynchroon gerepliceerd naar het secundaire knoop punt.

In dit artikel wordt beschreven hoe u de virtuele machines implementeert en configureert, het cluster raamwerk installeert en SAP HANA systeem replicatie installeert en configureert.
In de voorbeeld configuraties worden installatie opdrachten, instantie nummer **03** en Hana-systeem-id **HN1** gebruikt.

Lees eerst de volgende SAP-opmerkingen en-documenten:

* SAP-opmerking [1928533], die:
  * De lijst met Azure-VM-grootten die worden ondersteund voor de implementatie van SAP-software.
  * Belang rijke informatie over de capaciteit van Azure VM-grootten.
  * De ondersteunde SAP-software en besturings systemen (OS) en database combinaties.
  * De vereiste versie van de SAP-kernel voor Windows en Linux op Microsoft Azure.
* SAP-opmerking [2015553] bevat vereisten voor SAP-ondersteuning voor SAP-software-implementaties in Azure.
* SAP Note [2002167] heeft aanbevolen instellingen voor het besturings systeem voor Red Hat Enterprise Linux
* SAP Note [2009879] heeft SAP Hana richt lijnen voor Red Hat Enterprise Linux
* SAP Note [2178632] bevat gedetailleerde informatie over alle bewakings gegevens die zijn gerapporteerd voor SAP in Azure.
* SAP Note [2191498] heeft de vereiste SAP host agent-versie voor Linux in Azure.
* SAP Note [2243692] bevat informatie over SAP-licentie verlening op Linux in Azure.
* SAP Note [1999351] bevat extra informatie over probleem oplossing voor de uitgebreide bewakings extensie van Azure voor SAP.
* Op de [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) zijn alle vereiste SAP-notities voor Linux geïnstalleerd.
* [Azure Virtual Machines planning en implementatie voor SAP op Linux][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP op Linux (dit artikel)][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP op Linux][dbms-guide]
* [SAP HANA systeem replicatie in pacemaker-cluster](https://access.redhat.com/articles/3004101)
* Algemene documentatie voor RHEL
  * [Overzicht van Add-On met hoge Beschik baarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Add-On beheer met hoge Beschik baarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referentie voor Add-On met hoge Beschik baarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Specifieke RHEL-documentatie voor Azure:
  * [Ondersteunings beleid voor RHEL-clusters met hoge Beschik baarheid-Microsoft Azure Virtual Machines als cluster leden](https://access.redhat.com/articles/3131341)
  * [Installeren en configureren van een Red Hat Enterprise Linux 7,4 (en hoger) High-Availability cluster op Microsoft Azure](https://access.redhat.com/articles/3252491)
  * [SAP HANA op Red Hat Enterprise Linux installeren voor gebruik in Microsoft Azure](https://access.redhat.com/solutions/3193782)

## <a name="overview"></a>Overzicht

SAP HANA is geïnstalleerd op twee virtuele machines voor een hoge Beschik baarheid. De gegevens worden gerepliceerd met behulp van HANA-systeem replicatie.

![Overzicht van SAP HANA hoge Beschik baarheid](./media/sap-hana-high-availability-rhel/ha-hana.png)

Bij de installatie van de SAP HANA-systeem replicatie worden een specifieke virtuele hostnaam en een virtueel IP-adres gebruikt. Op Azure is een load balancer vereist voor het gebruik van een virtueel IP-adres. De volgende lijst bevat de configuratie van de load balancer:

* Front-end-configuratie: IP-adres 10.0.0.13 voor HN1-db
* Back-end-configuratie: verbonden met primaire netwerk interfaces van alle virtuele machines die deel moeten uitmaken van HANA-systeem replicatie
* Poort testen: poort 62503
* Taakverdelings regels: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP

## <a name="deploy-for-linux"></a>Implementeren voor Linux

Azure Marketplace bevat een installatie kopie voor Red Hat Enterprise Linux 7,4 voor SAP HANA die u kunt gebruiken om nieuwe virtuele machines te implementeren.

### <a name="deploy-with-a-template"></a>Implementeren met een sjabloon

U kunt een van de Quick Start-sjablonen gebruiken die zich op GitHub bevinden voor het implementeren van alle vereiste resources. De sjabloon implementeert de virtuele machines, de load balancer, de beschikbaarheidsset, enzovoort.
Voer de volgende stappen uit om de sjabloon te implementeren:

1. Open de [Database sjabloon][template-multisid-db] op de Azure Portal.
1. Voer de volgende parameters in:
    * **SAP-systeem-id**: Voer de SAP-systeem-id in van het SAP-systeem dat u wilt installeren. De ID wordt gebruikt als een voor voegsel voor de resources die worden geïmplementeerd.
    * **Type besturings systeem**: Selecteer een van de Linux-distributies. Selecteer voor dit voor beeld **RHEL 7**.
    * **Db-type**: Selecteer **Hana**.
    * **SAP-systeem grootte**: Voer het aantal sap's in dat het nieuwe systeem moet bieden. Als u niet zeker weet hoeveel SAP'S het systeem nodig heeft, vraagt u uw SAP-technologie partner of systeem integrator.
    * **Systeem beschikbaarheid**: Selecteer **ha**.
    * **Beheerders naam, beheerders wachtwoord of SSH-sleutel**: er wordt een nieuwe gebruiker gemaakt die kan worden gebruikt om u aan te melden bij de computer.
    * **Subnet-id**: als u de virtuele machine wilt implementeren in een bestaand VNet waarvoor u een subnet hebt gedefinieerd, moet de virtuele machine worden toegewezen aan, de id van het specifieke subnet benoemen. De ID is doorgaans hetzelfde als **/Subscriptions/ \<subscription ID> /resourceGroups/ \<resource group name> /providers/Microsoft.Network/virtualNetworks/ \<virtual network name> /subnets/ \<subnet name>**. Laat dit leeg als u een nieuw virtueel netwerk wilt maken

### <a name="manual-deployment"></a>Handmatige implementatie

1. Een resourcegroep maken.
1. Maak een virtueel netwerk.
1. Maak een beschikbaarheidsset.  
   Stel het maximale update domein in.
1. Maak een load balancer (intern). [Standaard Load Balancer](../../../load-balancer/load-balancer-overview.md)worden aanbevolen.
   * Selecteer het virtuele netwerk dat u in stap 2 hebt gemaakt.
1. Virtuele machine 1 maken.  
   Gebruik ten minste Red Hat Enterprise Linux 7,4 voor SAP HANA. In dit voor beeld wordt de Red Hat Enterprise Linux 7,4 voor SAP HANA-afbeelding gebruikt, <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> selecteert u de beschikbaarheidsset die u in stap 3 hebt gemaakt.
1. Maak de virtuele machine 2.  
   Gebruik ten minste Red Hat Enterprise Linux 7,4 voor SAP HANA. In dit voor beeld wordt de Red Hat Enterprise Linux 7,4 voor SAP HANA-afbeelding gebruikt, <https://portal.azure.com/#create/RedHat.RedHatEnterpriseLinux75forSAP-ARM> selecteert u de beschikbaarheidsset die u in stap 3 hebt gemaakt.
1. Voeg gegevens schijven toe.

> [!IMPORTANT]
> Zwevend IP wordt niet ondersteund voor een secundaire IP-configuratie in een NIC in scenario's voor taak verdeling. Zie beperkingen voor [Azure Load Balancer](../../../load-balancer/load-balancer-multivip-overview.md#limitations)voor meer informatie. Als u een extra IP-adres voor de virtuele machine nodig hebt, implementeert u een tweede NIC.    

> [!Note]
> Wanneer Vm's zonder open bare IP-adressen in de back-endadresgroep van intern (geen openbaar IP-adres load balancer) worden geplaatst, is er geen uitgaande Internet verbinding, tenzij er aanvullende configuratie wordt uitgevoerd om route ring naar open bare eind punten toe te staan. Zie [connectiviteit van open bare eind punten voor virtual machines met behulp van Azure Standard Load Balancer in scenario's met hoge Beschik baarheid voor SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md)voor meer informatie over het bezorgen van uitgaande verbindingen.  

1. Als u standaard load balancer gebruikt, volgt u deze configuratie stappen:
   1. Maak eerst een front-end-IP-adres groep:

      1. Open de load balancer, selecteer de **frontend-IP-adres groep** en selecteer **toevoegen**.
      1. Voer de naam in van de nieuwe front-end-IP-adres groep (bijvoorbeeld **Hana-frontend**).
      1. Stel de **toewijzing** in op **statisch** en voer het IP-adres in (bijvoorbeeld **10.0.0.13**).
      1. Selecteer **OK**.
      1. Nadat de nieuwe front-end-IP-groep is gemaakt, noteert u het IP-adres van de groep.

   1. Maak vervolgens een back-end-pool:

      1. Open de load balancer, selecteer **back-endservers** en selecteer **toevoegen**.
      1. Voer de naam van de nieuwe back-end-pool in (bijvoorbeeld **Hana-back-end**).
      1. Selecteer **een virtuele machine toevoegen**.
      1. Selecteer * * virtuele machine * *.
      1. Selecteer de virtuele machines van het SAP HANA cluster en de bijbehorende IP-adressen.
      1. Selecteer **Toevoegen**.

   1. Maak vervolgens een status test:

      1. Open de load balancer, selecteer **status controles** en selecteer **toevoegen**.
      1. Voer de naam in van de nieuwe status test (bijvoorbeeld **Hana-HP**).
      1. Selecteer **TCP** als protocol en poort 625 **03**. Laat de waarde voor **interval** ingesteld op 5 en de drempel waarde voor een **onjuiste status** ingesteld op 2.
      1. Selecteer **OK**.

   1. Maak vervolgens de regels voor taak verdeling:
   
      1. Open de load balancer, selecteer **regels voor taak verdeling** en selecteer **toevoegen**.
      1. Voer de naam in van de nieuwe load balancer regel (bijvoorbeeld **Hana-lb**).
      1. Selecteer het front-end-IP-adres, de back-end-pool en de status test die u eerder hebt gemaakt (bijvoorbeeld **Hana-frontend**, **Hana-back-end** en **Hana-HP**).
      1. Selecteer **ha-poorten**.
      1. Verhoog de **time-out voor inactiviteit** tot 30 minuten.
      1. Zorg ervoor dat u **zwevende IP-adressen inschakelt**.
      1. Selecteer **OK**.


1. Als uw scenario gebruikmaakt van basis load balancer, volgt u deze configuratie stappen:
   1. Configureer de load balancer. Maak eerst een front-end-IP-adres groep:

      1. Open de load balancer, selecteer de **frontend-IP-adres groep** en selecteer **toevoegen**.
      1. Voer de naam in van de nieuwe front-end-IP-adres groep (bijvoorbeeld **Hana-frontend**).
      1. Stel de **toewijzing** in op **statisch** en voer het IP-adres in (bijvoorbeeld **10.0.0.13**).
      1. Selecteer **OK**.
      1. Nadat de nieuwe front-end-IP-groep is gemaakt, noteert u het IP-adres van de groep.

   1. Maak vervolgens een back-end-pool:

      1. Open de load balancer, selecteer **back-endservers** en selecteer **toevoegen**.
      1. Voer de naam van de nieuwe back-end-pool in (bijvoorbeeld **Hana-back-end**).
      1. Selecteer **een virtuele machine toevoegen**.
      1. Selecteer de beschikbaarheidsset die u hebt gemaakt in stap 3.
      1. Selecteer de virtuele machines van het SAP HANA cluster.
      1. Selecteer **OK**.

   1. Maak vervolgens een status test:

      1. Open de load balancer, selecteer **status controles** en selecteer **toevoegen**.
      1. Voer de naam in van de nieuwe status test (bijvoorbeeld **Hana-HP**).
      1. Selecteer **TCP** als protocol en poort 625 **03**. Laat de waarde voor **interval** ingesteld op 5 en de drempel waarde voor een **onjuiste status** ingesteld op 2.
      1. Selecteer **OK**.

   1. Maak voor SAP HANA 1,0 de regels voor taak verdeling:

      1. Open de load balancer, selecteer **regels voor taak verdeling** en selecteer **toevoegen**.
      1. Voer de naam in van de nieuwe load balancer regel (bijvoorbeeld Hana-lb-3 **03** 15).
      1. Selecteer het front-end-IP-adres, de back-end-pool en de status test die u eerder hebt gemaakt (bijvoorbeeld **Hana-** front-end).
      1. Zorg ervoor dat het **protocol** is ingesteld op **TCP** en voer poort 3 **03** 15 in.
      1. Verhoog de **time-out voor inactiviteit** tot 30 minuten.
      1. Zorg ervoor dat u **zwevende IP-adressen inschakelt**.
      1. Selecteer **OK**.
      1. Herhaal deze stappen voor poort 3 **03** 17.

   1. Voor SAP HANA 2,0 maakt u de regels voor taak verdeling voor de systeem database:

      1. Open de load balancer, selecteer **regels voor taak verdeling** en selecteer **toevoegen**.
      1. Voer de naam in van de nieuwe load balancer regel (bijvoorbeeld Hana-lb-3 **03** 13).
      1. Selecteer het front-end-IP-adres, de back-end-pool en de status test die u eerder hebt gemaakt (bijvoorbeeld **Hana-** front-end).
      1. Zorg ervoor dat het **protocol** is ingesteld op **TCP** en voer poort 3 **03** 13 in.
      1. Verhoog de **time-out voor inactiviteit** tot 30 minuten.
      1. Zorg ervoor dat u **zwevende IP-adressen inschakelt**.
      1. Selecteer **OK**.
      1. Herhaal deze stappen voor poort 3 **03** 14.

   1. Voor SAP HANA 2,0 maakt u eerst de regels voor taak verdeling voor de Tenant database:

      1. Open de load balancer, selecteer **regels voor taak verdeling** en selecteer **toevoegen**.
      1. Voer de naam in van de nieuwe load balancer regel (bijvoorbeeld Hana-lb-3 **03** 40).
      1. Selecteer het frontend-IP-adres, de back-endadresgroep en de status test die u eerder hebt gemaakt (bijvoorbeeld **Hana-front-end**).
      1. Zorg ervoor dat het **protocol** is ingesteld op **TCP** en voer poort 3 **03** 40 in.
      1. Verhoog de **time-out voor inactiviteit** tot 30 minuten.
      1. Zorg ervoor dat u **zwevende IP-adressen inschakelt**.
      1. Selecteer **OK**.
      1. Herhaal deze stappen voor de poorten 3 **03** 41 en 3 **03** 42.

Lees voor meer informatie over de vereiste poorten voor SAP HANA de hoofdstuk [verbindingen met Tenant databases](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) in de hand leiding voor [SAP Hana Tenant-data bases](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) of [SAP Note 2388694][2388694].

> [!IMPORTANT]
> Schakel TCP-tijds tempels niet in op virtuele Azure-machines die achter Azure Load Balancer worden geplaatst. Door TCP-tijds tempels in te scha kelen, mislukken de status controles. Stel para meter **net.IPv4.tcp_timestamps** in op **0**. Zie [Load Balancer Health probe](../../../load-balancer/load-balancer-custom-probe-overview.md)(Engelstalig) voor meer informatie.
> Zie ook SAP-opmerking [2382421](https://launchpad.support.sap.com/#/notes/2382421). 

## <a name="install-sap-hana"></a>SAP HANA installeren

Voor de stappen in deze sectie worden de volgende voor voegsels gebruikt:

* **[A]**: de stap is van toepassing op alle knoop punten.
* **[1]**: de stap is alleen van toepassing op knoop punt 1.
* **[2]**: de stap is alleen van toepassing op knoop punt 2 van het pacemaker-cluster.

1. **[A]** de schijf indeling instellen: **Logical Volume Manager (LVM)**.

   We raden u aan LVM te gebruiken voor volumes die gegevens en logboek bestanden opslaan. In het volgende voor beeld wordt ervan uitgegaan dat aan de virtuele machines vier gegevens schijven zijn gekoppeld die worden gebruikt om twee volumes te maken.

   Alle beschik bare schijven weer geven:

   <pre><code>ls /dev/disk/azure/scsi1/lun*
   </code></pre>

   Voorbeelduitvoer:

   <pre><code>
   /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2  /dev/disk/azure/scsi1/lun3
   </code></pre>

   Maak fysieke volumes voor alle schijven die u wilt gebruiken:

   <pre><code>sudo pvcreate /dev/disk/azure/scsi1/lun0
   sudo pvcreate /dev/disk/azure/scsi1/lun1
   sudo pvcreate /dev/disk/azure/scsi1/lun2
   sudo pvcreate /dev/disk/azure/scsi1/lun3
   </code></pre>

   Maak een volume groep voor de gegevens bestanden. Gebruik één volume groep voor de logboek bestanden en een voor de gedeelde map van SAP HANA:

   <pre><code>sudo vgcreate vg_hana_data_<b>HN1</b> /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
   sudo vgcreate vg_hana_log_<b>HN1</b> /dev/disk/azure/scsi1/lun2
   sudo vgcreate vg_hana_shared_<b>HN1</b> /dev/disk/azure/scsi1/lun3
   </code></pre>

   Maak de logische volumes. Er wordt een lineair volume gemaakt wanneer u `lvcreate` zonder de `-i` Switch gebruikt. U wordt aangeraden een striped volume te maken voor betere I/O-prestaties en de Stripe-groottes af te stemmen op de waarden die worden beschreven in [SAP Hana VM-opslag configuraties](./hana-vm-operations-storage.md). Het `-i` argument moet het aantal onderliggende fysieke volumes zijn en het `-I` argument is de Stripe-grootte. In dit document worden twee fysieke volumes gebruikt voor het gegevens volume, dus is het `-i` argument switch ingesteld op **2**. De Stripe-grootte voor het gegevens volume is **256KiB**. Er wordt één fysiek volume gebruikt voor het logboek volume, dus nee `-i` of `-I` switches worden expliciet gebruikt voor de volume opdrachten van het logboek.  

   > [!IMPORTANT]
   > Gebruik de `-i` Switch en stel deze in op het nummer van het onderliggende fysieke volume wanneer u meer dan één fysiek volume gebruikt voor elke gegevens, elk logboek of gedeelde volumes. Gebruik de `-I` Schakel optie om de Stripe-grootte op te geven bij het maken van een striped volume.  
   > Zie [SAP Hana VM-opslag configuraties](./hana-vm-operations-storage.md) voor aanbevolen opslag configuraties, inclusief Stripe-grootte en aantal schijven.  

   <pre><code>sudo lvcreate <b>-i 2</b> <b>-I 256</b> -l 100%FREE -n hana_data vg_hana_data_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_<b>HN1</b>
   sudo lvcreate -l 100%FREE -n hana_shared vg_hana_shared_<b>HN1</b>
   sudo mkfs.xfs /dev/vg_hana_data_<b>HN1</b>/hana_data
   sudo mkfs.xfs /dev/vg_hana_log_<b>HN1</b>/hana_log
   sudo mkfs.xfs /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   </code></pre>

   Maak de koppelings directory's en kopieer de UUID van alle logische volumes:

   <pre><code>sudo mkdir -p /hana/data/<b>HN1</b>
   sudo mkdir -p /hana/log/<b>HN1</b>
   sudo mkdir -p /hana/shared/<b>HN1</b>
   # Write down the ID of /dev/vg_hana_data_<b>HN1</b>/hana_data, /dev/vg_hana_log_<b>HN1</b>/hana_log, and /dev/vg_hana_shared_<b>HN1</b>/hana_shared
   sudo blkid
   </code></pre>

   `fstab`Vermeldingen maken voor de drie logische volumes:

   <pre><code>sudo vi /etc/fstab
   </code></pre>

   Voeg de volgende regel toe aan het `/etc/fstab` bestand:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_data_<b>HN1</b>-hana_data&gt;</b> /hana/data/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_log_<b>HN1</b>-hana_log&gt;</b> /hana/log/<b>HN1</b> xfs  defaults,nofail  0  2
   /dev/disk/by-uuid/<b>&lt;UUID of /dev/mapper/vg_hana_shared_<b>HN1</b>-hana_shared&gt;</b> /hana/shared/<b>HN1</b> xfs  defaults,nofail  0  2
   </code></pre>

   De nieuwe volumes koppelen:

   <pre><code>sudo mount -a
   </code></pre>

1. **[A]** de schijf indeling instellen: **platte schijven**.

   Voor demo systemen kunt u uw HANA-gegevens en-logboek bestanden op één schijf plaatsen. Maak een partitie op/dev/disk/Azure/SCSI1/lun0 en Format teer deze met xfs:

   <pre><code>sudo sh -c 'echo -e "n\n\n\n\n\nw\n" | fdisk /dev/disk/azure/scsi1/lun0'
   sudo mkfs.xfs /dev/disk/azure/scsi1/lun0-part1
   
   # Write down the ID of /dev/disk/azure/scsi1/lun0-part1
   sudo /sbin/blkid
   sudo vi /etc/fstab
   </code></pre>

   Voeg deze regel toe aan het bestand/etc/fstab-bestand:

   <pre><code>/dev/disk/by-uuid/<b>&lt;UUID&gt;</b> /hana xfs  defaults,nofail  0  2
   </code></pre>

   Maak de doel directory en koppel de schijf:

   <pre><code>sudo mkdir /hana
   sudo mount -a
   </code></pre>

1. **[A] het** omzetten van de hostnaam voor alle hosts instellen.

   U kunt een DNS-server gebruiken of het bestand/etc/hosts-bestand op alle knoop punten wijzigen. In dit voor beeld ziet u hoe u het bestand/etc/hosts-bestand gebruikt.
   Vervang het IP-adres en de hostnaam in de volgende opdrachten:

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Voeg de volgende regels toe aan het bestand/etc/hosts-bestand. Wijzig het IP-adres en de hostnaam zodat deze overeenkomen met uw omgeving:

   <pre><code><b>10.0.0.5 hn1-db-0</b>
   <b>10.0.0.6 hn1-db-1</b>
   </code></pre>

1. **[A]** RHEL voor Hana-configuratie

   Configureer RHEL zoals beschreven in <https://access.redhat.com/solutions/2447641> en in de volgende SAP-opmerkingen:  
   - [2292690-SAP HANA DB: aanbevolen besturingssysteem instellingen voor RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)
   - [2777782-SAP HANA DB: aanbevolen besturingssysteem instellingen voor RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582-Linux: SAP-toepassingen uitvoeren die zijn gecompileerd met GCC 6. x](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824-Linux: SAP-toepassingen uitvoeren die zijn gecompileerd met GCC 7. x](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607-Linux: SAP-toepassingen uitvoeren die zijn gecompileerd met GCC 9. x](https://launchpad.support.sap.com/#/notes/2886607)

1. **[A]** de SAP Hana installeren

   Ga als volgt te werk om SAP HANA-systeem replicatie te installeren <https://access.redhat.com/articles/3004101> .

   * Voer het **hdblcm** -programma uit vanaf de Hana-DVD. Voer de volgende waarden in bij de prompt:
   * Installatie kiezen: Voer **1** in.
   * Selecteer extra onderdelen voor installatie: Voer **1** in.
   * Voer het installatiepad [/Hana/Shared] in: Selecteer ENTER.
   * Voer de naam van de lokale host [..] in: Selecteer ENTER.
   * Wilt u extra hosts toevoegen aan het systeem? (j/n) [n]: Selecteer ENTER.
   * Voer SAP HANA systeem-ID in: Voer de SID van HANA in, bijvoorbeeld: **HN1**.
   * Voer het exemplaar nummer [00] in: Voer het HANA-exemplaar nummer in. Voer **03** in als u de Azure-sjabloon hebt gebruikt of als u de sectie hand matige implementatie in dit artikel hebt gevolgd.
   * Database modus selecteren/index invoeren [1]: Selecteer ENTER.
   * Systeem gebruik selecteren/index invoeren [4]: Selecteer de waarde voor het systeem gebruik.
   * Voer de locatie van de gegevens volumes [/hana/data/HN1] in: Selecteer ENTER.
   * Locatie van logboek volumes invoeren [/hana/log/HN1]: Selecteer ENTER.
   * Maximale geheugen toewijzing beperken? [n]: Selecteer ENTER.
   * Voer de hostnaam van het certificaat in voor de host... [...]: Selecteer ENTER.
   * Voer het wacht woord voor de gebruiker van de SAP host agent (sapadm) in: Voer het wacht woord van de Hosta Gent in.
   * Bevestig het wacht woord van de gebruiker van de SAP-Hosta Gent (sapadm): Voer het wacht woord van de Hosta Gent in om het te bevestigen
   * Voer het wacht woord voor de systeem beheerder (hdbadm) in: Voer het beheerders wachtwoord voor het systeem in.
   * Wacht woord van systeem beheerder (hdbadm) bevestigen: Voer het wacht woord van de systeem beheerder nogmaals in om dit te bevestigen.
   * Voer de basismap van de systeem beheerder in [/usr/sap/HN1/home]: Selecteer ENTER.
   * Voer aanmeldings shell van systeem beheerder in [/bin/sh]: Selecteer ENTER.
   * Voer de gebruikers-ID van de systeem beheerder in [1001]: Selecteer ENTER.
   * ID van gebruikers groep opgeven (sapsys) [79]: Selecteer ENTER.
   * Voer het wacht woord voor de database gebruiker (systeem) in: Voer het wacht woord voor de database gebruiker in.
   * Wacht woord van database gebruiker (systeem) bevestigen: Voer het wacht woord voor de database gebruiker opnieuw in om dit te bevestigen.
   * Systeem opnieuw opstarten nadat de computer opnieuw is opgestart? [n]: Selecteer ENTER.
   * Wilt u doorgaan? (j/n): Valideer de samen vatting. Voer **y** in om door te gaan.

1. **[A]** de SAP Hosta Gent bijwerken.

   Down load het meest recente SAP host agent-archief vanuit het [SAP Software Center][sap-swcenter] en voer de volgende opdracht uit om de agent bij te werken. Vervang het pad naar het archief zodat dit verwijst naar het bestand dat u hebt gedownload:

   <pre><code>sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive &lt;path to SAP Host Agent SAR&gt;
   </code></pre>

1. **[A]** firewall configureren

   Maak de firewall regel voor de Azure load balancer-test poort.

   <pre><code>sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp
   sudo firewall-cmd --zone=public --add-port=625<b>03</b>/tcp --permanent
   </code></pre>

## <a name="configure-sap-hana-20-system-replication"></a>SAP HANA 2,0-systeem replicatie configureren

Voor de stappen in deze sectie worden de volgende voor voegsels gebruikt:

* **[A]**: de stap is van toepassing op alle knoop punten.
* **[1]**: de stap is alleen van toepassing op knoop punt 1.
* **[2]**: de stap is alleen van toepassing op knoop punt 2 van het pacemaker-cluster.

1. **[A]** firewall configureren

   Maak firewall regels om HANA-systeem replicatie en client verkeer toe te staan. De vereiste poorten worden vermeld op de [TCP/IP-poorten van alle SAP-producten](https://help.sap.com/viewer/ports). De volgende opdrachten zijn slechts een voor beeld van het toestaan van HANA 2,0-systeem replicatie en client verkeer naar data base SYSTEMDB, HN1 en NW1.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40301/tcp
   sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40307/tcp
   sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40303/tcp
   sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40340/tcp
   sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30340/tcp
   sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30341/tcp
   sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=30342/tcp
   </code></pre>

1. **[1]** de Tenant database maken.

   Als u SAP HANA 2,0 of MDC gebruikt, maakt u een Tenant database voor uw SAP net-Weaver-systeem. Vervang **NW1** door de sid van uw SAP-systeem.

   Voer \> de volgende opdracht uit als <hanasid adm:

   <pre><code>hdbsql -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> -d SYSTEMDB 'CREATE DATABASE <b>NW1</b> SYSTEM USER PASSWORD "<b>passwd</b>"'
   </code></pre>

1. **[1]** systeem replicatie op het eerste knoop punt configureren:

   Back-up maken van de data bases als <hanasid \> adm:

   <pre><code>hdbsql -d SYSTEMDB -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupSYS</b>')"
   hdbsql -d <b>HN1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupHN1</b>')"
   hdbsql -d <b>NW1</b> -u SYSTEM -p "<b>passwd</b>" -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackupNW1</b>')"
   </code></pre>

   Kopieer de PKI-bestanden van het systeem naar de secundaire site:

   <pre><code>scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/SSFS_<b>HN1</b>.DAT   <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/data/
   scp /usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/SSFS_<b>HN1</b>.KEY  <b>hn1-db-1</b>:/usr/sap/<b>HN1</b>/SYS/global/security/rsecssfs/key/
   </code></pre>

   De primaire site maken:

   <pre><code>hdbnsutil -sr_enable --name=<b>SITE1</b>
   </code></pre>

1. **[2]** systeem replicatie op het tweede knoop punt configureren:
    
   Registreer het tweede knoop punt om de systeem replicatie te starten. Voer de volgende opdracht uit als <hanasid \> adm:

   <pre><code>sapcontrol -nr <b>03</b> -function StopWait 600 10
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   </code></pre>

1. **[1]** replicatie status controleren

   Controleer de replicatie status en wacht totdat alle data bases zijn gesynchroniseerd. Als de status onbekend blijft, controleert u de firewall instellingen.

   <pre><code>sudo su - <b>hn1</b>adm -c "python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/systemReplicationStatus.py"
   # | Database | Host     | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
   # |          |          |       |              |           |         |           | Host      | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
   # | -------- | -------- | ----- | ------------ | --------- | ------- | --------- | --------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
   # | SYSTEMDB | <b>hn1-db-0</b> | 30301 | nameserver   |         1 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30301 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30307 | xsengine     |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30307 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>NW1</b>      | <b>hn1-db-0</b> | 30340 | indexserver  |         2 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30340 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   # | <b>HN1</b>      | <b>hn1-db-0</b> | 30303 | indexserver  |         3 |       1 | <b>SITE1</b>     | <b>hn1-db-1</b>  |     30303 |         2 | <b>SITE2</b>     | YES           | SYNC        | ACTIVE      |                |
   #
   # status system replication site "2": ACTIVE
   # overall system replication status: ACTIVE
   #
   # Local System Replication State
   # ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   #
   # mode: PRIMARY
   # site id: 1
   # site name: <b>SITE1</b>
   </code></pre>

## <a name="configure-sap-hana-10-system-replication"></a>SAP HANA 1,0-systeem replicatie configureren

Voor de stappen in deze sectie worden de volgende voor voegsels gebruikt:

* **[A]**: de stap is van toepassing op alle knoop punten.
* **[1]**: de stap is alleen van toepassing op knoop punt 1.
* **[2]**: de stap is alleen van toepassing op knoop punt 2 van het pacemaker-cluster.

1. **[A]** firewall configureren

   Maak firewall regels om HANA-systeem replicatie en client verkeer toe te staan. De vereiste poorten worden vermeld op de [TCP/IP-poorten van alle SAP-producten](https://help.sap.com/viewer/ports). De volgende opdrachten zijn slechts een voor beeld van het toestaan van HANA 2,0-systeem replicatie. Past u deze aan bij de installatie van SAP HANA 1,0.

   <pre><code>sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=40302/tcp
   </code></pre>

1. **[1]** de vereiste gebruikers maken.

   Voer de volgende opdracht uit als basis. Zorg ervoor dat u vetgedrukte teken reeksen (HANA-systeem-ID **HN1** en instantie nummer **03**) vervangt door de waarden van uw SAP Hana-installatie:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -u system -i <b>03</b> 'CREATE USER <b>hdb</b>hasync PASSWORD "<b>passwd</b>"'
   hdbsql -u system -i <b>03</b> 'GRANT DATA ADMIN TO <b>hdb</b>hasync'
   hdbsql -u system -i <b>03</b> 'ALTER USER <b>hdb</b>hasync DISABLE PASSWORD LIFETIME'
   </code></pre>

1. **[A]** de vermelding voor het opslag item maken.

   Voer de volgende opdracht uit als basis voor het maken van een nieuwe vermelding in de opslag locatie:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbuserstore SET <b>hdb</b>haloc localhost:3<b>03</b>15 <b>hdb</b>hasync <b>passwd</b>
   </code></pre>

1. **[1]** Maak een back-up van de data base.

   Back-up van de data bases maken als hoofdmap:

   <pre><code>PATH="$PATH:/usr/sap/<b>HN1</b>/HDB<b>03</b>/exe"
   hdbsql -d SYSTEMDB -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

   Als u een installatie met meerdere tenants gebruikt, moet u ook een back-up maken van de Tenant database:

   <pre><code>hdbsql -d <b>HN1</b> -u system -i <b>03</b> "BACKUP DATA USING FILE ('<b>initialbackup</b>')"
   </code></pre>

1. **[1]** Configureer de systeem replicatie op het eerste knoop punt.

   Maak de primaire site als <hanasid \> adm:

   <pre><code>su - <b>hdb</b>adm
   hdbnsutil -sr_enable –-name=<b>SITE1</b>
   </code></pre>

1. **[2]** Configureer de systeem replicatie op het secundaire knoop punt.

   Registreer de secundaire site als <hanasid \> adm:

   <pre><code>HDB stop
   hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>
   HDB start
   </code></pre>

## <a name="create-a-pacemaker-cluster"></a>Een Pacemaker-cluster maken

Volg de stappen bij het [instellen van pacemaker op Red Hat Enterprise Linux in azure](high-availability-guide-rhel-pacemaker.md) om een basis pacemaker-cluster voor deze Hana-server te maken.

## <a name="create-sap-hana-cluster-resources"></a>SAP HANA cluster resources maken

Installeer de SAP HANA resource agents op **Alle knoop punten**. Zorg ervoor dat u een opslag plaats met het pakket inschakelt. U hoeft geen extra opslag plaatsen in te scha kelen als u RHEL 8. x HA-installatie kopie gebruikt.  

<pre><code># Enable repository that contains SAP HANA resource agents
sudo subscription-manager repos --enable="rhel-sap-hana-for-rhel-7-server-rpms"
   
sudo yum install -y resource-agents-sap-hana
</code></pre>

Maak vervolgens de HANA-topologie. Voer de volgende opdrachten uit op een van de pacemaker-cluster knooppunten:

<pre><code>sudo pcs property set maintenance-mode=true

# Replace the bold string with your instance number and HANA system ID
sudo pcs resource create SAPHanaTopology_<b>HN1</b>_<b>03</b> SAPHanaTopology SID=<b>HN1</b> InstanceNumber=<b>03</b> \
op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600 \
clone clone-max=2 clone-node-max=1 interleave=true
</code></pre>

Maak vervolgens de HANA-resources.

> [!NOTE]
> Dit artikel bevat verwijzingen naar de term *Slave*, een term die door micro soft niet meer wordt gebruikt. Zodra de term uit de software wordt verwijderd, verwijderen we deze uit dit artikel.

Als u een cluster op **RHEL 7. x** maakt, gebruikt u de volgende opdrachten:  

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer.
#
sudo pcs resource create SAPHana_<b>HN1</b>_<b>03</b> SAPHana SID=<b>HN1</b> InstanceNumber=<b>03</b> PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
op start timeout=3600 op stop timeout=3600 \
op monitor interval=61 role="Slave" timeout=700 \
op monitor interval=59 role="Master" timeout=700 \
op promote timeout=3600 op demote timeout=3600 \
master notify=true clone-max=2 clone-node-max=1 interleave=true

sudo pcs resource create vip_<b>HN1</b>_<b>03</b> IPaddr2 ip="<b>10.0.0.13</b>"
sudo pcs resource create nc_<b>HN1</b>_<b>03</b> azure-lb port=625<b>03</b>
sudo pcs resource group add g_ip_<b>HN1</b>_<b>03</b> nc_<b>HN1</b>_<b>03</b> vip_<b>HN1</b>_<b>03</b>

sudo pcs constraint order SAPHanaTopology_<b>HN1</b>_<b>03</b>-clone then SAPHana_<b>HN1</b>_<b>03</b>-master symmetrical=false
sudo pcs constraint colocation add g_ip_<b>HN1</b>_<b>03</b> with master SAPHana_<b>HN1</b>_<b>03</b>-master 4000

sudo pcs property set maintenance-mode=false
</code></pre>

Als u een cluster op **RHEL 8. x** maakt, gebruikt u de volgende opdrachten:  

<pre><code># Replace the bold string with your instance number, HANA system ID, and the front-end IP address of the Azure load balancer.
#
sudo pcs resource create SAPHana_<b>HN1</b>_<b>03</b> SAPHana SID=<b>HN1</b> InstanceNumber=<b>03</b> PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
op start timeout=3600 op stop timeout=3600 \
op monitor interval=61 role="Slave" timeout=700 \
op monitor interval=59 role="Master" timeout=700 \
op promote timeout=3600 op demote timeout=3600 \
promotable meta notify=true clone-max=2 clone-node-max=1 interleave=true

sudo pcs resource create vip_<b>HN1</b>_<b>03</b> IPaddr2 ip="<b>10.0.0.13</b>"
sudo pcs resource create nc_<b>HN1</b>_<b>03</b> azure-lb port=625<b>03</b>
sudo pcs resource group add g_ip_<b>HN1</b>_<b>03</b> nc_<b>HN1</b>_<b>03</b> vip_<b>HN1</b>_<b>03</b>

sudo pcs constraint order SAPHanaTopology_<b>HN1</b>_<b>03</b>-clone then SAPHana_<b>HN1</b>_<b>03</b>-clone symmetrical=false
sudo pcs constraint colocation add g_ip_<b>HN1</b>_<b>03</b> with master SAPHana_<b>HN1</b>_<b>03</b>-clone 4000

sudo pcs property set maintenance-mode=false
</code></pre>

Zorg ervoor dat de cluster status OK is en dat alle resources worden gestart. Het is niet belang rijk op welke knoop punten de resources worden uitgevoerd.

> [!NOTE]
> De time-outs in de bovenstaande configuratie zijn slechts voor beelden en moeten mogelijk worden aangepast aan de specifieke HANA-instellingen. Het is bijvoorbeeld mogelijk dat u de time-out voor starten moet verhogen als u de SAP HANA data base langer wilt starten.  

<pre><code>sudo pcs status

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full list of resources:
#
# azure_fence     (stonith:fence_azure_arm):      Started hn1-db-0
#  Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
#      Started: [ hn1-db-0 hn1-db-1 ]
#  Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
#      Masters: [ hn1-db-0 ]
#      Slaves: [ hn1-db-1 ]
#  Resource Group: g_ip_HN1_03
#      nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
#      vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>


## <a name="configure-hana-activeread-enabled-system-replication-in-pacemaker-cluster"></a>De systeem replicatie HANA actief/gelezen configureren in het pacemaker-cluster

Vanaf SAP HANA 2,0 SPS 01 SAP kunnen Active/Lees-instellingen voor SAP HANA systeem replicatie worden ingeschakeld, waarbij de secundaire systemen van SAP HANA systeem replicatie actief kunnen worden gebruikt voor lees-intensieve workloads. Ter ondersteuning van dergelijke installatie in een cluster is een tweede virtueel IP-adres vereist, waarmee clients toegang kunnen krijgen tot de secundaire SAP HANA data base met lees functionaliteit. Om ervoor te zorgen dat de secundaire replicatie site nog steeds kan worden geopend nadat er een overname is uitgevoerd, moet het cluster het virtuele IP-adres verplaatsen met de secundaire SAPHana-resource.

In deze sectie worden de extra stappen beschreven die nodig zijn voor het beheren van de systeem replicatie HANA actief/gelezen in een Red Hat-cluster met hoge Beschik baarheid met een tweede virtueel IP-adres.    

Voordat u verder gaat, moet u ervoor zorgen dat u volledig geconfigureerde high-availability cluster met een hoge Beschik baarheid van Red Hat SAP HANA Data Base kunt beheren zoals beschreven in de bovenstaande segmenten van de documentatie.  

![SAP HANA hoge Beschik baarheid met secundaire Lees functie](./media/sap-hana-high-availability/ha-hana-read-enabled-secondary.png)

### <a name="additional-setup-in-azure-load-balancer-for-activeread-enabled-setup"></a>Aanvullende instellingen in azure load balancer voor de installatie van actief/Lees functionaliteit

Als u wilt door gaan met aanvullende stappen voor het inrichten van een tweede virtueel IP-adres, moet u ervoor zorgen dat u Azure Load Balancer hebt geconfigureerd, zoals beschreven in de sectie [hand matige implementatie](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel#manual-deployment) .

1. Voor **standaard** Load Balancer volgt u de volgende aanvullende stappen op dezelfde Load Balancer die u eerder hebt gemaakt in de vorige sectie.

   a. Maak een tweede front-end-IP-adres groep: 

   - Open de load balancer, selecteer de **frontend-IP-adres groep** en selecteer **toevoegen**.
   - Voer de naam in van de tweede front-end-IP-adres groep (bijvoorbeeld **Hana-secondaryIP**).
   - Stel de **toewijzing** in op **statisch** en voer het IP-adres in (bijvoorbeeld **10.0.0.14**).
   - Selecteer **OK**.
   - Nadat de nieuwe front-end-IP-groep is gemaakt, noteert u het IP-adres van de groep.

   b. Maak vervolgens een status test:

   - Open de load balancer, selecteer **status controles** en selecteer **toevoegen**.
   - Voer de naam van de nieuwe status test in (bijvoorbeeld **Hana-secondaryhp**).
   - Selecteer **TCP** als protocol en poort **62603**. Laat de waarde voor **interval** ingesteld op 5 en de drempel waarde voor een **onjuiste status** ingesteld op 2.
   - Selecteer **OK**.

   c. Maak vervolgens de regels voor taak verdeling:

   - Open de load balancer, selecteer **regels voor taak verdeling** en selecteer **toevoegen**.
   - Voer de naam in van de nieuwe load balancer regel (bijvoorbeeld **Hana-secondarylb**).
   - Selecteer het front-end-IP-adres, de back-end-pool en de status test die u eerder hebt gemaakt (bijvoorbeeld **Hana-secondaryIP**, **Hana-backend** en **Hana-secondaryhp**).
   - Selecteer **ha-poorten**.
   - Verhoog de **time-out voor inactiviteit** tot 30 minuten.
   - Zorg ervoor dat u **zwevende IP-adressen inschakelt**.
   - Selecteer **OK**.

### <a name="configure-hana-activeread-enabled-system-replication"></a>De systeem replicatie HANA actief/gelezen configureren

De stappen voor het configureren van HANA-systeem replicatie worden beschreven in de sectie [configure SAP HANA 2,0 System Replication](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel#configure-sap-hana-20-system-replication) . Als u een secundair scenario met lees functionaliteit implementeert en u de systeem replicatie op het tweede knoop punt configureert, voert u de volgende opdracht uit als **hanasid** adm:

```
sapcontrol -nr 03 -function StopWait 600 10 

hdbnsutil -sr_register --remoteHost=hn1-db-0 --remoteInstance=03 --replicationMode=sync --name=SITE2 --operationMode=logreplay_readaccess 
```

### <a name="adding-a-secondary-virtual-ip-address-resource-for-an-activeread-enabled-setup"></a>Een secundaire virtuele IP-adres bron toevoegen voor een installatie met Active/Read-ingeschakeld

Het tweede virtuele IP-adres en de juiste beperking voor de co-locatie kunnen worden geconfigureerd met de volgende opdrachten:

```
pcs property set maintenance-mode=true

pcs resource create secvip_HN1_03 ocf:heartbeat:IPaddr2 ip="10.40.0.16"

pcs resource create secnc_HN1_03 ocf:heartbeat:azure-lb port=62603

pcs resource group add g_secip_HN1_03 secnc_HN1_03 secvip_HN1_03

RHEL 8.x: 
pcs constraint colocation add g_secip_HN1_03 with slave SAPHana_HN1_03-clone 4000
RHEL 7.x:
pcs constraint colocation add g_secip_HN1_03 with slave SAPHana_HN1_03-master 4000

pcs property set maintenance-mode=false
```
Zorg ervoor dat de cluster status OK is en dat alle resources worden gestart. Het tweede virtuele IP-adres wordt uitgevoerd op de secundaire site, samen met de secundaire SAPHana-resource.

```
sudo pcs status

# Online: [ hn1-db-0 hn1-db-1 ]
#
# Full List of Resources:
#   rsc_hdb_azr_agt     (stonith:fence_azure_arm):      Started hn1-db-0
#   Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]:
#     Started: [ hn1-db-0 hn1-db-1 ]
#   Clone Set: SAPHana_HN1_03-clone [SAPHana_HN1_03] (promotable):
#     Masters: [ hn1-db-0 ]
#     Slaves: [ hn1-db-1 ]
#   Resource Group: g_ip_HN1_03:
#     nc_HN1_03         (ocf::heartbeat:azure-lb):      Started hn1-db-0
#     vip_HN1_03        (ocf::heartbeat:IPaddr2):       Started hn1-db-0
#   Resource Group: g_secip_HN1_03:
#     secnc_HN1_03      (ocf::heartbeat:azure-lb):      Started hn1-db-1
#     secvip_HN1_03     (ocf::heartbeat:IPaddr2):       Started hn1-db-1
```

In de volgende sectie vindt u de typische reeks failover-testen die moeten worden uitgevoerd.

Houd rekening met het tweede gedrag van virtuele IP tijdens het testen van een HANA-cluster dat is geconfigureerd met secundaire Lees functionaliteit:

1. Wanneer u **SAPHana_HN1_HDB03** cluster bron migreert naar **HN1-db-1**, wordt de tweede virtuele IP verplaatst naar de andere server **HN1-DB-0**. Als u AUTOMATED_REGISTER = false hebt geconfigureerd en HANA-systeem replicatie niet automatisch wordt geregistreerd, wordt het tweede virtuele IP-adres uitgevoerd op **HN1-DB-0,** omdat de server beschikbaar is en de Cluster Services online zijn.  

2. Wanneer het testen van de server vastloopt, worden de tweede virtuele IP-bronnen (**rsc_secip_HN1_HDB03**) en de bron van de Azure Load Balancer-poort (**rsc_secnc_HN1_HDB03**) uitgevoerd op de primaire server naast de primaire virtuele IP-resources.  Terwijl de secundaire server niet beschikbaar is, worden de toepassingen die zijn verbonden met de HANA-data base met lees functionaliteit, verbonden met de primaire HANA-data base. Het gedrag wordt verwacht, omdat u niet wilt dat toepassingen die zijn verbonden met een HANA-data base die is ingeschakeld voor lezen, niet toegankelijk zijn wanneer de secundaire server niet beschikbaar is.

3. Wanneer de secundaire server beschikbaar is en de Cluster Services online zijn, worden de tweede virtuele IP-en poort bronnen automatisch verplaatst naar de secundaire server, zelfs als HANA-systeem replicatie mogelijk niet als secundair is geregistreerd. U moet ervoor zorgen dat u de secundaire HANA-data base registreert als lezen ingeschakeld voordat u Cluster Services op die server gaat starten. U kunt de bron van het HANA-exemplaar cluster zodanig configureren dat deze automatisch wordt geregistreerd door para meter AUTOMATED_REGISTER = True in te stellen.
   
4. Tijdens de failover en terugval kunnen de bestaande verbindingen voor toepassingen, met behulp van de tweede virtuele IP om verbinding te maken met de HANA-data base, worden onderbroken.  

## <a name="test-the-cluster-setup"></a>De Cluster installatie testen

In deze sectie wordt beschreven hoe u de installatie kunt testen. Voordat u een test start, moet u ervoor zorgen dat pacemaker geen mislukte actie heeft (via de status van pc's), er zijn geen onverwachte locatie beperkingen (bijvoorbeeld rests van een migratie test) en de HANA-synchronisatie status, bijvoorbeeld met systemReplicationStatus:

<pre><code>[root@hn1-db-0 ~]# sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
</code></pre>

### <a name="test-the-migration"></a>De migratie testen

Resource status voordat u begint met testen:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

U kunt het knoop punt SAP HANA Master migreren door de volgende opdracht uit te voeren:

<pre><code># On RHEL <b>7.x</b> 
[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-master
# On RHEL <b>8.x</b>
[root@hn1-db-0 ~]# pcs resource move SAPHana_HN1_03-clone --master
</code></pre>

Als u `AUTOMATED_REGISTER="false"` deze opdracht instelt, moet u de SAP Hana hoofd knooppunt en de groep die het virtuele IP-adres bevat, migreren naar HN1-db-1.

Zodra de migratie is voltooid, ziet de uitvoer ' sudo PCs status ' er als volgt uit:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Stopped: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

De SAP HANA resource op HN1-DB-0 is gestopt. In dit geval configureert u het HANA-exemplaar als secundair door de volgende opdracht uit te voeren:

<pre><code>[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=hn1-db-1 --remoteInstance=03 --replicationMod
e=sync --name=SITE1
</code></pre>

De migratie maakt locatie beperkingen die opnieuw moeten worden verwijderd:

<pre><code># Switch back to root
exit
[root@hn1-db-0 ~]# pcs resource clear SAPHana_HN1_03-master
</code></pre>

Bewaak de status van de HANA-resource met de status ' PCs '. Zodra HANA is gestart op HN1-DB-0, ziet de uitvoer er als volgt uit:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-the-azure-fencing-agent"></a>De Azure omheinings agent testen

> [!NOTE]
> Dit artikel bevat verwijzingen naar de term *Slave*, een term die door micro soft niet meer wordt gebruikt. Zodra de term uit de software wordt verwijderd, verwijderen we deze uit dit artikel.  

Resource status voordat u begint met testen:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
    Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

U kunt de installatie van de Azure-omheining testen door de netwerk interface op het knoop punt uit te scha kelen waar SAP HANA als Master wordt uitgevoerd.
Zie [Red Hat Knowledge Base-artikel 79523](https://access.redhat.com/solutions/79523) voor een beschrijving van het simuleren van een netwerk fout. In dit voor beeld gebruiken we het net_breaker script om alle toegang tot het netwerk te blok keren.

<pre><code>[root@hn1-db-1 ~]# sh ./net_breaker.sh BreakCommCmd 10.0.0.6
</code></pre>

Afhankelijk van de cluster configuratie moet de virtuele machine nu opnieuw worden opgestart of gestopt.
Als u de `stonith-action` instelling instelt op uit, wordt de virtuele machine gestopt en worden de resources gemigreerd naar de actieve virtuele machine.

Nadat u de virtuele machine opnieuw hebt gestart, kan de SAP HANA resource niet worden gestart als secundaire als u deze instelt `AUTOMATED_REGISTER="false"` . In dit geval configureert u het HANA-exemplaar als secundair door de volgende opdracht uit te voeren:

<pre><code>su - <b>hn1</b>adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> sapcontrol -nr <b>03</b> -function StopWait 600 10
hn1adm@hn1-db-1:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-0</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE2</b>

# Switch back to root and clean up the failed state
exit
# On RHEL <b>7.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
# On RHEL <b>8.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03 node=&lt;hostname on which the resource needs to be cleaned&gt;
</code></pre>

Resource status na de test:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

### <a name="test-a-manual-failover"></a>Een hand matige failover testen

Resource status voordat u begint met testen:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

U kunt een hand matige failover testen door het cluster op het knoop punt HN1-DB-0 te stoppen:

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>

Na de failover kunt u het cluster opnieuw starten. Als u instelt `AUTOMATED_REGISTER="false"` , kan de SAP Hana resource op het knoop punt HN1-DB-0 niet worden gestart als secundair. In dit geval configureert u het HANA-exemplaar als secundair door de volgende opdracht uit te voeren:

<pre><code>[root@hn1-db-0 ~]# pcs cluster start
[root@hn1-db-0 ~]# su - hn1adm

# Stop the HANA instance just in case it is running
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> sapcontrol -nr 03 -function StopWait 600 10
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> hdbnsutil -sr_register --remoteHost=<b>hn1-db-1</b> --remoteInstance=<b>03</b> --replicationMode=sync --name=<b>SITE1</b>

# Switch back to root and clean up the failed state
hn1adm@hn1-db-0:/usr/sap/HN1/HDB03> exit
# On RHEL <b>7.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03-master
# On RHEL <b>8.x</b>
[root@hn1-db-1 ~]# pcs resource cleanup SAPHana_HN1_03 node=&lt;hostname on which the resource needs to be cleaned&gt;
</code></pre>

Resource status na de test:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-1 ]
     Slaves: [ hn1-db-0 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-1
</code></pre>

### <a name="test-a-manual-failover"></a>Een hand matige failover testen

Resource status voordat u begint met testen:

<pre><code>Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hn1-db-0 hn1-db-1 ]
Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hn1-db-0 ]
    Slaves: [ hn1-db-1 ]
Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hn1-db-0
    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hn1-db-0
</code></pre>

U kunt een hand matige failover testen door het cluster op het knoop punt HN1-DB-0 te stoppen:

<pre><code>[root@hn1-db-0 ~]# pcs cluster stop
</code></pre>


## <a name="next-steps"></a>Volgende stappen

* [Azure Virtual Machines planning en implementatie voor SAP][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP][dbms-guide]
* [VM-opslag configuraties SAP HANA](./hana-vm-operations-storage.md)