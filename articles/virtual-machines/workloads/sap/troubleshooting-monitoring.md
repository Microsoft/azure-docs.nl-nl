---
title: Bewaking van SAP HANA op Azure (grote exemplaren) | Microsoft Docs
description: Bewaak SAP HANA op een Azure (grote exemplaren).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 246b9398824597ec337ee9e9ea3dc24267311f60
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101669588"
---
# <a name="how-to-monitor-sap-hana-large-instances-on-azure"></a>SAP HANA (grote exemplaren) in azure controleren

SAP HANA op Azure (grote exemplaren) is niet anders dan andere IaaS-implementaties. u moet bewaken wat het besturings systeem en de toepassing doen en hoe de toepassingen de volgende resources verbruiken:

- CPU
- Geheugen
- Netwerkbandbreedte
- Schijfruimte

Met Azure Virtual Machines moet u nagaan of de resource klassen die hierboven worden genoemd, voldoende zijn of uitgeput raken. Hier vindt u meer informatie over elk van de verschillende klassen:

**CPU-resource verbruik:** De verhouding die door SAP voor een bepaalde werk belasting met HANA is gedefinieerd, wordt afgedwongen om ervoor te zorgen dat er voldoende CPU-resources beschikbaar zijn voor de gegevens die in het geheugen zijn opgeslagen. Het kan echter voor komen dat HANA veel Cpu's gebruikt die query's uitvoeren als gevolg van ontbrekende indexen of vergelijk bare problemen. Dit betekent dat u het CPU-resource verbruik van de HANA-eenheid voor grote instanties moet bewaken, evenals CPU-resources die worden verbruikt door de specifieke HANA-Services.

**Geheugen gebruik:** Is belang rijk om te bewaken vanuit HANA, evenals buiten HANA op de eenheid. In HANA bewaakt u hoe de gegevens worden gebruikt voor het gebruik van HANA toegewezen geheugen om binnen de vereiste aanpassings richtlijnen van SAP te blijven. U wilt ook geheugen gebruik bewaken op het niveau van de grote instantie om ervoor te zorgen dat extra geïnstalleerde niet-HANA-software niet te veel geheugen verbruikt en daarom concurreren met HANA voor geheugen.

**Netwerk bandbreedte:** De Azure VNet-gateway is beperkt in de band breedte van de gegevens die naar het Azure VNet worden verplaatst. het is dus handig om de gegevens die door alle Azure-Vm's in een VNet worden ontvangen, te bewaken om erachter te komen hoe dicht u de limieten van de Azure-gateway-SKU die u hebt geselecteerd. Op de HANA grote instantie-eenheid is het zinvol om het binnenkomende en uitgaande netwerk verkeer ook te controleren en om de volumes bij te houden die na verloop van tijd worden verwerkt.

**Schijf ruimte:** Het gebruik van schijf ruimte neemt doorgaans gedurende een periode toe. De meest voorkomende oorzaken zijn: gegevens volume verg root de uitvoering van back-ups van transactie logboeken, traceer bestanden opslaan en opslag momentopnamen uitvoeren. Daarom is het belang rijk om het schijfruimte gebruik te bewaken en de schijf ruimte die is gekoppeld aan de HANA-eenheid voor grote instanties te beheren.

Voor de **type II sku's** van de Hana grote instanties wordt de-server geleverd met de vooraf geladen hulpprogram ma's voor systeem diagnose. U kunt deze diagnostische hulpprogram ma's gebruiken om de systeem status controle uit te voeren. Voer de volgende opdracht uit om het status controle logboek bestand te genereren op/var/log/health_check.
```
/opt/sgi/health_check/microsoft_tdi.sh
```
Wanneer u met het Microsoft Ondersteuning-team werkt om een probleem op te lossen, wordt u mogelijk ook gevraagd om de logboek bestanden op te geven met behulp van deze diagnostische hulpprogram ma's. U kunt het bestand zip met de volgende opdracht.
```
tar  -czvf health_check_logs.tar.gz /var/log/health_check
```

**Volgende stappen**

- Zie [SAP Hana (grote exemplaren) in azure controleren](./hana-monitor-troubleshoot.md).
