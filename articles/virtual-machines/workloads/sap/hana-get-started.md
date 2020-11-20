---
title: Installatie van SAP HANA op virtuele machines van Azure | Microsoft Docs '
description: Hand leiding voor het installeren van SAP HANA op Azure-Vm's
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/19/2020
ms.author: juergent
ms.openlocfilehash: 6c1b19927207e1accb4ae6c47625eed41a6b0b92
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967785"
---
# <a name="installation-of-sap-hana-on-azure-virtual-machines"></a>Installatie van SAP HANA op virtuele machines van Azure
## <a name="introduction"></a>Inleiding
Deze hand leiding helpt u bij het aanwijzen van de juiste resources om HANA in azure virtual machines te implementeren. Deze hand leiding gaat u naar documentatie bronnen die u moet controleren voordat u SAP HANA installeert in een Azure-VM. Daarom kunt u de juiste stappen uitvoeren om te eindigen met een ondersteunde configuratie van SAP HANA in azure Vm's.  

> [!NOTE]
> In deze hand leiding worden de implementaties van SAP HANA in azure Vm's beschreven. Zie [SAP Hana (grote exemplaren) installeren en configureren in azure](./hana-installation.md)voor meer informatie over het implementeren van SAP Hana in Hana grote instanties.
 
## <a name="prerequisites"></a>Vereisten
In deze hand leiding wordt ervan uitgegaan dat u bekend bent met:
* SAP HANA en SAP NetWeaver en hoe ze on-premises moeten worden geïnstalleerd.
* SAP HANA en SAP-toepassings exemplaren installeren en uitvoeren op Azure.
* De concepten en procedures die zijn beschreven in:
   * Planning voor SAP-implementatie op Azure, waaronder Azure Virtual Network planning en Azure Storage gebruik. Zie [SAP NetWeaver op Azure virtual machines-planning en implementatie handleiding](./planning-guide.md)
   * Implementatie principes en manieren om Vm's in azure te implementeren. Zie [Azure virtual machines-implementatie voor SAP](./deployment-guide.md)
   * Concepten met hoge Beschik baarheid voor SAP HANA zoals beschreven in [SAP Hana hoge Beschik baarheid voor virtuele machines van Azure](./sap-hana-availability-overview.md)

## <a name="step-by-step-before-deploying"></a>Stap voor stap voordat u implementeert
In deze sectie worden de verschillende stappen vermeld die u moet uitvoeren voordat u begint met de installatie van SAP HANA op een virtuele Azure-machine. De volg orde wordt opgesomd en moet zo worden gevolgd dat deze wordt geïnventariseerd:

1. Niet alle mogelijke implementatie scenario's worden ondersteund op Azure. Daarom moet u de [SAP-werk belasting van het document controleren op ondersteunde scenario's voor virtuele Azure-machines](./sap-planning-supported-configurations.md) voor het scenario dat u in acht neemt met uw SAP Hana-implementatie. Als het scenario niet wordt weer gegeven, moet u aannemen dat het niet is getest en daarom niet wordt ondersteund
2. Ervan uitgaande dat u een ruwe indruk hebt op de geheugen vereisten voor uw SAP HANA-implementatie, moet u een aanpasbaar Azure-VM vinden. Niet alle virtuele machines die zijn gecertificeerd voor SAP NetWeaver, zoals beschreven in [SAP-ondersteunings opmerking #1928533](https://launchpad.support.sap.com/#/notes/1928533), zijn SAP Hana gecertificeerd. De bron van waarheid voor SAP HANA gecertificeerde Azure-Vm's is de website [SAP Hana hardware-map](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). De eenheden die beginnen met **S** zijn Hana-eenheden voor [grote instanties](./hana-overview-architecture.md) en geen virtuele Azure-machines.
3. Verschillende Azure VM-typen hebben verschillende minimum versies van het besturings systeem voor SUSE Linux of Red Hat Linux. Op de website [SAP Hana hardware-map](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure), klikt u op een vermelding in de lijst met SAP Hana gecertificeerde eenheden om gedetailleerde gegevens van deze eenheid op te halen. Naast de ondersteunde HANA-workloads, worden de versies van het besturings systeem die worden ondersteund met deze eenheden voor SAP HANA vermeld
4. Vanaf besturingssysteem releases moet u rekening houden met bepaalde minimale kernel-releases. Deze minimale releases worden beschreven in deze SAP-ondersteunings opmerkingen:
    - [SAP-ondersteunings Opmerking #2814271 SAP HANA back-up mislukt op Azure met controlesom fout](https://launchpad.support.sap.com/#/notes/2814271)
    - [SAP-ondersteunings Opmerking #2753418 mogelijke prestatie vermindering door timer terugval](https://launchpad.support.sap.com/#/notes/2753418)
    - [SAP-ondersteunings Opmerking #2791572 prestatie vermindering vanwege ontbrekende VDSO-ondersteuning voor Hyper-V in azure](https://launchpad.support.sap.com/#/notes/2791572)
4. Op basis van de besturingssysteem versie die wordt ondersteund voor het type van de virtuele machine, moet u controleren of de gewenste SAP HANA versie wordt ondersteund door de versie van het besturings systeem. Lees [SAP-ondersteunings notitie #2235581](https://launchpad.support.sap.com/#/notes/2235581) voor een ondersteunings matrix van SAP Hana releases met de verschillende versies van het besturings systeem.
5. Als u een geldige combi natie van een Azure VM-type, versie van het besturings systeem en SAP HANA versie hebt gevonden, moet u de SAP-product beschikbaarheids matrix controleren. In de SAP-beschikbaarheids matrix kunt u nagaan of het SAP-product dat u wilt uitvoeren op uw SAP HANA-data base wordt ondersteund.


## <a name="step-by-step-vm-deployment-and-guest-os-considerations"></a>Overwegingen voor stap-voor-stap VM-implementatie en gast besturingssysteem
In deze fase moet u de stappen door lopen om HANA te installeren en uiteindelijk het gekozen besturings systeem na de installatie te optimaliseren.

1. Kies de basis installatie kopie uit de Azure-galerie. Als u uw eigen installatie kopie van een besturings systeem voor SAP HANA wilt maken, moet u alle andere pakketten kennen die nodig zijn om een geslaagde SAP HANA installatie te kunnen volt ooien. Anders is het raadzaam om de SUSE-en Red Hat-installatie kopieën voor SAP of SAP HANA uit de galerie met installatie kopieën van Azure te gebruiken. Deze installatie kopieën bevatten de pakketten die nodig zijn voor een geslaagde HANA-installatie. Op basis van uw ondersteunings contract met de provider van het besturings systeem moet u een installatie kopie kiezen waarin u uw eigen licentie brengt. Of u kiest een installatie kopie van het besturings systeem dat ondersteuning bevat
2. Als u een installatie kopie voor een gast besturingssysteem hebt gekozen waarvoor u uw eigen licentie nodig hebt, moet u de installatie kopie van het besturings systeem registreren bij uw abonnement, zodat u de meest recente patches kunt downloaden en Toep assen. In deze stap wordt de open bare Internet toegang vereist. Tenzij u uw persoonlijke exemplaar van instelt, bijvoorbeeld een SMT-server in Azure.
3. Bepaal de netwerk configuratie van de virtuele machine. Meer informatie vindt u in het document [SAP Hana infrastructuur configuraties en-bewerkingen in azure](./hana-vm-operations.md). Houd er wel bij dat er geen quota voor netwerk doorvoer kunnen worden toegewezen aan virtuele netwerk kaarten in Azure. Als gevolg hiervan is het enige doel om verkeer via verschillende Vnic's te omleiden, gebaseerd op beveiligings overwegingen. We vertrouwen u om een ondersteunings risico te vinden tussen complexiteit van verkeers routering via meerdere Vnic's en de vereisten die worden afgedwongen door beveiligings aspecten.
3. Pas de meest recente patches toe op het besturings systeem wanneer de virtuele machine is geïmplementeerd en geregistreerd. Geregistreerd bij uw eigen abonnement. Of als u een installatie kopie hebt gekozen die ondersteuning biedt voor het besturings systeem, moet de VM al toegang hebben tot de patches. 
4. Pas de benodigde afstemmingen voor SAP HANA toe. Deze afstemmingen worden vermeld in deze SAP-ondersteunings opmerkingen:

    - [SAP-ondersteuning: #2694118 Red Hat Enterprise Linux HA Add-On op Azure](https://launchpad.support.sap.com/#/notes/2694118)
    - [SAP-ondersteuning Opmerking #1984787-SUSE LINUX Enter prise Server 12: installatie notities](https://launchpad.support.sap.com/#/notes/1984787) 
    - [SAP-ondersteunings Opmerking #2578899-SUSE Linux Enterprise Server 15: installatie Opmerking](https://launchpad.support.sap.com/#/notes/2578899)
    - [SAP-ondersteunings Opmerking #2002167-Red Hat Enterprise Linux 7. x: installatie en upgrade](https://launchpad.support.sap.com/#/notes/0002002167)
    - [SAP-ondersteunings Opmerking #2292690-SAP HANA DB: aanbevolen besturingssysteem instellingen voor RHEL 7](https://launchpad.support.sap.com/#/notes/0002292690) 
    -  [SAP-ondersteunings Opmerking #2772999-Red Hat Enterprise Linux 8. x: installatie en configuratie](https://launchpad.support.sap.com/#/notes/2772999) 
    -  [SAP-ondersteunings Opmerking #2777782-SAP HANA DB: aanbevolen besturingssysteem instellingen voor RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
    -  [SAP-ondersteuning Opmerking #2455582-Linux: SAP-toepassingen uitvoeren die zijn gecompileerd met GCC 6. x](https://launchpad.support.sap.com/#/notes/0002455582)
    -  [SAP-ondersteunings Opmerking #2382421-optimalisatie van de netwerk configuratie op HANA-en OS-niveau](https://launchpad.support.sap.com/#/notes/2382421)

1. Selecteer het Azure-opslag type voor SAP HANA. In deze stap moet u beslissen over de opslag indeling voor de SAP HANA-installatie. U gaat gebruikmaken van gekoppelde Azure-schijven of de systeem eigen Azure NFS-shares. De Azure-opslag typen of ondersteunde en combi Naties van verschillende Azure-opslag typen die kunnen worden gebruikt, worden beschreven in [SAP Hana opslag configuraties van virtuele Azure-machines](./hana-vm-operations-storage.md). Volg de configuraties die worden beschreven als uitgangs punt. Voor niet-productie systemen kunt u mogelijk een lagere door Voer of IOPS configureren. Voor productie doeleinden moet u mogelijk een bit meer door Voer en IOPS configureren.
2. Zorg ervoor dat u [Azure write Accelerator](../../how-to-enable-write-accelerator.md) hebt geconfigureerd voor uw volumes die de DBMS-transactie logboeken bevatten of Logboeken opnieuw uitvoeren wanneer u met de M-serie of Mv2-Series-vm's werkt. Houd rekening met de beperkingen voor Write Accelerator zoals beschreven.
2. Controleer of [Azure versneld netwerken](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) is ingeschakeld op de virtuele machine (s) die zijn geïmplementeerd.

> [!NOTE]
> Niet alle opdrachten in de verschillende SAP-Tune-profielen of zoals beschreven in de opmerkingen, kunnen worden uitgevoerd in Azure. Opdrachten die de energie modus van Vm's zouden wijzigen, retour neren doorgaans een fout, omdat de energie modus van de onderliggende hardware van Azure host niet kan worden gemanipuleerd.

## <a name="step-by-step-preparations-specific-to-azure-virtual-machines"></a>Stapsgewijze voor bereidingen die specifiek zijn voor virtuele Azure-machines
Een van de details van Azure is de installatie van een Azure VM-extensie die bewakings gegevens levert voor de SAP host-agent. De details over de installatie van deze bewakings uitbreiding worden beschreven in:

-  [SAP Note 2191498](https://launchpad.support.sap.com/#/notes/2191498/E) BEspreekt SAP-verbeterde bewaking met Linux-Vm's in azure 
-  [SAP Note 1102124](https://launchpad.support.sap.com/#/notes/1102124/E) bevat informatie over SAPOSCOL in Linux 
-  [SAP-opmerking 2178632](https://launchpad.support.sap.com/#/notes/2178632/E) bevat informatie over de belangrijkste bewakings gegevens voor SAP op Microsoft Azure
-  [Azure Virtual Machines-implementatie voor SAP NetWeaver](./deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca)

## <a name="sap-hana-installation"></a>Installatie van SAP HANA
Als de virtuele machines van Azure zijn geïmplementeerd en de besturings systemen zijn geregistreerd en geconfigureerd, kunt u SAP HANA installeren volgens de SAP-installatie. Als u aan deze documentatie wilt gaan, kunt u beginnen met deze SAP website [Hana-resources](https://www.sap.com/products/hana/implementation/resources.html)

Voor SAP HANA scale-out-configuraties met behulp van direct gekoppelde schijven van Azure Premium Storage of Ultra disk leest u de specifieke informatie in het document [SAP Hana infrastructuur configuraties en-bewerkingen op Azure](./hana-vm-operations.md#configuring-azure-infrastructure-for-sap-hana-scale-out)


## <a name="additional-resources-for-sap-hana-backup"></a>Aanvullende bronnen voor SAP HANA back-up
Zie voor informatie over het maken van een back-up van SAP HANA-data bases op virtuele machines van Azure:
* [Back-upgids voor SAP HANA op Azure Virtual Machines](./sap-hana-backup-guide.md)
* [Azure Backup op bestands niveau SAP HANA](./sap-hana-backup-file-level.md)

## <a name="next-steps"></a>Volgende stappen
Lees de documentatie:

- [Configuraties en bewerkingen van SAP HANA-infrastructuur in Azure](./hana-vm-operations.md)
- [Configuraties van SAP HANA in virtuele Azure-machineopslag](./hana-vm-operations-storage.md)
