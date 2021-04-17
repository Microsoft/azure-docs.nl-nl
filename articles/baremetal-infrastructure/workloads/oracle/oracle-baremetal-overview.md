---
title: Wat is BareMetal-infrastructuur voor Oracle?
description: Meer informatie over de functies van BareMetal Infrastructure voor Oracle-workloads.
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: efb7506a0fa98ca93390bfe7d3cb1401cdbb77e2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559045"
---
# <a name="what-is-baremetal-infrastructure-for-oracle"></a>Wat is BareMetal-infrastructuur voor Oracle?

In dit artikel wordt een overzicht gegeven van de functies van de BareMetal-infrastructuur voor Oracle-workloads.

BareMetal Infrastructure for Oracle is gebaseerd op Door Oracle gecertificeerde Unified Computing System (UCS) en FPodPod. Het FlexPod-platform biedt vooraf gevalideerde opslag-, netwerk- en servertechnologieën. Het biedt NFS-opslag en biedt integratie met behulp van het DirectNFS-protocol. De BareMetal-servers zijn toegewezen aan u, zonder hypervisor op de BareMetal-exemplaren. 

Deze exemplaren zijn voor het uitvoeren van essentiële toepassingen waarvoor een Oracle-workload is vereist. BareMetal-exemplaren bieden een lage latentie (0,35 ms) voor uw toepassingen die worden uitgevoerd op virtuele Azure-machines (VM's). BareMetal biedt een gedeelde opslagschijf en biedt ondersteuning voor multicasting die vereist is voor communicatie tussen knooppunt en een toegewezen privé-interconnect-netwerk. 

Andere functies van BareMetal Infrastructure voor Oracle zijn:

- Door Oracle gecertificeerde UCS-blades - UCSB200-M5, UCSB460-M4, UCSB480-M5
- Oracle Real Application Clusters (RAC) knooppunt-naar-knooppuntcommunicatie (multicast) met behulp van privé virtueel LAN (VLAN) -40 Gb.
- Door Microsoft beheerde hardware
  - Redundante opslag, netwerk, energie, beheer
  - Bewaking voor infrastructuur, reparaties en vervanging
  - Bevat Azure ExpressRoute van de domeincontroller van de klant
  - Beveiligde fysieke en netwerkbeveiliging, heeft toegang tot alle Azure-cloudservices

### <a name="supported-protocols"></a>Ondersteunde protocollen

De volgende protocollen worden gebruikt voor verschillende bevestigingspunten binnen BareMetal-servers voor Oracle-workloads.

- Os-mount – iSCSI
- Gegevens/logboek – NFSv3
- backup/archieve – NFSv4

### <a name="licensing"></a>Licentieverlening

- U gebruikt uw eigen on-premises besturingssysteem en Oracle-licenties.

### <a name="operating-system"></a>Besturingssysteem

Servers worden vooraf geladen met het besturingssysteem RHEL 7.6.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de SKU's voor Oracle BareMetal-workloads.

> [!div class="nextstepaction"]
> [BareMetal-SKU's voor Oracle-workloads](oracle-baremetal-skus.md)
