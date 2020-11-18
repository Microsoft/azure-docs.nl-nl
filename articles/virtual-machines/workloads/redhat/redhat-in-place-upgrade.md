---
title: In-place upgrade van Red Hat Enterprise Linux-installatie kopieën op Azure
description: Zoek stappen voor het uitvoeren van in-place upgrade van Red Hat Enter prise 7. x-installatie kopieën naar de meest recente versie van 8. x
author: mathapli
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.reviewer: cynthn
ms.openlocfilehash: 1160bc43db0dc9ec1714b1766c8cadf09660e291
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844558"
---
# <a name="red-hat-enterprise-linux-in-place-upgrades"></a>Red Hat Enterprise Linux in-place Upgrades

In dit artikel vindt u stapsgewijze instructies voor het uitvoeren van een in-place upgrade van Red Hat Enterprise Linux 7 tot Red Hat Enterprise Linux 8 met het hulp programma Leapp in Azure. Tijdens de in-place upgrade wordt het bestaande RHEL 7-besturings systeem vervangen door RHEL 8-versie.

>[!Note] 
> SQL Server op Red Hat Enterprise Linux aanbiedingen bieden geen ondersteuning voor in-place upgrade op Azure.

## <a name="what-to-expect-during-the-upgrade"></a>Wat u tijdens de upgrade kunt verwachten
Het systeem wordt een paar keer opnieuw opgestart tijdens de upgrade en dat is normaal. Bij de laatste keer opnieuw opstarten wordt de VM bijgewerkt naar RHEL 8 laatste secundaire release. 

Het upgrade proces kan 20 minuten tot enkele uren duren. Dit is afhankelijk van verschillende factoren, zoals de VM-grootte en het aantal pakketten dat op het systeem is geïnstalleerd.

## <a name="preparations-for-the-upgrade"></a>Voor bereidingen voor de upgrade
Een in-place upgrade is de officieel aanbevolen manier van Red Hat en Azure, zodat klanten het systeem kunnen upgraden naar de volgende primaire versie. Voordat u de upgrade uitvoert, moet u zich bewust zijn van de volgende zaken en rekening houden. 

>[!Important] 
> Maak een moment opname van de installatie kopie voordat u de upgrade uitvoert.

>[!NOTE]
> De opdrachten in dit artikel moeten worden uitgevoerd met behulp van het hoofd account

* Zorg ervoor dat u de nieuwste versie van RHEL 7 gebruikt, die momenteel RHEL 7,9 is. Als u een vergrendelde versie gebruikt en niet kunt upgraden naar RHEL 7,9, kunt u de [stappen hier gebruiken om over te scha kelen naar een niet-Eus-opslag plaats](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/redhat-rhui#switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock).

* Voer de onderstaande opdracht uit om te weten hoe uw upgrade wordt uitgevoerd en als deze wordt voltooid. De opdracht moet een bestand genereren onder '/var/log/leapp/leapp-report.txt ' waarin het proces wordt uitgelegd en wat er gebeurt en als de upgrade mogelijk is of niet
    ```bash
    leapp preupgrade --no-rhsm
    ```
* Zorg ervoor dat de seriële console functioneel is, omdat hiermee bewaking kan worden uitgevoerd tijdens het upgrade proces.

* SSH-basis toegang inschakelen in `/etc/ssh/sshd_config`
    1. Open het bestand `/etc/ssh/sshd_config`
    1. Zoeken naar ' #PermitRootLogin ja '
    1. ' # ' Verwijderen uit Opmerking

## <a name="steps-for-performing-the-upgrade"></a>Stappen voor het uitvoeren van de upgrade

Voer deze stappen zorgvuldig uit. U wordt geadviseerd om de upgrade op een test machine uit te proberen vóór productie-exemplaren.

1. Voer een yum-update uit om de meest recente client pakketten op te halen.
    ```bash
    yum update -y
    ```

1. Installeer het leapp-client-package.
    ```bash
    yum install leapp-rhui-azure
    ```
    
1. Gebruik het bestand leapp-data. tar. gz met repomap.csv en pes-events.jsop, aanwezig in de [RedHat-Portal](https://access.redhat.com/articles/3664871)en pak deze uit. 
    1. Down load het bestand.
    1. Pak de inhoud uit en verwijder het bestand met de volgende opdracht:
    ```bash
    tar -xzf leapp-data12.tar.gz -C /etc/leapp/files && rm leapp-data12.tar.gz
    ```

1. Voeg het bestand ' Answers ' voor ' Leapp ' toe.
    ```bash
    leapp answer --section remove_pam_pkcs11_module_check.confirm=True --add
    ``` 

1. Voer de upgrade ' Leapp ' uit.
    ```bash
    leapp upgrade --no-rhsm
    ```
1.  Nadat de `leapp upgrade` opdracht is voltooid, start u het systeem hand matig opnieuw op om het proces te volt ooien. Het systeem wordt opnieuw opgestart, zodat het niet meer beschikbaar is. Bewaak het proces met behulp van de seriële console.

1.  Controleer of de upgrade is voltooid.
    ```bash
    uname -a && cat /etc/redhat-release
    ```

1. Het verwijderen van de hoofd-SSH-toegang nadat de upgrade is voltooid.
    1. Open het bestand `/etc/ssh/sshd_config`
    1. Zoeken naar ' #PermitRootLogin ja '
    1. ' # ' Aan opmerking toevoegen

1. De sshd-service opnieuw starten om de wijzigingen van kracht te laten worden
    ```bash
    systemctl restart sshd
    ```

## <a name="common-issues"></a>Veelvoorkomende problemen
Dit zijn enkele algemene instanties die het `leapp preupgrade` of `leapp upgrade` proces kunnen mislukken.

**Fout: er zijn geen overeenkomsten gevonden voor de volgende uitgeschakelde invoeg toepassingen patronen**
```plaintext
STDERR:
No matches found for the following disabled plugin patterns: subscription-manager
Warning: Packages marked by Leapp for upgrade not found in repositories metadata: gpg-pubkey
```
**Oplossing**\
Schakel de invoeg toepassing voor abonnements beheer uit door het bestand `/etc/yum/pluginconf.d/subscription-manager.conf` te bewerken en ingeschakeld in te scha kelen `enabled=0` .

Dit wordt veroorzaakt door de yum-invoeg toepassing van Subscription-Manager, die niet wordt gebruikt voor PAYG-Vm's.

**Fout: mogelijk problemen met externe aanmelding via de hoofdmap** Het kan zijn dat de `leapp preupgrade` volgende fout is opgetreden:
```structured-text
============================================================
                     UPGRADE INHIBITED
============================================================

Upgrade has been inhibited due to the following problems:
    1. Inhibitor: Possible problems with remote login using root account
Consult the pre-upgrade report for details and possible remediation.

============================================================
                     UPGRADE INHIBITED
============================================================
```
**Oplossing**\
Toegang tot hoofdmap inschakelen in `/etc/sshd_config` .
Dit wordt veroorzaakt doordat geen basis-SSH-toegang is ingeschakeld in `/etc/sshd_config` de sectie "[voor bereidingen voor de upgrade](#preparations-for-the-upgrade)". 

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over de [Red Hat-afbeeldingen in azure](./redhat-images.md).
* Meer informatie over de [infra structuur voor Red Hat-updates](./redhat-rhui.md).
* Meer informatie over de [RHEL BYOS-aanbieding](./byos.md).
* Informatie over de Red Hat in-place upgrade-processen vindt u in de documentatie van Red Hat en [voert u een upgrade uit van RHEL 7 naar RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index)
* Informatie over Red Hat-ondersteunings beleid voor alle versies van RHEL vindt u op de pagina [levens cyclus van Red Hat Enterprise Linux](https://access.redhat.com/support/policy/updates/errata) .
