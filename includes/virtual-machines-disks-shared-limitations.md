---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/30/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 22a1a4b99717df32a40ea69ebb65a3a8e14ee2b4
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102510892"
---
Het inschakelen van gedeelde schijven is alleen beschikbaar voor een subset van schijf typen. Op dit moment kunnen alleen Ultra disks en Premium Ssd's gedeelde schijven inschakelen. Op elke beheerde schijf waarvoor gedeelde schijven zijn ingeschakeld, gelden de volgende beperkingen, geordend op schijf type:

### <a name="ultra-disks"></a>Ultraschijven

Ultra schijven hebben hun eigen afzonderlijke lijst met beperkingen, niet gerelateerd aan gedeelde schijven. Raadpleeg [using Azure Ultra disks](../articles/virtual-machines/disks-enable-ultra-ssd.md)voor Ultra Disk-beperkingen.

Bij het delen van ultra schijven hebben ze de volgende extra beperkingen:

- Momenteel beperkt tot Azure Resource Manager-of SDK-ondersteuning. 
- Alleen standaard schijven kunnen worden gebruikt in combi natie met sommige versies van Windows Server-failovercluster, voor meer informatie, [Hardware-vereisten en opslag opties voor failover clustering](/windows-server/failover-clustering/clustering-requirements).

Gedeelde Ultra schijven zijn beschikbaar in alle regio's die standaard ondersteuning bieden voor Ultra schijven, en u hoeft zich niet te registreren voor toegang om ze te gebruiken.

### <a name="premium-ssds"></a>Premium-SSD's

- Momenteel beperkt tot Azure Resource Manager-of SDK-ondersteuning. 
- Kan alleen worden ingeschakeld op gegevens schijven, niet op OS-schijven.
- **Alleen-lezen** host-caching is niet beschikbaar voor Premium-ssd's met `maxShares>1` .
- Schijf bursting is niet beschikbaar voor Premium-Ssd's met `maxShares>1` .
- Wanneer u beschikbaarheids sets en virtuele-machine schaal sets gebruikt met gedeelde Azure-schijven, wordt de uitlijning van het [opslag fout domein](../articles/virtual-machines/availability.md) met het fout domein van de virtuele machine niet afgedwongen voor de gedeelde gegevens schijf.
- Wanneer u [proximity placement groups (PPG)](../articles/virtual-machines/windows/proximity-placement-groups.md)gebruikt, moeten alle virtuele machines die een schijf delen deel uitmaken van dezelfde PPG.
- Alleen standaard schijven kunnen worden gebruikt in combi natie met sommige versies van Windows Server-failovercluster, voor meer informatie, [Hardware-vereisten en opslag opties voor failover clustering](/windows-server/failover-clustering/clustering-requirements).
- Azure Site Recovery-ondersteuning is nog niet beschikbaar.
- Azure Backup is beschikbaar via [Azure Disk Backup (preview)](../articles/backup/disk-backup-overview.md).

#### <a name="regional-availability"></a>Regionale beschikbaarheid

Gedeelde Premium-Ssd's zijn beschikbaar in alle regio's waar Managed disks beschikbaar zijn.