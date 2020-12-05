---
title: Upgrade van het besturings systeem voor de SAP HANA op Azure (grote exemplaren) | Microsoft Docs
description: Upgrade van het besturings systeem uitvoeren voor SAP HANA op Azure (grote exemplaren)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cdc6dd49fe98085edf3c6fb16606b9f540b5a3a0
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608687"
---
# <a name="operating-system-upgrade"></a>Upgrade van besturings systeem
In dit document worden de details van upgrades van besturings systemen op de HANA grote instanties beschreven.

>[!NOTE]
>De upgrade van het besturings systeem is de verantwoordelijkheid van de klant. micro soft Operations Support kan u begeleiden bij de belangrijkste gebieden die u tijdens de upgrade kunt bekijken. Neem ook contact op met de leverancier van uw besturings systeem voordat u een upgrade gaat plannen.

> [!NOTE]
> Dit artikel bevat verwijzingen naar de term *Black*, een term die micro soft niet meer gebruikt. Wanneer de periode van de software wordt verwijderd, worden deze uit dit artikel verwijderd.

Tijdens het inrichten van de HLI-eenheid installeert het micro soft Operations-team het besturings systeem.
U moet de installatie van het besturings systeem (voor beeld: patches, afstemming, upgrades enz.) op de////-eenheid hand matig uitvoeren.

Voordat u belang rijke wijzigingen doorvoert aan het besturings systeem (bijvoorbeeld een upgrade van SP1 naar SP2), neemt u contact op met het micro soft Operations-team door een ondersteunings ticket te openen om te raadplegen.

In uw ticket insluiten:

* De ID van uw HLI-abonnement.
* De naam van uw server.
* Het patch niveau dat u wilt Toep assen.
* De datum waarop u deze wijziging plant. 

U wordt aangeraden dit ticket ten minste één week te openen voor de gewenste upgrade, waardoor Opration team weet wat de firmware versie is.

Zie [SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581)voor de ondersteunings matrix van de verschillende SAP Hana versies met de verschillende Linux-versies.

## <a name="known-issues"></a>Bekende problemen

Hier volgen enkele veelvoorkomende bekende problemen tijdens de upgrade:
- De Software Foundation-software (SFS) wordt na de upgrade van het besturings systeem verwijderd uit de SKU-klasse. U moet de compatibele SFS na de upgrade van het besturings systeem opnieuw installeren.
- Ethernet-kaart Stuur programma's (ENIC en FNIC) worden teruggedraaid naar een oudere versie. Na de upgrade moet u de compatibele versie van de Stuur Programma's opnieuw installeren.

## <a name="sap-hana-large-instance-type-i-recommended-configuration"></a>Aanbevolen configuratie van SAP HANA grote instantie (type I)

De configuratie van het besturings systeem kan van de aanbevolen instellingen in de loop van de tijd worden overgenomen door patches, systeem upgrades en wijzigingen die door klanten zijn aangebracht. Daarnaast identificeert micro soft updates die nodig zijn voor bestaande systemen om ervoor te zorgen dat ze optimaal worden geconfigureerd voor de beste prestaties en tolerantie. De volgende instructies zijn een overzicht van aanbevelingen voor netwerk prestaties, stabiliteit van het systeem en optimale HANA-prestaties.

### <a name="compatible-enicfnic-driver-versions"></a>Compatibele versies van Stuur Programma's voor eNIC/fNIC
  Om de juiste netwerk prestaties en systeem stabiliteit te hebben, wordt aangeraden om ervoor te zorgen dat de systeemspecifieke, juiste versie van eNIC-en fNIC-Stuur Programma's wordt geïnstalleerd, zoals in de volgende compatibiliteits tabel wordt weer gegeven. Servers worden aan klanten geleverd met compatibele versies. In sommige gevallen kan de Stuur Programma's tijdens het bijwerken van het besturings systeem/de kernel worden teruggedraaid naar de standaard versies van het stuur programma. Zorg ervoor dat de juiste versie van het stuur programma post OS/kernel-patch bewerkingen uitvoert.
       
      
  |  BESTURINGSSYSTEEM leverancier    |  Versie van besturingssysteem pakket     |  Firmwareversie  |  eNIC-stuur programma |  fNIC-stuur programma | 
  |---------------|-------------------------|--------------------|--------------|--------------|
  |   SuSE        |  SLES 12 SP2            |   3.1.3 h           |  2.3.0.40    |   1.6.0.34   |
  |   SuSE        |  SLES 12 SP3            |   3.1.3 h           |  2.3.0.44    |   1.6.0.36   |
  |   SuSE        |  SLES 12 SP4            |   3.2.3 i           |  4.0.0.6     |   2.0.0.60   |
  |   SuSE        |  SLES 12 SP2            |   3.2.3 i           |  2.3.0.45    |   1.6.0.37   |
  |   SuSE        |  SLES 12 SP3            |   3.2.3 i           |  2.3.0.43    |   1.6.0.36   |
  |   SuSE        |  SLES 12 SP5            |   3.2.3 i           |  4.0.0.8     |   2.0.0.60   |
  |   Red Hat     |  RHEL 7,2               |   3.1.3 h           |  2.3.0.39    |   1.6.0.34   |
 

### <a name="commands-for-driver-upgrade-and-to-clean-old-rpm-packages"></a>Opdrachten voor het upgraden van Stuur Programma's en het opschonen van oude RPM-pakketten

#### <a name="command-to-check-existing-installed-drivers"></a>Opdracht voor het controleren van bestaande geïnstalleerde Stuur Programma's
```
rpm -qa | grep enic/fnic 
```
#### <a name="delete-existing-enicfnic-rpm"></a>Bestaande eNIC/fNIC-rpm verwijderen
```
rpm -e <old-rpm-package>
```
#### <a name="install-the-recommended-enicfnic-driver-packages"></a>De aanbevolen Stuur programmapakketten voor eNIC/fNIC installeren
```
rpm -ivh <enic/fnic.rpm> 
```

#### <a name="commands-to-confirm-the-installation"></a>Opdrachten om de installatie te bevestigen
```
modinfo enic
modinfo fnic
```

#### <a name="steps-for-enicfnic-drivers-installation-during-os-upgrade"></a>Stappen voor de installatie van eNIC/fNIC-Stuur Programma's tijdens de upgrade van het besturings systeem

* Versie van besturings systeem bijwerken
* Oude RPM-pakketten verwijderen
* Compatibele eNIC/fNIC-Stuur Programma's installeren als per geïnstalleerde versie van het besturings systeem
* Systeem opnieuw opstarten
* Nadat het systeem opnieuw is opgestart, controleert u de eNIC/fNIC-versie


### <a name="suse-hlis-grub-update-failure"></a>SuSE HLIs GRUB-Update fout
SAP on Azure HANA grote instanties (type I) kunnen na de upgrade een niet-opstart bare status hebben. Met de onderstaande procedure wordt dit probleem opgelost.
#### <a name="execution-steps"></a>Uitvoerings stappen


*   `multipath -ll`Opdracht uitvoeren.
*   Haal de LUN-ID op waarvan de grootte ongeveer 50G is of gebruik de opdracht: `fdisk -l | grep mapper`
*   `/etc/default/grub_installdevice`Bestand bijwerken met regel `/dev/mapper/<LUN ID>` . Voor beeld:/dev/mapper/3600a09803830372f483f495242534a56
>[!NOTE]
>LUN-ID varieert van server naar server.


### <a name="disable-edac"></a>EDAC uitschakelen 
   De module fout detectie en correctie (EDAC) helpt bij het detecteren en corrigeren van geheugen fouten. De onderliggende hardware voor SAP HANA on Azure Large Instances (type I) voert echter al dezelfde functie uit. Als dezelfde functie is ingeschakeld op het niveau van de hardware en het besturings systeem (OS), kunnen er conflicten ontstaan en kan dit ertoe leiden dat de server af en toe niet kan worden gepland. Daarom is het raadzaam om de module uit te scha kelen vanuit het besturings systeem.

#### <a name="execution-steps"></a>Uitvoerings stappen

* Controleer of de EDAC-module is ingeschakeld. Als een uitvoer in de onderstaande opdracht wordt geretourneerd, betekent dit dat de module is ingeschakeld. 
```
lsmod | grep -i edac 
```
* De modules uitschakelen door de volgende regels toe te voegen aan het bestand `/etc/modprobe.d/blacklist.conf`
```
blacklist sb_edac
blacklist edac_core
```
De computer moet opnieuw worden opgestart om wijzigingen te kunnen aanbrengen. `lsmod`Opdracht uitvoeren en controleren of de module niet aanwezig is in de uitvoer.

### <a name="kernel-parameters"></a>Kernel-para meters
   Zorg ervoor dat de juiste instelling `transparent_hugepage` voor `numa_balancing` , `processor.max_cstate` , `ignore_ce` en `intel_idle.max_cstate` wordt toegepast.

* intel_idle intel_idle.max_cstate = 1
* processor.max_cstate = 1
* transparent_hugepage = nooit
* numa_balancing = uitschakelen
* MCE = ignore_ce

#### <a name="execution-steps"></a>Uitvoerings stappen

* Deze para meters toevoegen aan de `GRB_CMDLINE_LINUX` regel in het bestand `/etc/default/grub`
```
intel_idle.max_cstate=1 processor.max_cstate=1 transparent_hugepage=never numa_balancing=disable mce=ignore_ce
```
* Maak een nieuw grub-bestand.
```
grub2-mkconfig -o /boot/grub2/grub.cfg
```
* Systeem opnieuw opstarten.


## <a name="next-steps"></a>Volgende stappen
- Zie [back-up en herstellen](hana-overview-high-availability-disaster-recovery.md) voor het back-uptype van het besturings systeem I SKU-klasse.
- Raadpleeg de [back-up van het besturings systeem voor de type II sku's van Revision 3-stem pels](os-backup-type-ii-skus.md) voor de klasse type II SKU.
