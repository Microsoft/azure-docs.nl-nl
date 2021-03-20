---
title: Overzicht van Microsoft Azure FXT Edge Filer
description: Hier wordt een beschrijving gegeven van de hybride opslagcache van Azure FXT Edge Filer, een actieve acceleratoroplossing voor toegang tot archieven en bestanden voor High Performance Computing
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: overview
ms.date: 07/01/2019
ms.author: rohogue
ms.openlocfilehash: 72308bf64246a8041bd40fa557280014a13c95eb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92218779"
---
# <a name="what-is-azure-fxt-edge-filer-hybrid-storage-cache"></a>Wat is hybride opslagcache van Azure FXT Edge Filer?

Azure FXT Edge Filer is een apparaat voor hybride opslagcache dat snelle toegang tot bestanden en een actief archief biedt voor HPC-taken (High Performance Computing).

Het apparaat werkt met meerdere gegevensbronnen die in een lokaal datacentrum, op afstand of in de cloud kunnen zijn opgeslagen. Azure FXT Edge Filer kan een uniforme naamruimte voor gegevens in verschillende opslagsystemen bieden.

Drie of meer FXT Edge Filer-hardwareapparaten werken samen als een geclusterd bestandssysteem om de cache te leveren. Neem contact op met uw Microsoft-vertegenwoordiger voor informatie over de aankoop van de vereiste hardware.

Lees de productinformatie en het gegevensblad over [Azure FXT Edge Filer](https://azure.microsoft.com/services/fxt-edge-filer/) voor meer informatie.

## <a name="use-cases"></a>Gebruiksvoorbeelden

Met Azure FXT Edge Filer wordt de productiviteit het beste verbeterd voor werkstromen zoals:

* Werkstromen met leesintensieve bestandstoegang
* NFSv3- of SMB2-protocollen
* Serverfarms met 1000 tot 100.000 CPU-cores

### <a name="nas-optimization-and-scaling"></a>NAS-optimalisatie en schaalaanpassing

U kunt de Azure FXT Edge Filer-cache gebruiken voor snelle toegang tot bestaande NetApp- en Dell EMC Isilon NAS-systemen. Ook kunt u Azure Blob of andere cloudopslag toevoegen voor schaalbaarheid, zonder de processen voor gegevenstoegang aan de clientzijde opnieuw te moeten inrichten.

### <a name="wan-caching"></a>Opslaan in WAN-cache

Azure FXT Edge Filer kan worden gebruikt voor snelle bestandstoegang van grootschalige gebruikers wanneer de gegevens die zij nodig hebben ergens anders zijn opgeslagen. Geef toegang terwijl u back-up- en andere gegevensbeheersystemen in een gecentraliseerd datacentrum opslaat.

### <a name="active-archive-in-azure-blob"></a>Actief archief in Azure Blob

Breid uw datacentrum uit naar cloudopslag met Azure FXT Edge Filer als het toegangspunt.

## <a name="features"></a>Functies

Er zijn twee hardwaremodellen beschikbaar.

| Modelleren | DRAM | NVMe SSD | Netwerkpoorten |
|-------|------|----------|---------------|
| FXT 6600 | 1536 GB | 25,6 TB | 6 x 25 Gb/10 Gb + 2 x 1 Gb |
| FXT 6400 | 768 GB | 12,8 TB | 6 x 25 Gb/10 Gb + 2 x 1 Gb |

## <a name="next-steps"></a>Volgende stappen

* Lees de [specificaties](fxt-specs.md) of volg de [installatiezelfstudie](fxt-install.md) voor meer informatie over Azure FXT Edge Filer.
* Ga naar de [productpagina van Azure FXT Edge Filer](https://azure.microsoft.com/services/fxt-edge-filer/) voor informatie over hoe u Azure FXT Edge Filer aanschaft.
