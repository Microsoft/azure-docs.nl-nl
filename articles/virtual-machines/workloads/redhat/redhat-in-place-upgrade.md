---
title: In-place upgrade van Red Hat Enterprise Linux-installatie kopieën op Azure
description: Meer informatie over het in-place upgrade van Red Hat Enter prise 7. x-installatie kopieën naar de meest recente versie van 8. x.
author: mathapli
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 04/16/2020
ms.author: alsin
ms.openlocfilehash: 1be0904cc640eff5af7a77bba3abd6aa062991a8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101676076"
---
# <a name="red-hat-enterprise-linux-in-place-upgrades"></a>Red Hat Enterprise Linux in-place Upgrades

In dit artikel vindt u instructies voor het uitvoeren van een in-place upgrade van Red Hat Enterprise Linux (RHEL) 7 tot Red Hat Enterprise Linux 8. De instructies gebruiken het `leapp` hulp programma in Azure. Tijdens de in-place upgrade wordt het bestaande RHEL 7-besturings systeem vervangen door de RHEL 8-versie.

>[!Note] 
> Aanbiedingen van SQL Server op Red Hat Enterprise Linux bieden geen ondersteuning voor in-place Upgrades in Azure.

## <a name="what-to-expect-during-the-upgrade"></a>Wat u tijdens de upgrade kunt verwachten
Tijdens de upgrade wordt het systeem enkele keren opnieuw opgestart. De laatste keer dat de computer opnieuw wordt opgestart, wordt de VM bijgewerkt naar de laatste secundaire release van RHEL 8. 

Het upgrade proces kan 20 minuten tot 2 uur duren. De totale tijd is afhankelijk van verschillende factoren, zoals de grootte van de virtuele machine en het aantal pakketten dat op het systeem is geïnstalleerd.

## <a name="preparations"></a>Granulaten
Red Hat en Azure adviseren u een in-place upgrade te gebruiken om een systeem over te zetten naar de volgende primaire versie. 

Voordat u met de upgrade begint, moet u rekening houden met de volgende overwegingen. 

>[!Important] 
> Maak een moment opname van de installatie kopie voordat u de upgrade start.

* Zorg ervoor dat u de nieuwste versie van RHEL 7 gebruikt. Momenteel is de nieuwste versie RHEL 7,9. Als u een vergrendelde versie gebruikt die niet kan worden bijgewerkt naar RHEL 7,9, voert u [de volgende stappen uit om over te scha kelen naar een niet-Eus (Extended update support)-opslag plaats](./redhat-rhui.md#switch-a-rhel-7x-vm-back-to-non-eus-remove-a-version-lock).

* Voer de volgende opdracht uit om de upgrade te controleren en te zien of deze wordt voltooid. De opdracht moet */var/log/leapp/leapp-report.txt* -bestand genereren. In dit bestand wordt het proces uitgelegd, wat er gebeurt en of de upgrade mogelijk is.

    >[!NOTE]
    > Gebruik het hoofd account om de opdrachten in dit artikel uit te voeren. 

    ```bash
    leapp preupgrade --no-rhsm
    ```
* Zorg ervoor dat de seriële console functioneel is. U gebruikt deze console voor bewaking tijdens het upgrade proces.

* Schakel SSH-basis toegang in */etc/ssh/sshd_config* in:
    1. Open het bestand */etc/ssh/sshd_config*.
    1. Zoek naar `#PermitRootLogin yes`.
    1. Verwijder het hekje ( `#` ) om de opmerking over de teken reeks op te heffen.

## <a name="upgrade-steps"></a>Upgrade stappen

Volg deze stappen zorgvuldig. Het is raadzaam om de upgrade op een test machine uit te voeren voordat u deze op productie-exemplaren probeert.

1. Voer een `yum` update uit om de meest recente client pakketten op te halen.
    ```bash
    yum update -y
    ```

1. Installeer de `leapp-client-package` .
    ```bash
    yum install leapp-rhui-azure
    ```
    
1. In de [Red Hat-Portal](https://access.redhat.com/articles/3664871)haalt u het bestand *leapp-data. tar. gz* dat *repomap.csv* en *pes-events.jsbevat op*. Pak het *leapp-data. tar. gz* -bestand uit.
    1. Down load het bestand *leapp-data. tar. gz* .
    1. Pak de inhoud uit en verwijder het bestand. Gebruik de volgende opdracht:
    ```bash
    tar -xzf leapp-data12.tar.gz -C /etc/leapp/files && rm leapp-data12.tar.gz
    ```

1. Voeg een `answers` bestand toe voor `leapp` .
    ```bash
    leapp answer --section remove_pam_pkcs11_module_check.confirm=True --add
    ``` 

1. Start de upgrade.
    ```bash
    leapp upgrade --no-rhsm
    ```
1.  Nadat de `leapp upgrade` opdracht is voltooid, start u het systeem hand matig opnieuw op om het proces te volt ooien. Het systeem is niet beschikbaar omdat het een paar keer opnieuw wordt opgestart. Controleer het proces met behulp van de seriële console.

1.  Controleer of de upgrade is voltooid.
    ```bash
    uname -a && cat /etc/redhat-release
    ```

1. Wanneer de upgrade is voltooid, verwijdert u de hoofd-SSH-toegang:
    1. Open het bestand */etc/ssh/sshd_config*.
    1. Zoek naar `#PermitRootLogin yes`.
    1. Voeg een hekje ( `#` ) toe als opmerking voor de teken reeks.

1. Start de SSHD-service opnieuw om de wijzigingen toe te passen.
    ```bash
    systemctl restart sshd
    ```
## <a name="common-problems"></a>Algemene problemen

De volgende fouten treden meestal op wanneer het `leapp preupgrade` proces mislukt of als het `leapp upgrade` proces mislukt:

* **Fout**: er zijn geen overeenkomsten gevonden voor de volgende uitgeschakelde invoeg toepassingen patronen.

    ```plaintext
    STDERR:
    No matches found for the following disabled plugin patterns: subscription-manager
    Warning: Packages marked by Leapp for upgrade not found in repositories metadata: gpg-pubkey
    ```

    **Oplossing**: Schakel de invoeg toepassing abonnement-manager uit. Schakel de bewerking uit door het bestand */etc/yum/pluginconf.d/Subscription-Manager.conf* te bewerken en `enabled` te wijzigen in `enabled=0` .

    Deze fout treedt op wanneer de invoeg toepassing Subscription-Manager `yum` die is ingeschakeld, niet wordt gebruikt voor `PAYG` vm's.

* **Fout**: mogelijk problemen met externe aanmelding via de hoofdmap.

    Mogelijk ziet u deze fout wanneer het `leapp preupgrade` mislukt:

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
    **Oplossing**: toegang tot de hoofdmap inschakelen in */etc/sshd_config*.

    Deze fout treedt op wanneer SSH-hoofd toegang niet is ingeschakeld in */etc/sshd_config*. Zie de sectie voor [bereidingen](#preparations) in dit artikel voor meer informatie. 


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [Red Hat-afbeeldingen in azure](./redhat-images.md).
* Meer informatie over de [infra structuur voor Red Hat-updates](./redhat-rhui.md).
* Meer informatie over de [RHEL BYOS-aanbieding](./byos.md).
* Zie [upgraden van RHEL 7 naar RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html-single/upgrading_from_rhel_7_to_rhel_8/index) in de Red Hat-documentatie voor meer informatie over de red hat in-place upgrade-processen.
* Zie [Red Hat Enterprise Linux levens cyclus](https://access.redhat.com/support/policy/updates/errata) in de Red Hat-documentatie voor meer informatie over Red Hat-ondersteunings beleid voor alle versies van RHEL.