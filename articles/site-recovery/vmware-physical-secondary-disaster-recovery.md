---
title: Herstel na nood geval voor virtuele VMware-machines/fysieke servers naar een secundaire site met Azure Site Recovery
description: Meer informatie over het instellen van herstel na nood gevallen van virtuele VMware-machines of fysieke Windows-en Linux-servers naar een secundaire site met Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: raynew
ms.openlocfilehash: 71d230c9fea25edfbf0ca4ea40f15b69779ad060
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84711898"
---
# <a name="set-up-disaster-recovery-of-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>Herstel na noodgeval instellen voor on-premises virtuele VMware-machines of fysieke servers naar een secundaire site

Inmage Scout in [Azure site Recovery](site-recovery-overview.md) biedt realtime replicatie tussen on-premises VMware-sites. Inmage Scout is opgenomen in Azure Site Recovery service abonnementen.

## <a name="end-of-support-announcement"></a>Aankondiging van aflopen van ondersteuning

Het Azure Site Recovery scenario voor replicatie tussen on-premises VMware-of fysieke data centers bereikt de volledige ondersteuning.

-   Vanaf 2018 augustus kan het scenario niet worden geconfigureerd in de Recovery Services kluis en kan de inmage Scout-software niet worden gedownload van de kluis. Bestaande implementaties worden ondersteund. 
-   Van 31 2020 december wordt het scenario niet ondersteund.
- Bestaande partners kunnen nieuwe klanten aan het scenario vrijgeven totdat de ondersteuning wordt beëindigd.

Tijdens 2018 en 2019 worden er twee updates uitgebracht: 

-   Update 7: lost problemen met de netwerk configuratie en naleving op en biedt ondersteuning voor TLS 1,2.
-   Update 8: voegt ondersteuning toe voor Linux-besturings systemen RHEL/CentOS 7.3/7,4/7.5 en voor SUSE 12

Na update 8 worden er geen verdere updates uitgebracht. Er wordt beperkte hotfix-ondersteuning geboden voor de besturings systemen die zijn toegevoegd in update 8 en probleem oplossingen op basis van de beste poging.

Azure Site Recovery blijft innoveren door VMware-en Hyper-V-klanten een naadloze en best mogelijke DRaaS-oplossing te bieden met Azure als een nood herstel site. Micro soft raadt aan bestaande inmage/ASR Scout-klanten te overwegen om de bedrijfs continuïteit van Azure Site Recovery van VMware naar Azure te gebruiken. Het scenario van VMware naar Azure van Azure Site Recovery is een oplossing op bedrijfs niveau voor VMware-toepassingen, die RPO en RTO aan minuten biedt, ondersteuning voor toepassings replicatie en-herstel met meerdere VM'S, naadloze onboarding, uitgebreide bewaking en een aanzienlijke TCO-voor deel.

### <a name="scenario-migration"></a>Scenario migratie
Als alternatief kunt u het beste herstel na nood gevallen instellen voor on-premises VMware-Vm's en fysieke machines door ze te repliceren naar Azure. Doe dit als volgt:

1.  Bekijk de onderstaande snelle vergelijking. Voordat u on-premises machines kunt repliceren, moet u controleren of ze voldoen aan de [vereisten](./vmware-physical-azure-support-matrix.md#replicated-machines) voor replicatie naar Azure. Als u virtuele VMware-machines repliceert, raden we u aan de [richt lijnen voor capaciteits planning](./site-recovery-plan-capacity-vmware.md)te controleren en het [Deployment planner-hulp programma](./site-recovery-deployment-planner.md) uit te voeren op identiteits vereisten en om de compatibiliteit te controleren.
2.  Nadat u de Deployment Planner hebt uitgevoerd, kunt u replicatie configureren: o voor VMware-Vm's, volgt u deze zelf studies om Azure voor te [bereiden](./tutorial-prepare-azure.md), [uw on-premises VMware-omgeving voor te bereiden](./vmware-azure-tutorial-prepare-on-premises.md)en [herstel na nood](./vmware-azure-tutorial-prepare-on-premises.md)geval in te stellen.
o voor fysieke computers, volgt u deze [zelf studie](./physical-azure-disaster-recovery.md).
3.  Nadat de computers zijn gerepliceerd naar Azure, kunt u een [nood herstel analyse](./site-recovery-test-failover-to-azure.md) uitvoeren om te controleren of alles werkt zoals verwacht.

### <a name="quick-comparison"></a>Snelle vergelijking

**Functie** | **Replicatie naar Azure** |**Replicatie tussen VMware-data centers**
--|--|--
**Vereiste onderdelen** |Mobility service op gerepliceerde machines. On-premises configuratie server, proces server, Master doel server. Tijdelijke proces server in azure voor failback.|Mobility-service, proces server, configuratie server en hoofd doel
**Configuratie en indeling** |Recovery Services kluis in de Azure Portal | VContinuum gebruiken 
**Gerepliceerd** |Schijf (Windows en Linux) |Volume-Windows<br> Disk-Linux
**Gedeeld schijf cluster** |Niet ondersteund|Ondersteund
**Limieten voor gegevens verloop (gemiddeld)** |10 MB/s gegevens per schijf<br> 25MB/s gegevens per VM<br> [Meer informatie](./site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) | > 10 MB/s gegevens per schijf  <br> > 25 MB/s gegevens per VM
**Controle** |Van Azure Portal|Van CX (configuratie server)
**Ondersteunings matrix** | [Klik hier voor meer informatie](./vmware-physical-azure-support-matrix.md)|[Met ASR Scout compatibele matrix downloaden](https://aka.ms/asr-scout-cm)


## <a name="prerequisites"></a>Vereisten
Vereisten voor het voltooien van deze zelfstudie:

- [Bekijk](vmware-physical-secondary-support-matrix.md) de ondersteunings vereisten voor alle onderdelen.
- Zorg ervoor dat de computers die u wilt repliceren, voldoen aan de ondersteuning van de [gerepliceerde machine](vmware-physical-secondary-support-matrix.md#replicated-vm-support).


## <a name="download-and-install-component-updates"></a>Downloaden en installeren van onderdeel updates

 Bekijk en installeer de meest recente [updates](#updates). Updates moeten in de volgende volg orde op de servers worden geïnstalleerd:

1. RX-server (indien van toepassing)
2. Configuratie servers
3. Proces servers
4. Master doel servers
5. vContinuum-servers
6. Bron server (zowel Windows-als Linux-servers)

Installeer de updates als volgt:

> [!NOTE]
>De versie van de update van de bestanden van de Scout-onderdelen kan niet hetzelfde zijn in het bestand Update. zip. De oudere versie geeft aan dat het onderdeel sinds de vorige update van deze update niet is gewijzigd.

Down load het bestand [Update](https://aka.ms/asr-scout-update7) . zip en de upgrade configuratie bestanden voor [MySQL en PHP](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade) . Het bestand Update. zip bevat alle binaire bestanden voor binaire bestanden en cumulatieve upgrades van de volgende onderdelen: 
- InMage_ScoutCloud_RX_8 InMage_ScoutCloud_RX_8.0.1.0_RHEL6-64_GA_02Mar2015. tar. gz
- RX_8 RX_8.0.7.0_GA_Update_7_2965621_28Dec18. tar. gz
- InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe
- InMage_CX_TP_8.0.1.0_Windows_GA_26Feb2015_release.exe
- CX_Windows_8.0.7.0_GA_Update_7_2965621_28Dec18.exe
- InMage_PI_8.0.1.0_Windows_GA_26Feb2015_release.exe
- InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe
- InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe
- InMage_UA_8 InMage_UA_8.0.7.0_OL5-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_OL5-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_OL6-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_OL6-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_RHEL5-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_RHEL5-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_RHEL6-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_RHEL7-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP1-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP1-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP2-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP2-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP3-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP3-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP4-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP4-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-64_GA_04Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-SP1-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-SP1-64_GA_04Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-SP2-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-SP2-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-SP3-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-SP3-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-SP4-64_GA_03Dec2018_release. tar. gz
  1. Pak de zip-bestanden uit.
  2. **RX-server**: Kopieer **RX_8.0.7.0_GA_Update_7_2965621_28Dec18. tar. gz** naar de RX-server en pak deze uit. Voer in de uitgepakte map **/install** uit.
  3. **Configuratie server en proces server**: **CX_Windows_8.0.7.0_GA_Update_7_2965621_28Dec18.exe** kopiëren naar de configuratie server en de proces server. Dubbel klik om het uit te voeren.<br>
  4. **Windows-hoofddoel server**: als u de Unified agent wilt bijwerken, moet u **InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe** kopiëren naar de-server. Dubbel klik erop om het uit te voeren. Hetzelfde bestand kan ook worden gebruikt voor een nieuwe installatie. Dezelfde Unified agent update is ook van toepassing op de bron server.
  De update hoeft niet te worden toegepast op het hoofd doel dat is voor bereid met **InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe**  omdat dit nieuw ga-installatie programma is met alle laatste wijzigingen.
  5. **vContinuum-server**: **InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe** kopiëren naar de server.  Zorg ervoor dat u de vContinuum-wizard hebt gesloten. Dubbel klik op het bestand om het uit te voeren.
  6. **Linux-hoofddoel server**: als u de Unified agent wilt bijwerken, kopieert u **InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release. tar. gz** naar de Linux-hoofddoel server en pakt u deze uit. Voer in de uitgepakte map **/install** uit.
  7. **Windows-bron server**: als u de Unified agent wilt bijwerken, moet u **InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe** kopiëren naar de bron server. Dubbel klik op het bestand om het uit te voeren. 
  8. **Linux-bron server**: als u de Unified agent wilt bijwerken, kopieert u de bijbehorende versie van het Unified agent-bestand naar de Linux-server en pakt u het uit. Voer in de uitgepakte map **/install** uit.  Voor beeld: Kopieer **InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release. tar. gz** naar de server en pak het uit voor RHEL 6,7 64-bits server. Voer in de uitgepakte map **/install** uit.
  9. Na de upgrade van de configuratie server, de proces server en de RX-server met de bovengenoemde installatie Programma's, moet de PHP-en MySQL-bibliotheken hand matig worden bijgewerkt met de stappen die worden beschreven in sectie 7,4 van de [hand leiding voor snelle installatie](https://aka.ms/asr-scout-quick-install-guide).

## <a name="enable-replication"></a>Replicatie inschakelen

1. Stel de replicatie tussen de bron-en doel-VMware-sites in.
2. Raadpleeg de volgende documenten voor meer informatie over de installatie, beveiliging en het herstel:

   * [Releaseopmerkingen](https://aka.ms/asr-scout-release-notes)
   * [Compatibiliteits matrix](https://aka.ms/asr-scout-cm)
   * [Gebruikershandleiding](https://aka.ms/asr-scout-user-guide)
   * [Gebruikers handleiding voor RX](https://aka.ms/asr-scout-rx-user-guide)
   * [Hand leiding voor snelle installatie](https://aka.ms/asr-scout-quick-install-guide)
   * [MYSQL-en PHP-bibliotheken bijwerken](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade)

## <a name="updates"></a>Updates

### <a name="site-recovery-scout-801-update-7"></a>Site Recovery Scout 8.0.1 update 7 
Bijgewerkt: 31 december 2018 down load [Scout update 7](https://aka.ms/asr-scout-update7).
Scout update 7 is een volledig installatie programma dat kan worden gebruikt voor de nieuwe installatie en om bestaande agents/MT bij te werken die zich op eerdere updates bevinden (van update 1 naar Update 6). Het bevat alle oplossingen van update 1 tot update 6 plus de nieuwe oplossingen en verbeteringen die hieronder worden beschreven.
 
#### <a name="new-features"></a>Nieuwe functies
* PCI-naleving
* TLS v 1.2-ondersteuning

#### <a name="bug-and-security-fixes"></a>Problemen met bug en beveiliging
* Opgelost: Windows-cluster/zelfstandige machines hebben een onjuiste IP-configuratie bij herstel/DR-analyse.
* Opgelost: soms wordt een schijf bewerking voor het V2V-cluster mislukt.
* Opgelost: de wizard vContinuum raakt vastgelopen tijdens de herstel fase als het hoofd doel Windows Server 2016 is
* Opgelost: MySQL-beveiligings problemen worden verholpen door MySQL te upgraden naar versie 5.7.23

#### <a name="manual-upgrade-for-php-and-mysql-on-csps-and-rx"></a>Hand matige upgrade voor PHP en MySQL op CS, PS en RX
Het PHP-script platform moet worden bijgewerkt naar versie 7.2.10 op de configuratie server, de proces server en de RX-server.
De MySQL-Database Management System moet worden bijgewerkt naar versie 5.7.23 op de configuratie server, de proces server en de RX-server.
Volg de hand matige stappen in de [hand leiding voor snelle installatie](https://aka.ms/asr-scout-quick-install-guide) om de PHP-en mysql-versies bij te werken.

### <a name="site-recovery-scout-801-update-6"></a>Site Recovery Scout 8.0.1 update 6 
Bijgewerkt: 12 oktober 2017

Down load [Scout update 6](https://aka.ms/asr-scout-update6).

Scout update 6 is een cumulatieve update. Het bevat alle correcties van update 1 tot en met Update 5 plus de nieuwe oplossingen en verbeteringen die hieronder worden beschreven. 

#### <a name="new-platform-support"></a>Ondersteuning voor nieuwe platforms
* Er is ondersteuning toegevoegd voor de bron Windows Server 2016
* Er is ondersteuning toegevoegd voor de volgende Linux-besturings systemen:
    - Red Hat Enterprise Linux (RHEL) 6,9
    - CentOS 6,9
    - Oracle Linux 5,11
    - Oracle Linux 6,8
* Er is ondersteuning toegevoegd voor VMware Center 6,5

Installeer de updates als volgt:

> [!NOTE]
>De versie van de update van de bestanden van de Scout-onderdelen kan niet hetzelfde zijn in het bestand Update. zip. De oudere versie geeft aan dat het onderdeel sinds de vorige update van deze update niet is gewijzigd.

Down load het bestand [Update](https://aka.ms/asr-scout-update6) . zip. Het bestand bevat de volgende onderdelen: 
- RX_8 RX_8.0.4.0_GA_Update_4_8725872_16Sep16. tar. gz
- CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe
- UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
- UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16. tar. gz
- vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe
- UA update4 bits voor RHEL5, OL5, OL6, SUSE 10, SUSE 11: UA_ \<Linux OS> _8.0.4.0_GA_Update_4_9035261_26Sep16. tar. gz
  1. Pak de zip-bestanden uit.
  2. **RX-server**: Kopieer **RX_8.0.4.0_GA_Update_4_8725872_16Sep16. tar. gz** naar de RX-server en pak deze uit. Voer in de uitgepakte map **/install** uit.
  3. **Configuratie server en proces server**: **CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe** kopiëren naar de configuratie server en de proces server. Dubbel klik om het uit te voeren.<br>
  4. **Windows-hoofddoel server**: als u de Unified agent wilt bijwerken, moet u **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** kopiëren naar de-server. Dubbel klik erop om het uit te voeren. Dezelfde Unified agent update is ook van toepassing op de bron server. Als de bron niet is bijgewerkt naar Update 4, moet u de Unified agent bijwerken.
  De update hoeft niet te worden toegepast op het hoofd doel dat is voor bereid met **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe**  omdat dit nieuw ga-installatie programma is met alle laatste wijzigingen.
  5. **vContinuum-server**: **vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe** kopiëren naar de server.  Zorg ervoor dat u de vContinuum-wizard hebt gesloten. Dubbel klik op het bestand om het uit te voeren.
  De update hoeft niet te worden toegepast op het hoofd doel dat is voor bereid met **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** omdat dit nieuw ga-installatie programma is met alle laatste wijzigingen.
  6. **Linux-hoofddoel server**: als u de Unified agent wilt bijwerken, kopieert u **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16. tar. gz** naar de hoofddoel server en pakt u deze uit. Voer in de uitgepakte map **/install** uit.
  7. **Windows-bron server**: als u de Unified agent wilt bijwerken, moet u **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** kopiëren naar de bron server. Dubbel klik op het bestand om het uit te voeren. 
  U hoeft de update 5-agent niet op de bron server te installeren als deze al is bijgewerkt naar Update 4 of als de bron agent is geïnstalleerd met de meest recente basis installatie **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe**.
  8. **Linux-bron server**: als u de Unified agent wilt bijwerken, kopieert u de bijbehorende versie van het Unified agent-bestand naar de Linux-server en pakt u het uit. Voer in de uitgepakte map **/install** uit.  Voor beeld: voor RHEL 6,7 64-bits server kopieert u **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16. tar. gz** naar de server en pakt u deze uit. Voer in de uitgepakte map **/install** uit.


> [!NOTE]
> * Het installatie programma voor de base Unified agent (UA) voor Windows is vernieuwd ter ondersteuning van Windows Server 2016. Het nieuwe installatie programma **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe** is verpakt met het base Scout ga pakket (**InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). Hetzelfde installatie programma wordt gebruikt voor alle ondersteunde Windows-versies. 
> * Het vContinuum voor het Master doel van Windows-& is vernieuwd voor de ondersteuning van Windows Server 2016. Het nieuwe installatie programma **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** is verpakt met het base Scout ga pakket (**InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). Hetzelfde installatie programma wordt gebruikt voor het implementeren van Windows 2016-Master doel en Windows 2012R2-Master doel.
> * Windows Server 2016 op de fysieke server wordt niet ondersteund door ASR Scout. Het ondersteunt alleen Windows Server 2016 VMware VM. 
>

#### <a name="bug-fixes-and-enhancements"></a>Oplossingen en verbeteringen voor oplossingen
- De failback-beveiliging voor de virtuele Linux-machine is mislukt en de lijst met schijven die moeten worden gerepliceerd, is leeg aan het einde van de configuratie.

### <a name="site-recovery-scout-801-update-5"></a>Site Recovery Scout 8.0.1 update 5
Scout Update 5 is een cumulatieve update. Het bevat alle oplossingen van update 1 tot update 4 en de nieuwe oplossingen die hieronder worden beschreven.
- Oplossingen van Site Recovery Scout update 4 naar Update 5 zijn specifiek voor de hoofd doel-en vContinuum-onderdelen.
- Als op de bron servers, het hoofd doel, de configuratie, het proces en de RX-servers update 4 al worden uitgevoerd, moet u deze alleen Toep assen op de hoofddoel server. 

#### <a name="new-platform-support"></a>Ondersteuning voor nieuwe platforms
* SUSE Linux Enterprise Server 11 Service Pack 4 (SP4)
* SLES 11 SP4 64 bits  **InMage_UA_8.0.1.0_SLES11-SP4-64_GA_13Apr2017_release. tar. gz** wordt geleverd met het base Scout ga-pakket (**InMage_Scout_Standard_8.0.1 GA.zip**). Down load het GA-pakket vanuit de portal, zoals beschreven in een kluis maken.


#### <a name="bug-fixes-and-enhancements"></a>Oplossingen en verbeteringen voor oplossingen

* Oplossingen voor verbeterde betrouw baarheid van Windows-cluster ondersteuning:
    * Opgelost: enkele van de P2V MSCS-cluster schijven worden onbewerkt na het herstel.
    * Vaste-P2V-cluster herstel mislukt als gevolg van niet-overeenkomende schijf volgordes.
    * Opgelost: de MSCS-cluster bewerking voor het toevoegen van schijven mislukt met een fout met een niet-overeenkomende schijf grootte.
    * Opgelost: de gereedheids controle voor het bron-MSCS-cluster met toewijzing van RDM Lun's mislukt in omvang verificatie.
    * Vast: cluster beveiliging op één knoop punt mislukt vanwege niet-overeenkomende SCSI-problemen. 
    * Vast-opnieuw beveiligen van de P2V Windows-cluster server mislukt als de doel cluster schijven aanwezig zijn. 
    
* Opgelost: tijdens failback-beveiliging, als de geselecteerde hoofddoel server zich niet op dezelfde ESXi-server bevindt als de beveiligde bron machine (tijdens de doorstuur beveiliging), haalt vContinuum de verkeerde hoofddoel server op tijdens het herstel van failback en mislukt de herstel bewerking.

> [!NOTE]
> * De P2V-cluster oplossingen zijn alleen van toepassing op fysieke MSCS-clusters die recent zijn beveiligd met Site Recovery Scout Update 5. Als u de cluster oplossingen op beveiligde P2V MSCS-clusters met oudere updates wilt installeren, volgt u de stappen in sectie 12 van de [release opmerkingen van site Recovery Scout](https://aka.ms/asr-scout-release-notes).
> * Als op het moment van de herbeveiliging dezelfde set schijven actief is op elk van de cluster knooppunten, zoals bij de eerste keer dat deze wordt beveiligd, kan de beveiliging van een fysieke MSCS-cluster alleen worden gebruikt om bestaande doel schijven opnieuw te gebruiken. Als dat niet het geval is, gebruikt u de hand matige stappen in sectie 12 van [site Recovery Scout-release opmerkingen](https://aka.ms/asr-scout-release-notes)om de doel schijven te verplaatsen naar het juiste gegevens archief, voor hergebruik tijdens het opnieuw beveiligen. Als u het MSCS-cluster opnieuw beveiligt in de P2V-modus zonder de upgrade stappen te volgen, wordt er een nieuwe schijf op de doel-ESXi-server gemaakt. U moet de oude schijven hand matig verwijderen uit de gegevens opslag.
> * Wanneer een bron-SLES11 of SLES11 (met een service pack) op de juiste manier opnieuw wordt opgestart, markeert u hand matig de replicatie paren van de **hoofd** schijf voor opnieuw synchroniseren. Er is geen melding in de CX-interface. Als u de hoofd schijf niet markeert voor hersynchronisatie, worden er mogelijk problemen met de gegevens integriteit opgemerkt.


### <a name="azure-site-recovery-scout-801-update-4"></a>Azure Site Recovery Scout 8.0.1 update 4
Scout update 4 is een cumulatieve update. Dit omvat alle oplossingen van update 1 tot update 3 en de nieuwe oplossingen die hieronder worden beschreven.

#### <a name="new-platform-support"></a>Ondersteuning voor nieuwe platforms

* Er is ondersteuning toegevoegd voor vCenter/vSphere 6,0, 6,1 en 6,2
* Er is ondersteuning toegevoegd voor deze Linux-besturings systemen:
  * Red Hat Enterprise Linux (RHEL) 7,0, 7,1 en 7,2
  * CentOS 7,0, 7,1 en 7,2
  * Red Hat Enterprise Linux (RHEL) 6,8
  * CentOS 6,8

> [!NOTE]
> RHEL/CentOS 7 64 bits  **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release. tar. gz** is verpakt met het base Scout ga-pakket **InMage_Scout_Standard_8.0.1 GA.zip**. Down load het Scout GA-pakket vanuit de portal, zoals beschreven in een kluis maken.

#### <a name="bug-fixes-and-enhancements"></a>Oplossingen en verbeteringen voor oplossingen

* Verbeterde verwerking van het afsluiten van de volgende Linux-besturings systemen en klonen, om ongewenste hersynchronisaties problemen te voor komen:
    * Red Hat Enterprise Linux (RHEL) 6. x
    * Oracle Linux (OL) 6. x
* Voor Linux zijn alle toegangs machtigingen voor mappen in de installatiemap voor Unified agent nu beperkt tot de lokale gebruiker.
* In Windows is een oplossing voor een probleem met een time-out opgetreden bij het uitgeven van veelvoorkomende gedistribueerde consistentie bladwijzers, op sterk geladen gedistribueerde toepassingen, zoals SQL Server en share point-clusters.
* Een aan het logboek gerelateerde oplossing in het basis installatie programma van de configuratie server.
* Er is een download koppeling naar VMware vCLI 6,0 toegevoegd aan het Windows Master target-basis installatie programma.
* Er zijn aanvullende controles en logboeken toegevoegd, voor wijzigingen in de netwerk configuratie tijdens het uitvoeren van een failover en nood herstel analyse.
* Een oplossing voor een probleem waardoor Bewaar informatie niet aan de configuratie server moet worden gemeld.  
* Voor fysieke clusters is een oplossing voor een probleem waardoor het formaat van het volume wordt gewijzigd in de vContinuum-wizard bij het verkleinen van het bron volume.
* Een oplossing voor een probleem met de cluster beveiliging die is mislukt met de fout: ' kan de schijf handtekening niet vinden ', als de cluster schijf een PRDM-schijf is.
* Een oplossing voor een crash van een cxps-Transport Server, veroorzaakt door een out-of-Range-uitzonde ring.
* De kolommen Server naam en IP-adres kunnen nu worden verkleind op de pagina **push installatie** van de wizard vContinuum.
* Uitbrei dingen voor RX API:
  * De vijf meest recente algemene beschik bare consistentie punten zijn nu beschikbaar (alleen gegarandeerde Tags).
  * Details over capaciteit en vrije ruimte worden weer gegeven voor alle beveiligde apparaten.
  * Status van Scout-stuur programma op de bron server is beschikbaar.

> [!NOTE]
> * **InMage_Scout_Standard_8.0.1_GA.zip** base-pakket bevat:
>     * Een bijgewerkt basis installatie programma voor de configuratie server (**InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe**)
>     * Een Windows Master target-basis installatie programma (**InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**).
>     * Voor alle nieuwe installaties gebruikt u de nieuwe configuratie server en Windows Master doel-GA bits.
> * Update 4 kan rechtstreeks worden toegepast op 8.0.1 GA.
> * De configuratie server en RX-updates kunnen niet worden teruggedraaid nadat ze zijn toegepast.


### <a name="azure-site-recovery-scout-801-update-3"></a>Azure Site Recovery Scout 8.0.1 update 3

Alle Site Recovery updates zijn cumulatief. Update 3 bevat alle correcties van update 1 en update 2. Update 3 kan rechtstreeks worden toegepast op 8.0.1 GA. De configuratie server en RX-updates kunnen niet worden teruggedraaid nadat ze zijn toegepast.

#### <a name="bug-fixes-and-enhancements"></a>Oplossingen en verbeteringen voor oplossingen
Met update 3 worden de volgende problemen opgelost:

* De configuratie server en RX worden niet geregistreerd in de kluis wanneer ze zich achter de proxy bevinden.
* Het aantal uren waarin de Recovery Point Objective (RPO) niet is bereikt, wordt niet bijgewerkt in het status rapport.
* De configuratie server wordt niet gesynchroniseerd met RX wanneer de details van de ESX-hardware, of netwerk gegevens, UTF-8-tekens bevatten.
* Windows Server 2008 R2-domein controllers worden niet gestart na het herstel.
* Offline synchronisatie werkt niet zoals verwacht.
* Na een VM-failover wordt het verwijderen van replicatie paren gedurende lange tijd niet uitgevoerd in de console van de configuratie server. Gebruikers kunnen de failback-of Resume-bewerkingen niet volt ooien.
* Algemene momentopname bewerkingen door de consistentie taak zijn geoptimaliseerd, om het verbreken van de verbinding van de toepassing, zoals SQL Server-clients, te verminderen.
* De prestaties van het consistentie programma (VACP.exe) zijn verbeterd. Het geheugen gebruik dat is vereist voor het maken van moment opnamen in Windows is gereduceerd.
* De push-installatie service loopt vast wanneer het wacht woord groter is dan 16 tekens.
* vContinuum controleert niet of er nieuwe vCenter-referenties worden gevraagd wanneer de referenties worden gewijzigd.
* In Linux kan de cachemgr (Master Target cache manager) geen bestanden downloaden van de proces server. Dit resulteert in een beperking van het replicatie paar.
* Wanneer de schijf volgorde van het fysieke failovercluster (MSCS) niet op alle knoop punten hetzelfde is, is replicatie niet ingesteld voor een aantal cluster volumes. Het cluster moet opnieuw worden beveiligd om gebruik te kunnen maken van deze oplossing.  
* De SMTP-functionaliteit werkt niet zoals verwacht, nadat RX is bijgewerkt van Scout 7,1 naar Scout 8.0.1.
* Er zijn meer statistieken toegevoegd in het logboek voor de terugdraai bewerking, om de benodigde tijd voor het volt ooien van het proces bij te houden.
* Er is ondersteuning toegevoegd voor Linux-besturings systemen op de bron server:
  * Red Hat Enterprise Linux (RHEL) 6 update 7
  * CentOS 6 update 7
* De configuratie server en RX-consoles tonen nu meldingen voor het paar dat in de bitmapmodus gaat.
* De volgende beveiligings oplossingen zijn toegevoegd in RX:
    * Autorisatie overs Laan via manipulatie van para meters: beperkte toegang tot niet-toepasselijke gebruikers.
    * Aanvraag vervalsing op meerdere sites: het concept van het pagina-token is geïmplementeerd en wordt wille keurig voor elke pagina gegenereerd. Dit betekent dat er slechts één aanmeldings exemplaar is voor dezelfde gebruiker en dat de pagina niet kan worden vernieuwd. In plaats daarvan wordt omgeleid naar het dash board.
    * Malicious file upload: bestanden zijn beperkt tot specifieke extensies: z, AIFF, ASF, AVI, BMP, CSV, doc, docx, FLA, FLV, GIF, gz, gzip, JPEG, JPG, log, Mid, MOV, MP3, MP4, mpc, MPEG, MPG, ODS, ODT, PDF, PNG, PPT, PPTX, pxd, Qt, RAM, RAR, RM, RMI, rmvb, RTF, dit SDC, SITD, SWF, sxc, sxw, tar, tgz, TIF, TIFF, txt, VSD, WAV, WMA, WMV, xls , xlsx, XML en zip.
    * Permanente cross-site scripting: er zijn invoer validaties toegevoegd.

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Azure Site Recovery Scout 8.0.1 update 2 (update 03Dec15)

Correcties in update 2 zijn onder andere:

* **Configuratie server**: problemen waardoor de gratis meter functie voor 31 dagen niet werkt zoals verwacht, wanneer de configuratie server is geregistreerd bij Azure site Recovery kluis.
* **Unified agent**: oplossing voor een probleem in update 1 waardoor de update niet wordt geïnstalleerd op de hoofddoel server tijdens de upgrade van versie 8,0 naar 8.0.1.

### <a name="azure-site-recovery-scout-801-update-1"></a>Azure Site Recovery Scout 8.0.1 update 1
Update 1 bevat de volgende fout oplossingen en nieuwe functies:

* 31 dagen gratis beveiliging per Server exemplaar. Zo kunt u de functionaliteit testen of een concept instellen.
* Alle bewerkingen op de server, inclusief failover en failback, zijn gedurende de eerste 31 dagen gratis. De tijd begint wanneer een server voor het eerst wordt beveiligd met Site Recovery Scout. Vanaf de dag van de 32nd wordt elke beveiligde server in rekening gebracht tegen de standaard instantie snelheid voor Site Recovery beveiliging van een site die eigendom is van een klant.
* Op elk gewenst moment is het aantal beveiligde servers dat momenteel in rekening wordt gebracht, beschikbaar op het **dash board** in de kluis.
* Er is ondersteuning toegevoegd voor vSphere Command-Line interface (vCLI) 5,5-update 2.
* Er is ondersteuning toegevoegd voor deze Linux-besturings systemen op de bron server:
    * RHEL 6 update 6
    * RHEL 5 update 11
    * CentOS 6 update 6
    * CentOS 5 update 11
* Fouten opgelost om de volgende problemen op te lossen:
  * De kluis registratie voor de configuratie server of RX-server mislukt.
  * Cluster volumes worden niet zoals verwacht weer gegeven wanneer geclusterde Vm's opnieuw worden beveiligd wanneer ze worden hervat.
  * Failback mislukt wanneer de hoofddoel server wordt gehost op een andere ESXi-server dan de on-premises productie-Vm's.
  * De machtigingen voor het configuratie bestand worden gewijzigd wanneer u een upgrade uitvoert naar 8.0.1. Deze wijziging is van invloed op beveiliging en bewerkingen.
  * De drempel waarde voor opnieuw synchroniseren wordt niet op de verwachte manier afgedwongen, waardoor het replicatie gedrag inconsistent is.
  * De RPO-instellingen worden niet correct weer gegeven in de console van de configuratie server. De niet-gecomprimeerde gegevens waarde toont de gecomprimeerde waarde onjuist.
  * De bewerking verwijderen wordt niet zoals verwacht verwijderd in de vContinuum-wizard en de replicatie wordt niet verwijderd van de console van de configuratie server.
  * In de wizard vContinuum wordt de schijf automatisch uitgeschakeld wanneer u in de schijf weergave op **Details** klikt tijdens de beveiliging van MSCS-vm's.
  * In het scenario fysiek naar virtueel (P2V) zijn vereiste HP-Services (zoals CIMnotify en CqMgHost) niet verplaatst naar hand matig in VM-herstel. Dit probleem leidt tot extra opstart tijd.
  * De beveiliging van de Linux-VM mislukt wanneer er meer dan 26 schijven op de hoofddoel server zijn.

