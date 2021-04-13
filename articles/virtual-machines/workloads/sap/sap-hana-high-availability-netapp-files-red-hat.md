---
title: Hoge beschikbaarheid van SAP HANA omhoog schalen met ANF op RHEL-| Microsoft Docs
description: Hoge beschikbaarheid van SAP HANA met ANF op virtuele Azure-machines (VM's).
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/12/2021
ms.author: radeltch
ms.openlocfilehash: 774344c4215088482b110de91f8951bae4a41d25
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365821"
---
# <a name="high-availability-of-sap-hana-scale-up-with-azure-netapp-files-on-red-hat-enterprise-linux"></a>Hoge beschikbaarheid van SAP HANA omhoog schalen met Azure NetApp Files op Red Hat Enterprise Linux

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

In dit artikel wordt beschreven hoe u SAP HANA-systeemreplicatie configureert in scale-upimplementatie, wanneer de HANA-bestandssystemen via NFS worden bevestigd met behulp van Azure NetApp Files (ANF). In de voorbeeldconfiguraties en installatieopdrachten worden **exemplaarnummer 03** en HANA-systeem-id **HN1** gebruikt. SAP HANA replicatie bestaat uit één primair knooppunt en ten minste één secundair knooppunt.

Wanneer stappen in dit document worden gemarkeerd met de volgende voorvoegsels, is de betekenis als volgt:

- **[A]**: De stap is van toepassing op alle knooppunten
- **[1]**: De stap is alleen van toepassing op knooppunt1
- **[2]**: De stap is alleen van toepassing op knooppunt2
 
Lees eerst de volgende SAP-opmerkingen en -documenten:

- SAP Note [1928533,](https://launchpad.support.sap.com/#/notes/1928533)met:
    - De lijst met Azure VM-grootten die worden ondersteund voor de implementatie van SAP-software.
    - Belangrijke capaciteitsinformatie voor Azure VM-grootten.
    - De ondersteunde combinaties van SAP-software en besturingssysteem (OS) en database.
    - De vereiste SAP-kernelversie voor Windows en Linux op Microsoft Azure.
- SAP Note [2015553 bevat](https://launchpad.support.sap.com/#/notes/2015553) een lijst met vereisten voor SAP-ondersteunde SAP-software-implementaties in Azure.
- SAP Note [405827 bevat](https://launchpad.support.sap.com/#/notes/405827) een lijst met aanbevolen bestandssysteem voor de HANA-omgeving.
- SAP Note [2002167 heeft](https://launchpad.support.sap.com/#/notes/2002167) aanbevolen besturingssysteeminstellingen voor Red Hat Enterprise Linux.
- SAP Note [2009879 heeft](https://launchpad.support.sap.com/#/notes/2009879) SAP HANA Richtlijnen voor Red Hat Enterprise Linux.
- SAP Note [2178632 heeft](https://launchpad.support.sap.com/#/notes/2178632) gedetailleerde informatie over alle controlemetrieken die zijn gerapporteerd voor SAP in Azure.
- SAP Note [2191498](https://launchpad.support.sap.com/#/notes/2191498) heeft de vereiste versie van de SAP-hostagent voor Linux in Azure.
- SAP Note [2243692 heeft](https://launchpad.support.sap.com/#/notes/2243692) informatie over SAP-licenties op Linux in Azure.
- SAP Note [1999351 heeft](https://launchpad.support.sap.com/#/notes/1999351) aanvullende informatie over probleemoplossing voor de Verbeterde bewakingsextensie van Azure voor SAP.
- [SAP Community Wiki heeft](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) alle vereiste SAP-notities voor Linux.
- [Azure Virtual Machines en implementatie voor SAP on Linux][planning-guide]
- [Azure Virtual Machines-implementatie voor SAP op Linux][deployment-guide]
- [Azure Virtual Machines DBMS-implementatie voor SAP op Linux][dbms-guide]
- [SAP HANA systeemreplicatie in het Pacemaker-cluster.](https://access.redhat.com/articles/3004101)
- Algemene RHEL-documentatie
    - [Overzicht van Add-On hoge beschikbaarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
    - [Beheer van Add-On hoge beschikbaarheid.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
    - [Naslag voor Add-On beschikbaarheid.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
    - [Systeem SAP HANA replicatie configureren in Scale-Up Pacemaker-cluster wanneer de HANA-bestandssystemen zich op NFS-shares](https://access.redhat.com/solutions/5156571)
- Azure-specifieke RHEL-documentatie:
    - [Ondersteuningsbeleid voor RHEL-clusters met hoge beschikbaarheid : Microsoft Azure Virtual Machines clusterleden.](https://access.redhat.com/articles/3131341)
    - [Installeren en configureren van een Red Hat Enterprise Linux 7.4 (en hoger) High-Availability cluster op Microsoft Azure.](https://access.redhat.com/articles/3252491)
    - [Installeer SAP HANA op Red Hat Enterprise Linux voor gebruik in Microsoft Azure.](https://access.redhat.com/solutions/3193782)
    - [Configureer SAP HANA omhoog schalen van het Pacemaker-cluster wanneer de HANA-bestandssystemen zich op NFS-shares](https://access.redhat.com/solutions/5156571)
- [NetApp SAP-toepassingen op Microsoft Azure met Azure NetApp Files](https://www.netapp.com/us/media/tr-4746.pdf)
- [NFS v4.1-volumes in Azure NetApp Files voor SAP HANA](./hana-vm-operations-netapp.md)

## <a name="overview"></a>Overzicht

Traditioneel in een opschaalomgeving worden alle bestandssystemen voor SAP HANA vanuit lokale opslag. Het instellen van hoge beschikbaarheid van SAP HANA-systeemreplicatie op Red Hat Enterprise Linux is gepubliceerd in de handleiding [SAP HANA-systeemreplicatie instellen op RHEL](./sap-hana-high-availability-rhel.md)

Om een hoge beschikbaarheid SAP HANA van een scale-upsysteem op [Azure NetApp Files](../../../azure-netapp-files/index.yml) NFS-shares te bereiken, hebben we een extra resourceconfiguratie in het cluster nodig, zodat HANA-resources kunnen worden hersteld wanneer één knooppunt de toegang tot de NFS-shares op ANF verliest.  Het cluster beheert de NFS-mounts, zodat het de status van de resources kan bewaken. De afhankelijkheden tussen de bestandssysteem mounts en de SAP HANA resources worden afgedwongen.  

![SAP HANA omhoog schalen op ANF](./media/sap-hana-high-availability-rhel/sap-hana-scale-up-netapp-files-red-hat.png)

SAP HANA worden op elk knooppunt Azure NetApp Files aan NFS-shares. Bestandssystemen /hana/data, /hana/log en /hana/shared zijn uniek voor elk knooppunt. 

Gemonteerd op knooppunt1 (**handtekeningadb1**)

- 10.32.2.4:/**handtekeningadb1**-data-mnt00001 op /hana/data
- 10.32.2.4:/**handtekeningadb1**-log-mnt00001 op /hana/log
- 10.32.2.4:/**handtekeningadb1**-shared-mnt00001 op /hana/shared

Gemonteerd op knooppunt2 (**handtekeningadb2**)

- 10.32.2.4:/**handtekeningadb2**-data-mnt00001 op /hana/data
- 10.32.2.4:/**handtekeningadb2**-log-mnt00001 op /hana/log
- 10.32.2.4:/**handtekeningadb2**-shared-mnt00001 op /hana/shared

> [!NOTE]
> Bestandssystemen /hana/shared, /hana/data en /hana/log worden niet gedeeld tussen de twee knooppunten. Elk clusterknooppunt heeft zijn eigen, afzonderlijke bestandssystemen.   

De configuratie SAP HANA systeemreplicatie maakt gebruik van een toegewezen virtuele hostnaam en virtuele IP-adressen. In Azure is een load balancer vereist voor het gebruik van een virtueel IP-adres. In de volgende lijst ziet u de configuratie van de load balancer:

- Front-endconfiguratie: IP-adres 10.32.0.10 voor hn1-db
- Back-endconfiguratie: verbonden met primaire netwerkinterfaces van alle virtuele machines die deel moeten uitmaken van HANA-systeemreplicatie
- Testpoort: poort 62503
- Taakverdelingsregels: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP (als u Basic Azure Load Balancer gebruikt)  

## <a name="set-up-the-azure-netapp-file-infrastructure"></a>De Azure NetApp-bestandsinfrastructuur instellen

Voordat u verdergaat met het instellen van Azure NetApp Files infrastructuur, moet u zich vertrouwd maken met de Documentatie voor Azure [NetApp Files.](../../../azure-netapp-files/index.yml)

Azure NetApp Files is beschikbaar in verschillende [Azure-regio's.](https://azure.microsoft.com/global-infrastructure/services/?products=netapp) Controleer of uw geselecteerde Azure-regio een Azure NetApp Files.

Zie Beschikbaarheid per Azure Azure NetApp Files regio voor meer informatie over Azure NetApp Files [beschikbaarheid per Azure-regio.](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)

Voordat u een Azure NetApp Files implementeert, vraagt u onboarding aan bij Azure NetApp Files door naar [Registreren voor Azure NetApp Files te gaan.](../../../azure-netapp-files/azure-netapp-files-register.md)

### <a name="deploy-azure-netapp-files-resources"></a>Resources Azure NetApp Files implementeren

In de volgende instructies wordt ervan uit gegaan dat u uw virtuele [Azure-netwerk al hebt geïmplementeerd.](../../../virtual-network/virtual-networks-overview.md) De Azure NetApp Files resources en VM's, waar de Azure NetApp Files-resources worden aangesloten, moeten worden geïmplementeerd in hetzelfde virtuele Azure-netwerk of in virtuele Peered Azure-netwerken.

1. Als u de resources nog niet hebt geïmplementeerd, vraagt u [onboarding aan bij Azure NetApp Files.](../../../azure-netapp-files/azure-netapp-files-register.md)

2. Maak een NetApp-account in de geselecteerde Azure-regio door de instructies in [Een NetApp-account maken te volgen.](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)

3.  Stel een Azure NetApp Files-capaciteitspool in door de instructies te volgen in [Set up an Azure NetApp Files capacity pool (Een Azure NetApp Files-capaciteitspool instellen).](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)

    De HANA-architectuur die in dit artikel wordt gepresenteerd, maakt gebruik van één Azure NetApp Files capaciteitspool op *Ultra* Service-niveau. Voor HANA-workloads in Azure wordt u aangeraden een Azure NetApp Files *Ultra-* of *Premium-serviceniveau* [te gebruiken.](../../../azure-netapp-files/azure-netapp-files-service-levels.md)

4.  Delegeer een subnet naar Azure NetApp Files, zoals beschreven in de instructies in [Een subnet delegeren aan Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).

5.  Implementeer Azure NetApp Files volumes door de instructies te volgen in [Een NFS-volume maken voor Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md).

    Wanneer u de volumes implementeert, moet u de versie NFSv4.1 selecteren. Implementeer de volumes in het Azure NetApp Files subnet. De IP-adressen van de Azure NetApp-volumes worden automatisch toegewezen.

    Houd er rekening mee dat de Azure NetApp Files en de Azure-VM's zich in hetzelfde virtuele Azure-netwerk of in peered virtuele Azure-netwerken moeten hebben. Als bijvoorbeeld de volumesadb1-data-mnt00001, nuadb1-log-mnt00001 zijn, zijn de volumenamen en nfs://10.32.2.4/hanadb1-data-mnt00001, nfs://10.32.2.4/hanadb1-log-mnt00001, en meer, de bestandspaden voor de Azure NetApp Files-volumes.
    
    Op **basis vanadb1**

    - Volume volumeadb1-data-mnt00001 (nfs://10.32.2.4:/hanadb1-data-mnt00001) 
    - Volume volumeadb1-log-mnt00001 (nfs://10.32.2.4:/hanadb1-log-mnt00001)
    - Volumeadb1-shared-mnt00001 (nfs://10.32.2.4:/hanadb1-shared-mnt00001)
    
    Op **basis vanadb2**

    - Volumeadb2-data-mnt00001 (nfs://10.32.2.4:/hanadb2-data-mnt00001)
    - Volumeadb2-log-mnt00001 (nfs://10.32.2.4:/hanadb2-log-mnt00001)
    - Volumeadb2-shared-mnt00001 (nfs://10.32.2.4:/hanadb2-shared-mnt00001)

### <a name="important-considerations"></a>Belangrijke overwegingen

Wanneer u uw Azure NetApp Files voor SAP HANA scale-upsystemen maakt, moet u rekening houden met het volgende:

- De minimale capaciteitspool is 4 tebibyte (TiB).
- De minimale volumegrootte is 100 gibibyte (GiB).
- Azure NetApp Files en alle virtuele machines waarop de Azure NetApp Files-volumes worden aangesloten, moeten zich in hetzelfde virtuele Azure-netwerk of in [peered](../../../virtual-network/virtual-network-peering-overview.md) virtuele netwerken in dezelfde regio.
- Het geselecteerde virtuele netwerk moet een subnet hebben dat wordt gedelegeerd aan Azure NetApp Files.
- De doorvoer van een Azure NetApp Files volume is een functie van het volumequotum en serviceniveau, zoals beschreven in [Serviceniveau voor Azure NetApp Files.](../../../azure-netapp-files/azure-netapp-files-service-levels.md) Wanneer u de HANA Azure NetApp-volumes wilt indelen, moet u ervoor zorgen dat de resulterende doorvoer voldoet aan de HANA-systeemvereisten.
- Met het Azure NetApp Files [exportbeleid](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)kunt u de toegestane clients, het toegangstype (lezen/schrijven, alleen-lezen, etc.) bepalen.
- De Azure NetApp Files functie is nog niet zonebewust. Op dit moment is de functie niet geïmplementeerd in alle beschikbaarheidszones in een Azure-regio. Let op de mogelijke gevolgen voor latentie in sommige Azure-regio's.

> [!IMPORTANT]
> Voor SAP HANA workloads is lage latentie essentieel. Werk samen met uw Microsoft-vertegenwoordiger om ervoor te zorgen dat de virtuele machines en Azure NetApp Files volumes in nabijheid worden geïmplementeerd.

### <a name="sizing-of-hana-database-on-azure-netapp-files"></a>Het formaat van de HANA-database op Azure NetApp Files

De doorvoer van een Azure NetApp Files volume is een functie van de volumegrootte en het serviceniveau, zoals beschreven in [Serviceniveau voor Azure NetApp Files.](../../../azure-netapp-files/azure-netapp-files-service-levels.md)

Wanneer u de infrastructuur voor SAP in Azure ontwerpt, moet u rekening houden met enkele minimale opslagvereisten van SAP. Deze worden omgezet in minimale doorvoerkenmerken:

- Lezen/schrijven in /hana/log van 250 MB/s (MB/s) met I/O-grootten van 1 MB.
- Leesactiviteit van ten minste 400 MB/s voor /hana/data voor I/O-grootten van 16 MB en 64 MB.
- Schrijfactiviteit van ten minste 250 MB/s voor /hana/gegevens met een I/O-grootte van 16 MB en 64 MB.

De [Azure NetApp Files doorvoerlimieten](../../../azure-netapp-files/azure-netapp-files-service-levels.md) per 1 TiB volumequotum zijn:

- Premium Storage -64 MiB/s.
- Ultra Storage-laag - 128 MiB/s.

Om te voldoen aan de sap minimale doorvoervereisten voor /hana/data en /hana/log, en de richtlijnen voor /hana/shared, zijn de aanbevolen grootten:

|    Volume    | Grootte van Premium Storage laag | Grootte van Ultra Storage-laag | Ondersteund NFS-protocol |
| :----------: | :--------------------------: | :------------------------: | :--------------------: |
|  /hana/log   |            4 TiB             |           2 TiB            |          v4.1          |
|  /hana/data  |           6,3 TiB            |          3,2 TiB           |          v4.1          |
| /hana/shared |           1 x RAM            |          1 x RAM           |          v3 of v4.1    |


> [!NOTE]
> De Azure NetApp Files die hier worden vermeld, zijn bedoeld om te voldoen aan de minimale vereisten die SAP aanbeveelt voor hun infrastructuurproviders. In echte klantimplementaties en workloadscenario's zijn deze grootten mogelijk niet voldoende. Gebruik deze aanbevelingen als uitgangspunt en pas u aan op basis van de vereisten van uw specifieke workload.

> [!TIP]
> U kunt de Azure NetApp Files dynamisch, zonder de volumes  te ontkoppelen, de virtuele machines te stoppen of SAP HANA. Deze aanpak biedt flexibiliteit om te voldoen aan zowel de verwachte als onvoorziene doorvoervraag van uw toepassing.

> [!NOTE]
> Alle opdrachten voor het aan/hana/shared in dit artikel worden weergegeven voor NFSv4.1 /hana/shared volumes.
> Als u de volumes /hana/shared hebt geïmplementeerd als NFSv3-volumes, vergeet dan niet om de mount-opdrachten voor /hana/shared voor NFSv3 aan te passen.


## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>Virtuele Linux-machine implementeren via Azure Portal 

Eerst moet u de Azure NetApp Files maken. Ga vervolgens als volgt te werk:

1.  Een resourcegroep maken.
2.  Maak een virtueel netwerk.
3.  Maak een beschikbaarheidsset. Stel het maximale updatedomein in.
4.  Maak een load balancer (intern). We raden standaard load balancer.
    Selecteer het virtuele netwerk dat u in stap 2 hebt gemaakt.
5.  Maak virtuele machine 1 (**;adb1**). 
6.  Virtuele machine 2 maken (**virtualadb2**).  
7.  Tijdens het maken van een virtuele machine voegen we geen schijf toe, omdat al onze bevestigingspunten zich op NFS-shares van Azure NetApp Files. 

> [!IMPORTANT]
> Zwevend IP wordt niet ondersteund op een secundaire IP-configuratie van een NIC in taakverdelingsscenario's. Zie Beperkingen voor [Azure Load Balancer voor meer informatie.](../../../load-balancer/load-balancer-multivip-overview.md#limitations) Als u een extra IP-adres nodig hebt voor de VM, implementeert u een tweede NIC.    

> [!NOTE] 
> Wanneer VM's zonder openbare IP-adressen in de back-endpool van interne (geen openbaar IP-adres) Standard Azure load balancer worden geplaatst, is er geen uitgaande internetverbinding, tenzij er aanvullende configuratie wordt uitgevoerd om routering naar openbare eindpunten toe te staan. Zie Public [endpoint connectivity for Virtual Machines using Azure Standard Load Balancer in SAP high-availability scenarios](./high-availability-guide-standard-load-balancer-outbound-connections.md)(Openbare eindpuntconnectiviteit voor Virtual Machines met behulp van Azure Standard Load Balancer in SAP-scenario's met hoge beschikbaarheid) voor meer informatie over het bereiken van uitgaande connectiviteit.

8.  Als u standard load balancer gebruikt, volgt u deze configuratiestappen:
    1.  Maak eerst een front-end-IP-adresgroep:
        1.  Open de load balancer, selecteer **front-end-IP-adresgroep** en selecteer **Toevoegen.**
        1.  Voer de naam in van de nieuwe front-end-IP-adresgroep (bijvoorbeeld **hana-frontend**).
        1.  Stel Toewijzing **in** op **Statisch** en voer het IP-adres in (bijvoorbeeld **10.32.0.10).**
        1.  Selecteer **OK**.
        1.  Nadat de nieuwe front-end-IP-adresgroep is gemaakt, noteer het IP-adres van de groep.
    1.  Maak vervolgens een back-endpool:
        1.  Open de load balancer, **selecteer back-endpools** en selecteer **Toevoegen.**
        1.  Voer de naam van de nieuwe back-endpool in (bijvoorbeeld **hana-backend).**
        1.  Selecteer **Een virtuele machine toevoegen.**
        1.  Selecteer ** Virtuele machine**.
        1.  Selecteer de virtuele machines van het SAP HANA cluster en hun IP-adressen.
        1.  Selecteer **Toevoegen**.
    1.  Maak vervolgens een statustest:
        1.  Open het load balancer, **selecteer statustests** en selecteer **Toevoegen.**
        1.  Voer de naam van de nieuwe statustest in (bijvoorbeeld **hana-hp**).
        1.  Selecteer TCP als protocol en poort 625 **03.** Houd de **waarde Interval** ingesteld op 5 en de drempelwaarde **Voor** onjuiste status ingesteld op 2.
        1.  Selecteer **OK**.
    1.  Maak vervolgens de taakverdelingsregels:
        1.  Open het load balancer, selecteer **Taakverdelingsregels** en selecteer **Toevoegen.**
        1.  Voer de naam van de nieuwe load balancer in (bijvoorbeeld **hana-lb**).
        1.  Selecteer het front-end-IP-adres, de back-endpool en de statustest die u eerder hebt gemaakt (bijvoorbeeld **hana-frontend,** **hana-backend** en **hana-hp).**
        1.  Selecteer **HA-poorten.**
        1.  Zorg ervoor dat **zwevend IP-adres is ingeschakeld.**
        1.  Selecteer **OK**.


9. Als uw scenario het gebruik van basisgegevens load balancer, volgt u deze configuratiestappen:
    1.  Configureer de load balancer. Maak eerst een front-end-IP-adresgroep:
        1.  Open de load balancer, selecteer **front-end-IP-adresgroep** en selecteer **Toevoegen.**
        1.  Voer de naam van de nieuwe front-end-IP-adresgroep in (bijvoorbeeld **hana-frontend).**
        1.  Stel toewijzing **in** op **Statisch** en voer het IP-adres in (bijvoorbeeld **10.32.0.10).**
        1.  Selecteer **OK**.
        1.  Nadat de nieuwe front-end-IP-adresgroep is gemaakt, noteer het IP-adres van de groep.
    1.  Maak vervolgens een back-endpool:
        1.  Open de load balancer, **selecteer back-endpools** en selecteer **Toevoegen.**
        1.  Voer de naam van de nieuwe back-endpool in (bijvoorbeeld **hana-backend).**
        1.  Selecteer **Een virtuele machine toevoegen.**
        1.  Selecteer de beschikbaarheidsset die u in stap 3 hebt gemaakt.
        1.  Selecteer de virtuele machines van het SAP HANA cluster.
        1.  Selecteer **OK**.
    1.  Maak vervolgens een statustest:
        1.  Open de load balancer, selecteer **statustests** en selecteer **Toevoegen.**
        1.  Voer de naam van de nieuwe statustest in (bijvoorbeeld **hana-hp**).
        1.  Selecteer **TCP** als protocol en poort 625 **03.** Houd de **waarde Interval** ingesteld op 5 en de drempelwaarde **Voor** onjuiste status ingesteld op 2.
        1.  Selecteer **OK**.
    1.  Maak SAP HANA 1.0 de taakverdelingsregels:
        1.  Open de load balancer, selecteer **taakverdelingsregels** en selecteer **Toevoegen.**
        1.  Voer de naam van de nieuwe load balancer in (bijvoorbeeld hana-lb-3 **03** 15).
        1.  Selecteer het front-end-IP-adres, de back-endpool en de statustest die u eerder hebt gemaakt (bijvoorbeeld **hana-frontend).**
        1.  Laat **Protocol** ingesteld op **TCP** en voer poort 3 **03** 15 in.
        1.  **Verhoog de time-out voor inactief** naar 30 minuten.
        1.  Zorg ervoor dat **zwevend IP-adres is ingeschakeld.**
        1.  Selecteer **OK**.
        1.  Herhaal deze stappen voor poort **3 03** 17.
    1.  Maak SAP HANA 2.0 de taakverdelingsregels voor de systeemdatabase:
        1.  Open de load balancer, selecteer **taakverdelingsregels** en selecteer **Toevoegen.**
        1.  Voer de naam van de nieuwe load balancer in (bijvoorbeeld hana-lb-3 **03** 13).
        1.  Selecteer het front-end-IP-adres, de back-endpool en de statustest die u eerder hebt gemaakt (bijvoorbeeld **hana-frontend).**
        1.  Laat **Protocol** ingesteld op **TCP** en voer poort 3 **03** 13 in.
        1.  **Verhoog de time-out voor** inactief naar 30 minuten.
        1.  Zorg ervoor dat **zwevend IP-adres is ingeschakeld.**
        1.  Selecteer **OK**.
        1.  Herhaal deze stappen voor poort **3 03** 14.
    1.  Voor SAP HANA 2.0 maakt u eerst de taakverdelingsregels voor de tenantdatabase:
        1.  Open het load balancer, selecteer **Taakverdelingsregels** en selecteer **Toevoegen.**
        1.  Voer de naam van de nieuwe load balancer in (bijvoorbeeld hana-lb-3 **03** 40).
        1.  Selecteer het front-end-IP-adres, de back-endpool en de statustest die u eerder hebt gemaakt (bijvoorbeeld **hana-frontend).**
        1.  Laat **Protocol** ingesteld op **TCP** en voer poort 3 **03** 40 in.
        1.  **Verhoog de time-out voor inactief** naar 30 minuten.
        1.  Zorg ervoor dat **zwevend IP-adres is ingeschakeld.**
        1.  Selecteer **OK**.
        1.  Herhaal deze stappen voor poorten **3 03** 41 en 3 **03** 42.

Lees het hoofdstuk Verbindingen met [tenantdatabases](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) in de handleiding [SAP HANA Tenantdatabases](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) of SAP-notitie [2388694](https://launchpad.support.sap.com/#/notes/2388694)voor meer informatie over de vereiste poorten voor SAP HANA.

> [!IMPORTANT]
> Schakel geen TCP-tijdstempels in op Azure-VM's die achter de Azure Load Balancer. Als u TCP-tijdstempels inschakelen, mislukken de statustests. Stel parameter **net.ipv4.tcp_timestamps** in **op 0.** Zie [statustests Load Balancer voor meer informatie.](../../../load-balancer/load-balancer-custom-probe-overview.md) Zie ook SAP note [2382421](https://launchpad.support.sap.com/#/notes/2382421).

## <a name="mount-the-azure-netapp-files-volume"></a>Het Azure NetApp Files volume

1. **[A]** Maak bevestigingspunten voor de HANA-databasevolumes. 

    ```
    mkdir -p /hana/data
    mkdir -p /hana/log
    mkdir -p /hana/shared
    ```

2. **[A]** Controleer de NFS-domeininstelling. Zorg ervoor dat het domein is geconfigureerd als het standaarddomein  Azure NetApp Files, dat wil zeggen defaultv4iddomain.com en de toewijzing is ingesteld op **niemand.**

    ```
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = defaultv4iddomain.com
    [Mapping]
    Nobody-User = nobody
    Nobody-Group = nobody
    ```

    > [!IMPORTANT]    
    > Zorg ervoor dat u het NFS-domein in /etc/idmapd.conf op de VM in stelt op de standaarddomeinconfiguratie op Azure NetApp Files: **defaultv4iddomain.com**. Als er een verschil is tussen de domeinconfiguratie op de NFS-client (dat wil zeggen de VM) en de NFS-server, dat wil zeggen de Azure NetApp-configuratie, worden de machtigingen voor bestanden op Azure NetApp-volumes die zijn bevestigd op de VM's weergegeven als niemand.
    

3. **[1]** De knooppuntspecifieke volumes op knooppunt1 (**handtekeningadb1**) 

    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-data-mnt00001 /hana/data
    ```
    
4.  **[2]** De knooppuntspecifieke volumes aan knooppunt2 (**handtekeningadb2**)
    
    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-data-mnt00001 /hana/data
    ```

5. **[A]** Controleer of alle HANA-volumes zijn bevestigd met NFS-protocolversie NFSv4.

    ```
    sudo nfsstat -m
    
    # Verify that flag vers is set to 4.1 
    # Example from hanadb1
    
    /hana/log from 10.32.2.4:/hanadb1-log-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    /hana/data from 10.32.2.4:/hanadb1-data-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    /hana/shared from 10.32.2.4:/hanadb1-shared-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    ```

6. **[A]** Controleer **nfs4_disable_idmapping.** Deze moet worden ingesteld op **Y.** Voer de opdracht voor het nfs4_disable_idmapping **mapstructuur** te maken. U kunt de map niet handmatig maken onder /sys/modules, omdat toegang is gereserveerd voor de kernel/stuurprogramma's.

    ```
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # If you need to set nfs4_disable_idmapping to Y
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

   Zie voor meer informatie over het wijzigen **nfs_disable_idmapping** [https://access.redhat.com/solutions/1749883](https://access.redhat.com/solutions/1749883) parameter. 


## <a name="sap-hana-installation"></a>SAP HANA installeren

1. **[A]** Hostnaamresolutie instellen voor alle hosts.

   U kunt een DNS-server gebruiken of het bestand /etc/hosts op alle knooppunten wijzigen. In dit voorbeeld ziet u hoe u het bestand /etc/hosts gebruikt. Vervang het IP-adres en de hostnaam in de volgende opdrachten:

   ```
   sudo vi /etc/hosts
   # Insert the following lines in the /etc/hosts file. Change the IP address and hostname to match your environment  
   10.32.0.4   hanadb1
   10.32.0.5   hanadb2
   ```

2. **[A]** RHEL voor HANA-configuratie

   Configureer RHEL zoals beschreven in de onderstaande SAP-notitie op basis van uw RHEL-versie

   - [2292690 - SAP HANA DB: aanbevolen instellingen voor het besturingssysteem voor RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)
   - [2777782 - SAP HANA DB: aanbevolen besturingssysteeminstellingen voor RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582 - Linux: SAP-toepassingen uitvoeren die zijn gecompileerd met GCC 6.x](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824 - Linux: SAP-toepassingen uitvoeren die zijn gecompileerd met GCC 7.x](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607 - Linux: SAP-toepassingen uitvoeren die zijn gecompileerd met GCC 9.x](https://launchpad.support.sap.com/#/notes/2886607)

3. **[A]** Installeer de SAP HANA

   MDC is gestart met HANA 2.0 SPS 01 en is de standaardoptie. Wanneer u het HANA-systeem installeert, worden SYSTEMDB en een tenant met dezelfde SID samen gemaakt. In sommige gevallen wilt u niet de standaard tenant. Als u geen initiële tenant wilt maken samen met de installatie, kunt u SAP Note [2629711 volgen](https://launchpad.support.sap.com/#/notes/2629711)

    Voer het **hdblcm-programma** uit vanaf de HANA-dvd. Voer de volgende waarden in bij de prompt:  
    Installatie kiezen: voer **1** in (voor installatie)  
    Aanvullende onderdelen selecteren voor installatie: Voer **1 in.**  
    Voer installatiepad [/hana/shared]: druk op Enter om de standaardinstelling te accepteren  
    Voer Lokale hostnaam [..]: druk op Enter om de standaardwaarde te accepteren  
    Wilt u extra hosts toevoegen aan het systeem? (y/n) [n]: **n**  
    Voer SAP HANA systeem-id in: voer **HN1 in.**  
    Instantienummer [00]: Voer **03 in**  
    Databasemodus selecteren/Index invoeren [1]: druk op Enter om de standaardinstelling te accepteren  
    Selecteer Systeemgebruik/Voer index in [4]: voer **4** in (voor aangepast)  
    Voer Locatie van gegevensvolumes [/hana/data]: druk op Enter om de standaardwaarde te accepteren  
    Voer Locatie van logboekvolumes [/hana/log]: druk op Enter om de standaardwaarde te accepteren  
    Maximale geheugentoewijzing beperken? [n]: druk op Enter om de standaardwaarde te accepteren  
    Voer de hostnaam van het certificaat in voor host '...' [...]: druk op Enter om de standaardwaarde te accepteren  
    Sap Host Agent User-wachtwoord (sapadm) invoeren: voer het gebruikerswachtwoord van de hostagent in  
    Sap Host Agent-gebruikerswachtwoord (sapadm) bevestigen: voer ter bevestiging opnieuw het gebruikerswachtwoord van de hostagent in  
    Voer het wachtwoord van de systeembeheerder (hn1adm) in: voer het wachtwoord van de systeembeheerder in  
    Bevestig het wachtwoord van de systeembeheerder (hn1adm) : voer ter bevestiging opnieuw het wachtwoord van de systeembeheerder in  
    Voer System Administrator Home Directory [/usr/sap/HN1/home]: druk op Enter om de standaardwaarde te accepteren  
    Voer De aanmeldingsshell van de systeembeheerder [/bin/sh]: druk op Enter om de standaardinstelling te accepteren  
    Voer de gebruikers-id van de systeembeheerder [1001]: druk op Enter om de standaardwaarde te accepteren  
    Voer de id in van de gebruikersgroep (sapsys) [79]: druk op Enter om de standaardwaarde te accepteren  
    Wachtwoord van databasegebruiker (SYSTEM) invoeren: voer het wachtwoord van de databasegebruiker in  
    Wachtwoord van databasegebruiker (SYSTEM) bevestigen: voer het wachtwoord van de databasegebruiker opnieuw in om te bevestigen  
    Start u het systeem opnieuw op na het opnieuw opstarten van de machine? [n]: druk op Enter om de standaardinstelling te accepteren  
    Wilt u doorgaan? (y/n): Valideer de samenvatting. Voer **y in om** door te gaan  

4. **[A]** SAP-hostagent upgraden

   Download het meest recente SAP Host Agent-archief van [het SAP Software Center](https://launchpad.support.sap.com/#/softwarecenter) en voer de volgende opdracht uit om de agent bij te werken. Vervang het pad naar het archief om te wijzen naar het bestand dat u hebt gedownload:

   ```
   sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
   ```

5. **[A]** Firewall configureren

   Maak de firewallregel voor de Azure load balancer-testpoort.

   ```
   sudo firewall-cmd --zone=public --add-port=62503/tcp
   sudo firewall-cmd --zone=public --add-port=62503/tcp –permanent
   ```

## <a name="configure-sap-hana-system-replication"></a>Systeemreplicatie SAP HANA configureren

Volg de stappen in Set up [SAP HANA System Replication](./sap-hana-high-availability-rhel.md#configure-sap-hana-20-system-replication) (Systeemreplicatie SAP HANA configureren). 

## <a name="cluster-configuration"></a>Clusterconfiguratie

In deze sectie worden de benodigde stappen beschreven voor een naadloze werking van het cluster wanneer SAP HANA is geïnstalleerd op NFS-shares met behulp van Azure NetApp Files. 

### <a name="create-a-pacemaker-cluster"></a>Een Pacemaker-cluster maken

Volg de stappen in [Pacemaker instellen op](./high-availability-guide-rhel-pacemaker.md) Red Hat Enterprise Linux in Azure om een eenvoudig Pacemaker-cluster voor deze HANA-server te maken.

### <a name="implement-the-python-system-replication-hook-saphanasr"></a>De Python-systeemreplicatie hook SAPHanaSR implementeren

Dit is een belangrijke stap om de integratie met het cluster te optimaliseren en de detectie te verbeteren wanneer een cluster-failover nodig is. Het wordt ten zeerste aanbevolen om de PYTHON-hook van SAPHanaSR te configureren.    

1. **[A]** Installeer de "systeemreplicatie hook" van HANA. De hook moet worden geïnstalleerd op beide HANA DB-knooppunten.           

   > [!TIP]
   > De Python-hook kan alleen worden geïmplementeerd voor HANA 2.0.        

   1. Bereid de hook voor als `root` .  

    ```bash
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR/srHook/SAPHanaSR.py /hana/shared/myHooks
     chown -R hn1adm:sapsys /hana/shared/myHooks
    ```

   2. Stop HANA op beide knooppunten. Voer uit <sid \> adm:  
   
    ```bash
    sapcontrol -nr 03 -function StopSystem
    ```

   3. Pas `global.ini` deze aan op elk clusterknooppunt.  
 
    ```bash
    # add to global.ini
    [ha_dr_provider_SAPHanaSR]
    provider = SAPHanaSR
    path = /hana/shared/myHooks
    execution_order = 1
    
    [trace]
    ha_dr_saphanasr = info
    ```

2. **[A]** Het cluster vereist configuratie van sudoers op elk clusterknooppunt voor <sid \> adm. In dit voorbeeld wordt dit bereikt door een nieuw bestand te maken. Voer de opdrachten uit als `root` .    
    ```bash
    cat << EOF > /etc/sudoers.d/20-saphana
    # Needed for SAPHanaSR python hook
    hn1adm ALL=(ALL) NOPASSWD: /usr/sbin/crm_attribute -n hana_hn1_site_srHook_*
    EOF
    ```

3. **[A]** Start SAP HANA beide knooppunten. Voer uit <sid \> adm.  

    ```bash
    sapcontrol -nr 03 -function StartSystem 
    ```

4. **[1] Controleer** de hookinstallatie. Voer uit <sid \> adm op de actieve HANA-systeemreplicatiesite.   

    ```bash
     cdtrace
     awk '/ha_dr_SAPHanaSR.*crm_attribute/ \
     { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*
     # Example output
     # 2021-04-12 21:36:16.911343 ha_dr_SAPHanaSR SFAIL
     # 2021-04-12 21:36:29.147808 ha_dr_SAPHanaSR SFAIL
     # 2021-04-12 21:37:04.898680 ha_dr_SAPHanaSR SOK

    ```

Zie Enable the SAP HA/DR provider hook (De SAP HA/DR-provider hook inschakelen) voor meer informatie over de implementatie van de [SAP HANA-systeemreplicatie hook.](https://access.redhat.com/articles/3004101#enable-srhook)  

### <a name="configure-filesystem-resources"></a>Bestandssysteembronnen configureren

In dit voorbeeld heeft elk clusterknooppunt zijn eigen HANA NFS-bestandssystemen /hana/shared, /hana/data en /hana/log.   

1. **[1]** Plaats het cluster in de onderhoudsmodus.

   ```
   pcs property set maintenance-mode=true
   ```

2. **[1]** Maak de resources van het bestandssysteem voor de **mounts van deadb1.**

    ```
    pcs resource create hana_data1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_log1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_shared1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    ```

3. **[2]** Maak de resources van het bestandssysteem voor de **mounts van de kunt uadb2.**

    ```
    pcs resource create hana_data2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_log2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_shared2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    ```

    `OCF_CHECK_LEVEL=20` kenmerk wordt toegevoegd aan de monitorbewerking, zodat elke monitor een lees-/schrijftest uitvoert op het bestandssysteem. Zonder dit kenmerk controleert de monitorbewerking alleen of het bestandssysteem is bevestigd. Dit kan een probleem zijn, omdat wanneer de verbinding verloren gaat, het bestandssysteem kan blijven worden bevestigd ondanks dat het niet toegankelijk is.

    `on-fail=fence` kenmerk wordt ook toegevoegd aan de monitorbewerking. Met deze optie wordt dat knooppunt onmiddellijk omheind als de monitorbewerking op een knooppunt mislukt. Zonder deze optie is het standaardgedrag om alle resources te stoppen die afhankelijk zijn van de mislukte resource, vervolgens de mislukte resource opnieuw op te starten en vervolgens alle resources te starten die afhankelijk zijn van de mislukte resource. Dit gedrag kan niet alleen lang duren wanneer een SAPHana-resource afhankelijk is van de mislukte resource, maar kan ook helemaal mislukken. De SAPHana-resource kan niet worden gestopt als de NFS-server met de uitvoerbare HANA-bestanden niet toegankelijk is.

4. **[1]** Locatiebeperkingen configureren

   Configureer locatiebeperkingen om ervoor te zorgen dat de resources die unieke mounts beheren, nooit kunnen worden uitgevoerd op termijnadb2 en vice versa.

    ```
    pcs constraint location hanadb1_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb2
    pcs constraint location hanadb2_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb1
    ```

    De `resource-discovery=never` optie wordt ingesteld omdat de unieke bevestigingen voor elk knooppunt hetzelfde bevestigingspunt delen. Maakt bijvoorbeeld gebruik `hana_data1` van het `/hana/data` bevestigingspunt , `hana_data2` en maakt ook gebruik van het bevestigingspunt `/hana/data` . Dit kan een fout-positief veroorzaken voor een testbewerking, wanneer de resourcetoestand wordt gecontroleerd bij het opstarten van het cluster en dit op zijn beurt onnodig herstelgedrag kan veroorzaken. Dit kan worden vermeden door het instellen van `resource-discovery=never`

5. **[1]** Kenmerkbronnen configureren

   Kenmerkbronnen configureren. Deze kenmerken worden ingesteld op true als alle NFS-mounts (/hana/data, /hana/log en /hana/data) van een knooppunt zijn bevestigd en anders worden ingesteld op false.

   ```
   pcs resource create hana_nfs1_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs1_active
   pcs resource create hana_nfs2_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs2_active
   ```

6. **[1]** Locatiebeperkingen configureren

   Configureer locatiebeperkingen om ervoor te zorgen dat de kenmerkresource van worden uitgevoerd op nuadb2 en vice versa.

   ```
   pcs constraint location hana_nfs1_active avoids hanadb2
   pcs constraint location hana_nfs2_active avoids hanadb1
   ```

7. **[1]** Orderbeperkingen maken

   Configureer de volgordebeperkingen zodat de kenmerkbronnen van een knooppunt pas starten nadat alle NFS-bevestigingen van het knooppunt zijn bevestigd.
   
    ```
    pcs constraint order hanadb1_nfs then hana_nfs1_active
    pcs constraint order hanadb2_nfs then hana_nfs2_active
    ```

   > [!TIP]
   > Als uw configuratie bestandssystemen bevat, buiten groep of , moet u de optie opnemen, zodat er geen `hanadb1_nfs` `hanadb2_nfs` `sequential=false` volgordeafhankelijkheden zijn tussen de bestandssystemen. Alle bestandssystemen moeten vóór beginnen, maar ze hoeven niet in een volgorde `hana_nfs1_active` ten opzichte van elkaar te beginnen. Zie voor meer informatie Hoe kan ik configureren SAP HANA systeemreplicatie in Scale-Up in een Pacemaker-cluster wanneer de [HANA-bestandssystemen zich op NFS-shares](https://access.redhat.com/solutions/5156571)

### <a name="configure-sap-hana-cluster-resources"></a>Clusterbronnen SAP HANA configureren

1. Volg de stappen in [SAP HANA clusterbronnen maken](./sap-hana-high-availability-rhel.md#create-sap-hana-cluster-resources) om de SAP HANA resources in het cluster te maken. Zodra SAP HANA resources zijn gemaakt, moeten we een locatieregelbeperking maken tussen SAP HANA resources en Bestandssystemen (NFS-mounts)

2. **[1]** Beperkingen configureren tussen de SAP HANA resources en de NFS-mounts

   Beperkingen voor locatieregel worden zo ingesteld dat de SAP HANA-resources alleen op een knooppunt kunnen worden uitgevoerd als alle NFS-bevestigingen van het knooppunt zijn bevestigd.

    ```
    pcs constraint location SAPHanaTopology_HN1_03-clone rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # On RHEL 7.x
    pcs constraint location SAPHana_HN1_03-master rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # On RHEL 8.x
    pcs constraint location SAPHana_HN1_03-clone rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # Take the cluster out of maintenance mode
    sudo pcs property set maintenance-mode=false
    ```

   De status van het cluster en alle resources controleren
   > [!NOTE]
   > Dit artikel bevat verwijzingen naar de term *slave*, een term die Microsoft niet meer gebruikt. Zodra de term uit de software wordt verwijderd, verwijderen we deze uit dit artikel.
   
    ```
    sudo pcs status
    
    Online: [ hanadb1 hanadb2 ]
    
    Full list of resources:
    
    rsc_hdb_azr_agt(stonith:fence_azure_arm):  Started hanadb1
    
    Resource Group: hanadb1_nfs
    hana_data1 (ocf::heartbeat:Filesystem):Started hanadb1
    hana_log1  (ocf::heartbeat:Filesystem):Started hanadb1
    hana_shared1   (ocf::heartbeat:Filesystem):Started hanadb1
    
    Resource Group: hanadb2_nfs
    hana_data2 (ocf::heartbeat:Filesystem):Started hanadb2
    hana_log2  (ocf::heartbeat:Filesystem):Started hanadb2
    hana_shared2   (ocf::heartbeat:Filesystem):Started hanadb2
    
    hana_nfs1_active   (ocf::pacemaker:attribute): Started hanadb1
    hana_nfs2_active   (ocf::pacemaker:attribute): Started hanadb2
    
    Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hanadb1 hanadb2 ]
    
    Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hanadb1 ]
    Slaves: [ hanadb2 ]
    
    Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):  Started hanadb1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):   Started hanadb1
    ```

## <a name="configure-hana-activeread-enabled-system-replication-in-pacemaker-cluster"></a>Actieve/leessysteemreplicatie van HANA configureren in Pacemaker-cluster

Vanaf SAP HANA 2.0 SPS 01 staat SAP actief/lezen ingeschakelde installatie toe voor SAP HANA-systeemreplicatie, waarbij de secundaire systemen van SAP HANA-systeemreplicatie actief kunnen worden gebruikt voor lees-intensieve werkbelastingen. Voor de ondersteuning van een dergelijke installatie in een cluster is een tweede virtueel IP-adres vereist waarmee clients toegang hebben tot de secundaire database met SAP HANA lezen. Om ervoor te zorgen dat de secundaire replicatiesite nog steeds toegankelijk is nadat een overname is uitgevoerd, moet het cluster het virtuele IP-adres verplaatsen met de secundaire van de SAPHana-resource.

De aanvullende configuratie die is vereist voor het beheren van HANA Active/Read ingeschakelde systeemreplicatie in een Red Hat-cluster met hoge beschikbaarheid met tweede virtuele IP wordt beschreven in [Configure HANA Active/Read Enabled System Replication in Pacemaker cluster](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel#configure-hana-activeread-enabled-system-replication-in-pacemaker-cluster).  

Voordat u verdergaat, moet u ervoor zorgen dat u het red hat-cluster met hoge beschikbaarheid volledig hebt geconfigureerd SAP HANA database, zoals beschreven in de bovenstaande segmenten van de documentatie.    


## <a name="test-the-cluster-setup"></a>De installatie van het cluster testen

In deze sectie wordt beschreven hoe u uw installatie kunt testen. 

1. Voordat u een test start, moet u ervoor zorgen dat Pacemaker geen mislukte actie heeft (via de status pc's), er geen onverwachte locatiebeperkingen zijn (bijvoorbeeld leftovers van een migratietest) en dat HANA-systeemreplicatie de synchronisatiestatus heeft, bijvoorbeeld met systemReplicationStatus:

    ```
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    ```

2. Controleer de clusterconfiguratie op een foutscenario wanneer een knooppunt geen toegang meer heeft tot de NFS-share (/hana/shared)

   De SAP HANA resourceagents zijn afhankelijk van binaire bestanden die zijn opgeslagen om `/hana/shared` bewerkingen uit te voeren tijdens de failover. Het  `/hana/shared` bestandssysteem wordt in het gepresenteerde scenario via NFS bevestigd.  
   Het is moeilijk om een fout te simuleren, waarbij een van de servers de toegang tot de NFS-share verliest. Een test die kan worden uitgevoerd, is om het bestandssysteem opnieuw te mounten als alleen-lezen.
   Met deze aanpak wordt gevalideerd dat het cluster een failover kan maken als de toegang `/hana/shared` tot op het actieve knooppunt verloren gaat.     


   **Verwacht resultaat:** Bij het maken als alleen-lezen bestandssysteem mislukt het kenmerk van de resource die lees-/schrijfbewerkingen uitvoert op het bestandssysteem, omdat er niets op het bestandssysteem kan worden geschreven en failover van `/hana/shared` `OCF_CHECK_LEVEL` de `hana_shared1` HANA-resource wordt uitgevoerd.  Hetzelfde resultaat wordt verwacht wanneer uw HANA-knooppunt geen toegang meer heeft tot de NFS-shares. 

   Resourcetoestand voordat u de test start:

    ```
    sudo pcs status
    # Example output
    Full list of resources:
     rsc_hdb_azr_agt        (stonith:fence_azure_arm):      Started hanadb1

     Resource Group: hanadb1_nfs
         hana_data1 (ocf::heartbeat:Filesystem):    Started hanadb1
         hana_log1  (ocf::heartbeat:Filesystem):    Started hanadb1
         hana_shared1       (ocf::heartbeat:Filesystem):    Started hanadb1

    Resource Group: hanadb2_nfs
         hana_data2 (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_log2  (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_shared2       (ocf::heartbeat:Filesystem):    Started hanadb2

     hana_nfs1_active       (ocf::pacemaker:attribute):     Started hanadb1
     hana_nfs2_active       (ocf::pacemaker:attribute):     Started hanadb2

     Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
         Started: [ hanadb1 hanadb2 ]

     Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
         Masters: [ hanadb1 ]
         Slaves: [ hanadb2 ]

     Resource Group: g_ip_HN1_03
         nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hanadb1
         vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hanadb1
    ```

   U kunt /hana/shared in de alleen-lezenmodus op het actieve clusterknooppunt plaatsen met behulp van de onderstaande opdracht:

    ```
    sudo mount -o ro 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    ```

   als de actie is ingesteld op stonith ( ), wordt de computer opnieuw opgestart of wordt poweroff `pcs property show stonith-action` gebruikt.  Zodra de server (cpadb1) niet meer werkt, wordt de HANA-resource verplaatst naar cpadb2. U kunt de status van het cluster controleren vanuitb2.

    ```
    pcs status

    Full list of resources:

     rsc_hdb_azr_agt        (stonith:fence_azure_arm):      Started hanadb2

     Resource Group: hanadb1_nfs
         hana_data1 (ocf::heartbeat:Filesystem):    Stopped
         hana_log1  (ocf::heartbeat:Filesystem):    Stopped
         hana_shared1       (ocf::heartbeat:Filesystem):    Stopped

     Resource Group: hanadb2_nfs
         hana_data2 (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_log2  (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_shared2       (ocf::heartbeat:Filesystem):    Started hanadb2

     hana_nfs1_active       (ocf::pacemaker:attribute):     Stopped
     hana_nfs2_active       (ocf::pacemaker:attribute):     Started hanadb2

     Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
         Started: [ hanadb2 ]
         Stopped: [ hanadb1 ]

     Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
         Masters: [ hanadb2 ]
         Stopped: [ hanadb1 ]

     Resource Group: g_ip_HN1_03
         nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hanadb2
         vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hanadb2
    ```

   We raden u aan om de configuratie van SAP HANA cluster grondig te testen door ook de tests uit te voeren die worden beschreven in Setup SAP HANA System Replication on RHEL (Systeemreplicatie instellen [op RHEL).](./sap-hana-high-availability-rhel.md#test-the-cluster-setup)

## <a name="next-steps"></a>Volgende stappen

* [Azure Virtual Machines en implementatie voor SAP][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP][dbms-guide]
* [NFS v4.1-volumes in Azure NetApp Files voor SAP HANA](./hana-vm-operations-netapp.md)