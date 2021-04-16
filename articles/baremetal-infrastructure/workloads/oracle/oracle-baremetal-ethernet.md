---
title: Ethernet-configuratie van BareMetal voor Oracle
description: Meer informatie over de configuratie van Ethernet-interfaces op BareMetal-exemplaren voor Oracle-workloads.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: e98af51075d5193cab14d18f1cdb1f431c8fa892
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559069"
---
# <a name="ethernet-configuration-of-baremetal-for-oracle"></a>Ethernet-configuratie van BareMetal voor Oracle

In dit artikel kijken we naar de configuratie van Ethernet-interfaces op BareMetal-exemplaren voor Oracle-workloads.

Elke inrichtende BareMetal-instantie voor Oracle is vooraf geconfigureerd met sets Ethernet-interfaces. De Ethernet-interfaces zijn onderverdeeld in vier typen:

- Wordt gebruikt voor of door clienttoegang.
- Wordt gebruikt voor communicatie tussen knooppunt en knooppunt. Deze interface wordt geconfigureerd op alle servers, ongeacht de aangevraagde topologie. Deze wordt alleen gebruikt voor uitschaalscenario's.
- Wordt gebruikt voor knooppunt-naar-opslag-connectiviteit.
- Wordt gebruikt voor het instellen van herstel na noodherstel en connectiviteit met het wereldwijde bereik voor connectiviteit tussen regio's.

## <a name="architecture"></a>Architectuur

In het volgende diagram ziet u de architectuur van de vooraf geconfigureerde Ethernet-interfaces voor BareMetal Infrastructure. 

[![Diagram met de architectuur van de vooraf geconfigureerde Ethernet-interfaces voor Oracle-workloads.](media/oracle-baremetal-ethernet/architecture-ethernet.png)](media/oracle-baremetal-ethernet/architecture-ethernet.png#lightbox)

De standaardconfiguratie wordt geleverd met één client-IP-interface (eth1) die verbinding maakt vanuit uw Azure Virtual Network (VNET) waarmee u Secure Shell (SSH) kunt gebruiken voor toegang tot een BareMetal-exemplaar.

> [!NOTE]
> Neem voor een andere clientinterface (eth10) van een ander Azure-VNET contact op met uw Microsoft CSA om een serviceaanvraag in te dienen. Bijvoorbeeld als u ontwikkel-/test- en productie-/DR-omgevingen wilt.

| **Logische NIC-interface** | **Naam met RHEL-besturingssysteem** | **Gebruiksscenario** |
| --- | --- | --- |
| A | eth1.tenant | Client naar BareMetal-exemplaar |
| C | eth2.tenant | Knooppunt naar opslag; ondersteunt de coördinatie en toegang tot de opslagcontrollers voor het beheer van de opslagomgeving. |
| B | eth3.tenant | Knooppunt-naar-knooppunt (privé-interconnect) |
| C | eth4.tenant | Gereserveerd/ iSCSI |
| C | eth5.tenant | Gereserveerde/logboekback-up |
| C | eth6.tenant | Node-to-storage_Data Backup (RMAN, Snapshot) |
| C | eth7.tenant | Node-to-storage_dNFS-Pri; biedt connectiviteit met de NetApp-opslag array. |
| C | eth8.tenant | Node-to-storage_dNFS-Sec; biedt connectiviteit met de NetApp-opslag array. |
| D | eth9.tenant | Dr-connectiviteit voor Global Reach instellen voor toegang tot DEAS in een andere regio. |
| A | \*eth10.tenant | \* Client naar BareMetal-exemplaar
 |

Indien nodig kunt u zelf meer netwerkinterfacecontrollerkaarten (NIC's) definiëren. De configuraties van bestaande NIC's *kunnen echter niet* worden gewijzigd.

## <a name="usage-rules"></a>Gebruiksregels

Voor BareMetal-exemplaren heeft de standaardwaarde negen toegewezen IP-adressen op de vier logische NIC's. De volgende gebruiksregels zijn van toepassing:

- Ethernet A moet een toegewezen IP-adres hebben dat zich buiten het adresbereik van de IP-adresgroep van de server dat u bij Microsoft hebt ingediend. Dit IP-adres mag niet worden bewaard in de map etc/hosts van het besturingssysteem.
- Ethernet B moet uitsluitend worden onderhouden in de map etc/hosts voor communicatie tussen de verschillende exemplaren. Behoud deze IP-adressen in scale-out RAC-configuraties (Oracle Real Application Clusters) als de IP-adressen die worden gebruikt voor de configuratie tussen knooppunt.
- Ethernet C moet een toegewezen IP-adres hebben dat wordt gebruikt voor communicatie met NFS-opslag. Dit type adres mag niet worden onderhouden in de map etc/hosts.
- Ethernet D moet uitsluitend worden gebruikt voor het instellen van een wereldwijd bereik voor toegang tot BareMetal-exemplaren in uw DR-regio.

## <a name="next-step"></a>Volgende stap

Meer informatie over de BareMetal-infrastructuur voor Oracle-architectuur.

> [!div class="nextstepaction"]
> [Architectuur van BareMetal-infrastructuur voor Oracle](oracle-baremetal-architecture.md)
