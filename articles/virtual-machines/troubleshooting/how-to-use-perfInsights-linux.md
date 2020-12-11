---
title: PerfInsights Linux gebruiken in Microsoft Azure | Microsoft Docs
description: Meer informatie over hoe u PerfInsights kunt gebruiken om problemen met de prestaties van Linux-VM'S op te lossen.
services: virtual-machines-linux'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 7/10/2019
ms.author: genli
ms.openlocfilehash: 340164ef86d34f273b3d5a98b62300bc6cc50df7
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109416"
---
# <a name="how-to-use-perfinsights"></a>PerfInsights gebruiken

[PerfInsights Linux](https://aka.ms/perfinsightslinuxdownload) is een hulp programma voor zelf ondersteuning waarmee de diagnostische gegevens worden verzameld en geanalyseerd, en biedt een rapport voor het oplossen van problemen met de prestaties van virtuele Linux-machines in Azure. PerfInsights kan worden uitgevoerd op ondersteunde virtuele machines als een zelfstandig hulp programma of rechtstreeks vanuit de portal door gebruik te maken van [prestatie diagnostiek voor virtuele Azure-machines](performance-diagnostics.md).

Als u prestatie problemen ondervindt met virtuele machines, moet u dit hulp programma uitvoeren voordat u contact opneemt met de ondersteuning.

## <a name="supported-troubleshooting-scenarios"></a>Ondersteunde scenario's voor probleem oplossing

PerfInsights kunnen diverse soorten informatie verzamelen en analyseren. In de volgende secties worden algemene scenario's besproken.

### <a name="quick-performance-analysis"></a>Analyse van snelle prestaties

In dit scenario worden basis gegevens verzameld, zoals opslag en hardwareconfiguratie van uw virtuele machine, verschillende logboeken, waaronder:

- Informatie over het besturings systeem

- Informatie over PCI-apparaat

- Algemene gast besturingssysteem logboeken

- Configuratie bestanden

- Opslag gegevens

- Configuratie van virtuele Azure-machine (verzameld met [azure instance metadata service](../windows/instance-metadata-service.md))

- Lijst met actieve processen, schijf, geheugen en CPU-gebruik

- Netwerk gegevens

Dit is een passieve verzameling informatie die niet van invloed is op het systeem.

>[!Note]
>Het analyse scenario voor snelle prestaties wordt automatisch opgenomen in elk van de volgende scenario's:

### <a name="performance-analysis"></a>Analyse van prestaties

Dit scenario is vergelijkbaar met analyse van snelle prestaties, maar maakt het vastleggen van diagnostische gegevens voor langere duur mogelijk.

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Wat voor soort informatie wordt verzameld door PerfInsights

Informatie over de virtuele Linux-machine, het besturings systeem, het blok keren van apparaten, gebruikers van hoge bron, configuratie en diverse logboeken wordt verzameld. Hier vindt u meer informatie:

- Besturingssysteem
  - Linux-distributie en-versie
  - Informatie over kernel
  - Stuur programmagegevens

- Hardware
  - PCI-apparaten [ `*` ]

- Processen en geheugen
  - Lijst met processen (taak naam, gebruikt geheugen, bestanden geopend)
  - Totaal, beschikbaar en vrij fysiek geheugen
  - Totaal, beschikbaar en vrij wissel geheugen
  - Profileren van het vastleggen van CPU en het CPU-gebruik wordt met 5 seconden per interval verwerkt
  - Profileren van het vastleggen van geheugen gebruik door een proces met 5 seconden per interval

- Netwerken  
  - Lijst met netwerk adapters met adapter statistieken
  - Netwerk routerings tabel
  - Poorten en status geopend

- Storage
  - Lijst met apparaten blok keren
  - Lijst met partities
  - Koppelings punten lijst
  - MDADM-volume gegevens
  - LVM-volume gegevens
  - Profilerings Capture op alle schijven met een interval van 5 seconden

- Logboeken
  - /var/log/messages
  - /var/log/syslog
  - /var/log/kern.log
  - /var/log/cron.log
  - /var/log/boot.log
  - /var/log/yum.log
  - /var/log/dpkg.log
  - /var/log/sysstat of/var/log/sa [ `**` ]
  - /var/log/cloud-init.log
  - /var/log/cloud-init-output.log
  - /var/log/gpu-manager.log
  - /var/log/waagent.log
  - /var/log/Azure/[extensie map]/ \* logboek\*
  - /var/opt/microsoft/omsconfig/omsconfig.log
  - /var/opt/microsoft/omsagent/log/omsagent.log
  - /etc/waagent.config
  - Uitvoer van journalctl voor de afgelopen vijf dagen

- [Meta gegevens van Azure virtual machine-instantie](../windows/instance-metadata-service.md)

>[!Note]
>[ `*` ] PCI-gegevens zijn nog niet verzameld voor Debian-en SLES-distributies.
> 
>[ `**` ]/var/log/sysstat of/var/log/sa bevat de rapport bestanden voor systeem activiteiten die worden verzameld door het sysstat-pakket. Als het sysstat-pakket niet is geïnstalleerd op de virtuele machine, biedt het PerfInsights-hulp programma een aanbeveling om het te installeren.

## <a name="run-the-perfinsights-linux-on-your-vm"></a>De PerfInsights Linux uitvoeren op uw VM

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>Wat moet ik weten voordat ik het hulp programma uitvoer

#### <a name="tool-requirements"></a>Hulp programma vereisten

- Dit hulp programma moet worden uitgevoerd op de VM met het prestatie probleem.
- Python 3. x of python 2,7 moet zijn geïnstalleerd op de virtuele machine.

- De volgende distributies worden momenteel ondersteund:

    | Distributie               | Versie                                         |
    |----------------------------|-------------------------------------------------|
    | Oracle Linux server        | 6,10 [ `*` ], 7,3, 7,5, 7,6, 7,7, 7,8 |
    | CentOS                     | 6,5 [ `*` ], 7,6, 7,7, 7,8                                    |
    | RHEL                       | 7,2, 7,5, 8,0 [ `*` ], 8,1, 8,2                               |
    | Ubuntu                     | 14,04, 16,04, 18,04, 20,04                               |
    | Debian                     | 8, 9, 10 [ `*` ]                                    |
    | SLES                       | 12 SP4 [ `*` ], 12 SP5 [ `*` ], 15 [ `*` ], 15 SP1 [ `*` ], 15 SP2 [ `*` ]                                      |
    |                            |                                                   |

>[!Note]
>[ `*` ] Raadpleeg de sectie [bekende problemen](#known-issues)

### <a name="known-issues"></a>Bekende problemen

- Voor RHEL 8 is python niet standaard geïnstalleerd. Als u PerfInsights Linux wilt uitvoeren, moet u eerst python 2,7 installeren

- Het verzamelen van informatie over de gast agent kan mislukken op CentOS 6. x

- Informatie over PCI-apparaten wordt niet verzameld op op Debian gebaseerde distributies

- LVM-gegevens worden gedeeltelijk verzameld op sommige distributies

### <a name="how-do-i-run-perfinsights"></a>PerfInsights uitvoeren Hoe kan ik

U kunt PerfInsights uitvoeren op een virtuele machine door Azure-prestatie diagnostiek van Azure Portal te installeren. U kunt deze ook uitvoeren als een zelfstandig hulp programma.

>[!Note]
>PerfInsights verzamelt en analyseert de gegevens. Er worden geen wijzigingen aangebracht in het systeem.

#### <a name="install-and-run-perfinsights-from-the-azure-portal"></a>PerfInsights installeren en uitvoeren vanuit de Azure Portal

Zie [Azure prestatie diagnostiek](performance-diagnostics.md)voor meer informatie over deze optie.  

#### <a name="run-perfinsights-in-standalone-mode"></a>PerfInsights uitvoeren in de zelfstandige modus

Voer de volgende stappen uit om het PerfInsights-hulp programma uit te voeren:

1. Down load [PerfInsights. tar. gz](https://aka.ms/perfinsightslinuxdownload) naar een map op uw virtuele machine en pak de inhoud uit met behulp van de onderstaande opdrachten van de Terminal.

   ```bash
   wget https://download.microsoft.com/download/9/F/8/9F80419C-D60D-45F1-8A98-718855F25722/PerfInsights.tar.gz
   ```

   ```bash
   tar xzvf PerfInsights.tar.gz
   ```

2. Ga naar de map die het `perfinsights.py` bestand bevat en voer uit `perfinsights.py` om de beschik bare commandline-para meters weer te geven.

    ```bash
    cd <the path of PerfInsights folder>
    sudo python perfinsights.py
    ```

    ![Scherm opname van de uitvoer van PerfInsights linux commandline](media/how-to-use-perfinsights-linux/perfinsights-linux-commandline.png)

    De basis syntaxis voor het uitvoeren van PerfInsights-scenario's is:

    ```bash
    sudo python perfinsights.py -r <ScenarioName> -d [duration]<H | M | S> [AdditionalOptions]
    ```

    U kunt het onderstaande voor beeld gebruiken om een scenario voor snelle prestatie analyse uit te voeren gedurende 1 minuut en de resultaten te maken onder de map/tmp/output:

    ```bash
    sudo python perfinsights.py -r quick -d 1M -a -o /tmp/output
    ```

    U kunt het onderstaande voor beeld gebruiken voor het uitvoeren van een prestatie analyse scenario gedurende 5 minuten en het uploaden van de tar-bal naar het opslag account:

    ```bash
    sudo python perfinsights.py -r vmslow -d 300S -a -t <StorageAccountName> -k <StorageAccountKey> -i <full resource Uri of the current VM>
    ```

    >[!Note]
    >Voordat u een scenario uitvoert, wordt de gebruiker door PerfInsights gevraagd om de diagnostische gegevens te delen en de gebruiksrecht overeenkomst te accepteren. Gebruik **de optie-a of--accept-vrijwaring-en-share-Diagnostics** om deze prompts over te slaan.
    >
    >Als u een actief ondersteunings ticket hebt met micro soft en PerfInsights wilt uitvoeren volgens de aanvraag van de ondersteunings technicus waarmee u werkt, moet u ervoor zorgen dat u het nummer van het ondersteunings ticket opgeeft met de optie **-s of--Support-aanvraag** .

Wanneer de uitvoering is voltooid, wordt een nieuw tar-bestand weer gegeven in dezelfde map als PerfInsights, tenzij er geen uitvoermap is opgegeven. De naam van het bestand is **PerformanceDiagnostics \_ jjjj-mm-dd \_ hh-mm-SS-FFF. tar. gz.** U kunt dit bestand naar de ondersteunings agent verzenden voor analyse of het rapport openen in het bestand om de bevindingen en aanbevelingen te bekijken.

## <a name="review-the-diagnostics-report"></a>Het diagnostische rapport controleren

Binnen het **PerformanceDiagnostics \_ jjjj-mm-dd \_ hh-mm-SS-FFF. tar. gz** -bestand kunt u een HTML-rapport vinden met informatie over de bevindingen van PerfInsights. Als u het rapport wilt bekijken, vouwt u het bestand **PerformanceDiagnostics \_ jjjj-mm-dd \_ hh-mm-SS-FFF. tar. gz** uit en opent u vervolgens het bestand **PerfInsights Report.html** .

### <a name="overview-tab"></a>Tabblad Overzicht

Het tabblad **overzicht** bevat basis informatie over de uitvoering en de gegevens van de virtuele machine. Het tabblad **bevindingen** bevat een samen vatting van de aanbevelingen van alle verschillende secties van het PerfInsights-rapport.

![Scherm afbeelding van het tabblad Overzicht van het PerfInsights-rapport.](media/how-to-use-perfinsights-linux/perfinsights-linux-overview.png)  
![Scherm afbeelding van het tabblad Linux van het PerfInsights-rapport.](media/how-to-use-perfinsights-linux/perfinsights-linux-findings-tab.png)

> [!NOTE]
> Bevindingen die zijn gecategoriseerd als hoog, zijn bekende problemen die prestatie problemen kunnen veroorzaken. Bevindingen die zijn gecategoriseerd als medium, vertegenwoordigen niet-optimale configuraties die niet noodzakelijkerwijs prestatie problemen veroorzaken. Bevindingen die zijn gecategoriseerd als laag, zijn alleen informatieve instructies.

Bekijk de aanbevelingen en koppelingen voor alle belang rijke en middel grote bevindingen. Meer informatie over hoe deze prestaties kunnen beïnvloeden en ook over de aanbevolen procedures voor de prestaties geoptimaliseerde configuraties.

### <a name="cpu-tab"></a>Tabblad CPU

Tabblad **CPU** bevat informatie over het CPU-verbruik op het hele systeem tijdens het uitvoeren van de PerfInsights. Informatie over de hoge CPU-gebruiks perioden en de belangrijkste, veeleisende CPU-consumenten zijn handig voor het oplossen van problemen met hoge CPU.

![Scherm opname van het tabblad CPU van PerfInsights-rapport](media/how-to-use-perfinsights-linux/perfinsights-linux-cpu-tab.png)

### <a name="storage-tab"></a>Tabblad opslag

In het gedeelte **bevindingen** worden verschillende bevindingen en aanbevelingen met betrekking tot opslag weer gegeven.

Op de tabbladen **apparaten** en andere gerelateerde secties, zoals **partities**, **LVM** en **MDADM** , wordt beschreven hoe blok apparaten worden geconfigureerd en aan elkaar zijn gerelateerd.

![Scherm afbeelding van het tabblad opslag](media/how-to-use-perfinsights-linux/perfinsights-linux-storage-tab.png)  
![Scherm afbeelding van het tabblad MDADM](media/how-to-use-perfinsights-linux/perfinsights-linux-mdadm-config.png)

### <a name="linux-tab"></a>Tabblad Linux

Op het tabblad **Linux** vindt u informatie over de hardware en het besturings systeem die worden uitgevoerd in uw VM. Details bevatten een lijst met actieve processen en informatie over gast agent, PCI, CPU, stuur Programma's en LIS-stuur programma.

![Scherm afbeelding van het tabblad Linux](media/how-to-use-perfinsights-linux/perfinsights-linux-tab.png)

## <a name="next-steps"></a>Volgende stappen

U kunt Diagnostische logboeken en rapporten uploaden naar Microsoft Ondersteuning voor verdere beoordeling. Wanneer u met de Microsoft Ondersteuning personeel werkt, kunnen ze een aanvraag indienen om de uitvoer die door PerfInsights wordt gegenereerd, te verzenden om te helpen bij het oplossen van het probleem.

Op de volgende scherm afbeelding wordt een bericht weer gegeven dat vergelijkbaar is met wat u kunt ontvangen:

![Scherm opname van het voorbeeld bericht van Microsoft Ondersteuning](media/how-to-use-perfinsights-linux/support-email.png)

Volg de instructies in het bericht voor toegang tot de werk ruimte bestands overdracht. Voor extra beveiliging moet u uw wacht woord wijzigen bij het eerste gebruik.

Nadat u zich hebt aangemeld, vindt u een dialoog venster voor het uploaden van het bestand **PerformanceDiagnostics \_ jjjj-mm-dd \_ hh-mm-SS-FFF. tar. gz** dat door PerfInsights is verzameld.
