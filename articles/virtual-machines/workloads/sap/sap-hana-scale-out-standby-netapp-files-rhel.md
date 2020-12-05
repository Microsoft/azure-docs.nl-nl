---
title: SAP HANA uitschalen met stand-by met Azure NetApp Files op RHEL | Microsoft Docs
description: Hand leiding voor hoge Beschik baarheid voor SAP NetWeaver op Red Hat Enterprise Linux met Azure NetApp Files voor SAP-toepassingen
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
ms.date: 06/15/2020
ms.author: radeltch
ms.openlocfilehash: d2cc8487f9864a27c1a2b02ef6e846bc43727e27
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608534"
---
# <a name="deploy-a-sap-hana-scale-out-system-with-standby-node-on-azure-vms-by-using-azure-netapp-files-on-red-hat-enterprise-linux"></a>Een SAP HANA scale-out systeem met stand-by-knoop punt op virtuele Azure-machines implementeren met behulp van Azure NetApp Files op Red Hat Enterprise Linux 

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


In dit artikel wordt beschreven hoe u een Maxi maal beschikbaar SAP HANA systeem implementeert in een scale-out configuratie met stand-by op Azure Red Hat Enterprise Linux virtuele machines (Vm's) met behulp van [Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-introduction.md) voor de gedeelde opslag volumes.  

In de voorbeeld configuraties, installatie opdrachten, enzovoort, is de HANA-instantie **03** en de Hana-systeem-id **HN1**. De voor beelden zijn gebaseerd op HANA 2,0 SP4 en Red Hat Enterprise Linux voor SAP 7,6. 

> [!NOTE]
> Dit artikel bevat verwijzingen naar de termen *Master* en *Slave*, termen die micro soft niet meer gebruikt. Wanneer deze voor waarden worden verwijderd uit de software, worden ze uit dit artikel verwijderd.


Raadpleeg voordat u begint de volgende SAP-opmerkingen en-documenten:

* [Documentatie over Azure NetApp Files][anf-azure-doc] 
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
* Algemene documentatie voor RHEL
  * [Overzicht van Add-On met hoge Beschik baarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Add-On beheer met hoge Beschik baarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referentie voor Add-On met hoge Beschik baarheid](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Red Hat Enterprise Linux-netwerk gids](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide)
* Documentatie voor Azure-specifieke RHEL:
  * [SAP HANA op Red Hat Enterprise Linux installeren voor gebruik in Microsoft Azure](https://access.redhat.com/public-cloud/microsoft-azure)
* [NetApp SAP-toepassingen op Microsoft Azure met behulp van Azure NetApp Files][anf-sap-applications-azure]


## <a name="overview"></a>Overzicht

Eén methode voor het bereiken van HANA hoge Beschik baarheid is door automatische failover van de host te configureren. Als u de automatische failover van de host wilt configureren, voegt u een of meer virtuele machines toe aan het HANA-systeem en configureert u deze als stand-by-knoop punten. Wanneer het actieve knoop punt mislukt, neemt een stand-by-knoop punt automatisch over. In de gepresenteerde configuratie met Azure virtual machines behaalt u automatische failover door gebruik te maken [van NFS op Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-introduction.md).  

> [!NOTE]
> Het knoop punt stand-by moet toegang hebben tot alle database volumes. De HANA-volumes moeten worden gekoppeld als NFSv4-volumes. Het verbeterde vergrendelings mechanisme op basis van bestands-lease in het NFSv4-protocol wordt gebruikt voor `I/O` omheininging. 

> [!IMPORTANT]
> Als u de ondersteunde configuratie wilt maken, moet u de HANA-gegevens en logboek volumes als NFSv 4.1-volumes implementeren en koppelen met behulp van het NFSv 4.1-protocol. De configuratie van de automatische failover van de HANA-host met het stand-by-knoop punt wordt niet ondersteund met NFSv3.

![Overzicht van de hoge Beschik baarheid van SAP netweave](./media/sap-hana-high-availability-rhel/sap-hana-scale-out-standby-netapp-files-rhel.png)

In het voor gaande diagram, dat volgt SAP HANA netwerk aanbevelingen, worden drie subnetten weer gegeven in één virtueel Azure-netwerk: 
* Voor client communicatie
* Voor communicatie met het opslag systeem
* Voor interne HANA-communicatie tussen knoop punten

De Azure NetApp-volumes bevinden zich in een afzonderlijk subnet, [gedelegeerd aan Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).  

Voor deze voorbeeld configuratie zijn de subnetten:  

  - `client` 10.9.1.0/26  
  - `storage` 10.9.3.0/26  
  - `hana` 10.9.2.0/26  
  - `anf` 10.9.0.0/26 (gedelegeerd subnet naar Azure NetApp Files)

## <a name="set-up-the-azure-netapp-files-infrastructure"></a>De Azure NetApp Files-infra structuur instellen 

Voordat u verdergaat met de installatie van Azure NetApp Files-infra structuur, moet u vertrouwd raken met de [Azure NetApp files-documentatie][anf-azure-doc]. 

Azure NetApp Files is beschikbaar in verschillende [Azure-regio's](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Controleer of de geselecteerde Azure-regio Azure NetApp Files bevat.  

Zie [Azure NetApp files Beschik baarheid per Azure][anf-avail-matrix]-regio voor meer informatie over de beschik baarheid van Azure NetApp files per Azure-regio.  

Voordat u Azure NetApp Files implementeert, vraagt u om onboarding naar Azure NetApp Files door te gaan met de [registratie voor Azure NetApp files instructies][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Azure NetApp Files-resources implementeren  

Bij de volgende instructies wordt ervan uitgegaan dat u uw [virtuele Azure-netwerk](../../../virtual-network/virtual-networks-overview.md)al hebt geïmplementeerd. De Azure NetApp Files resources en virtuele machines, waarbij de Azure NetApp Files resources worden gekoppeld, moeten worden geïmplementeerd in hetzelfde virtuele Azure-netwerk of in peered virtuele netwerken van Azure.  

1. Als u de resources nog niet hebt geïmplementeerd, vraagt u [om onboarding naar Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-register.md).  

2. Maak een NetApp-account in uw geselecteerde Azure-regio door de instructies in [een NetApp-account maken](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)te volgen.  

3. Stel een Azure NetApp Files capaciteits groep in door de instructies in [een Azure NetApp files-capaciteits groep instellen](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)te volgen.  

   De HANA-architectuur die in dit artikel wordt gepresenteerd, maakt gebruik van één Azure NetApp Files capaciteits groep op het *Ultra service* -niveau. Voor HANA-workloads in azure kunt u het beste een Azure NetApp Files *Ultra* -of *Premium* - [service niveau](../../../azure-netapp-files/azure-netapp-files-service-levels.md)gebruiken.  

4. Delegeer een subnet aan Azure NetApp Files, zoals beschreven in de instructies in [een subnet overdragen aan Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md).  

5. Implementeer Azure NetApp Files volumes door de instructies in [een NFS-volume maken voor Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)te volgen.  

   Wanneer u de volumes implementeert, moet u ervoor zorgen dat u de **nfsv 4.1** -versie selecteert. Implementeer de volumes in het aangewezen Azure NetApp Files [subnet](/rest/api/virtualnetwork/subnets). De IP-adressen van de Azure NetApp-volumes worden automatisch toegewezen. 
   
   Houd er rekening mee dat de Azure NetApp Files resources en de virtuele Azure-machines zich in hetzelfde virtuele Azure-netwerk moeten bevinden of in een Peerd Azure Virtual Network. Bijvoorbeeld: **HN1**-data-Mnt00001, **HN1**-log-mnt00001, enzovoort, zijn de volume namen en NFS://10.9.0.4/**HN1**-data-mnt00001, NFS://10.9.0.4/**HN1**-log-mnt00001, enzovoort, de bestands paden voor de Azure NetApp files volumes.  

   * volume **HN1**-data-mnt00001 (NFS://10.9.0.4/**HN1**-data-mnt00001)
   * volume **HN1**-data-mnt00002 (NFS://10.9.0.4/**HN1**-data-mnt00002)
   * volume **HN1**-log-mnt00001 (NFS://10.9.0.4/**HN1**-log-mnt00001)
   * volume **HN1**-log-mnt00002 (NFS://10.9.0.4/**HN1**-log-mnt00002)
   * volume **HN1**-shared (NFS://10.9.0.4/**HN1**-Shared)
   
   In dit voor beeld hebben we een afzonderlijke Azure NetApp Files-volume gebruikt voor elk HANA-gegevens-en logboek volume. Voor een kosten geoptimaliseerde configuratie op kleinere of niet-productieve systemen is het mogelijk om alle gegevens koppelingen op één volume te plaatsen en alle logboeken te koppelen aan een ander enkel volume.  

### <a name="important-considerations"></a>Belangrijke overwegingen

Let op de volgende belang rijke overwegingen bij het maken van uw Azure NetApp Files voor SAP HANA scale-out met het scenario samen stellen per knoop punt:

- De minimale capaciteits pool is 4 tebibytes (TiB).  
- De minimale volume grootte is 100 gibibytes (GiB).
- Azure NetApp Files en alle virtuele machines waar de Azure NetApp Files volumes worden gekoppeld, moeten zich in hetzelfde virtuele Azure-netwerk of in gekoppelde [virtuele netwerken](../../../virtual-network/virtual-network-peering-overview.md) in dezelfde regio bevinden.  
- Het geselecteerde virtuele netwerk moet een subnet hebben dat is overgedragen aan Azure NetApp Files.
- De door Voer van een Azure NetApp Files volume is een functie van volume quota en service niveau, zoals beschreven in [service niveau voor Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). Wanneer u het formaat van de HANA Azure NetApp-volumes wijzigt, moet u ervoor zorgen dat de resulterende door voer voldoet aan de HANA-systeem vereisten.  
- Met de Azure NetApp Files [export beleid](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)kunt u de toegestane clients, het toegangs type (lezen-schrijven, alleen-lezen, enzovoort) beheren. 
- De functie Azure NetApp Files is nog geen zone-bewust. Op dit moment wordt de functie niet geïmplementeerd in alle beschikbaarheids zones in een Azure-regio. Houd rekening met de mogelijke latentie implicaties in sommige Azure-regio's.  

> [!IMPORTANT]
> Voor SAP HANA werk belastingen is lage latentie van cruciaal belang. Werk samen met uw micro soft-vertegenwoordiger om ervoor te zorgen dat de virtuele machines en de Azure NetApp Files-volumes dicht bij elkaar worden geïmplementeerd.  

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Grootte van HANA-Data Base op Azure NetApp Files

De door Voer van een Azure NetApp Files volume is een functie van de volume grootte en het service niveau, zoals beschreven in [service niveau voor Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). 

Bij het ontwerpen van de infra structuur voor SAP in azure, moet u rekening houden met een minimum aan vereisten voor opslag door SAP, dat wordt omgezet in kenmerken met minimale door Voer:

- Lezen/schrijven op/Hana/log van 250 MB per seconde (MB/s) met 1 MB I/O-grootten.  
- Lees de activiteit van ten minste 400 MB/s voor/Hana/data voor de I/O-grootte van 16 MB en 64 MB.  
- Schrijf activiteit van ten minste 250 MB/s voor/Hana/data met 16 MB en 64-MB I/O-grootten. 

De [Azure NetApp files doorvoer limieten](../../../azure-netapp-files/azure-netapp-files-service-levels.md) per 1 TIB volume quota zijn:
- Premium Storage-laag-64-MiB/s  
- Ultra Storage-laag-128 MiB/s  

Om te voldoen aan de vereisten voor de minimum doorvoer van SAP voor gegevens en logboeken en de richt lijnen voor/Hana/Shared, zijn de aanbevolen grootten:

| Volume | Grootte van<br>Laag Premium Storage | Grootte van<br>Ultra Storage-laag | Ondersteund NFS-protocol |
| --- | --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v 4.1 |
| /hana/data | 6,3 TiB | 3,2 TiB | v 4.1 |
| /hana/shared | 1xRAM per 4 worker-knoop punten | 1xRAM per 4 worker-knoop punten | v3 of v 4.1 |

De configuratie van de SAP HANA voor de lay-out die in dit artikel wordt weer gegeven, met behulp van Azure NetApp Files Ultra Storage-laag, is als volgt:

| Volume | Grootte van<br>Ultra Storage-laag | Ondersteund NFS-protocol |
| --- | --- | --- |
| /hana/log/mnt00001 | 2 TiB | v 4.1 |
| /hana/log/mnt00002 | 2 TiB | v 4.1 |
| /hana/data/mnt00001 | 3,2 TiB | v 4.1 |
| /hana/data/mnt00002 | 3,2 TiB | v 4.1 |
| /hana/shared | 2 TiB | v3 of v 4.1 |

> [!NOTE]
> De aanbevelingen voor Azure NetApp Files formaat die hier worden vermeld, zijn bedoeld om te voldoen aan de minimale vereisten die door SAP wordt aanbevolen voor hun infrastructuur providers. In echte klant implementaties en werkbelasting scenario's zijn deze grootten mogelijk niet voldoende. U kunt deze aanbevelingen als uitgangs punt gebruiken en aanpassen op basis van de vereisten van uw specifieke werk belasting.  

> [!TIP]
> U kunt het formaat van Azure NetApp Files volumes dynamisch aanpassen, zonder dat u de volumes hoeft te *ontkoppelen* , de virtuele machines te stoppen of SAP Hana stop te zetten. Deze benadering biedt flexibiliteit om te voldoen aan de verwachte en onvoorziene doorvoer vereisten van uw toepassing.

## <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Virtuele Linux-machines implementeren via de Azure Portal

Eerst moet u de Azure NetApp Files volumes maken. Voer vervolgens de volgende stappen uit:
1. Maak de [subnetten van het virtuele Azure-netwerk](../../../virtual-network/virtual-network-manage-subnet.md) in uw [virtuele Azure-netwerk](../../../virtual-network/virtual-networks-overview.md). 
1. Implementeer de Vm's. 
1. Maak de extra netwerk interfaces en koppel de netwerk interfaces aan de bijbehorende Vm's.  

   Elke virtuele machine heeft drie netwerk interfaces, die overeenkomen met de drie subnetten van het virtuele netwerk van Azure ( `client` `storage` en `hana` ). 

   Zie [een virtuele Linux-machine in azure maken met meerdere netwerk interface kaarten](../../linux/multiple-nics.md)voor meer informatie.  

> [!IMPORTANT]
> Voor SAP HANA werk belastingen is lage latentie van cruciaal belang. Werk samen met uw micro soft-vertegenwoordiger om ervoor te zorgen dat de virtuele machines en de Azure NetApp Files volumes dicht bij elkaar worden geïmplementeerd. Wanneer u een [nieuw SAP Hana systeem](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) wilt maken dat SAP Hana Azure NetApp files gebruikt, dient u de benodigde gegevens in te dienen. 
 
In de volgende instructies wordt ervan uitgegaan dat u de resource groep, het virtuele netwerk van Azure en de drie subnetten van het virtuele netwerk van Azure hebt gemaakt: `client` , `storage` en `hana` . Wanneer u de Vm's implementeert, selecteert u het subnet van de client, zodat de client netwerk interface de primaire interface op de Vm's is. U moet ook een expliciete route naar het Azure NetApp Files gedelegeerde subnet configureren via de gateway van het opslag subnet. 

> [!IMPORTANT]
> Zorg ervoor dat het besturings systeem dat u selecteert, SAP-gecertificeerd is voor SAP HANA op de specifieke VM-typen die u gebruikt. Ga naar de site [SAP Hana Certified IaaS platforms](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) voor een lijst met SAP Hana gecertificeerde VM-typen en versies van besturings systemen voor deze typen. Klik in de details van het vermelde VM-type om de volledige lijst met door SAP HANA ondersteunde versies van besturings systemen voor dat type op te halen.  

1. Maak een beschikbaarheidsset voor SAP HANA. Zorg ervoor dat u het maximale update domein instelt.  

2. Maak drie virtuele machines (**hanadb1**, **hanadb2**, **hanadb3**) door de volgende stappen uit te voeren:  

   a. Gebruik een Red Hat Enterprise Linux-installatie kopie in de Azure-galerie die wordt ondersteund voor SAP HANA. In dit voor beeld hebben we een RHEL-installatie kopie van SAP-HA 7,6 gebruikt.  

   b. Selecteer de beschikbaarheidsset die u eerder hebt gemaakt voor SAP HANA.  

   c. Selecteer het subnet van het virtuele Azure-netwerk. Selecteer [versneld netwerk](../../../virtual-network/create-vm-accelerated-networking-cli.md).  

   Wanneer u de virtuele machines implementeert, wordt de naam van de netwerk interface automatisch gegenereerd. In deze instructies voor de eenvoud verwijzen we naar de automatisch gegenereerde netwerk interfaces, die zijn gekoppeld aan het subnet van het virtuele netwerk van Azure, als **hanadb1-client**, **hanadb2-client** en **hanadb3-client**. 

3. Maak drie netwerk interfaces, één voor elke virtuele machine, voor het `storage` subnet van het virtuele netwerk (in dit voor beeld **hanadb1-Storage**, **hanadb2-Storage** en **hanadb3-Storage**).  

4. Maak drie netwerk interfaces, één voor elke virtuele machine, voor het `hana`  subnet van het virtuele netwerk (in dit voor beeld **hanadb1-Hana**, **hanadb2-Hana** en **hanadb3-Hana**).  

5. Koppel de zojuist gemaakte virtuele netwerk interfaces aan de bijbehorende virtuele machines door de volgende stappen uit te voeren:  

    a. Ga naar de virtuele machine in de [Azure Portal](https://portal.azure.com/#home).  

    b. Selecteer **virtual machines** in het linkerdeel venster. Filter op de naam van de virtuele machine (bijvoorbeeld **hanadb1**) en selecteer vervolgens de virtuele machine.  

    c. Selecteer in het deel venster **overzicht** de optie **stoppen** om de toewijzing van de virtuele machine ongedaan te maken.  

    d. Selecteer **netwerken** en koppel vervolgens de netwerk interface. Selecteer in de vervolg keuzelijst **netwerk interface koppelen** de al gemaakte netwerk interfaces voor de `storage` `hana` subnetten en.  
    
    e. Selecteer **Opslaan**. 
 
    f. Herhaal stap b tot en met e voor de resterende virtuele machines (in het voor beeld  **hanadb2** en **hanadb3**).
 
    g. Zorg ervoor dat de virtuele machines met de status gestopt nu actief zijn. Vervolgens wordt [versneld netwerken](../../../virtual-network/create-vm-accelerated-networking-cli.md) ingeschakeld voor alle nieuw gekoppelde netwerk interfaces.  

6. Voer de volgende stappen uit om versnelde netwerken in te scha kelen voor de extra netwerk interfaces voor de `storage` `hana` subnetten en:  

    a. Open [Azure Cloud shell](https://azure.microsoft.com/features/cloud-shell/) in het [Azure Portal](https://portal.azure.com/#home).  

    b. Voer de volgende opdrachten uit om versneld netwerken in te scha kelen voor de extra netwerk interfaces die zijn gekoppeld aan de `storage` `hana` subnetten.  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-storage</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true

    </code></pre>

7. Start de virtuele machines door de volgende stappen uit te voeren:  

    a. Selecteer **virtual machines** in het linkerdeel venster. Filter op de naam van de virtuele machine (bijvoorbeeld **hanadb1**) en selecteer deze.  

    b. Selecteer **Start** in het deel venster **overzicht** .  

## <a name="operating-system-configuration-and-preparation"></a>Configuratie en voor bereiding van het besturings systeem

De instructies in de volgende secties worden voorafgegaan door een van de volgende:
* **[A]**: van toepassing op alle knoop punten
* **[1]**: alleen van toepassing op knoop punt 1
* **[2]**: alleen van toepassing op knoop punt 2
* **[3]**: alleen van toepassing op knoop punt 3

Configureer en bereid uw besturings systeem uit door de volgende stappen uit te voeren:

1. **[A]** de host-bestanden op de virtuele machines onderhouden. Neem vermeldingen op voor alle subnetten. De volgende vermeldingen zijn toegevoegd aan `/etc/hosts` voor dit voor beeld.  

    <pre><code>
    # Storage
    10.9.3.4   hanadb1-storage
    10.9.3.5   hanadb2-storage
    10.9.3.6   hanadb3-storage
    # Client
    10.9.1.5   hanadb1
    10.9.1.6   hanadb2
    10.9.1.7   hanadb3
    # Hana
    10.9.2.4   hanadb1-hana
    10.9.2.5   hanadb2-hana
    10.9.2.6   hanadb3-hana
    </code></pre>

3. **[A]** Voeg een netwerk route toe zodat de communicatie met de Azure NetApp files via de netwerk interface van de opslag.  

   In dit voor beeld wordt gebruikt `Networkmanager` om de extra netwerk route te configureren. Bij de volgende instructies wordt ervan uitgegaan dat de netwerk interface van de opslag `eth1` .  
   Bepaal eerst de naam van de verbinding voor het apparaat `eth1` . In dit voor beeld is de naam van de verbinding voor het apparaat `eth1` `Wired connection 1` .  

    <pre><code>
    # Execute as root
    nmcli connection
    # Result
    #NAME                UUID                                  TYPE      DEVICE
    #System eth0         5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03  ethernet  eth0
    #Wired connection 1  4b0789d1-6146-32eb-83a1-94d61f8d60a7  ethernet  eth1
    </code></pre>

   Configureer vervolgens extra route naar het Azure NetApp Files gedelegeerd netwerk via `eth1` .  

    <pre><code>
    # Add the following route 
    # ANFDelegatedSubnet/cidr via StorageSubnetGW dev StorageNetworkInterfaceDevice
    nmcli connection modify <b>"Wired connection 1"</b> +ipv4.routes <b>"10.9.0.0/26 10.9.3.1"</b>
    </code></pre>

    Start de VM opnieuw op om de wijzigingen te activeren.  

3. **[A]** Installeer het NFS-client pakket.  

    <pre><code>
    yum install nfs-utils
    </code></pre>

3. **[A]** het besturings systeem voorbereiden voor het uitvoeren van SAP Hana op Azure NETAPP met NFS, zoals beschreven in [NetApp SAP-toepassingen op Microsoft Azure met behulp van Azure NetApp files][anf-sap-applications-azure]. Maak een */etc/sysctl.d/NetApp-Hana.conf* voor het configuratie bestand voor de configuratie-instellingen van NetApp.  

    <pre><code>
    vi /etc/sysctl.d/netapp-hana.conf
    # Add the following entries in the configuration file
    net.core.rmem_max = 16777216
    net.core.wmem_max = 16777216
    net.core.rmem_default = 16777216
    net.core.wmem_default = 16777216
    net.core.optmem_max = 16777216
    net.ipv4.tcp_rmem = 65536 16777216 16777216
    net.ipv4.tcp_wmem = 65536 16777216 16777216
    net.core.netdev_max_backlog = 300000 
    net.ipv4.tcp_slow_start_after_idle=0 
    net.ipv4.tcp_no_metrics_save = 1
    net.ipv4.tcp_moderate_rcvbuf = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_sack = 1
    </code></pre>

4. **[A] Maak een** configuratie bestand */etc/sysctl.d/MS-AZ.conf* met aanvullende optimalisatie-instellingen.  

    <pre><code>
    vi /etc/sysctl.d/ms-az.conf
    # Add the following entries in the configuration file
    net.ipv6.conf.all.disable_ipv6 = 1
    net.ipv4.tcp_max_syn_backlog = 16348
    net.ipv4.ip_local_port_range = 40000 65300
    net.ipv4.conf.all.rp_filter = 0
    sunrpc.tcp_slot_table_entries = 128
    vm.swappiness=10
    </code></pre>

5. **[A]** Wijzig de sunrpc-instellingen, zoals aanbevolen in de [NetApp SAP-toepassingen op Microsoft Azure met behulp van Azure NetApp files][anf-sap-applications-azure].  

    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

6. **[A]** Red Hat voor Hana-configuratie.

    RHEL configureren zoals beschreven in SAP Note [2292690], [2455582], [2593824] en <https://access.redhat.com/solutions/2447641> .

    > [!NOTE]
    > Als u HANA 2,0 SP04 installeert, moet u het pakket installeren `compat-sap-c++-7` zoals beschreven in SAP-opmerking [2593824]voordat u SAP Hana kunt installeren. 

## <a name="mount-the-azure-netapp-files-volumes"></a>De Azure NetApp Files volumes koppelen

1. **[A]** koppel punten maken voor de Hana-database volumes.  

    <pre><code>
    mkdir -p /hana/data/<b>HN1</b>/mnt00001
    mkdir -p /hana/data/<b>HN1</b>/mnt00002
    mkdir -p /hana/log/<b>HN1</b>/mnt00001
    mkdir -p /hana/log/<b>HN1</b>/mnt00002
    mkdir -p /hana/shared
    mkdir -p /usr/sap/<b>HN1</b>
    </code></pre>

2. **[1]** Maak knoop punt-specifieke directory's voor/usr/sap op **HN1**-Shared.  

    <pre><code>
    # Create a temporary directory to mount <b>HN1</b>-shared
    mkdir /mnt/tmp
    # if using NFSv3 for this volume, mount with the following command
    mount <b>10.9.0.4</b>:/<b>HN1</b>-shared /mnt/tmp
    # if using NFSv4.1 for this volume, mount with the following command
    mount -t nfs -o sec=sys,vers=4.1 <b>10.9.0.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **[A]** Controleer de NFS-domein instelling. Zorg ervoor dat het domein is geconfigureerd als standaard Azure NetApp Files domein, dat wil zeggen **`defaultv4iddomain.com`** en dat de toewijzing is ingesteld op **niemand**.  

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
    mount 10.9.0.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

   Zie voor meer informatie over het wijzigen van `nfs4_disable_idmapping` para meters https://access.redhat.com/solutions/1749883 .

6. **[A]** Hiermee koppelt u de gedeelde Azure NetApp files volumes.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-data-mnt00001 /hana/data/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-data-mnt00002 /hana/data/<b>HN1</b>/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-log-mnt00001 /hana/log/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.9.0.4:/<b>HN1</b>-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    </code></pre>

7. **[1]** koppel de knooppunt-specifieke volumes op **hanadb1**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** koppel de knooppunt-specifieke volumes op **hanadb2**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** koppel de knooppunt-specifieke volumes op **hanadb3**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** Controleer of alle Hana-volumes zijn gekoppeld met de NFS-protocol versie **NFSv4**.  

    <pre><code>
    sudo nfsstat -m
    # Verify that flag vers is set to <b>4.1</b> 
    # Example from <b>hanadb1</b>
    /hana/data/<b>HN1</b>/mnt00001 from 10.9.0.4:/<b>HN1</b>-data-mnt00001
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/log/<b>HN1</b>/mnt00002 from 10.9.0.4:/<b>HN1</b>-log-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/data/<b>HN1</b>/mnt00002 from 10.9.0.4:/<b>HN1</b>-data-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/log/<b>HN1</b>/mnt00001 from 10.9.0.4:/<b>HN1</b>-log-mnt00001
    Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /usr/sap/<b>HN1</b> from 10.9.0.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b>
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    /hana/shared from 10.9.0.4:/<b>HN1</b>-shared/shared
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.9.3.4,local_lock=none,addr=10.9.0.4
    </code></pre>

## <a name="installation"></a>Installatie  

In dit voor beeld voor de implementatie van SAP HANA in scale-out configuratie met een stand-by-knoop punt met Azure, hebben we HANA 2,0 SP4 gebruikt.  

### <a name="prepare-for-hana-installation"></a>Voor bereiding voor HANA-installatie

1. **[A]** vóór de Hana-installatie stelt u het hoofd wachtwoord in. U kunt het hoofd wachtwoord uitschakelen nadat de installatie is voltooid. Opdracht uitvoeren `root` als `passwd` .  

2. **[1]** Controleer of u zich kunt aanmelden via SSH naar **hanadb2** en **hanadb3**, zonder dat u wordt gevraagd om een wacht woord.  

    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A]** aanvullende pakketten installeren die vereist zijn voor Hana 2,0 SP4. Zie SAP Note [2593824](https://launchpad.support.sap.com/#/notes/2593824)voor meer informatie. 

    <pre><code>
    yum install libgcc_s1 libstdc++6 compat-sap-c++-7 libatomic1 
    </code></pre>

4. **[2], [3]** Wijzig het eigendom van SAP HANA `data` en `log` directory's in **HN1** adm.   

    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

5. **[A]** de firewall tijdelijk uitschakelen, zodat deze de Hana-installatie niet verstoort. U kunt deze opnieuw inschakelen nadat de HANA-installatie is voltooid. 
   <pre><code>
    # Execute as root
    systemctl stop firewalld
    systemctl disable firewalld
   </code></pre>

### <a name="hana-installation"></a>HANA-installatie

1. **[1]** installeer SAP Hana door de instructies in de [SAP Hana 2,0-installatie en de update gids](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html)te volgen. In dit voor beeld installeren we SAP HANA scale-out met Master, één werk nemer en één stand-by-knoop punt.  

   a. Start het **hdblcm** -programma vanuit de map Hana-installatie software. Gebruik de `internal_network` para meter en geef de adres ruimte door voor het subnet, dat wordt gebruikt voor de interne Hana-communicatie tussen knoop punten.  

    <pre><code>
    ./hdblcm --internal_network=10.9.2.0/26
    </code></pre>

   b. Voer bij de prompt de volgende waarden in:

     * Voor **Kies een actie**: Voer **1** (voor installatie) in
     * Voor **aanvullende onderdelen voor installatie**: Voer **2, 3** in
     * Voor installatiepad: druk op ENTER (standaard ingesteld op/Hana/Shared)
     * Voor de naam van de **lokale host**: druk op ENTER om de standaard instelling te accepteren
     * Onder wilt **u hosts toevoegen aan het systeem?**: Voer **y** in
     * Voor **het toevoegen door komma's gescheiden hostnamen**: Voer **hanadb2 in, hanadb3**
     * Voor **root-gebruikers naam** [root]: druk op ENTER om de standaard instelling te accepteren
     * Voor rollen voor host hanadb2: Voer **1**  (voor werk nemer) in
     * Voor **host-failovergroep** voor host hanadb2 [standaard]: druk op ENTER om de standaard instelling te accepteren
     * Voor het **opslag partitie nummer** voor host hanadb2 [<<assign automatically>>]: druk op ENTER om de standaard waarde te accepteren
     * Voor de **werk groep** voor host hanadb2 [standaard]: druk op ENTER om de standaard instelling te accepteren
     * Voor **Select rollen** voor host hanadb3: Voer **2** in (voor stand-by)
     * Voor **host-failovergroep** voor host hanadb3 [standaard]: druk op ENTER om de standaard instelling te accepteren
     * Voor de **werk groep** voor host hanadb3 [standaard]: druk op ENTER om de standaard instelling te accepteren
     * Voer voor **SAP Hana systeem-id**: **HN1**
     * Voor **instantie nummer** [00]: Voer **03** in
     * Voor de **lokale host-werk groep** [standaard]: druk op ENTER om de standaard instelling te accepteren
     * Voor **Selecteer systeem gebruik/Voer index [4]**: Voer **4** in (voor aangepast)
     * Voor **locatie van gegevens volumes** [/Hana/data/HN1]: druk op ENTER om de standaard instelling te accepteren
     * Voor de **locatie van de logboek volumes** [/Hana/log/HN1]: druk op ENTER om de standaard instelling te accepteren
     * Voor **maximale geheugen toewijzing beperken?** [n]: Voer **n** in
     * Voor de naam van het **certificaat voor host hanadb1** [hanadb1]: druk op ENTER om de standaard waarde te accepteren
     * Voor de naam van het **certificaat voor host hanadb2** [hanadb2]: druk op ENTER om de standaard waarde te accepteren
     * Voor de naam van het **certificaat voor host hanadb3** [hanadb3]: druk op ENTER om de standaard waarde te accepteren
     * Voor het **wacht woord van de systeem beheerder (hn1adm)**: Voer het wacht woord in
     * Voor **systeem database gebruikers (systeem) wacht woord**: Voer het systeem wachtwoord in
     * Voor het bevestigen van het **wacht woord van de systeem database gebruiker (systeem)**: Voer het systeem wachtwoord in
     * **Systeem opnieuw opstarten na** het opnieuw opstarten van de computer? [n]: Voer **n** in 
     * Voor wilt **u door gaan (j/n)**: de samen vatting valideren en als alles goed lijkt, voert u **y** in


2. **[1]** global.ini controleren  

   Geef global.ini weer en zorg ervoor dat de configuratie voor de interne SAP HANA communicatie tussen knoop punten is ingesteld. Controleer het **communicatie** gedeelte. De adres ruimte van het `hana` subnet moet `listeninterface` worden ingesteld op `.internal` . Controleer de sectie **internal_hostname_resolution** . Deze moet de IP-adressen hebben voor de HANA-virtuele machines die deel uitmaken van het `hana` subnet.  

   <pre><code>
    sudo cat /usr/sap/<b>HN1</b>/SYS/global/hdb/custom/config/global.ini
    # Example 
    #global.ini last modified 2019-09-10 00:12:45.192808 by hdbnameserve
    [communication]
    internal_network = <b>10.9.2.0/26</b>
    listeninterface = .internal
    [internal_hostname_resolution]
    <b>10.9.2.4</b> = <b>hanadb1</b>
    <b>10.9.2.5</b> = <b>hanadb2</b>
    <b>10.9.2.6</b> = <b>hanadb3</b>
   </code></pre>

3. **[1]** toewijzing van hosts toevoegen om ervoor te zorgen dat de client-IP-adressen worden gebruikt voor client communicatie. Voeg een sectie toe `public_host_resolution` en voeg de bijbehorende IP-adressen uit het subnet van de client toe.  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.9.1.5</b>
    map_<b>hanadb2</b> = <b>10.9.1.6</b>
    map_<b>hanadb3</b> = <b>10.9.1.7</b>
   </code></pre>

4. **[1]** Start SAP Hana opnieuw op om de wijzigingen te activeren.  

   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** Controleer of de client interface de IP-adressen uit het subnet gebruikt `client` voor communicatie.  

   <pre><code>
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.9.1.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.9.1.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.9.1.5</b>"
   </code></pre>

   Zie SAP Note [2183363-configuratie van SAP Hana intern netwerk](https://launchpad.support.sap.com/#/notes/2183363)voor meer informatie over het controleren van de configuratie.  

5. **[A]** de firewall opnieuw inschakelen.  
   - HANA stoppen
       <pre><code>
        sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
       </code></pre>
   - De firewall opnieuw inschakelen
       <pre><code>
        # Execute as root
        systemctl start firewalld
        systemctl enable firewalld
       </code></pre>

   - Open de benodigde firewall poorten

       > [!IMPORTANT]
       > Maak firewall regels om HANA-communicatie tussen knoop punten en client verkeer toe te staan. De vereiste poorten worden vermeld op de [TCP/IP-poorten van alle SAP-producten](https://help.sap.com/viewer/ports). De volgende opdrachten zijn slechts een voor beeld. In dit scenario met het gebruikte systeem nummer 03.

       <pre><code>
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
       </code></pre>

   - HANA starten
       <pre><code>
        sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
       </code></pre>

6. Als u SAP HANA voor de onderliggende Azure NetApp Files opslag wilt optimaliseren, stelt u de volgende SAP HANA para meters in:

   - `max_parallel_io_requests`**128**
   - `async_read_submit`**op**
   - `async_write_submit_active`**op**
   - `async_write_submit_blocks`**alle**

   Zie voor meer informatie [NETAPP SAP-toepassingen op Microsoft Azure met behulp van Azure NetApp files][anf-sap-applications-azure]. 

   Vanaf SAP HANA 2,0-systemen kunt u de para meters instellen in `global.ini` . Zie SAP Note [1999930](https://launchpad.support.sap.com/#/notes/1999930)voor meer informatie.  
   
   Voor SAP HANA 1,0-systemen SPS12 en eerder, kunnen deze para meters tijdens de installatie worden ingesteld, zoals beschreven in SAP Note [2267798](https://launchpad.support.sap.com/#/notes/2267798).  

7. De opslag die wordt gebruikt door Azure NetApp Files heeft een maximale bestands grootte van 16 terabytes (TB). SAP HANA is niet impliciet op de hoogte van de opslag beperking en maakt niet automatisch een nieuw gegevens bestand wanneer de maximale bestands grootte van 16 TB is bereikt. Als SAP HANA probeert het bestand groter dan 16 TB uit te breiden, zullen deze pogingen tot fouten leiden en uiteindelijk in een index server vastlopen. 

   > [!IMPORTANT]
   > Stel de volgende para meters in om te voor komen dat SAP HANA probeert gegevens bestanden te verg Roten dan de [limiet van 16 TB](../../../azure-netapp-files/azure-netapp-files-resource-limits.md) van het opslag subsysteem `global.ini` .  
   > - datavolume_striping = True
   > - datavolume_striping_size_gb = 15000 Zie SAP Note [2400005](https://launchpad.support.sap.com/#/notes/2400005)voor meer informatie.
   > Houd rekening met SAP Note [2631285](https://launchpad.support.sap.com/#/notes/2631285). 

## <a name="test-sap-hana-failover"></a>SAP HANA failover testen 

1. Een knoop punt crash simuleren op een SAP HANA worker-knoop punt. Ga als volgt te werk: 

   a. Voordat u het knooppunt crash simuleert, voert u de volgende opdrachten uit als **HN1** adm om de status van de omgeving vast te leggen:  

   <pre><code>
    # Check the landscape status
    python /usr/sap/HN1/HDB<b>03</b>/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |

    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
   </code></pre>

   b. Als u een knoop punt crash wilt simuleren, voert u de volgende opdracht uit als basis op het worker-knoop punt, dat in dit geval **hanadb2** is:  
   
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   c. Controleer het systeem op voltooiing van de failover. Wanneer de failover is voltooid, moet u de status vastleggen, die er ongeveer als volgt uitziet:  

    <pre><code>
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | no     | info   |          |        |         2 |         0 | default  | default  | master 2   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb3 | yes    | info   |          |        |         0 |         2 | default  | default  | master 3   | slave      | standby     | slave       | standby | worker  | default | default |
   </code></pre>

   > [!IMPORTANT]
   > Wanneer een knoop punt kernel-paniek uitmaakt, vermijdt u vertragingen met SAP HANA failover door in te stellen op `kernel.panic` 20 seconden op *alle* Hana virtuele machines. De configuratie wordt uitgevoerd in `/etc/sysctl` . Start de virtuele machines opnieuw op om de wijziging te activeren. Als deze wijziging niet wordt uitgevoerd, kan de failover 10 of meer minuten duren wanneer een knoop punt zich in de kernel bevindt.  

2. Beëindig de naam server door het volgende te doen:

   a. Voordat u de test uitvoert, controleert u de status van de omgeving door de volgende opdrachten uit te voeren als **HN1** adm:  

   <pre><code>
    #Landscape status 
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
   </code></pre>

   b. Voer de volgende opdrachten uit als **HN1** adm op het actieve hoofd knooppunt, dat in dit geval **hanadb1** is:  

    <pre><code>
        hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
    </code></pre>
    
    Het **hanadb3** van het stand-by-knoop punt gaat over als hoofd knooppunt. Dit is de status van de resource nadat de testfailover is voltooid:  

    <pre><code>
     # Check the instance status
     sapcontrol -nr 03  -function GetSystemInstanceList
     GetSystemInstanceList
     OK
     hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
     hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
     hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
     hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
     # Check the landscape status
     python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
     | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
     |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
     |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
     | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
     | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
     | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
     | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
    </code></pre>

   c. Start het HANA-exemplaar op **hanadb1** (dat wil zeggen, op dezelfde virtuele machine, waarbij de naam server is afgebroken). Het **hanadb1** -knoop punt wordt opnieuw lid van de omgeving en blijft de functie stand-by.  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Nadat SAP HANA op **hanadb1** is gestart, wordt de volgende status verwacht:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   d. Beëindig opnieuw de naam server op het huidige actieve hoofd knooppunt (op het knoop punt **hanadb3**).  
   
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   De rol van het hoofd knooppunt wordt hervat met het knoop punt **hanadb1** . Nadat de testfailover is voltooid, ziet de status er als volgt uit:

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

   e. Start SAP HANA op **hanadb3**, dat klaar is om te fungeren als stand-by-knoop punt.  

   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Nadat SAP HANA op **hanadb3** is gestart, ziet de status er als volgt uit:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

## <a name="next-steps"></a>Volgende stappen

* [Azure Virtual Machines planning en implementatie voor SAP][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP][dbms-guide]
* Zie [hoge Beschik baarheid van SAP Hana op azure virtual machines (vm's)][sap-hana-ha]voor meer informatie over het instellen van hoge Beschik baarheid en het plannen van nood herstel van SAP Hana op Azure-vm's.
