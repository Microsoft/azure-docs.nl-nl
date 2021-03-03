---
title: Hoge Beschik baarheid van SAP HANA scale-up met ANF op RHEL | Microsoft Docs
description: Stel een hoge Beschik baarheid van SAP HANA met ANF op Azure virtual machines (Vm's).
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/01/2021
ms.author: radeltch
ms.openlocfilehash: 2939e00d704f5c2799a1f16822cccdcc963fb73e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101671568"
---
# <a name="high-availability-of-sap-hana-scale-up-with-azure-netapp-files-on-red-hat-enterprise-linux"></a>Hoge Beschik baarheid van SAP HANA omhoog schalen met Azure NetApp Files op Red Hat Enterprise Linux

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

In dit artikel wordt beschreven hoe u SAP HANA systeem replicatie configureert in een scale-up-implementatie, wanneer de HANA-bestands systemen worden gekoppeld via NFS, met behulp van Azure NetApp Files (ANF). In de voorbeeld configuraties en installatie opdrachten instantie nummer **03** en Hana-systeem-id **HN1** worden gebruikt. SAP HANA replicatie bestaat uit één primair knoop punt en ten minste één secundair knoop punt.

Wanneer de stappen in dit document zijn gemarkeerd met de volgende voor voegsels, is de betekenis als volgt:

- **[A]**: de stap is van toepassing op alle knoop punten
- **[1]**: de stap is alleen van toepassing op Knooppunt1
- **[2]**: de stap is alleen van toepassing op Knooppunt2
 
Lees eerst de volgende SAP-opmerkingen en-documenten:

- SAP-opmerking [1928533](https://launchpad.support.sap.com/#/notes/1928533), die:
    - De lijst met Azure-VM-grootten die worden ondersteund voor de implementatie van SAP-software.
    - Belang rijke informatie over de capaciteit van Azure VM-grootten.
    - De ondersteunde SAP-software en besturings systemen (OS) en database combinaties.
    - De vereiste versie van de SAP-kernel voor Windows en Linux op Microsoft Azure.
- SAP-opmerking [2015553](https://launchpad.support.sap.com/#/notes/2015553) bevat vereisten voor SAP-ondersteuning voor SAP-software-implementaties in Azure.
- SAP Note [405827](https://launchpad.support.sap.com/#/notes/405827) vermeldt het aanbevolen bestands systeem voor de Hana-omgeving.
- SAP Note [2002167](https://launchpad.support.sap.com/#/notes/2002167) heeft aanbevolen instellingen voor het besturings systeem voor Red Hat Enterprise Linux.
- SAP Note [2009879](https://launchpad.support.sap.com/#/notes/2009879) heeft SAP Hana richt lijnen voor Red Hat Enterprise Linux.
- SAP Note [2178632](https://launchpad.support.sap.com/#/notes/2178632) bevat gedetailleerde informatie over alle bewakings gegevens die zijn gerapporteerd voor SAP in Azure.
- SAP Note [2191498](https://launchpad.support.sap.com/#/notes/2191498) heeft de vereiste SAP host agent-versie voor Linux in Azure.
- SAP Note [2243692](https://launchpad.support.sap.com/#/notes/2243692) bevat informatie over SAP-licentie verlening op Linux in Azure.
- SAP Note [1999351](https://launchpad.support.sap.com/#/notes/1999351) bevat extra informatie over probleem oplossing voor de uitgebreide bewakings extensie van Azure voor SAP.
- Op de [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) zijn alle vereiste SAP-notities voor Linux geïnstalleerd.
- [Azure Virtual Machines planning en implementatie voor SAP op Linux][planning-guide]
- [Azure Virtual Machines-implementatie voor SAP op Linux][deployment-guide]
- [Azure Virtual Machines DBMS-implementatie voor SAP op Linux][dbms-guide]
- [SAP HANA systeem replicatie in pacemaker-cluster.](https://access.redhat.com/articles/3004101)
- Algemene documentatie voor RHEL
    - [Overzicht van Add-On met hoge Beschik baarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
    - [Add-On beheer met hoge Beschik baarheid.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
    - [Referentie voor maximale Beschik baarheid Add-On.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
    - [SAP HANA systeem replicatie configureren in Scale-Up in een pacemaker-cluster wanneer de HANA-bestands systemen zich op NFS-shares bevinden](https://access.redhat.com/solutions/5156571)
- Documentatie voor Azure-specifieke RHEL:
    - [Ondersteunings beleid voor RHEL-clusters met hoge Beschik baarheid-Microsoft Azure Virtual Machines als cluster leden.](https://access.redhat.com/articles/3131341)
    - [Het installeren en configureren van een Red Hat Enterprise Linux 7,4 (en hoger) High-Availability cluster op Microsoft Azure.](https://access.redhat.com/articles/3252491)
    - [Installeer SAP HANA op Red Hat Enterprise Linux voor gebruik in Microsoft Azure.](https://access.redhat.com/solutions/3193782)
    - [SAP HANA scale-up systeem replicatie configureren pacemaker-cluster wanneer de HANA-bestands systemen zich op NFS-shares bevinden](https://access.redhat.com/solutions/5156571)
- [NetApp SAP-toepassingen op Microsoft Azure met behulp van Azure NetApp Files](https://www.netapp.com/us/media/tr-4746.pdf)
- [NFS v4.1-volumes in Azure NetApp Files voor SAP HANA](./hana-vm-operations-netapp.md)

## <a name="overview"></a>Overzicht

Traditioneel in scale-up omgeving alle bestands systemen voor SAP HANA worden gekoppeld vanuit lokale opslag. Het instellen van een hoge Beschik baarheid van SAP HANA systeem replicatie op Red Hat Enterprise Linux wordt gepubliceerd in de hand leiding [SAP Hana systeem replicatie op RHEL instellen](./sap-hana-high-availability-rhel.md) .

We hebben een extra resource configuratie in het cluster nodig om te kunnen profiteren van SAP HANA hoge Beschik baarheid van het scale-up systeem op [Azure NetApp files](../../../azure-netapp-files/index.yml) NFS-shares, zodat de Hana-bronnen kunnen worden hersteld, wanneer een knoop punt de toegang tot de NFS-shares op ANF verliest.  Het cluster beheert de NFS-koppelingen, zodat het de status van de bronnen kan bewaken. De afhankelijkheden tussen de bestandssysteem koppeling en de SAP HANA bronnen worden afgedwongen.  

![Schaal van SAP HANA HA op ANF](./media/sap-hana-high-availability-rhel/sap-hana-scale-up-netapp-files-red-hat.png)

SAP HANA bestandssysteem bestanden worden gekoppeld op NFS-shares met behulp van Azure NetApp Files op elk knoop punt. Bestands systemen/Hana/Data,/Hana/log en/Hana/Shared zijn uniek voor elk knoop punt. 

Gekoppeld op Knooppunt1 (**hanadb1**)

- 10.32.2.4:/**hanadb1**-data-mnt00001 op/Hana/data
- 10.32.2.4:/**hanadb1**-log-mnt00001 op/Hana/log
- 10.32.2.4:/**hanadb1**-Shared-mnt00001 op/Hana/Shared

Gekoppeld op Knooppunt2 (**hanadb2**)

- 10.32.2.4:/**hanadb2**-data-mnt00001 op/Hana/data
- 10.32.2.4:/**hanadb2**-log-mnt00001 op/Hana/log
- 10.32.2.4:/**hanadb2**-Shared-mnt00001 op/Hana/Shared

> [!NOTE]
> Bestands systemen/Hana/Shared,/Hana/data en/Hana/log worden niet gedeeld tussen de twee knoop punten. Elk cluster knooppunt heeft zijn eigen, afzonderlijke bestands systemen.   

De configuratie van de SAP HANA systeem replicatie maakt gebruik van een specifieke virtuele hostnaam en een virtueel IP-adres. Op Azure is een load balancer vereist voor het gebruik van een virtueel IP-adres. De volgende lijst bevat de configuratie van de load balancer:

- Front-end-configuratie: IP-adres 10.32.0.10 voor HN1-db
- Back-end-configuratie: verbonden met primaire netwerk interfaces van alle virtuele machines die deel moeten uitmaken van HANA-systeem replicatie
- Poort testen: poort 62503
- Taakverdelings regels: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP (als basis-Azure Load Balancer wordt gebruikt)  

## <a name="set-up-the-azure-netapp-file-infrastructure"></a>De Azure NetApp-bestands infrastructuur instellen

Voordat u verdergaat met de configuratie voor Azure NetApp Files-infra structuur, moet u vertrouwd raken met de [documentatie over Azure NetApp-bestanden](../../../azure-netapp-files/index.yml).

Azure NetApp Files is beschikbaar in verschillende [Azure-regio's](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Controleer of de geselecteerde Azure-regio Azure NetApp Files bevat.

Zie [Azure NetApp files Beschik baarheid per Azure](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)-regio voor meer informatie over de beschik baarheid van Azure NetApp files per Azure-regio.

Voordat u Azure NetApp Files implementeert, vraagt u om onboarding naar Azure NetApp Files door te gaan met de [registratie voor Azure NetApp files instructies](../../../azure-netapp-files/azure-netapp-files-register.md).

### <a name="deploy-azure-netapp-files-resources"></a>Azure NetApp Files-resources implementeren

Bij de volgende instructies wordt ervan uitgegaan dat u uw [virtuele Azure-netwerk](../../../virtual-network/virtual-networks-overview.md)al hebt geïmplementeerd. De Azure NetApp Files resources en virtuele machines, waarbij de Azure NetApp Files resources worden gekoppeld, moeten worden geïmplementeerd in hetzelfde virtuele Azure-netwerk of in peered virtuele netwerken van Azure.

1. Als u de resources nog niet hebt geïmplementeerd, vraagt u [om onboarding naar Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-register.md).

2. Maak een NetApp-account in uw geselecteerde Azure-regio door de instructies in [een NetApp-account maken](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)te volgen.

3.  Stel een Azure NetApp Files capaciteits groep in door de instructies in [een Azure NetApp files-capaciteits groep instellen](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)te volgen.

    De HANA-architectuur die in dit artikel wordt gepresenteerd, maakt gebruik van één Azure NetApp Files capaciteits groep op het *Ultra* service-niveau. Voor HANA-workloads in azure kunt u het beste een Azure NetApp Files *Ultra* -of *Premium* - [service niveau](../../../azure-netapp-files/azure-netapp-files-service-levels.md)gebruiken.

4.  Delegeer een subnet aan Azure NetApp Files, zoals beschreven in de instructies in [een subnet overdragen aan Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).

5.  Implementeer Azure NetApp Files volumes door de instructies in [een NFS-volume maken voor Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)te volgen.

    Wanneer u de volumes implementeert, moet u ervoor zorgen dat u de NFSv 4.1-versie selecteert. Implementeer de volumes in het aangewezen Azure NetApp Files subnet. De IP-adressen van de Azure NetApp-volumes worden automatisch toegewezen.

    Houd er rekening mee dat de Azure NetApp Files resources en de virtuele Azure-machines zich in hetzelfde virtuele Azure-netwerk moeten bevinden of in een Peerd Azure Virtual Network. Bijvoorbeeld, hanadb1-data-mnt00001, hanadb1-log-mnt00001, enzovoort, zijn de volume namen en nfs://10.32.2.4/hanadb1-data-mnt00001, nfs://10.32.2.4/hanadb1-log-mnt00001, enzovoort, de bestands paden voor de Azure NetApp Files volumes.
    
    Op **hanadb1**

    - Volume hanadb1-data-mnt00001 (nfs://10.32.2.4:/hanadb1-data-mnt00001) 
    - Volume hanadb1-log-mnt00001 (nfs://10.32.2.4:/hanadb1-log-mnt00001)
    - Volume hanadb1-Shared-mnt00001 (nfs://10.32.2.4:/hanadb1-shared-mnt00001)
    
    Op **hanadb2**

    - Volume hanadb2-data-mnt00001 (nfs://10.32.2.4:/hanadb2-data-mnt00001)
    - Volume hanadb2-log-mnt00001 (nfs://10.32.2.4:/hanadb2-log-mnt00001)
    - Volume hanadb2-Shared-mnt00001 (nfs://10.32.2.4:/hanadb2-shared-mnt00001)

### <a name="important-considerations"></a>Belangrijke overwegingen

Houd bij het maken van uw Azure NetApp Files voor SAP HANA scale-up-systemen rekening met de volgende overweging:

- De minimale capaciteits pool is 4 tebibytes (TiB).
- De minimale volume grootte is 100 gibibytes (GiB).
- Azure NetApp Files en alle virtuele machines waar de Azure NetApp Files volumes worden gekoppeld, moeten zich in hetzelfde virtuele Azure-netwerk of in gekoppelde [virtuele netwerken](../../../virtual-network/virtual-network-peering-overview.md) in dezelfde regio bevinden.
- Het geselecteerde virtuele netwerk moet een subnet hebben dat is overgedragen aan Azure NetApp Files.
- De door Voer van een Azure NetApp Files volume is een functie van volume quota en service niveau, zoals beschreven in [service niveau voor Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). Wanneer u de grootte van de HANA Azure NetApp-volumes wijzigt, moet u ervoor zorgen dat de resulterende door voer voldoet aan de HANA-systeem vereisten.
- Met de Azure NetApp Files [export beleid](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)kunt u de toegestane clients, het toegangs type (lezen-schrijven, alleen-lezen, enzovoort) beheren.
- De functie Azure NetApp Files is nog geen zone-bewust. Op dit moment wordt de functie niet geïmplementeerd in alle beschikbaarheids zones in een Azure-regio. Houd rekening met de mogelijke latentie implicaties in sommige Azure-regio's.

> [!IMPORTANT]
> Voor SAP HANA werk belastingen is lage latentie van cruciaal belang. Werk samen met uw micro soft-vertegenwoordiger om ervoor te zorgen dat de virtuele machines en de Azure NetApp Files-volumes in de buurt worden geïmplementeerd.

### <a name="sizing-of-hana-database-on-azure-netapp-files"></a>Grootte van HANA-Data Base op Azure NetApp Files

De door Voer van een Azure NetApp Files volume is een functie van de volume grootte en het service niveau, zoals beschreven in [service niveau voor Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-service-levels.md).

Bij het ontwerpen van de infra structuur voor SAP in azure, moet u rekening houden met een minimum aan vereisten voor opslag door SAP, dat wordt omgezet in kenmerken met minimale door Voer:

- Lezen/schrijven op/Hana/log van 250 MB per seconde (MB/s) met 1 MB I/O-grootten.
- Lees de activiteit van ten minste 400 MB/s voor/Hana/data voor de I/O-grootte van 16 MB en 64 MB.
- Schrijf activiteit van ten minste 250 MB/s voor/Hana/data met 16 MB en 64-MB I/O-grootten.

De [Azure NetApp files doorvoer limieten](../../../azure-netapp-files/azure-netapp-files-service-levels.md) per 1 TIB volume quota zijn:

- Premium Storage-laag-64-MiB/s.
- Ultra Storage-laag-128 MiB/s.

Om te voldoen aan de SAP-vereisten voor minimale door Voer voor/Hana/data en/Hana/log en de richt lijnen voor/Hana/Shared, zijn de aanbevolen grootten:

|    Volume    | Grootte van Premium Storage laag | Grootte van ultra Storage-laag | Ondersteund NFS-protocol |
| :----------: | :--------------------------: | :------------------------: | :--------------------: |
|  /hana/log   |            4 TiB             |           2 TiB            |          v 4.1          |
|  /hana/data  |           6,3 TiB            |          3,2 TiB           |          v 4.1          |
| /hana/shared |           1 x RAM-geheugen            |          1 x RAM-geheugen           |          v3 of v 4.1    |


> [!NOTE]
> De aanbevelingen voor Azure NetApp Files formaat die hier worden vermeld, zijn bedoeld om te voldoen aan de minimale vereisten die door SAP wordt aanbevolen voor hun infrastructuur providers. In echte klant implementaties en werkbelasting scenario's zijn deze grootten mogelijk niet voldoende. U kunt deze aanbevelingen als uitgangs punt gebruiken en aanpassen op basis van de vereisten van uw specifieke werk belasting.

> [!TIP]
> U kunt het formaat van Azure NetApp Files volumes dynamisch aanpassen, zonder dat u de volumes hoeft te *ontkoppelen* , de virtuele machines te stoppen of SAP Hana stop te zetten. Deze benadering biedt flexibiliteit om te voldoen aan de verwachte en onvoorziene doorvoer vereisten van uw toepassing.

> [!NOTE]
> Alle opdrachten om/Hana/Shared in dit artikel te koppelen, worden weer gegeven voor NFSv 4.1/Hana/Shared-volumes.
> Als u de/Hana/Shared-volumes hebt geïmplementeerd als NFSv3-volumes, vergeet dan niet om de koppelings opdrachten voor/Hana/Shared voor NFSv3 aan te passen.


## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>Virtuele Linux-machine implementeren via Azure Portal 

Eerst moet u de Azure NetApp Files volumes maken. Voer vervolgens de volgende stappen uit:

1.  Een resourcegroep maken.
2.  Maak een virtueel netwerk.
3.  Maak een beschikbaarheidsset. Stel het maximale update domein in.
4.  Maak een load balancer (intern). Standaard load balancer worden aanbevolen.
    Selecteer het virtuele netwerk dat u in stap 2 hebt gemaakt.
5.  Maak een virtuele machine 1 (**hanadb1**). 
6.  Maak de virtuele machine 2 (**hanadb2**).  
7.  Tijdens het maken van een virtuele machine wordt er geen schijf toegevoegd, omdat al onze koppel punten zich op NFS-shares van Azure NetApp Files bevindt. 

> [!IMPORTANT]
> Zwevend IP wordt niet ondersteund voor een secundaire IP-configuratie in een NIC in scenario's voor taak verdeling. Zie beperkingen voor [Azure Load Balancer](../../../load-balancer/load-balancer-multivip-overview.md#limitations)voor meer informatie. Als u een extra IP-adres voor de virtuele machine nodig hebt, implementeert u een tweede NIC.    

> [!NOTE] 
> Wanneer Vm's zonder open bare IP-adressen in de back-endadresgroep van intern (geen openbaar IP-adres load balancer) worden geplaatst, is er geen uitgaande Internet verbinding, tenzij er aanvullende configuratie wordt uitgevoerd om route ring naar open bare eind punten toe te staan. Zie [connectiviteit van open bare eind punten voor virtual machines met behulp van Azure Standard Load Balancer in scenario's met hoge Beschik baarheid voor SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md)voor meer informatie over het bezorgen van uitgaande verbindingen.

8.  Als u standaard load balancer gebruikt, volgt u deze configuratie stappen:
    1.  Maak eerst een front-end-IP-adres groep:
        1.  Open de load balancer, selecteer de **frontend-IP-adres groep** en selecteer **toevoegen**.
        1.  Voer de naam in van de nieuwe front-end-IP-adres groep (bijvoorbeeld **Hana-frontend**).
        1.  Stel de **toewijzing** in op **statisch** en voer het IP-adres in (bijvoorbeeld **10.32.0.10**).
        1.  Selecteer **OK**.
        1.  Nadat de nieuwe front-end-IP-groep is gemaakt, noteert u het IP-adres van de groep.
    1.  Maak vervolgens een back-end-pool:
        1.  Open de load balancer, selecteer **back-endservers** en selecteer **toevoegen**.
        1.  Voer de naam van de nieuwe back-end-pool in (bijvoorbeeld **Hana-back-end**).
        1.  Selecteer **een virtuele machine toevoegen**.
        1.  Selecteer * * virtuele machine * *.
        1.  Selecteer de virtuele machines van het SAP HANA cluster en de bijbehorende IP-adressen.
        1.  Selecteer **Toevoegen**.
    1.  Maak vervolgens een status test:
        1.  Open de load balancer, selecteer **status controles** en selecteer **toevoegen**.
        1.  Voer de naam in van de nieuwe status test (bijvoorbeeld **Hana-HP**).
        1.  Selecteer TCP als protocol en poort 625 **03**. Laat de waarde voor **interval** ingesteld op 5 en de drempel waarde voor een **onjuiste status** ingesteld op 2.
        1.  Selecteer **OK**.
    1.  Maak vervolgens de regels voor taak verdeling:
        1.  Open de load balancer, selecteer **regels voor taak verdeling** en selecteer **toevoegen**.
        1.  Voer de naam in van de nieuwe load balancer regel (bijvoorbeeld **Hana-lb**).
        1.  Selecteer het front-end-IP-adres, de back-end-pool en de status test die u eerder hebt gemaakt (bijvoorbeeld **Hana-frontend**, **Hana-back-end** en **Hana-HP**).
        1.  Selecteer **ha-poorten**.
        1.  Verhoog de **time-out voor inactiviteit** tot 30 minuten.
        1.  Zorg ervoor dat u **zwevende IP-adressen inschakelt**.
        1.  Selecteer **OK**.


9. Als uw scenario gebruikmaakt van basis load balancer, volgt u deze configuratie stappen:
    1.  Configureer de load balancer. Maak eerst een front-end-IP-adres groep:
        1.  Open de load balancer, selecteer de **frontend-IP-adres groep** en selecteer **toevoegen**.
        1.  Voer de naam in van de nieuwe front-end-IP-adres groep (bijvoorbeeld **Hana-frontend**).
        1.  Stel de **toewijzing** in op **statisch** en voer het IP-adres in (bijvoorbeeld **10.32.0.10**).
        1.  Selecteer **OK**.
        1.  Nadat de nieuwe front-end-IP-groep is gemaakt, noteert u het IP-adres van de groep.
    1.  Maak vervolgens een back-end-pool:
        1.  Open de load balancer, selecteer **back-endservers** en selecteer **toevoegen**.
        1.  Voer de naam van de nieuwe back-end-pool in (bijvoorbeeld **Hana-back-end**).
        1.  Selecteer **een virtuele machine toevoegen**.
        1.  Selecteer de beschikbaarheidsset die u hebt gemaakt in stap 3.
        1.  Selecteer de virtuele machines van het SAP HANA cluster.
        1.  Selecteer **OK**.
    1.  Maak vervolgens een status test:
        1.  Open de load balancer, selecteer **status controles** en selecteer **toevoegen**.
        1.  Voer de naam in van de nieuwe status test (bijvoorbeeld **Hana-HP**).
        1.  Selecteer **TCP** als protocol en poort 625 **03**. Laat de waarde voor **interval** ingesteld op 5 en de drempel waarde voor een **onjuiste status** ingesteld op 2.
        1.  Selecteer **OK**.
    1.  Maak voor SAP HANA 1,0 de regels voor taak verdeling:
        1.  Open de load balancer, selecteer **regels voor taak verdeling** en selecteer **toevoegen**.
        1.  Voer de naam in van de nieuwe load balancer regel (bijvoorbeeld Hana-lb-3 **03** 15).
        1.  Selecteer het front-end-IP-adres, de back-end-pool en de status test die u eerder hebt gemaakt (bijvoorbeeld **Hana-** front-end).
        1.  Zorg ervoor dat het **protocol** is ingesteld op **TCP** en voer poort 3 **03** 15 in.
        1.  Verhoog de **time-out voor inactiviteit** tot 30 minuten.
        1.  Zorg ervoor dat u **zwevende IP-adressen inschakelt**.
        1.  Selecteer **OK**.
        1.  Herhaal deze stappen voor poort 3 **03** 17.
    1.  Voor SAP HANA 2,0 maakt u de regels voor taak verdeling voor de systeem database:
        1.  Open de load balancer, selecteer **regels voor taak verdeling** en selecteer **toevoegen**.
        1.  Voer de naam in van de nieuwe load balancer regel (bijvoorbeeld Hana-lb-3 **03** 13).
        1.  Selecteer het front-end-IP-adres, de back-end-pool en de status test die u eerder hebt gemaakt (bijvoorbeeld **Hana-** front-end).
        1.  Zorg ervoor dat het **protocol** is ingesteld op **TCP** en voer poort 3 **03** 13 in.
        1.  Verhoog de **time-out voor inactiviteit** tot 30 minuten.
        1.  Zorg ervoor dat u **zwevende IP-adressen inschakelt**.
        1.  Selecteer **OK**.
        1.  Herhaal deze stappen voor poort 3 **03** 14.
    1.  Voor SAP HANA 2,0 maakt u eerst de regels voor taak verdeling voor de Tenant database:
        1.  Open de load balancer, selecteer **regels voor taak verdeling** en selecteer **toevoegen**.
        1.  Voer de naam in van de nieuwe load balancer regel (bijvoorbeeld Hana-lb-3 **03** 40).
        1.  Selecteer het frontend-IP-adres, de back-endadresgroep en de status test die u eerder hebt gemaakt (bijvoorbeeld **Hana-front-end**).
        1.  Zorg ervoor dat het **protocol** is ingesteld op **TCP** en voer poort 3 **03** 40 in.
        1.  Verhoog de **time-out voor inactiviteit** tot 30 minuten.
        1.  Zorg ervoor dat u **zwevende IP-adressen inschakelt**.
        1.  Selecteer **OK**.
        1.  Herhaal deze stappen voor de poorten 3 **03** 41 en 3 **03** 42.

Lees voor meer informatie over de vereiste poorten voor SAP HANA de hoofdstuk [verbindingen met Tenant databases](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) in de hand leiding voor [SAP Hana Tenant-data bases](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) of SAP Note [2388694](https://launchpad.support.sap.com/#/notes/2388694).

> [!IMPORTANT]
> Schakel TCP-tijds tempels niet in op virtuele Azure-machines die achter Azure Load Balancer worden geplaatst. Door TCP-tijds tempels in te scha kelen, mislukken de status controles. Stel para meter **net.IPv4.tcp_timestamps** in op **0**. Zie [Load Balancer Health probe](../../../load-balancer/load-balancer-custom-probe-overview.md)(Engelstalig) voor meer informatie. Zie ook SAP-opmerking [2382421](https://launchpad.support.sap.com/#/notes/2382421).

## <a name="mount-the-azure-netapp-files-volume"></a>Het Azure NetApp Files volume koppelen

1. **[A]** koppel punten maken voor de Hana-database volumes. 

    ```
    mkdir -p /hana/data
    mkdir -p /hana/log
    mkdir -p /hana/shared
    ```

2. **[A]** Controleer de NFS-domein instelling. Zorg ervoor dat het domein is geconfigureerd als de standaard Azure NetApp Files domein, d.w.z. **defaultv4iddomain.com** en de toewijzing is ingesteld op **niemand**.

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
    > Zorg ervoor dat u het NFS-domein in/etc/idmapd.conf op de virtuele machine zo instelt dat deze overeenkomt met de standaard domein configuratie op Azure NetApp Files: **defaultv4iddomain.com**. Als er een verschil is tussen de domein configuratie op de NFS-client (de virtuele machine) en de NFS-server, dat wil zeggen de Azure NetApp-configuratie, worden de machtigingen voor bestanden op Azure NetApp-volumes die zijn gekoppeld op de Vm's weer gegeven als niemand.
    

3. **[1]** koppel de knooppunt-specifieke volumes op Knooppunt1 (**hanadb1**) 

    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-data-mnt00001 /hana/data
    ```
    
4.  **[2]** koppel de knooppunt-specifieke volumes op Knooppunt2 (**hanadb2**)
    
    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-data-mnt00001 /hana/data
    ```

5. **[A]** Controleer of alle Hana-volumes zijn gekoppeld met de NFS-protocol versie NFSv4.

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

6. **[A] Controleer de** **nfs4_disable_idmapping**. Deze moet worden ingesteld op **Y**. Als u de mapstructuur wilt maken waar **nfs4_disable_idmapping** zich bevindt, voert u de opdracht Mount uit. U kunt de map niet hand matig maken onder/sys/modules, omdat de toegang is gereserveerd voor de kernel/Stuur Programma's.

    ```
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # If you need to set nfs4_disable_idmapping to Y
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

   Zie voor meer informatie over het wijzigen van **nfs_disable_idmapping** para meter [https://access.redhat.com/solutions/1749883](https://access.redhat.com/solutions/1749883) . 


## <a name="sap-hana-installation"></a>Installatie van SAP HANA

1. **[A] het** omzetten van de hostnaam voor alle hosts instellen.

   U kunt een DNS-server gebruiken of het bestand/etc/hosts-bestand op alle knoop punten wijzigen. In dit voor beeld ziet u hoe u het bestand/etc/hosts-bestand gebruikt. Vervang het IP-adres en de hostnaam in de volgende opdrachten:

   ```
   sudo vi /etc/hosts
   # Insert the following lines in the /etc/hosts file. Change the IP address and hostname to match your environment  
   10.32.0.4   hanadb1
   10.32.0.5   hanadb2
   ```

2. **[A]** RHEL voor Hana-configuratie

   Configureer RHEL zoals beschreven in de onderstaande SAP-notitie op basis van uw RHEL-versie

   - [2292690-SAP HANA DB: aanbevolen besturingssysteem instellingen voor RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)
   - [2777782-SAP HANA DB: aanbevolen besturingssysteem instellingen voor RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582-Linux: SAP-toepassingen uitvoeren die zijn gecompileerd met GCC 6. x](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824-Linux: SAP-toepassingen uitvoeren die zijn gecompileerd met GCC 7. x](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607-Linux: SAP-toepassingen uitvoeren die zijn gecompileerd met GCC 9. x](https://launchpad.support.sap.com/#/notes/2886607)

3. **[A]** de SAP Hana installeren

   Gestart met HANA 2,0 SPS 01, MDC is de standaard optie. Wanneer u HANA-systeem installeert, worden SYSTEMDB en een Tenant met dezelfde SID samen gemaakt. In sommige gevallen wilt u niet de standaard Tenant. Als u de eerste Tenant niet samen met de installatie wilt maken, kunt u SAP-notitie [2629711](https://launchpad.support.sap.com/#/notes/2629711) volgen

    Voer het **hdblcm** -programma uit vanaf de Hana-DVD. Voer de volgende waarden in bij de prompt:  
    Installatie kiezen: Voer **1** in (voor installatie)  
    Selecteer extra onderdelen voor installatie: Voer **1** in.  
    Voer het installatiepad [/Hana/Shared] in: druk op ENTER om de standaard instelling te accepteren  
    Voer de naam van de lokale host [..] in: druk op ENTER om de standaard waarde te accepteren  
    Wilt u extra hosts toevoegen aan het systeem? (j/n) [n]: **n**  
    Voer SAP HANA systeem-ID in: Voer **HN1** in.  
    Instantie nummer [00] invoeren: Voer **03** in  
    Database modus selecteren/index invoeren [1]: druk op ENTER om de standaard instelling te accepteren  
    Systeem gebruik selecteren/index invoeren [4]: Voer **4** in (voor aangepast)  
    Voer de locatie van de gegevens volumes [/Hana/data] in: druk op ENTER om de standaard waarde te accepteren  
    Voer de locatie van de logboek volumes [/Hana/log] in: druk op ENTER om de standaard waarde te accepteren  
    Maximale geheugen toewijzing beperken? [n]: druk op ENTER om de standaard instelling te accepteren  
    Voer de hostnaam van het certificaat in voor de host... [...]: druk op ENTER om de standaard instelling te accepteren  
    Voer het wacht woord voor de gebruiker van de SAP host agent (sapadm) in: Voer het wacht woord van de Hosta Gent in  
    Wacht woord van SAP host agent User (sapadm) bevestigen: Voer het gebruikers wachtwoord van de Hosta Gent opnieuw in om te bevestigen  
    Voer het wacht woord voor de systeem beheerder (hn1adm) in: Voer het wacht woord voor de systeem beheerder in  
    Wacht woord van systeem beheerder (hn1adm) bevestigen: Voer het wacht woord van de systeem beheerder nogmaals in om te bevestigen  
    Voer de basismap van de systeem beheerder [/usr/sap/HN1/home] in: druk op ENTER om de standaard instelling te accepteren  
    Voer aanmeldings shell van systeem beheerder in [/bin/sh]: druk op ENTER om de standaard instelling te accepteren  
    Voer de gebruikers-ID van de systeem beheerder in [1001]: druk op ENTER om de standaard waarde te accepteren  
    ID opgeven van gebruikers groep (sapsys) [79]: druk op ENTER om de standaard waarde te accepteren  
    Voer het wacht woord voor de database gebruiker (systeem) in: Voer het wacht woord voor de database gebruiker in  
    Wacht woord van database gebruiker (systeem) bevestigen: Voer het wacht woord van de database gebruiker opnieuw in om te bevestigen  
    Systeem opnieuw opstarten nadat de computer opnieuw is opgestart? [n]: druk op ENTER om de standaard instelling te accepteren  
    Wilt u doorgaan? (j/n): Valideer de samen vatting. Voer **y** in om door te gaan  

4. **[A]** upgrade SAP Hosta Gent

   Down load het meest recente SAP host agent-archief vanuit het [SAP Software Center](https://launchpad.support.sap.com/#/softwarecenter) en voer de volgende opdracht uit om de agent bij te werken. Vervang het pad naar het archief zodat dit verwijst naar het bestand dat u hebt gedownload:

   ```
   sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
   ```

5. **[A]** firewall configureren

   Maak de firewall regel voor de Azure load balancer-test poort.

   ```
   sudo firewall-cmd --zone=public --add-port=62503/tcp
   sudo firewall-cmd --zone=public --add-port=62503/tcp –permanent
   ```

## <a name="configure-sap-hana-system-replication"></a>SAP HANA systeem replicatie configureren

Volg de stappen in [SAP Hana systeem replicatie](./sap-hana-high-availability-rhel.md#configure-sap-hana-20-system-replication) instellen om SAP Hana systeem replicatie te configureren. 

## <a name="cluster-configuration"></a>Clusterconfiguratie

In deze sectie worden de vereiste stappen beschreven die nodig zijn om het cluster naadloos te laten werken wanneer SAP HANA is geïnstalleerd op NFS-shares met behulp van Azure NetApp Files. 

### <a name="create-a-pacemaker-cluster"></a>Een Pacemaker-cluster maken

Volg de stappen bij het [instellen van pacemaker op Red Hat Enterprise Linux](./high-availability-guide-rhel-pacemaker.md) in azure om een basis pacemaker-cluster voor deze Hana-server te maken.

### <a name="configure-filesystem-resources"></a>Bestandssysteem bronnen configureren

In dit voor beeld heeft elk cluster knooppunt een eigen HANA NFS-bestands systeem/Hana/Shared,/Hana/data en/Hana/log.   

1. **[1]** plaats het cluster in de onderhouds modus.

   ```
   pcs property set maintenance-mode=true
   ```

2. **[1]** Maak de bestandssysteem bronnen voor de **hanadb1** -koppelingen.

    ```
    pcs resource create hana_data1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_log1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_shared1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    ```

3. **[2]** Hiermee maakt u de bestandssysteem bronnen voor de **hanadb2** -koppelingen.

    ```
    pcs resource create hana_data2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_log2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_shared2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    ```

    `OCF_CHECK_LEVEL=20` het kenmerk wordt toegevoegd aan de controle bewerking, zodat elke monitor een lees-schrijf test op het bestands systeem uitvoert. Zonder dit kenmerk controleert de controle bewerking alleen of het bestands systeem is gekoppeld. Dit kan een probleem zijn omdat wanneer de verbinding is verbroken, het bestands systeem mogelijk nog niet toegankelijk is.

    `on-fail=fence` het kenmerk wordt ook toegevoegd aan de controle bewerking. Als deze optie is ingeschakeld, wordt het knoop punt onmiddellijk geomheiningd als de monitor bewerking mislukt voor een knoop punt. Als deze optie niet is ingesteld, is het standaard gedrag om alle resources te stoppen die afhankelijk zijn van de bron die is mislukt, start u de mislukte resource opnieuw op en start u alle resources die afhankelijk zijn van de bron die is mislukt. Dit gedrag kan niet alleen lang duren wanneer een SAPHana-resource afhankelijk is van de bron die is mislukt, maar kan ook mislukken. De SAPHana-resource kan niet worden gestopt als de NFS-server met de HANA-uitvoer bare bestanden niet toegankelijk is.

4. **[1]** locatie beperkingen configureren

   Configureer locatie beperkingen om ervoor te zorgen dat de resources die hanadb1-unieke koppelingen beheren, nooit kunnen worden uitgevoerd op hanadb2 en vice versa.

    ```
    pcs constraint location hanadb1_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb2
    pcs constraint location hanadb2_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb1
    ```

    De `resource-discovery=never` optie is ingesteld, omdat de unieke koppelingen voor elk knoop punt hetzelfde koppel punt delen. Maakt bijvoorbeeld gebruik van een `hana_data1` koppel punt `/hana/data` en `hana_data2` maakt ook gebruik van een koppel punt `/hana/data` . Dit kan een fout positief veroorzaken voor een test bewerking, wanneer de resource status wordt gecontroleerd tijdens het opstarten van het cluster. Dit kan een onnodig herstel gedrag veroorzaken. Dit kan worden vermeden door het instellen van `resource-discovery=never`

5. **[1]** kenmerk resources configureren

   Kenmerk resources configureren. Deze kenmerken worden ingesteld op True als alle NFS-koppelingen van een knoop punt (/Hana/Data,/Hana/log en/Hana/data) zijn gekoppeld en worden ingesteld op ONWAAR, anders.

   ```
   pcs resource create hana_nfs1_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs1_active
   pcs resource create hana_nfs2_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs2_active
   ```

6. **[1]** locatie beperkingen configureren

   Configureer locatie beperkingen om ervoor te zorgen dat de hanadb1's-kenmerk resource nooit wordt uitgevoerd op hanadb2 en vice versa.

   ```
   pcs constraint location hana_nfs1_active avoids hanadb2
   pcs constraint location hana_nfs2_active avoids hanadb1
   ```

7. **[1]** volgorde beperkingen maken

   Configureer de volg orde van beperkingen zodat de kenmerk resources van een knoop punt alleen starten nadat alle NFS-koppels van het knoop punt zijn gekoppeld.
   
    ```
    pcs constraint order hanadb1_nfs then hana_nfs1_active
    pcs constraint order hanadb2_nfs then hana_nfs2_active
    ```

   > [!TIP]
   > Als uw configuratie bestands systemen bevat, buiten groep `hanadb1_nfs` of `hanadb2_nfs` , neemt u de `sequential=false` optie op, zodat er geen volg orde van afhankelijkheden tussen de bestands systemen bestaat. Alle bestands systemen moeten vóór `hana_nfs1_active` worden gestart, maar ze hoeven niet in een wille keurige volg orde ten opzichte van elkaar te beginnen. Zie [Hoe kan ik SAP Hana systeem replicatie in Scale-Up configureren in een pacemaker-cluster wanneer de Hana-bestands systemen zich op NFS-shares bevinden](https://access.redhat.com/solutions/5156571) voor meer informatie.

### <a name="configure-sap-hana-cluster-resources"></a>SAP HANA cluster resources configureren

1. Volg de stappen in [Create SAP Hana cluster resources](./sap-hana-high-availability-rhel.md#create-sap-hana-cluster-resources) om de SAP Hana resources in het cluster te maken. Zodra SAP HANA resources zijn gemaakt, moet er een beperking voor de locatie regel worden gemaakt tussen SAP HANA resources en bestands systemen (NFS-koppelingen)

2. **[1]** beperkingen configureren tussen de SAP Hana resources en de NFS-koppelingen

   Locatie regel beperkingen worden zo ingesteld dat de SAP HANA-resources alleen op een knoop punt kunnen worden uitgevoerd als alle NFS-koppels van het knoop punt zijn gekoppeld.

    ```
    pcs constraint location SAPHanaTopology_HN1_03-clone rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # On RHEL 7.x
    pcs constraint location SAPHana_HN1_03-master rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # On RHEL 8.x
    pcs constraint location SAPHana_HN1_03-clone rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # Take the cluster out of maintenance mode
    sudo pcs property set maintenance-mode=false
    ```

   Controleer de status van het cluster en alle resources
   > [!NOTE]
   > Dit artikel bevat verwijzingen naar de term *Slave*, een term die door micro soft niet meer wordt gebruikt. Zodra de term uit de software wordt verwijderd, verwijderen we deze uit dit artikel.
   
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

## <a name="test-the-cluster-setup"></a>De Cluster installatie testen

In deze sectie wordt beschreven hoe u de installatie kunt testen. 

1. Voordat u een test start, moet u ervoor zorgen dat pacemaker geen mislukte actie heeft (via de status van pc's), er zijn geen onverwachte locatie beperkingen (bijvoorbeeld rests van een migratie test) en de HANA-systeem replicatie is gesynchroniseerd status, bijvoorbeeld met systemReplicationStatus:

    ```
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    ```

2. De cluster configuratie controleren voor een fout scenario wanneer een knoop punt de toegang tot de NFS-share verliest (/Hana/Shared)

   De SAP HANA resource agents zijn afhankelijk van binaire bestanden, die zijn opgeslagen op `/hana/shared` om bewerkingen uit te voeren tijdens een failover. Het bestands systeem  `/hana/shared` wordt gekoppeld via NFS in het weer gegeven scenario.  
   Het is moeilijk om een fout te simuleren, waarbij een van de servers de toegang tot de NFS-share verliest. Een test die kan worden uitgevoerd, is het opnieuw koppelen van het bestands systeem als alleen-lezen.
   Met deze methode wordt gecontroleerd of het cluster failover kan doen als de toegang tot `/hana/shared` het actieve knoop punt is verbroken.     


   **Verwacht resultaat:** Als `/hana/shared` er een alleen-lezen bestands systeem wordt gemaakt, `OCF_CHECK_LEVEL` mislukt het kenmerk van de resource `hana_shared1` waardoor een lees-en schrijf bewerking wordt uitgevoerd op het bestands systeem, omdat er niets kan worden geschreven op het bestands systeem en de Hana-resource-failover wordt uitgevoerd.  Hetzelfde resultaat wordt verwacht wanneer het HANA-knoop punt de toegang tot de NFS-shares verliest. 

   Resource status voordat u begint met testen:

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

   U kunt/Hana/Shared in de modus alleen-lezen plaatsen op het actieve cluster knooppunt, met behulp van de onderstaande opdracht:

    ```
    sudo mount -o ro 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    ```

   hanadb1 wordt opnieuw opgestart of uitgeschakeld op basis van de actie die is ingesteld op stonith ( `pcs property show stonith-action` ).  Zodra de server (hanadb1) niet beschikbaar is, gaat HANA resource over naar hanadb2. U kunt de status van het cluster controleren vanuit hanadb2.

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

   We raden u aan om de SAP HANA-cluster configuratie grondig te testen door ook de tests uit te voeren die worden beschreven in [Setup SAP Hana systeem replicatie op RHEL](./sap-hana-high-availability-rhel.md#test-the-cluster-setup).

## <a name="next-steps"></a>Volgende stappen

* [Azure Virtual Machines planning en implementatie voor SAP][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP][dbms-guide]
* [NFS v4.1-volumes in Azure NetApp Files voor SAP HANA](./hana-vm-operations-netapp.md)