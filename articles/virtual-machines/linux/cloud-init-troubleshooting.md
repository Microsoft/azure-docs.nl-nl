---
title: Problemen oplossen met Cloud-init
description: Problemen met het inrichten van een Azure VM oplossen met Cloud-init.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: 842107245fe26155d53866bf95e11b08d7593ad1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582150"
---
# <a name="troubleshooting-vm-provisioning-with-cloud-init"></a>Problemen met VM-inrichting oplossen met Cloud-init

Als u gegeneraliseerde aangepaste installatie kopieën hebt gemaakt met behulp van Cloud-init voor het inrichten, maar u hebt gevonden dat de virtuele machine niet correct is gemaakt, moet u de aangepaste installatie kopieën oplossen.

Enkele voor beelden van problemen met het inrichten:
- De VM wordt gedurende 40 minuten vastgemaakt aan het maken en het maken van de VM is gemarkeerd als mislukt
- `CustomData` wordt niet verwerkt
- De tijdelijke schijf kan niet worden gekoppeld
- Gebruikers worden niet gemaakt of er zijn problemen met de gebruikers toegang
- Het netwerk is niet juist ingesteld
- Bestands-of partitie fouten wisselen

In dit artikel wordt stapsgewijs beschreven hoe u problemen met Cloud-init kunt oplossen. Zie [Cloud-init dieper](./cloud-init-deep-dive.md)voor meer gedetailleerde informatie.

## <a name="step-1-test-the-deployment-without-customdata"></a>Stap 1: de implementatie testen zonder `customData`

Cloud-init kan worden geaccepteerd `customData` , die wordt door gegeven wanneer de virtuele machine wordt gemaakt. U moet er eerst voor zorgen dat dit geen problemen met implementaties veroorzaakt. Probeer de virtuele machine in te richten zonder een configuratie door te geven. Als u vindt dat de virtuele machine niet kan worden ingericht, gaat u door met de onderstaande stappen, als u de configuratie die u doorgeeft vindt, gaat u verder met [stap 4](). 

## <a name="step-2-review-image-requirements"></a>Stap 2: de vereisten voor de installatie kopie controleren
De belangrijkste oorzaak van het inrichtings probleem van de VM is dat de installatie kopie van het besturings systeem niet voldoet aan de vereisten voor het uitvoeren op Azure. Zorg ervoor dat uw installatie kopieën correct zijn voor bereid voordat u ze in azure inricht. 


De volgende artikelen illustreren de stappen voor het voorbereiden van verschillende Linux-distributies die worden ondersteund in Azure:

- [CentOS-distributies](create-upload-centos.md)
- [Debian Linux](debian-create-upload-vhd.md)
- [Flatcar Container Linux](flatcar-create-upload-vhd.md)
- [Oracle Linux](oracle-create-upload-vhd.md)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
- [SLES en OpenSUSE](suse-create-upload-vhd.md)
- [Ubuntu](create-upload-ubuntu.md)
- [Overige: niet-goedgekeurde distributies](create-upload-generic.md)

Voor de [ondersteunde Azure-Cloud-init-installatie kopieën](./using-cloud-init.md)beschikken de Linux-distributies al over de vereiste pakketten en configuraties om de installatie kopie op de juiste wijze in Azure in te richten. Als u vindt dat uw virtuele machine niet kan worden gemaakt op basis van uw eigen gecuratore installatie kopie, probeert u een ondersteunde Azure Marketplace-installatie kopie die al is geconfigureerd voor Cloud-init, met uw optionele `customData` . Als het `customData` correct werkt met een installatie kopie van Azure Marketplace, is er waarschijnlijk een probleem met uw gecuratore installatie kopie.

## <a name="step-3-collect--review-vm-logs"></a>Stap 3: VM-logboeken verzamelen & controleren

Wanneer de virtuele machine niet kan worden ingericht, wordt in azure de status ' maken ' weer gegeven, gedurende 20 minuten, waarna de VM opnieuw wordt opgestart en een andere 20 minuten wordt gewacht voordat de VM-implementatie ten slotte als mislukt wordt gemarkeerd met een `OSProvisioningTimedOut` fout.

Terwijl de virtuele machine actief is, hebt u de logboeken van de virtuele machine nodig om te begrijpen waarom het inrichten is mislukt.  Als u wilt weten waarom het inrichten van de VM is mislukt, stopt u de virtuele machine niet. Zorg ervoor dat de VM wordt uitgevoerd. Als u logboeken wilt verzamelen, moet u de mislukte VM in een actieve status laten staan. Gebruik een van de volgende methoden om de logboeken te verzamelen:

- [Seriële console](/troubleshoot/azure/virtual-machines/serial-console-grub-single-user-mode)

- [Schakel diagnostische gegevens over opstarten in](/previous-versions/azure/virtual-machines/linux/tutorial-monitor#enable-boot-diagnostics) voordat u de virtuele machine maakt en [weer geven](/previous-versions/azure/virtual-machines/linux/tutorial-monitor#view-boot-diagnostics) tijdens het opstarten.

- [Voer AZ VM Repair uit](/troubleshoot/azure/virtual-machines/repair-linux-vm-using-azure-virtual-machine-repair-commands) om de besturingssysteem schijf te koppelen en koppelen, zodat u deze logboeken kunt verzamelen:
```bash
/var/log/cloud-init*
/var/log/waagent*
/var/log/syslog*
/var/log/rsyslog*
/var/log/messages*
/var/log/kern*
/var/log/dmesg*
/var/log/boot*
```
Begin met de Cloud-init-Logboeken om te beginnen met de eerste probleemoplossings procedure, en ontdek waar de fout is opgetreden, en gebruik vervolgens de andere logboeken om meer inzicht te krijgen. 
* /var/log/cloud-init.log
* /var/log/cloud-init-output.log
* Seriële/opstart logboeken

Begin in alle logboeken te zoeken naar ' failed ', ' WARNing ', ' WARNing ', ' Err ', ' error ', ' ERROR '. Het is raadzaam om configuratie in te stellen op het negeren van hoofdletter gevoelige Zoek opdrachten. 

> [!TIP]
> Als u problemen met een aangepaste installatie kopie wilt oplossen, kunt u overwegen om een gebruiker toe te voegen tijdens de installatie kopie. Als het inrichten van de gebruiker met beheerders rechten niet kan worden ingesteld, kunt u zich nog steeds aanmelden bij het besturings systeem.

## <a name="analyzing-the-logs"></a>De logboeken analyseren

Hier vindt u meer informatie over wat u moet zoeken in elk Cloud-init-logboek.

### <a name="varlogcloud-initlog"></a>/var/log/cloud-init.log

Standaard worden alle Cloud-init-gebeurtenissen met de prioriteit debug of hoger geschreven naar `/var/log/cloud-init.log` . Dit biedt uitgebreide logboeken van elke gebeurtenis die is opgetreden tijdens het initialiseren van Cloud-init. 

Bijvoorbeeld:

```console
2019-10-10 04:51:25,321 - util.py[DEBUG]: Failed mount of '/dev/sr0' as 'auto': Unexpected error while running command.
Command: ['mount', '-o', 'ro,sync', '-t', 'auto', u'/dev/sr0', '/run/cloud-init/tmp/tmpLIrklc']
Exit code: 32
Reason: -
Stdout:
Stderr: mount: unknown filesystem type 'udf'
2020-01-31 00:21:53,352 - DataSourceAzure.py[WARNING]: /dev/sr0 was not mountable
```


Als u een fout of waarschuwing hebt gevonden, leest u achterwaarts in het Cloud-init-logboek om te zien wat er in de Cloud werd geprobeerd voordat de fout of waarschuwing wordt weer gegeven. In veel gevallen worden in de Cloud-init opdrachten uitgevoerd voor het besturings systeem of worden er inrichtings bewerkingen uitgevoerd vóór de fout. Dit kan inzicht geven in de oorzaak van fouten in de logboeken. In het volgende voor beeld ziet u dat Cloud-init een apparaat heeft gekoppeld voordat het een fout heeft voorgedaan.

```output
2019-10-10 04:51:24,010 - util.py[DEBUG]: Running command ['mount', '-o', 'ro,sync', '-t', 'auto', u'/dev/sr0', '/run/cloud-init/tmp/tmpXXXXX'] with allowed return codes [0] (shell=False, capture=True)
```

Als u toegang hebt tot de [seriële console](/troubleshoot/azure/virtual-machines/serial-console-grub-single-user-mode), kunt u proberen om de opdracht uit te voeren die door Cloud-init werd uitgevoerd.

De logboek registratie voor `/var/log/cloud-init.log` kan ook opnieuw worden geconfigureerd binnen/etc/cloud/cloud.cfg.d/05_logging. cfg. Raadpleeg de [Cloud-init-documentatie](https://cloudinit.readthedocs.io/en/latest/topics/logging.html)voor meer informatie over de Cloud-init-logboek registratie. 

### <a name="varlogcloud-init-outputlog"></a>/var/log/cloud-init-output.log

U kunt informatie ophalen van de `stdout` en `stderr` tijdens de [fasen van Cloud-init](cloud-init-deep-dive.md). Dit omvat normaal gesp roken routerings tabel gegevens, netwerk gegevens, verificatie gegevens van SSH-hostgegevens `stdout` en `stderr` voor elke fase van Cloud-init, samen met de tijds tempel voor elke fase. Indien gewenst, `stderr` en `stdout` logboek registratie kan opnieuw worden geconfigureerd van `/etc/cloud/cloud.cfg.d/05_logging.cfg` .

### <a name="serialboot-logs"></a>Seriële/opstart logboeken 

Cloud-init heeft meerdere afhankelijkheden. deze worden beschreven in de vereiste onderdelen voor installatie kopieën op Azure, zoals netwerken, opslag, de mogelijkheid om een ISO te koppelen en het koppelen en Format teren van de tijdelijke schijf. Dit kan fouten veroorzaken en ertoe leiden dat Cloud-init mislukt. Als de virtuele machine bijvoorbeeld geen DHCP-lease kan krijgen, mislukt de Cloud-init.

Als u nog steeds niet kunt isoleren waarom Cloud-init niet kan inrichten, moet u weten welke Cloud-init-fasen en wanneer modules worden uitgevoerd. Zie meer informatie over [het dieper in Cloud-init](cloud-init-deep-dive.md) .


## <a name="step-4-investigate-why-the-configuration-isnt-being-applied"></a>Stap 4: onderzoeken waarom de configuratie niet wordt toegepast
Niet elke fout in de Cloud-init resulteert in een fatale inrichtings fout. Als u bijvoorbeeld de `runcmd` module in een Cloud-init-configuratie gebruikt, kan het inrichten van de VM mislukken vanwege een afsluit code die niet gelijk is aan nul van de opdracht die wordt uitgevoerd. Dit is omdat deze wordt uitgevoerd na de kern functionaliteit van de inrichting die in de eerste 3 fasen van Cloud-init optreedt. Als u problemen wilt oplossen waarom de configuratie niet is toegepast, raadpleegt u de logboeken in stap 3 en Cloud-init-modules hand matig. Bijvoorbeeld:

- `runcmd` -de scripts worden uitgevoerd zonder fouten? Voer de configuratie hand matig uit vanaf de terminal om er zeker van te zijn dat ze worden uitgevoerd zoals verwacht.
- Pakketten installeren: heeft de virtuele machine toegang tot pakket opslagplaatsen?
- U moet ook de `customData` gegevens configuratie controleren die aan de VM is door gegeven. Dit is te vinden in `/var/lib/cloud/instances/<unique-instance-identifier>/user-data.txt` .


## <a name="next-steps"></a>Volgende stappen

Als u nog steeds niet kunt isoleren waarom de configuratie van de Cloud-init niet is uitgevoerd, moet u beter kijken wat er gebeurt in elke Cloud-init-fase en wanneer de modules worden uitgevoerd. Zie meer informatie over de [configuratie van Cloud-init](./cloud-init-deep-dive.md) .