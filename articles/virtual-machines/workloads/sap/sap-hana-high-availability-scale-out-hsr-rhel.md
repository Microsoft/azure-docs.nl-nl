---
title: SAP HANA scale-out met HSR en pacemaker op RHEL | Microsoft Docs
description: SAP HANA scale-out met HSR en pacemaker op RHEL
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
ms.date: 02/01/2021
ms.author: radeltch
ms.openlocfilehash: 6ce169eee4f931d02071a326c65ba94cfe723ae1
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2021
ms.locfileid: "99259030"
---
# <a name="high-availability-of-sap-hana-scale-out-system-on-red-hat-enterprise-linux"></a>Hoge Beschik baarheid van SAP HANA scale-out systeem op Red Hat Enterprise Linux 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
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
[1900823]:https://launchpad.support.sap.com/#/notes/1900823
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2593824]:https://launchpad.support.sap.com/#/notes/2593824
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


In dit artikel wordt beschreven hoe u een Maxi maal beschikbaar SAP HANA systeem implementeert in een scale-out configuratie met HANA System Replication (HSR) en pacemaker op Azure Red Hat Enterprise Linux virtuele machines (Vm's). De gedeelde bestands systemen in de weer gegeven architectuur worden geleverd door [Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-introduction.md) en worden gekoppeld via NFS.  

In de voorbeeld configuraties, installatie opdrachten, enzovoort, is de HANA-instantie **03** en de Hana-systeem-id **HN1**. De voor beelden zijn gebaseerd op HANA 2,0 SP4 en Red Hat Enterprise Linux voor SAP 7,6. 

Raadpleeg voordat u begint de volgende SAP-opmerkingen en-documenten:

* SAP-opmerking [1928533] omvat:  
  * Een lijst met Azure-VM-grootten die worden ondersteund voor de implementatie van SAP-software
  * Informatie over belang rijke capaciteit voor Azure VM-grootten
  * Ondersteunde SAP-software en besturings systemen (OS) en database combinaties
  * De vereiste versie van de SAP-kernel voor Windows en Linux op Microsoft Azure
* SAP-opmerking [2015553]: bevat vereisten voor SAP-ondersteunde SAP-software-implementaties in azure
* SAP-Opmerking [2002167] heeft aanbevolen instellingen voor het besturings systeem voor Red Hat Enterprise Linux
* SAP Note [2009879] heeft SAP Hana richt lijnen voor Red Hat Enterprise Linux
* SAP-opmerking [2178632]: bevat gedetailleerde informatie over alle bewakings gegevens die zijn gerapporteerd voor SAP in azure
* SAP-opmerking [2191498]: bevat de vereiste SAP host agent-versie voor Linux in azure
* SAP-opmerking [2243692]: bevat informatie over SAP-licentie verlening op Linux in azure
* SAP-opmerking [1999351]: bevat aanvullende informatie voor het oplossen van problemen met de uitgebreide bewakings extensie van Azure voor SAP
* SAP-opmerking [1900823]: bevat informatie over SAP Hana opslag vereisten
* [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): bevat alle vereiste SAP-notities voor Linux
* [Azure Virtual Machines planning en implementatie voor SAP op Linux][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP op Linux][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP op Linux][dbms-guide]
* [SAP HANA netwerk vereisten](https://www.sap.com/documents/2016/08/1cd2c2fb-807c-0010-82c7-eda71af511fa.html)
* Algemene documentatie voor RHEL
  * [Overzicht van Add-On met hoge Beschik baarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Add-On beheer met hoge Beschik baarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referentie voor Add-On met hoge Beschik baarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Red Hat Enterprise Linux-netwerk gids](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide)
  * [Hoe kan ik SAP HANA Scale-Out systeem replicatie configureren in een pacemaker-cluster met HANA-bestands systemen op NFS-shares](https://access.redhat.com/solutions/5423971)
* Documentatie voor Azure-specifieke RHEL:
  * [SAP HANA op Red Hat Enterprise Linux installeren voor gebruik in Microsoft Azure](https://access.redhat.com/public-cloud/microsoft-azure)
  * [Red Hat Enterprise Linux oplossing voor SAP HANA Scale-Out en systeem replicatie](https://access.redhat.com/solutions/4386601)
* [NetApp SAP-toepassingen op Microsoft Azure met behulp van Azure NetApp Files][anf-sap-applications-azure]
* [Documentatie over Azure NetApp Files][anf-azure-doc] 
* [NFS v4.1-volumes in Azure NetApp Files voor SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-netapp)

## <a name="overview"></a>Overzicht

Eén methode voor het leveren van HANA hoge Beschik baarheid voor HANA-scale-out-installaties is het configureren van HANA-systeem replicatie en het beveiligen van de oplossing met pacemaker-cluster om automatische failover toe te staan. Als er een storing optreedt in een actief knoop punt, wordt de bron van de HANA-resources door het cluster overgedragen aan de andere site.  
In de weer gegeven configuratie ziet u drie HANA-knoop punten op elke site, plus het meren deel van de maker-knoop punt om te voor komen dat het scenario Split De instructies kunnen worden aangepast, zodat er meer Vm's worden toegevoegd als HANA DB-knoop punten.  

Het HANA gedeelde bestands systeem `/hana/shared` in de weer gegeven architectuur wordt geleverd door [Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-introduction.md). Het wordt gekoppeld via NFSv 4.1 op elk HANA-knoop punt in dezelfde HANA-systeem replicatie site. Bestands systemen `/hana/data` en `/hana/log` zijn lokale bestands systemen en worden niet gedeeld tussen de Hana DB-knoop punten. SAP HANA wordt geïnstalleerd in niet-gedeelde modus. 

> [!TIP]
> Zie [SAP Hana Azure vm's Storage-configuraties](./hana-vm-operations-storage.md)voor aanbevolen SAP Hana Storage-configuraties.   

[![SAP HANA scale-out met HSR-en pacemaker-cluster](./media/sap-hana-high-availability-rhel/sap-hana-high-availability-scale-out-hsr-rhel.png)](./media/sap-hana-high-availability-rhel/sap-hana-high-availability-scale-out-hsr-rhel-detail.png#lightbox)

In het voor gaande diagram worden drie subnetten weer gegeven in één virtueel Azure-netwerk, volgens de SAP HANA netwerk aanbevelingen: 
* voor client communicatie- `client` 10.23.0.0/24  
* voor de interne HANA-communicatie tussen knoop punten- `inter` 10.23.1.128/26  
* voor HANA-systeem replicatie- `hsr` 10.23.1.192/26  

Als `/hana/data` en `/hana/log` worden geïmplementeerd op lokale schijven, is het niet nodig om afzonderlijke subnetten en afzonderlijke virtuele netwerk kaarten te implementeren voor communicatie met de opslag.  

De Azure NetApp-volumes worden geïmplementeerd in een afzonderlijk subnet, [overgedragen aan Azure NetApp Files] ( https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet: `anf` 10.23.1.0/26.   

## <a name="set-up-the-infrastructure"></a>De infra structuur instellen

In de volgende instructies wordt ervan uitgegaan dat u de resource groep, het virtuele netwerk van Azure, al hebt gemaakt met drie Azure Network-subnetten: `client` , `inter` en `hsr` .

### <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Virtuele Linux-machines implementeren via de Azure Portal
1. Implementeer de virtuele Azure-machines.  
Voor de configuratie die in dit document wordt weer gegeven, implementeert u zeven virtuele machines: 
   - drie virtuele machines die fungeren als HANA DB-knoop punten voor de HANA-replicatie site 1: **Hana-S1-db1**, **Hana-S1-DB2** en **Hana-S1-db3**  
   - drie virtuele machines die fungeren als HANA DB-knoop punten voor de HANA-replicatie site 2: **Hana-S2-db1**, **Hana-S2-DB2** en **Hana-S2-db3**  
   - een kleine virtuele machine die als *hoofd Maker* fungeert: **Hana-s-mm**

   De Vm's die zijn geïmplementeerd als SAP DB HANA-knoop punten moeten worden gecertificeerd door SAP voor HANA zoals gepubliceerd in de [SAP Hana hardware-map](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Wanneer u de HANA DB-knoop punten implementeert, moet u ervoor zorgen dat [versneld netwerk](../../../virtual-network/create-vm-accelerated-networking-cli.md) is geselecteerd.  
  
   Voor het hoofd knooppunt van de Maker kunt u een kleine virtuele machine implementeren, omdat deze VM geen van de SAP HANA resources uitvoert. De meerderheid van de VM van de Maker wordt gebruikt in de cluster configuratie om een oneven aantal cluster knooppunten te krijgen in een split-hersenen scenario. De meerderheid van de VM van de hoofd Maker heeft alleen één virtuele netwerk interface in het `client` subnet in dit voor beeld nodig.        

   Implementeer lokale beheerde schijven voor `/hana/data` en `/hana/log` . De mini maal Aanbevolen opslag configuratie voor `/hana/data` en `/hana/log` wordt beschreven in [SAP Hana Azure vm's Storage-configuraties](./hana-vm-operations-storage.md).

   Implementeer de primaire netwerk interface voor elke virtuele machine in het subnet van het `client` virtuele netwerk.  
   Wanneer de virtuele machine wordt geïmplementeerd via Azure Portal, wordt de naam van de netwerk interface automatisch gegenereerd. In deze instructies voor de eenvoud verwijzen we naar de automatisch gegenereerde primaire netwerk interfaces, die zijn gekoppeld aan het `client` subnet van het virtuele netwerk van Azure als **Hana-S1-db1-client**, **Hana-S1-DB2-client**, **Hana-S1-db3-client**, enzovoort.  


   > [!IMPORTANT]
   > Zorg ervoor dat het besturings systeem dat u selecteert, SAP-gecertificeerd is voor SAP HANA op de specifieke VM-typen die u gebruikt. Ga naar de site [SAP Hana Certified IaaS platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) voor een lijst met SAP Hana gecertificeerde VM-typen en versies van besturings systemen voor deze typen. Klik in de details van het vermelde VM-type om de volledige lijst met door SAP HANA ondersteunde versies van besturings systemen voor dat type op te halen.  
  

2. Maak zes netwerk interfaces, één voor elke HANA DB-virtuele machine, in het subnet van het `inter` virtuele netwerk (in dit voor beeld **Hana-S1-db1-** Inter, **Hana-S1-DB2-intersite**, **Hana-S1-db3-Inter**, **Hana-S2-db1-** Inter, **Hana-S2**-- **intercompany, Hana-S2-db3-** inter).  

3. Maak zes netwerk interfaces, één voor elke HANA DB-virtuele machine, in het subnet van het `hsr` virtuele netwerk (in dit voor beeld **Hana-S1-db1-HSR**, **Hana-S1-DB2-HSR**, **Hana-S1-db3-HSR**, **Hana-S2-db1-HSR**, **Hana-S2**-HSR **-db3**)  

4. Koppel de zojuist gemaakte virtuele netwerk interfaces aan de bijbehorende virtuele machines:  

    a. Ga naar de virtuele machine in de [Azure Portal](https://portal.azure.com/#home).  

    b. Selecteer **virtual machines** in het linkerdeel venster. Filter op de naam van de virtuele machine (bijvoorbeeld **Hana-S1-db1**) en selecteer vervolgens de virtuele machine.  

    c. Selecteer in het deel venster **overzicht** de optie **stoppen** om de toewijzing van de virtuele machine ongedaan te maken.  

    d. Selecteer **netwerken** en koppel vervolgens de netwerk interface. Selecteer in de vervolg keuzelijst **netwerk interface koppelen** de al gemaakte netwerk interfaces voor de `inter` `hsr` subnetten en.  
    
    e. Selecteer **Opslaan**. 
 
    f. Herhaal stap b tot en met e voor de resterende virtuele machines (in het voor beeld  **Hana-S1-DB2**, **Hana-S1-db3**, **Hana-S2-db1**, **Hana-S2-DB2** en **Hana-S2-db3**).
 
    g. Zorg ervoor dat de virtuele machines met de status gestopt nu actief zijn. Vervolgens wordt [versneld netwerken](../../../virtual-network/create-vm-accelerated-networking-cli.md) ingeschakeld voor alle nieuw gekoppelde netwerk interfaces.  

5. Voer de volgende stappen uit om versnelde netwerken in te scha kelen voor de extra netwerk interfaces voor de `inter` `hsr` subnetten en:  

    a. Open [Azure Cloud shell](https://azure.microsoft.com/features/cloud-shell/) in het [Azure Portal](https://portal.azure.com/#home).  

    b. Voer de volgende opdrachten uit om versneld netwerken in te scha kelen voor de extra netwerk interfaces die zijn gekoppeld aan de `inter` `hsr` subnetten.  

    ```azurecli
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-inter --accelerated-networking true
    
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-hsr --accelerated-networking true
    ```

7. De HANA DB-virtuele machines starten

### <a name="deploy-azure-load-balancer"></a>Azure Load Balancer implementeren

1. U kunt het beste standaard load balancer gebruiken. Volg deze configuratie stappen voor het implementeren van standaard load balancer:
   1. Maak eerst een front-end-IP-adres groep:

      1. Open de load balancer, selecteer de **frontend-IP-adres groep** en selecteer **toevoegen**.
      1. Voer de naam in van de nieuwe front-end-IP-adres groep (bijvoorbeeld **Hana-frontend**).
      1. Stel de **toewijzing** in op **statisch** en voer het IP-adres in (bijvoorbeeld **10.23.0.18**).
      1. Selecteer **OK**.
      1. Nadat de nieuwe front-end-IP-groep is gemaakt, noteert u het IP-adres van de groep.

   1. Maak vervolgens een back-end-pool en voeg alle cluster-Vm's toe aan de back-end-groep:

      1. Open de load balancer, selecteer **back-endservers** en selecteer **toevoegen**.
      1. Voer de naam van de nieuwe back-end-pool in (bijvoorbeeld **Hana-back-end**).
      1. Selecteer **een virtuele machine toevoegen**.
      1. **Virtuele machine** selecteren.
      1. Selecteer de virtuele machines van het SAP HANA cluster en de bijbehorende IP-adressen voor het `client` subnet.
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

   > [!IMPORTANT]
   > Zwevend IP wordt niet ondersteund voor een secundaire IP-configuratie in een NIC in scenario's voor taak verdeling. Zie beperkingen voor [Azure Load Balancer](../../../load-balancer/load-balancer-multivip-overview.md#limitations)voor meer informatie. Als u een extra IP-adres voor de virtuele machine nodig hebt, implementeert u een tweede NIC.    
   
   > [!Note]
   > Wanneer Vm's zonder open bare IP-adressen in de back-endadresgroep van intern (geen openbaar IP-adres load balancer) worden geplaatst, is er geen uitgaande Internet verbinding, tenzij er aanvullende configuratie wordt uitgevoerd om route ring naar open bare eind punten toe te staan. Zie [connectiviteit van open bare eind punten voor virtual machines met behulp van Azure Standard Load Balancer in scenario's met hoge Beschik baarheid voor SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md)voor meer informatie over het bezorgen van uitgaande verbindingen.  


   > [!IMPORTANT]
   > Schakel TCP-tijds tempels niet in op virtuele Azure-machines die achter Azure Load Balancer worden geplaatst. Door TCP-tijds tempels in te scha kelen, mislukken de status controles. Stel para meter **net.IPv4.tcp_timestamps** in op **0**. Zie [Load Balancer Health probe](../../../load-balancer/load-balancer-custom-probe-overview.md)(Engelstalig) voor meer informatie.
   > Zie ook SAP-opmerking [2382421](https://launchpad.support.sap.com/#/notes/2382421).  

### <a name="deploy-the-azure-netapp-files-infrastructure"></a>De Azure NetApp Files-infra structuur implementeren 

Implementeer de ANF-volumes voor het `/hana/shared` Bestands systeem. U hebt `/hana/shared` voor elke Hana-systeem replicatie site een afzonderlijk volume nodig. Zie [de Azure NetApp files-infra structuur instellen](./sap-hana-scale-out-standby-netapp-files-rhel.md#set-up-the-azure-netapp-files-infrastructure)voor meer informatie.

In dit voor beeld zijn de volgende Azure NetApp Files volumes gebruikt: 

* volume **HN1**-Shared-s1 (NFS://10.23.1.7/**HN1**-Shared-S1)
* volume **HN1**-gedeeld-s2 (NFS://10.23.1.7/**HN1**-Shared-S2)

## <a name="operating-system-configuration-and-preparation"></a>Configuratie en voor bereiding van het besturings systeem

De instructies in de volgende secties worden voorafgegaan door een van de volgende afkortingen:
* **[A]**: van toepassing op alle knoop punten
* **[Ah]**: van toepassing op alle Hana DB-knoop punten
* **[M]**: van toepassing op het hoofd knooppunt van de maker
* **[AH1]**: van toepassing op alle Hana DB-knoop punten op site 1
* **[AH2]**: van toepassing op alle Hana DB-knoop punten op site 2
* **[1]**: alleen van toepassing op Hana DB-knoop punt 1, site 1
* **[2]**: alleen van toepassing op Hana DB-knoop punt 1, site 2


Configureer en bereid uw besturings systeem uit door de volgende stappen uit te voeren:

1. **[A]** de host-bestanden op de virtuele machines onderhouden. Neem vermeldingen op voor alle subnetten. De volgende vermeldingen zijn toegevoegd aan `/etc/hosts` voor dit voor beeld.  

    ```bash
     # Client subnet
     10.23.0.11 hana-s1-db1
     10.23.0.12 hana-s1-db1
     10.23.0.13 hana-s1-db2
     10.23.0.14 hana-s2-db1
     10.23.0.15 hana-s2-db2
     10.23.0.16 hana-s2-db3
     10.23.0.17 hana-s-mm
     # Internode subnet
     10.23.1.138 hana-s1-db1-inter
     10.23.1.139 hana-s1-db2-inter
     10.23.1.140 hana-s1-db3-inter
     10.23.1.141 hana-s2-db1-inter
     10.23.1.142 hana-s2-db2-inter
     10.23.1.143 hana-s2-db3-inter
     # HSR subnet
     10.23.1.202 hana-s1-db1-hsr
     10.23.1.203 hana-s1-db2-hsr
     10.23.1.204 hana-s1-db3-hsr
     10.23.1.205 hana-s2-db1-hsr
     10.23.1.206 hana-s2-db2-hsr
     10.23.1.207 hana-s2-db3-hsr
    ```


2. **[A]** Installeer het NFS-client pakket.  

    ```yum install nfs-utils ```


3. **[Ah]** Red Hat voor HANA-configuratie.  

    Configureer RHEL zoals beschreven in <https://access.redhat.com/solutions/2447641> en in de volgende SAP-opmerkingen:  
   - [2292690-SAP HANA DB: aanbevolen besturingssysteem instellingen voor RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)
   - [2777782-SAP HANA DB: aanbevolen besturingssysteem instellingen voor RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582-Linux: SAP-toepassingen uitvoeren die zijn gecompileerd met GCC 6. x](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824-Linux: SAP-toepassingen uitvoeren die zijn gecompileerd met GCC 7. x](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607-Linux: SAP-toepassingen uitvoeren die zijn gecompileerd met GCC 9. x](https://launchpad.support.sap.com/#/notes/2886607)

## <a name="prepare-the-file-systems"></a>De bestands systemen voorbereiden
### <a name="mount-the-shared-file-systems"></a>De gedeelde bestands systemen koppelen

In dit voor beeld worden de gedeelde HANA-bestands systemen geïmplementeerd op Azure NetApp Files en gekoppeld via NFSv4.  

1. **[Ah]** Maak koppel punten voor de HANA-database volumes.  

    ```bash
    mkdir -p /hana/shared
    ```

2. **[Ah]** Controleer de instelling van het NFS-domein. Zorg ervoor dat het domein is geconfigureerd als de standaard Azure NetApp Files domein, dat wil zeggen, **`defaultv4iddomain.com`** en dat de toewijzing is ingesteld op **niemand**.  
   Deze stap is alleen nodig als u Azure NetAppFiles NFSv 4.1 gebruikt.  

    > [!IMPORTANT]
    > Zorg ervoor dat u het NFS-domein in `/etc/idmapd.conf` op de VM instelt op de standaard domein configuratie op Azure NetApp files: **`defaultv4iddomain.com`** . Als er een verschil is tussen de domein configuratie op de NFS-client (de virtuele machine) en de NFS-server, dat wil zeggen de Azure NetApp-configuratie, worden de machtigingen voor bestanden op Azure NetApp-volumes die zijn gekoppeld op de Vm's weer gegeven als `nobody` .  

    ```bash
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = defaultv4iddomain.com
    [Mapping]
    Nobody-User = nobody
    Nobody-Group = nobody
    ```

3. **[Ah]** Verifiëren `nfs4_disable_idmapping` . Deze moet worden ingesteld op **Y**. Als u de mapstructuur wilt maken waar `nfs4_disable_idmapping` zich bevindt, voert u de koppeling-opdracht uit. U kunt de map niet hand matig maken onder/sys/modules, omdat de toegang is gereserveerd voor de kernel/Stuur Programma's.  
   Deze stap is alleen nodig als u Azure NetAppFiles NFSv 4.1 gebruikt.  

    ```bash
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.9.0.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

   Zie voor meer informatie over het wijzigen van `nfs4_disable_idmapping` para meters https://access.redhat.com/solutions/1749883 .

4. **[AH1]** Koppel de gedeelde Azure NetApp Files volumes op de SITE1 HANA DB-Vm's.  

    ```bash
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.23.1.7:/HN1-shared-s1 /hana/shared
    ```

5. **[AH2]** Koppel de gedeelde Azure NetApp Files volumes op de SITE2 HANA DB-Vm's.  

    ```bash
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.23.1.7:/HN1-shared-s2 /hana/shared
    ```


10. **[Ah]** Controleer of de bijbehorende `/hana/shared/` bestands systemen zijn gekoppeld op alle Hana DB-vm's met NFS-protocol versie **NFSv4**.  

    ```bash
    sudo nfsstat -m
    # Verify that flag vers is set to 4.1 
    # Example from SITE 1, hana-s1-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s1
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.11,local_lock=none,addr=10.23.1.7
    # Example from SITE 2, hana-s2-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s2
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.14,local_lock=none,addr=10.23.1.7
    ```

### <a name="prepare-the-data-and-log-local-file-systems"></a>De gegevens en lokale bestands systemen van het logboek voorbereiden
In de weer gegeven configuratie worden bestands systemen `/hana/data` en `/hana/log` geïmplementeerd op beheerde schijven en zijn ze lokaal gekoppeld aan elke Hana DB-VM. U moet de stappen uitvoeren om de lokale gegevens en logboek volumes te maken op elke HANA DB-virtuele machine. 

Stel de schijf indeling in met  **Logical Volume Manager (LVM)**. In het volgende voor beeld wordt ervan uitgegaan dat aan elke HANA virtuele machine drie gegevens schijven zijn gekoppeld, die worden gebruikt voor het maken van twee volumes.

1. **[Ah]** Alle beschik bare schijven weer geven:
    ```bash
    ls /dev/disk/azure/scsi1/lun*
    ```

   Voorbeelduitvoer:

    ```bash
    /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2 
    ```

2. **[Ah]** Maak fysieke volumes voor alle schijven die u wilt gebruiken:
    ```bash
    sudo pvcreate /dev/disk/azure/scsi1/lun0
    sudo pvcreate /dev/disk/azure/scsi1/lun1
    sudo pvcreate /dev/disk/azure/scsi1/lun2
    ```

3. **[Ah]** Maak een volume groep voor de gegevens bestanden. Gebruik één volume groep voor de logboek bestanden en een voor de gedeelde map van SAP HANA:
    ```bash
    sudo vgcreate vg_hana_data_HN1 /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
    sudo vgcreate vg_hana_log_HN1 /dev/disk/azure/scsi1/lun2
    ```

4. **[Ah]** Maak de logische volumes. 
   Er wordt een lineair volume gemaakt wanneer u `lvcreate` zonder de `-i` Switch gebruikt. U wordt aangeraden een striped volume te maken voor betere I/O-prestaties en de Stripe-groottes af te stemmen op de waarden die worden beschreven in [SAP Hana VM-opslag configuraties](./hana-vm-operations-storage.md). Het `-i` argument moet het aantal onderliggende fysieke volumes zijn en het `-I` argument is de Stripe-grootte. In dit document worden twee fysieke volumes gebruikt voor het gegevens volume, dus is het `-i` argument switch ingesteld op **2**. De Stripe-grootte voor het gegevens volume is **256 KiB**. Er wordt één fysiek volume gebruikt voor het logboek volume, dus nee `-i` of `-I` switches worden expliciet gebruikt voor de volume opdrachten van het logboek.  

   > [!IMPORTANT]
   > Gebruik de `-i` Switch en stel deze in op het nummer van het onderliggende fysieke volume wanneer u meer dan één fysiek volume gebruikt voor elk gegevens-of logboek volume. Gebruik de `-I` Schakel optie om de Stripe-grootte op te geven bij het maken van een striped volume.  
   > Zie [SAP Hana VM-opslag configuraties](./hana-vm-operations-storage.md) voor aanbevolen opslag configuraties, inclusief Stripe-grootte en aantal schijven.  

    ```bash
    sudo lvcreate -i 2 -I 256 -l 100%FREE -n hana_data vg_hana_data_HN1
    sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_HN1
    sudo mkfs.xfs /dev/vg_hana_data_HN1/hana_data
    sudo mkfs.xfs /dev/vg_hana_log_HN1/hana_log
    ```

5. **[Ah]** Maak de koppelings directory's en kopieer de UUID van alle logische volumes:
    ```bash
    sudo mkdir -p /hana/data/HN1
    sudo mkdir -p /hana/log/HN1
    # Write down the ID of /dev/vg_hana_data_HN1/hana_data and /dev/vg_hana_log_HN1/hana_log
    sudo blkid
    ```

6. **[Ah]** `fstab` Vermeldingen maken voor de logische volumes en koppelen:
    ```bash
    sudo vi /etc/fstab
    ```

   Voeg de volgende regel toe aan het `/etc/fstab` bestand:

    ```bash
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_data_HN1-hana_data /hana/data/HN1 xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_log_HN1-hana_log /hana/log/HN1 xfs  defaults,nofail  0  2
    ```

   De nieuwe volumes koppelen:

    ```bash
    sudo mount -a
    ```

## <a name="installation"></a>Installatie  

In dit voor beeld voor de implementatie van SAP HANA in scale-out configuratie met HSR op Azure Vm's hebben we HANA 2,0 SP4 gebruikt.  

### <a name="prepare-for-hana-installation"></a>Voor bereiding voor HANA-installatie

1. **[Ah]** Voordat de HANA-installatie wordt geïnstalleerd, stelt u het Hoofdwacht woord in. U kunt het hoofd wachtwoord uitschakelen nadat de installatie is voltooid. Opdracht uitvoeren `root` als `passwd` .  

2. **[1, 2]** Wijzig de machtigingen voor `/hana/shared` 
    ```bash
    chmod 775 /hana/shared
    ```

3. **[1]** Controleer of u zich kunt aanmelden via SSH naar de Hana DB-vm's in deze site **Hana-S1-DB2** en **Hana-S1-db3**, zonder dat u wordt gevraagd om een wacht woord.  
   Als dat niet het geval is, worden de Exchange SSH-sleutels gebruikt, zoals beschreven in een [verificatie op basis van een sleutel](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/s2-ssh-configuration-keypairs).  
    ```bash
    ssh root@hana-s1-db2
    ssh root@hana-s1-db3
    ```

4. **[2]** Controleer of u zich kunt aanmelden via SSH naar de Hana DB-vm's in deze site **Hana-S2-DB2** en **Hana-S2-db3**, zonder dat u wordt gevraagd om een wacht woord.  
   Als dat niet het geval is, worden de Exchange SSH-sleutels gebruikt, zoals beschreven in een [verificatie op basis van een sleutel](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/s2-ssh-configuration-keypairs).  
    ```bash
    ssh root@hana-s2-db2
    ssh root@hana-s2-db3
    ```

5. **[Ah]** Installeer extra pakketten die vereist zijn voor HANA 2,0 SP4. Zie SAP Note [2593824](https://launchpad.support.sap.com/#/notes/2593824) voor RHEL 7 voor meer informatie. 

    ```bash
    # If using RHEL 7
    yum install libgcc_s1 libstdc++6 compat-sap-c++-7 libatomic1
    # If using RHEL 8
    yum install libatomic libtool-ltdl.x86_64
    ```


6. **[A]** de firewall tijdelijk uitschakelen, zodat deze de Hana-installatie niet verstoort. U kunt deze opnieuw inschakelen nadat de HANA-installatie is voltooid. 
    ```bash
    # Execute as root
    systemctl stop firewalld
    systemctl disable firewalld
    ```

### <a name="hana-installation-on-the-first-node-on-each-site"></a>HANA-installatie op het eerste knoop punt op elke site

1. **[1]** installeer SAP Hana door de instructies in de [SAP Hana 2,0-installatie en de update gids](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html)te volgen. In de volgende instructies wordt de SAP HANA-installatie weer gegeven op het eerste knoop punt op SITE 1.   

   a. Start het **hdblcm** -programma `root` vanuit de map Hana-installatie software. Gebruik de `internal_network` para meter en geef de adres ruimte door voor het subnet, dat wordt gebruikt voor de interne Hana-communicatie tussen knoop punten.  

    ```bash
    ./hdblcm --internal_network=10.23.1.128/26
    ```

   b. Voer bij de prompt de volgende waarden in:

     * Voor **Kies een actie**: Voer **1** (voor installatie) in
     * Voor **aanvullende onderdelen voor installatie**: Voer **2, 3** in
     * Voor installatiepad: druk op ENTER (standaard ingesteld op/Hana/Shared)
     * Voor de naam van de **lokale host**: druk op ENTER om de standaard instelling te accepteren
     * Wilt **u hosts toevoegen aan het systeem?**: Voer **n** in
     * Voer voor **SAP Hana systeem-id**: **HN1**
     * Voor **instantie nummer** [00]: Voer **03** in
     * Voor de **lokale host-werk groep** [standaard]: druk op ENTER om de standaard instelling te accepteren
     * Voor **Selecteer systeem gebruik/Voer index [4]**: Voer **4** in (voor aangepast)
     * Voor **locatie van gegevens volumes** [/Hana/data/HN1]: druk op ENTER om de standaard instelling te accepteren
     * Voor de **locatie van de logboek volumes** [/Hana/log/HN1]: druk op ENTER om de standaard instelling te accepteren
     * Voor **maximale geheugen toewijzing beperken?** [n]: Voer **n** in
     * Voor de hostnaam **van het certificaat voor host Hana-S1-db1** [Hana-S1-db1]: druk op ENTER om de standaard waarde te accepteren
     * **Wacht woord voor SAP host agent-gebruiker (sapadm)**: Voer het wacht woord in
     * **Wacht woord voor de SAP host agent gebruiker (sapadm) bevestigen**: Voer het wacht woord in
     * Voor het **wacht woord van de systeem beheerder (hn1adm)**: Voer het wacht woord in
     * Voor de basismap van de **systeem beheerder** [/usr/sap/HN1/Home]: druk op ENTER om de standaard instelling te accepteren
     * Voor de aanmelding van de **systeem beheerder** [/bin/sh]: druk op ENTER om de standaard instelling te accepteren
     * Voor de **gebruikers-id van de systeem beheerder** [1001]: druk op ENTER om de standaard instelling te accepteren
     * Voor de **invoer-ID van de gebruikers groep (sapsys)** [79]: druk op ENTER om de standaard waarde te accepteren
     * Voor **systeem database gebruikers (systeem) wacht woord**: Voer het systeem wachtwoord in
     * Voor het bevestigen van het **wacht woord van de systeem database gebruiker (systeem)**: Voer het systeem wachtwoord in
     * **Systeem opnieuw opstarten na** het opnieuw opstarten van de computer? [n]: Voer **n** in 
     * Voor wilt **u door gaan (j/n)**: de samen vatting valideren en als alles goed lijkt, voert u **y** in

2. **[2]** Herhaal de vorige stap om SAP Hana op het eerste knoop punt op site 2 te installeren.   

3. **[1, 2]** Controleer global.ini  

   Geef global.ini weer en zorg ervoor dat de configuratie voor de interne SAP HANA communicatie tussen knoop punten is ingesteld. Controleer het **communicatie** gedeelte. De adres ruimte van het `inter` subnet moet `listeninterface` worden ingesteld op `.internal` . Controleer de sectie **internal_hostname_resolution** . Deze moet de IP-adressen hebben voor de HANA-virtuele machines die deel uitmaken van het `inter` subnet.  

   ```bash
     sudo cat /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
     # Example from SITE1 
     [communication]
     internal_network = 10.23.1.128/26
     listeninterface = .internal
     [internal_hostname_resolution]
     10.23.1.138 = hana-s1-db1
     10.23.1.139 = hana-s1-db2
     10.23.1.140 = hana-s1-db3
   ```

4. **[1, 2]** voorbereiden op `global.ini` installatie in niet-gedeelde omgeving, zoals beschreven in SAP-opmerking [2080991](https://launchpad.support.sap.com/#/notes/0002080991).  

   ```bash
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    [persistence]
    basepath_shared = no
   ```

4. **[1, 2]** Start SAP Hana opnieuw op om de wijzigingen te activeren.  

   ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem
   ```

6. **[1, 2]** Controleer of de client interface de IP-adressen uit het subnet gebruikt `client` voor communicatie.  

    ```bash
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "password" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result - example from SITE 2
    "hana-s2-db1","net_publicname","10.23.0.14"
   ```

   Zie SAP Note [2183363-configuratie van SAP Hana intern netwerk](https://launchpad.support.sap.com/#/notes/2183363)voor meer informatie over het controleren van de configuratie.  

7. **[Ah]** Wijzig de machtigingen voor de gegevens-en logboek mappen om HANA-installatie fouten te voor komen.  

   ```bash
    sudo chmod o+w -R /hana/data /hana/log
   ```

8. **[1]** Installeer de secundaire Hana-knoop punten. De voorbeeld instructies in deze stap zijn voor SITE 1.  
   a. Start het residente **hdblcm** -programma als `root` .    
    ```bash
     cd /hana/shared/HN1/hdblcm
     ./hdblcm 
    ```

   b. Voer bij de prompt de volgende waarden in:

     * Voor **Kies een actie**: Voer **2** in (voor hosts toevoegen)
     * **Voer door komma's gescheiden host-namen in om toe te voegen**: Hana-S1-DB2, Hana-S1-db3
     * Voor **aanvullende onderdelen voor installatie**: Voer **2, 3** in
     * Voer de naam van de **hoofd gebruikersnaam [root]** in: druk op ENTER om de standaard instelling te accepteren.
     * Voor **geselecteerde rollen voor de host Hana-S1-DB2 [1]**: 1 (voor de werk nemer)
     * Voor het invoeren van een **host-failovergroep voor host Hana-S1-DB2 [standaard]**: druk op ENTER om de standaard instelling te accepteren
     * Voor het invoeren van het **opslag partitie nummer voor de host Hana-S1-DB2 [<<assign automatically>>]**: druk op ENTER om de standaard waarde te accepteren
     * Voor het **invoeren van een werk groep voor de host Hana-S1-DB2 [standaard]**: druk op ENTER om de standaard instelling te accepteren
     * Voor **Select-rollen voor de host Hana-S1-db3 ' [1]**: 1 (voor de werk nemer)
     * Voor het invoeren van een **host-failovergroep voor host ' Hana-S1-db3 ' [standaard]**: druk op ENTER om de standaard instelling te accepteren
     * Voor het invoeren van het **opslag partitie nummer voor de host Hana-S1-db3 [<<assign automatically>>]**: druk op ENTER om de standaard waarde te accepteren
     * Voor het **invoeren van een werknemers groep voor host Hana-S1-db3 ' [standaard]**: druk op ENTER om de standaard instelling te accepteren
     * Voor het **wacht woord van de systeem beheerder (hn1adm)**: Voer het wacht woord in
     * Voer het wacht woord in voor het **sapadm-wacht woord (SAP host agent User)**:
     * **Wacht woord voor de SAP host agent gebruiker (sapadm) bevestigen**: Voer het wacht woord in
     * Voor de hostnaam **van het certificaat voor host Hana-S1-DB2** [Hana-S1-DB2]: druk op ENTER om de standaard instelling te accepteren
     * Voor de hostnaam **van het certificaat voor host Hana-S1-db3** [Hana-S1-db3]: druk op ENTER om de standaard waarde te accepteren
     * Voor wilt **u door gaan (j/n)**: de samen vatting valideren en als alles goed lijkt, voert u **y** in

9. **[2]** Herhaal de vorige stap om de secundaire SAP Hana knooppunten op site 2 te installeren.   

## <a name="configure-sap-hana-20-system-replication"></a>SAP HANA 2,0-systeem replicatie configureren

1. **[1]** systeem replicatie op site 1 configureren:

   Maak een back-up van de data bases als **HN1** adm:

    ```bash
    hdbsql -d SYSTEMDB -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupSYS')"
    hdbsql -d HN1 -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupHN1')"
    ```

   Kopieer de PKI-bestanden van het systeem naar de secundaire site:

    ```bash
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/data/SSFS_HN1.DAT hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/data/
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/key/SSFS_HN1.KEY  hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/key/
    ```

   De primaire site maken:

    ```bash
    hdbnsutil -sr_enable --name=HANA_S1
    ```

2. **[2]** systeem replicatie op site 2 configureren:
    
   Registreer de tweede site om de systeem replicatie te starten. Voer de volgende opdracht uit als <hanasid \> adm:

    ```bash
    sapcontrol -nr 03 -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=hana-s1-db1 --remoteInstance=03 --replicationMode=sync --name=HANA_S2
    sapcontrol -nr 03 -function StartSystem
    ```

3. **[1]** replicatie status controleren

   Controleer de replicatie status en wacht totdat alle data bases zijn gesynchroniseerd.

    ```bash
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    # | Database | Host          | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary     | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
    # |          |               |       |              |           |         |           | Host          | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
    # | -------- | ------------- | ----- | ------------ | --------- | ------- | --------- | ------------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
    # | HN1      | hana-s1-db3   | 30303 | indexserver  |         5 |       1 | HANA_S1   | hana-s2-db3   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | SYSTEMDB | hana-s1-db1   | 30301 | nameserver   |         1 |       1 | HANA_S1   | hana-s2-db1   |     30301 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30307 | xsengine     |         2 |       1 | HANA_S1   | hana-s2-db1   |     30307 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30303 | indexserver  |         3 |       1 | HANA_S1   | hana-s2-db1   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db2   | 30303 | indexserver  |         4 |       1 | HANA_S1   | hana-s2-db2   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    #
    # status system replication site "2": ACTIVE
    # overall system replication status: ACTIVE
    #
    # Local System Replication State
    #   
    # mode: PRIMARY
    # site id: 1
    # site name: HANA_S1
    ```

4. **[1, 2]** Wijzig de Hana-configuratie zodat communicatie voor Hana-systeem replicatie als deze wordt aangegeven door de replicatie van de virtuele netwerk interfaces van Hana-systemen.   
   - HANA stoppen op beide sites
    ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem HDB
    ```

   - Bewerk global.ini om de toewijzing van de host voor HANA-systeem replicatie toe te voegen: gebruik de IP-adressen uit het `hsr` subnet.  
    ```bash
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [system_replication_hostname_resolution]
    10.23.1.202 = hana-s1-db1
    10.23.1.203 = hana-s1-db2
    10.23.1.204 = hana-s1-db3
    10.23.1.205 = hana-s2-db1
    10.23.1.206 = hana-s2-db2
    10.23.1.207 = hana-s2-db3
    ```

   - HANA op beide sites starten
   ```bash
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem HDB
   ```

   Zie voor meer informatie de [naam omzetting van hosts voor systeem replicatie](https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/1.0.12/en-US/c0cba1cb2ba34ec89f45b48b2157ec7b.html).  

5. **[Ah]** Schakel de firewall opnieuw in.  
   - De firewall opnieuw inschakelen
       ```bash
       # Execute as root
       systemctl start firewalld
       systemctl enable firewalld
       ```

   - Open de benodigde firewall poorten. U moet de poorten voor het HANA-exemplaar nummer aanpassen.  

       > [!IMPORTANT]
       > Maak firewall regels om HANA-communicatie tussen knoop punten en client verkeer toe te staan. De vereiste poorten worden vermeld op de [TCP/IP-poorten van alle SAP-producten](https://help.sap.com/viewer/ports). De volgende opdrachten zijn slechts een voor beeld. In dit scenario met het gebruikte systeem nummer 03.

       ```bash
        # Execute as root
        sudo firewall-cmd --zone=public --add-port=30301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30301/tcp
        sudo firewall-cmd --zone=public --add-port=30303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30303/tcp
        sudo firewall-cmd --zone=public --add-port=30306/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30306/tcp
        sudo firewall-cmd --zone=public --add-port=30307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30307/tcp
        sudo firewall-cmd --zone=public --add-port=30313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30313/tcp
        sudo firewall-cmd --zone=public --add-port=30315/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30315/tcp
        sudo firewall-cmd --zone=public --add-port=30317/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30317/tcp
        sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30340/tcp
        sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30341/tcp
        sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30342/tcp
        sudo firewall-cmd --zone=public --add-port=1128/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1128/tcp
        sudo firewall-cmd --zone=public --add-port=1129/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1129/tcp
        sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40302/tcp
        sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40301/tcp
        sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40307/tcp
        sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40303/tcp
        sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40340/tcp
        sudo firewall-cmd --zone=public --add-port=50313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50313/tcp
        sudo firewall-cmd --zone=public --add-port=50314/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50314/tcp
        sudo firewall-cmd --zone=public --add-port=30310/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30310/tcp
        sudo firewall-cmd --zone=public --add-port=30302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30302/tcp
       ```

## <a name="create-a-pacemaker-cluster"></a>Een Pacemaker-cluster maken

Volg de stappen bij het [instellen van pacemaker op Red Hat Enterprise Linux in azure](high-availability-guide-rhel-pacemaker.md) om een basis pacemaker-cluster voor deze Hana-server te maken.
Neem alle virtuele machines op, met inbegrip van de hoofd Maker in het cluster.  

> [!IMPORTANT]
> Stel deze waarde niet `quorum expected-votes` in op 2, omdat dit geen cluster met twee knoop punten is.  
> Zorg ervoor dat de cluster eigenschap `concurrent-fencing`  is ingeschakeld, zodat de knooppunt omheining wordt gedeserialiseerd.   

## <a name="create-file-system-resources"></a>Bestandssysteem bronnen maken

1. **[1, 2]** stop SAP Hana op beide replicatie sites. Voer uit als <sid \> adm.  

    ```bash
    sapcontrol -nr 03 -function StopSystem
    ```

2. **[Ah]** Het ontkoppelen van bestands systeem `/hana/shared` , dat tijdelijk is gekoppeld voor de installatie op alle Hana DB-vm's. U moet alle processen en sessies die gebruikmaken van het bestands systeem, stoppen voordat u de koppeling kunt opheffen. 
 
    ```bash
    umount /hana/shared 
    ```

3. **[1]** Maak de bestandssysteem cluster bronnen voor `/hana/shared` in uitgeschakelde status. De resources worden gemaakt met de optie `--disabled` , omdat u de locatie beperkingen moet definiëren voordat de koppelingen worden ingeschakeld.  

    ```bash
    # /hana/shared file system for site 1
    pcs resource create fs_hana_shared_s1 --disabled ocf:heartbeat:Filesystem device=10.23.1.7:/HN1-shared-s1  directory=/hana/shared \
    fstype=nfs options='defaults,rw,hard,timeo=600,rsize=262144,wsize=262144,proto=tcp,intr,noatime,sec=sys,vers=4.1,lock,_netdev' op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 \
    op start interval=0 timeout=120 op stop interval=0 timeout=120

    # /hana/shared file system for site 2   
    pcs resource create fs_hana_shared_s2 --disabled ocf:heartbeat:Filesystem device=10.23.1.7:/HN1-shared-s1 directory=/hana/shared \
    fstype=nfs options='defaults,rw,hard,timeo=600,rsize=262144,wsize=262144,proto=tcp,intr,noatime,sec=sys,vers=4.1,lock,_netdev' op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 \
    op start interval=0 timeout=120 op stop interval=0 timeout=120

    # clone the /hana/shared file system resources for both site1 and site2
    pcs resource clone fs_hana_shared_s1 meta clone-node-max=1 interleave=true
    pcs resource clone fs_hana_shared_s2 meta clone-node-max=1 interleave=true
    ```
 
   `OCF_CHECK_LEVEL=20` het kenmerk wordt toegevoegd aan de controle bewerking, zodat de bewakings bewerkingen een lees-en schrijf test uitvoeren op het bestands systeem. Zonder dit kenmerk controleert de controle bewerking alleen of het bestands systeem is gekoppeld. Dit kan een probleem zijn omdat de verbinding is verbroken, het bestands systeem mogelijk nog niet toegankelijk is.  

   `on-fail=fence` het kenmerk wordt ook toegevoegd aan de controle bewerking. Als deze optie is ingeschakeld, wordt het knoop punt onmiddellijk geomheiningd als de monitor bewerking mislukt voor een knoop punt. Als deze optie niet is ingesteld, is het standaard gedrag om alle resources te stoppen die afhankelijk zijn van de bron die is mislukt, start u de mislukte resource opnieuw op en start u alle resources die afhankelijk zijn van de bron die is mislukt. Dit gedrag kan niet alleen lang duren wanneer een SAPHana-resource afhankelijk is van de bron die is mislukt, maar kan ook mislukken. De SAPHana-resource kan niet worden gestopt als de NFS-share met de HANA binaire bestanden niet toegankelijk is.  

4. **[1]** de knooppunt kenmerken configureren en verifiëren. Alle SAP HANA DB-knoop punten op replicatie site 1 zijn toegewezen kenmerk `S1` en alle SAP Hana DB-knoop punten op replicatie site 2 zijn kenmerk toegewezen `S2` .  

    ```bash
    # HANA replication site 1
    pcs node attribute hana-s1-db1 NFS_SID_SITE=S1
    pcs node attribute hana-s1-db2 NFS_SID_SITE=S1
    pcs node attribute hana-s1-db3 NFS_SID_SITE=S1
    # HANA replication site 2
    pcs node attribute hana-s2-db1 NFS_SID_SITE=S2
    pcs node attribute hana-s2-db2 NFS_SID_SITE=S2
    pcs node attribute hana-s2-db3 NFS_SID_SITE=S2
    #To verify the attribute assignment to nodes execute
    pcs node attribute
    ```

5. **[1]** de beperkingen configureren die bepalen waar de NFS-bestands systemen worden gekoppeld en de bestands systeem bronnen inschakelen.  
    ```bash
    # Configure the constraints
    pcs constraint location fs_hana_shared_s1-clone rule resource-discovery=never score=-INFINITY NFS_SID_SITE ne S1
    pcs constraint location fs_hana_shared_s2-clone rule resource-discovery=never score=-INFINITY NFS_SID_SITE ne S2
    # Enable the file system resources
    pcs resource enable fs_hana_shared_s1
    pcs resource enable fs_hana_shared_s2
    ```

   Wanneer u de bestands systeem bronnen inschakelt, koppelt het cluster de `/hana/shared` bestands systemen.
 
6. **[Ah]** Controleer of de ANF-volumes zijn gekoppeld onder `/hana/shared` op alle Hana DB-vm's op beide sites.

    ```bash
    sudo nfsstat -m
    # Verify that flag vers is set to 4.1 
    # Example from SITE 1, hana-s1-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s1
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.11,local_lock=none,addr=10.23.1.7
    # Example from SITE 2, hana-s2-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s2
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.14,local_lock=none,addr=10.23.1.7
    ```

7. **[1]** de kenmerk resources worden geconfigureerd. Configureer de beperkingen waarmee de kenmerken worden ingesteld op `true` , als de NFS-koppeling voor `hana/shared` zijn gekoppeld.  

    ```bash
    # Configure the attribure resources
    pcs resource create hana_nfs_s1_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs_s1_active
    pcs resource create hana_nfs_s2_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs_s2_active
    # Clone the attribure resources
    pcs resource clone hana_nfs_s1_active meta clone-node-max=1 interleave=true
    pcs resource clone hana_nfs_s2_active meta clone-node-max=1 interleave=true
    # Configure the constraints, which will set the attribute values
    pcs constraint order fs_hana_shared_s1-clone then hana_nfs_s1_active-clone
    pcs constraint order fs_hana_shared_s2-clone then hana_nfs_s2_active-clone
    ```

   > [!TIP]
   > Als uw configuratie andere bestands systemen bevat, behalve/ `hana/shared` , die gekoppeld zijn aan NFS, neemt u vervolgens `sequential=false` de optie op, zodat er geen volg orde van afhankelijkheden tussen de bestands systemen is. Alle aan NFS gekoppelde bestands systemen moeten vóór de bijbehorende kenmerk resource worden gestart, maar ze hoeven niet in een volg orde te beginnen ten opzichte van elkaar. Zie voor meer informatie [Hoe kan ik SAP HANA Scale-Out HSR configureren in een pacemaker-cluster wanneer de Hana-bestands systemen NFS-shares zijn](https://access.redhat.com/solutions/5423971).  

8. **[1]** plaats pacemaker in onderhouds modus, in voor bereiding voor het maken van de Hana-cluster resources.  
    ```bash
    pcs property set maintenance-mode=true
    ```

## <a name="create-sap-hana-cluster-resources"></a>SAP HANA cluster resources maken

1. **[A]** Installeer de Hana scale-out resource agent op alle cluster knooppunten, met inbegrip van de hoofd Maker.    

    ```bash
    yum install -y resource-agents-sap-hana-scaleout 
    ```


   > [!NOTE]
   > Raadpleeg het [ondersteunings beleid voor RHEL ha-clusters: beheer van SAP Hana in een cluster](https://access.redhat.com/articles/3397471) voor de mini maal ondersteunde versie van het pakket voor de versie van `resource-agents-sap-hana-scaleout` uw besturings systeem.  

2. **[1, 2]** Installeer de Hana "systeem replicatie Hook". De Hook moet worden geïnstalleerd op één HANA DB-knoop punt op elke systeem replicatie site. SAP HANA moet nog steeds actief zijn.        

   1. De Hook voorbereiden als `root` 
    ```bash
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR-ScaleOut/SAPHanaSR.py /hana/shared/myHooks
     chown -R hn1adm:sapsys /hana/shared/myHooks
    ```

   2. Stel `global.ini`
    ```bash
    # add to global.ini
    [ha_dr_provider_SAPHanaSR]
    provider = SAPHanaSR
    path = /hana/shared/myHooks
    execution_order = 1
    
    [trace]
    ha_dr_saphanasr = info
    ```

3. **[Ah]** Het cluster vereist sudo-configuratie op het cluster knooppunt voor <sid \> adm. In dit voor beeld dat wordt bereikt door een nieuw bestand te maken. Voer de opdrachten uit als `root` .    
    ```bash
    cat << EOF > /etc/sudoers.d/20-saphana
    # SAPHanaSR-ScaleOut needs for srHook
     Cmnd_Alias SOK = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SOK -t crm_config -s SAPHanaSR
     Cmnd_Alias SFAIL = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SFAIL -t crm_config -s SAPHanaSR
     hn1adm ALL=(ALL) NOPASSWD: SOK, SFAIL
     EOF
    ```

4. **[1, 2]** Start SAP Hana op beide replicatie sites. Voer uit als <sid \> adm.  

    ```bash
    sapcontrol -nr 03 -function StartSystem 
    ```

5. **[1]** Controleer de Hook-installatie. Voer uit als <sid \> adm op de actieve Hana-systeem replicatie site.   

    ```bash
    cdtrace
     awk '/ha_dr_SAPHanaSR.*crm_attribute/ \
     { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*

     # Example entries
     # 2020-07-21 22:04:32.364379 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:46.905661 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:52.092016 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:52.782774 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:53.117492 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:06:35.599324 ha_dr_SAPHanaSR SOK
    ```

6. **[1]** de Hana-cluster resources maken. Voer de volgende opdrachten uit als `root` .    
   1. Zorg ervoor dat het cluster al een onderhouds modus heeft.  
    
   2. Maak vervolgens de bron HANA-topologie.  
      Als u RHEL **7. x** -cluster bouwt, gebruikt u de volgende opdrachten:  
      ```bash
      pcs resource create SAPHanaTopology_HN1_HDB03 SAPHanaTopologyScaleOut \
       SID=HN1 InstanceNumber=03 \
       op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600

      pcs resource clone SAPHanaTopology_HN1_HDB03 meta clone-node-max=1 interleave=true
      ```

      Als u RHEL **8. x** -cluster bouwt, gebruikt u de volgende opdrachten:  
      ```bash
      pcs resource create SAPHanaTopology_HN1_HDB03 SAPHanaTopology \
       SID=HN1 InstanceNumber=03 meta clone-node-max=1 interleave=true \
       op methods interval=0s timeout=5 \
       op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600

      pcs resource clone SAPHanaTopology_HN1_HDB03 meta clone-node-max=1 interleave=true
      ```

   3. Maak vervolgens de bron HANA-instantie.  
      > [!NOTE]
      > Dit artikel bevat verwijzingen naar de term *Slave*, een term die door micro soft niet meer wordt gebruikt. Zodra de term uit de software wordt verwijderd, verwijderen we deze uit dit artikel.  
 
      Als u RHEL **7. x** -cluster bouwt, gebruikt u de volgende opdrachten:    
      ```bash
      pcs resource create SAPHana_HN1_HDB03 SAPHanaController \
       SID=HN1 InstanceNumber=03 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
       op start interval=0 timeout=3600 op stop interval=0 timeout=3600 op promote interval=0 timeout=3600 \
       op monitor interval=60 role="Master" timeout=700 op monitor interval=61 role="Slave" timeout=700
     
      pcs resource master msl_SAPHana_HN1_HDB03 SAPHana_HN1_HDB03 \
       meta master-max="1" clone-node-max=1 interleave=true
      ```

      Als u RHEL **8. x** -cluster bouwt, gebruikt u de volgende opdrachten:  
      ```bash
      pcs resource create SAPHana_HN1_HDB03 SAPHanaController \
       SID=HN1 InstanceNumber=03 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
       op demote interval=0s timeout=320 op methods interval=0s timeout=5 \
       op start interval=0 timeout=3600 op stop interval=0 timeout=3600 op promote interval=0 timeout=3600 \
       op monitor interval=60 role="Master" timeout=700 op monitor interval=61 role="Slave" timeout=700
     
      pcs resource promotable SAPHana_HN1_HDB03 \
       meta master-max="1" clone-node-max=1 interleave=true
      ```
      > [!IMPORTANT]
      > U wordt aangeraden als best practice die u alleen AUTOMATED_REGISTER instelt op **Nee**, terwijl u uitgebreide failover-tests uitvoert, om te voor komen dat een niet-geslaagd primair exemplaar automatisch als secundair wordt geregistreerd. Zodra de failover-tests zijn voltooid, stelt u AUTOMATED_REGISTER in op **Ja**, zodat de replicatie van het overname systeem automatisch kan worden hervat. 

   4. Virtuele IP-adressen en gekoppelde resources maken.  
      ```bash
      pcs resource create vip_HN1_03 ocf:heartbeat:IPaddr2 ip=10.23.0.18 op monitor interval="10s" timeout="20s"
      sudo pcs resource create nc_HN1_03 azure-lb port=62503
      sudo pcs resource group add g_ip_HN1_03 nc_HN1_03 vip_HN1_03
      ```

   5. De cluster beperkingen maken  
      Als u RHEL **7. x** -cluster bouwt, gebruikt u de volgende opdrachten:  
      ```bash
      #Start HANA topology, before the HANA instance
      pcs constraint order SAPHanaTopology_HN1_HDB03-clone then msl_SAPHana_HN1_HDB03

      pcs constraint colocation add g_ip_HN1_03 with master msl_SAPHana_HN1_HDB03 4000
      #HANA resources are only allowed to run on a node, if the node's NFS file systems are mounted. The constraint also avoids the majority maker node
      pcs constraint location SAPHanaTopology_HN1_HDB03-clone rule resource-discovery=never score=-INFINITY hana_nfs_s1_active ne true and hana_nfs_s2_active ne true
      ```
 
      Als u RHEL **8. x** -cluster bouwt, gebruikt u de volgende opdrachten:  
      ```bash
      #Start HANA topology, before the HANA instance
      pcs constraint order SAPHanaTopology_HN1_HDB03-clone then SAPHana_HN1_HDB03-clone

      pcs constraint colocation add g_ip_HN1_03 with master SAPHana_HN1_HDB03-clone 4000
      #HANA resources are only allowed to run on a node, if the node's NFS file systems are mounted. The constraint also avoids the majority maker node
      pcs constraint location SAPHanaTopology_HN1_HDB03-clone rule resource-discovery=never score=-INFINITY hana_nfs_s1_active ne true and hana_nfs_s2_active ne true
      ```

7. **[1]** het cluster uit de onderhouds modus plaatsen. Zorg ervoor dat de cluster status OK is en dat alle resources worden gestart.  
    ```bash
    sudo pcs property set maintenance-mode=false
    #If there are failed cluster resources, you may need to run the next command
    pcs resource cleanup
    ```
  
   > [!NOTE]
   > De time-outs in de bovenstaande configuratie zijn slechts voor beelden en moeten mogelijk worden aangepast aan de specifieke HANA-instellingen. Het is bijvoorbeeld mogelijk dat u de time-out voor starten moet verhogen als u de SAP HANA data base langer wilt starten.
  
## <a name="test-sap-hana-failover"></a>SAP HANA failover testen 

1. Controleer voordat u een test start het cluster en de replicatie status van het SAP HANA systeem.  

   a. Controleren of er geen mislukte cluster acties zijn  
     ```bash
     #Verify that there are no failed cluster actions
     pcs status
     # Example
     #Stack: corosync
     #Current DC: hana-s-mm (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
     #Last updated: Thu Sep 24 06:00:20 2020
     #Last change: Thu Sep 24 05:59:17 2020 by root via crm_attribute on hana-s1-db1
     #
     #7 nodes configured
     #45 resources configured
     #
     #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #
     #Active resources:
     #
     #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
     #Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
     #    Started: [ hana--s1-db1 hana-s1-db2 hana-s1-db3 ]
     #Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
     #    Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
     #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
     #Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
     #    Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
     #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
     #    Masters: [ hana-s1-db1 ]
     #    Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Resource Group: g_ip_HN1_03
     #    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s1-db1
     #    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
     ```

   b. Controleren of SAP HANA systeem replicatie is gesynchroniseerd

      ```bash
      # Verify HANA HSR is in sync
      sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
      #| Database | Host        | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary     | Secondary| Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
      #|          |             |       |              |           |         |           | Host          | Port     | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
      #| -------- | ----------- | ----- | ------------ | --------- | ------- | --------- | ------------- | -------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
      #| HN1      | hana-s1-db3 | 30303 | indexserver  |         5 |       2 | HANA_S1   | hana-s2-db3 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db2 | 30303 | indexserver  |         4 |       2 | HANA_S1   | hana-s2-db2 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |  
      #| SYSTEMDB | hana-s1-db1 | 30301 | nameserver   |         1 |       2 | HANA_S1   | hana-s2-db1 |     30301  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db1 | 30307 | xsengine     |         2 |       2 | HANA_S1   | hana-s2-db1 |     30307  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db1 | 30303 | indexserver  |         3 |       2 | HANA_S1   | hana-s2-db1 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |

      #status system replication site "1": ACTIVE
      #overall system replication status: ACTIVE

      #Local System Replication State
      #~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

      #mode: PRIMARY
      #site id: 1
      #site name: HANA_S1
      ```

2. Controleer de cluster configuratie voor een fout scenario, wanneer een knoop punt de toegang tot de NFS-share ( `/hana/shared` ) verliest.  

   De SAP HANA resource agents zijn afhankelijk van binaire bestanden, die zijn opgeslagen op `/hana/shared` om bewerkingen uit te voeren tijdens een failover. Het bestands systeem `/hana/shared` wordt gekoppeld via NFS in de weer gegeven configuratie. Een test die kan worden uitgevoerd, is het opnieuw koppelen van het `/hana/shared` Bestands systeem als *alleen-lezen*. Deze methode valideert dat er een failover wordt uitgevoerd voor het cluster als de toegang tot `/hana/shared` de site van de actieve systeem replicatie verloren gaat.  

   **Verwacht resultaat**: wanneer u opnieuw koppelt `/hana/shared` als *alleen-lezen*, mislukt de bewakings bewerking die een lees-en schrijf bewerking uitvoert op bestands systeem, omdat deze niet kan schrijven naar het bestands systeem en de Hana-resource-failover activeert. Hetzelfde resultaat wordt verwacht wanneer het HANA-knoop punt de toegang tot de NFS-share verliest.  
     
   U kunt de status van de cluster bronnen controleren door in of uit te voeren `crm_mon` `pcs status` . Resource status voordat u begint met testen:
      ```bash
      # Output of crm_mon
      #7 nodes configured
      #45 resources configured

      #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      #
      #Active resources:

      #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
      # Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
      #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
      #     Masters: [ hana-s1-db1 ]
      #     Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Resource Group: g_ip_HN1_03
      #     nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s1-db1
      #     vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
      ```

   Voer de volgende opdracht uit als u storingen wilt simuleren `/hana/shared` op een van de primaire replicatie site-vm's:
      ```bash
      # Execute as root 
      mount -o ro /hana/shared
      # Or if the above command returns an error
      sudo mount -o ro 10.23.1.7/HN1-shared-s1 /hana/shared
      ```
  
   De HANA-VM, die de toegang tot heeft verloren om opnieuw op te `/hana/shared` starten of te stoppen, afhankelijk van de cluster configuratie. De cluster bronnen worden gemigreerd naar de andere HANA-systeem replicatie site.  
         
   Als het cluster niet is gestart op de virtuele machine, die opnieuw is opgestart, start u het cluster door het volgende uit te voeren: 

      ```bash
      # Start the cluster 
      pcs cluster start
      ```
   
   Wanneer het cluster wordt gestart, wordt het bestands systeem `/hana/shared` automatisch gekoppeld.     
   Als u AUTOMATED_REGISTER = False instelt, moet u SAP HANA systeem replicatie op de secundaire site configureren. In dit geval kunt u deze opdrachten uitvoeren om SAP HANA als secundair opnieuw te configureren.   

      ```bash
      # Execute on the secondary 
      su - hn1adm
      # Make sure HANA is not running on the secondary site. If it is started, stop HANA
      sapcontrol -nr 03 -function StopWait 600 10
      # Register the HANA secondary site
      hdbnsutil -sr_register --name=HANA_S1 --remoteHost=hana-s2-db1 --remoteInstance=03 --replicationMode=sync
      # Switch back to root and cleanup failed resources
      pcs resource cleanup SAPHana_HN1_HDB03
      ```

   De status van de resources na de test: 

      ```bash
      # Output of crm_mon
      #7 nodes configured
      #45 resources configured
    
      #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      
      #Active resources:
    
      #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
      # Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
      #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
      #     Masters: [ hana-s2-db1 ]
      #     Slaves: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db2 hana-s2-db3 ]
      # Resource Group: g_ip_HN1_03
      #     nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s2-db1
      #     vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s2-db1
      ```


We raden u aan om de SAP HANA-cluster configuratie grondig te testen door ook de tests uit te voeren, zoals beschreven in [ha voor SAP Hana op Azure-vm's op RHEL](./sap-hana-high-availability-rhel.md#test-the-cluster-setup).


## <a name="next-steps"></a>Volgende stappen

* [Azure Virtual Machines planning en implementatie voor SAP][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP][dbms-guide]
* [NFS v4.1-volumes in Azure NetApp Files voor SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-netapp)
* Zie [hoge Beschik baarheid van SAP Hana op azure virtual machines (vm's)][sap-hana-ha]voor meer informatie over het instellen van hoge Beschik baarheid en het plannen van nood herstel van SAP Hana op Azure-vm's.