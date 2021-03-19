---
title: Azure VMware-oplossing op CloudSimple-firewall tabellen
description: Meer informatie over CloudSimple-firewall tabellen en firewall regels van de privécloud, met inbegrip van standaard regels die op elke firewall tabel worden gemaakt.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8c44c39f66a0a0161eea8a7e9656bbe0e3d1015c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "88140867"
---
# <a name="firewall-tables-overview"></a>Overzicht van Firewall tabellen

Een firewall tabel bevat regels voor het filteren van netwerk verkeer naar en van de Privécloud. U kunt Firewall tabellen Toep assen op een VLAN/subnet. De regels bepalen netwerk verkeer tussen een bron netwerk of IP-adres en een bestemmings netwerk of IP-adres.

## <a name="firewall-rules"></a>Firewall-regels

In de volgende tabel worden de para meters in een firewall regel beschreven.

| Eigenschap | Details |
| ---------| --------|
| **Naam** | Een naam die een unieke identificatie vormt van de firewall regel en het doel ervan. |
| **Priority** | Een getal tussen 100 en 4096, met 100 de hoogste prioriteit. Regels worden in volg orde van prioriteit verwerkt. Wanneer het verkeer een regel overeenkomst tegen komt, stopt de verwerking van de regel. Als gevolg hiervan worden regels met een lagere prioriteit die dezelfde kenmerken hebben als regels met hogere prioriteiten, niet verwerkt.  Wees voorzichtig om conflicterende regels te voor komen. |
| **Status bijhouden** | Bijhouden kan stateless (Privécloud, Internet of VPN) of stateful (openbaar IP-adres) zijn.  |
| **Protocol** | De opties zijn onder andere TCP of UDP. Als ICMP vereist is, gebruikt u een. |
| **Richting** | Hiermee wordt aangegeven of de regel van toepassing is op binnenkomend of uitgaand verkeer. |
| **Actie** | Het type verkeer dat in de regel is gedefinieerd, toestaan of weigeren. |
| **Bron** | Een IP-adres, een CIDR-blok (Classless Inter-Domain Routing) (10.0.0.0/24) of een wille keurig.  Als u een bereik, een servicetag of een toepassings beveiligings groep opgeeft, kunt u minder beveiligings regels maken. |
| **Bron poort** | Poort van waaruit het netwerk verkeer afkomstig is.  U kunt een afzonderlijke poort of een bereik van poorten opgeven, zoals 443 of 8000-8080. Als u bereiken opgeeft, hoeft u minder beveiligingsregels te maken. |
| **Doel** | Een IP-adres, een CIDR-blok (Classless Inter-Domain Routing) (10.0.0.0/24) of een wille keurig.  Als u een bereik, een servicetag of een toepassings beveiligings groep opgeeft, kunt u minder beveiligings regels maken.  |
| **Doel poort** | Poort waarnaar het netwerk verkeer loopt.  U kunt een afzonderlijke poort of een bereik van poorten opgeven, zoals 443 of 8000-8080. Als u bereiken opgeeft, hoeft u minder beveiligingsregels te maken.|

### <a name="stateless"></a>Stateless

Een staatloze regel ziet er alleen uit bij afzonderlijke pakketten en filtert deze op basis van de regel.  
Er zijn mogelijk extra regels vereist voor de verkeers stroom in de omgekeerde richting.  Gebruik stateless regels voor verkeer tussen de volgende punten:

* Subnetten van persoonlijke Clouds
* On-premises subnet en een persoonlijk Cloud subnet
* Internet verkeer van de Privécloud

### <a name="stateful"></a>Stateful

 Een stateful regel is op de hoogte van de verbindingen die het passeren. Voor bestaande verbindingen wordt een stroomrecord gemaakt. Communicatie wordt toegestaan of geweigerd op basis van de verbindingsstatus van de stroomrecord.  Gebruik dit regel type voor open bare IP-adressen voor het filteren van verkeer van het internet.

### <a name="default-rules"></a>Standaardregels

In elke firewall tabel worden de volgende standaard regels gemaakt.

|Prioriteit|Name|Status bijhouden|Richting|Verkeers type|Protocol|Bron|Bronpoort|Doel|Doelpoort|Bewerking|
|--------|----|--------------|---------|------------|--------|------|-----------|-----------|----------------|------|
|65000|alles-naar-Internet toestaan|Stateful|Uitgaand|Openbaar IP-of Internet verkeer|Alle|Alle|Alle|Alle|Alle|Toestaan|
|65001|weigeren: alle van Internet|Stateful|Inkomend|Openbaar IP-of Internet verkeer|Alle|Alle|Alle|Alle|Alle|Weigeren|
|65002|alles-naar-intranet toestaan|Stateless|Uitgaand|Intern of VPN-verkeer in de privécloud|Alle|Alle|Alle|Alle|Alle|Toestaan|
|65003|toestaan-alles-uit-intranet|Stateless|Inkomend|Intern of VPN-verkeer in de privécloud|Alle|Alle|Alle|Alle|Alle|Toestaan|

## <a name="next-steps"></a>Volgende stappen

* [Firewalltabellen en -regels instellen](firewall.md)
