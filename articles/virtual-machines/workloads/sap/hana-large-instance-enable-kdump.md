---
title: Script om kdump in te scha kelen in SAP HANA (grote exemplaren) | Microsoft Docs
description: Script om kdump in te scha kelen in SAP HANA (grote instanties) HLI type I, HLI type II
services: virtual-machines-linux
documentationcenter: ''
author: prtyag
manager: hrushib
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/30/2020
ms.author: prtyag
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e4b06cbcbef79e243116bddb33adbcf6476fac8a
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102213365"
---
# <a name="kdump-for-sap-hana-on-azure-large-instances-hli"></a>Kdump voor SAP HANA on Azure Large Instances (HLI)

Het configureren en inschakelen van kdump is een stap die nodig is om systeem crashes op te lossen die geen duidelijke oorzaak hebben.
Er zijn momenten waarop een systeem onverwacht vastloopt dat niet kan worden verklaard door een hardware-of infrastructuur probleem.
In deze gevallen kan het een besturings systeem of toepassings probleem zijn en kdump kan SUSE bepalen waarom een systeem is vastgelopen.

## <a name="enable-kdump-service"></a>Kdump-service inschakelen

Dit document bevat informatie over het inschakelen van de kdump-service op Azure HANA grote instantie (**type I en type II**)

## <a name="supported-skus"></a>Ondersteunde Sku's

|  Type van de grote hoeveelheid Hana-exemplaren   |  BESTURINGSSYSTEEM leverancier   |  Versie van besturingssysteem pakket   |  SKU |
|-----------------------------|--------------|-----------------------|-------------|
|   Type I                    |  SuSE        |   SLES 12 SP3         |  S224m      |
|   Type I                    |  SuSE        |   SLES 12 SP4         |  S224m      |
|   Type I                    |  SuSE        |   SLES 12 SP2         |  S72        |
|   Type I                    |  SuSE        |   SLES 12 SP2         |  S72m       |
|   Type I                    |  SuSE        |   SLES 12 SP3         |  S72m       |
|   Type I                    |  SuSE        |   SLES 12 SP2         |  S96        |
|   Type I                    |  SuSE        |   SLES 12 SP3         |  S96        |
|   Type I                    |  SuSE        |   SLES 12 SP2         |  S192       |
|   Type I                    |  SuSE        |   SLES 12 SP3         |  S192       |
|   Type I                    |  SuSE        |   SLES 12 SP4         |  S192       |
|   Type I                    |  SuSE        |   SLES 12 SP2         |  S192m      |
|   Type I                    |  SuSE        |   SLES 12 SP3         |  S192m      |
|   Type I                    |  SuSE        |   SLES 12 SP4         |  S192m      |
|   Type I                    |  SuSE        |   SLES 12 SP2         |  S144       |
|   Type I                    |  SuSE        |   SLES 12 SP3         |  S144       |
|   Type I                    |  SuSE        |   SLES 12 SP2         |  S144m      |
|   Type I                    |  SuSE        |   SLES 12 SP3         |  S144m      |
|   Type II                   |  SuSE        |   SLES 12 SP2         |  S384       |
|   Type II                   |  SuSE        |   SLES 12 SP3         |  S384       |
|   Type II                   |  SuSE        |   SLES 12 SP4         |  S384       |
|   Type II                   |  SuSE        |   SLES 12 SP2         |  S384xm     |
|   Type II                   |  SuSE        |   SLES 12 SP3         |  S384xm     |
|   Type II                   |  SuSE        |   SLES 12 SP4         |  S384xm     |
|   Type II                   |  SuSE        |   SLES 12 SP2         |  S576m      |
|   Type II                   |  SuSE        |   SLES 12 SP3         |  S576m      |
|   Type II                   |  SuSE        |   SLES 12 SP4         |  S576m      |

## <a name="prerequisites"></a>Vereisten

- De kdump-service maakt gebruik van `/var/crash` Directory voor het schrijven van dumps, zorg ervoor dat de partitie overeenkomt met deze map voldoende ruimte heeft voor dumps.

## <a name="setup-details"></a>Details van de installatie

- Script voor het inschakelen van kdump vindt u [hier](https://github.com/Azure/sap-hana-tools/blob/master/tools/enable-kdump.sh)
> [!NOTE]
> Dit script wordt gemaakt op basis van de instellingen van het lab en de klant wordt naar verwachting contact opnemen met de leverancier van het besturings systeem voor verdere afstemming.
> Er wordt een afzonderlijke LUN ingericht voor de nieuwe en bestaande servers voor het opslaan van de dumps en het script zal ervoor zorgen dat het bestands systeem van het LUN wordt geconfigureerd.
> Micro soft is niet verantwoordelijk voor het analyseren van de dump. De klant moet een ticket openen met de leverancier van het besturings systeem om het te kunnen analyseren.

- Dit script uitvoeren op een HANA grote instantie met behulp van de onderstaande opdracht

    > [!NOTE]
    > de sudo-bevoegdheid is vereist om deze opdracht uit te voeren.

    ```bash
    sudo bash enable-kdump.sh
    ```

- Als de opdracht uitvoer van kdump is ingeschakeld, moet u het systeem opnieuw opstarten om de wijzigingen toe te passen.

- Als de uitvoer van de opdracht bepaalde bewerking niet kan uitvoeren, sluit u!!!!, en wordt de kdump-service niet ingeschakeld. Raadpleeg het [ondersteunings probleem](#support-issue)van de sectie.

## <a name="test-kdump"></a>Kdump testen

> [!NOTE]
>  Onder bewerking wordt een kernel-crash geactiveerd en wordt het systeem opnieuw opgestart.

- Kernel-crash activeren

    ```bash
    echo c > /proc/sysrq-trigger
    ```

- Nadat het systeem opnieuw is opgestart, controleert u de `/var/crash` Directory op kernel crash logs.

- Als de `/var/crash` map bevat met de huidige datum, wordt de kdump ingeschakeld.

## <a name="support-issue"></a>Ondersteunings probleem

Als het script mislukt met een fout of kdump niet is ingeschakeld, verhoogt u de service aanvraag met het micro soft-ondersteunings team met de volgende details.

* HLI-abonnements-ID

* Servernaam

* BESTURINGSSYSTEEM leverancier

* Besturingssysteemversie

* Kernelversie

## <a name="related-documents"></a>Gerelateerde documenten
- Meer informatie over [het configureren van de kdump](https://www.suse.com/support/kb/doc/?id=3374462)
