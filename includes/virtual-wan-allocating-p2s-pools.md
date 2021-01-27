---
title: bestand opnemen
description: bestand opnemen
services: virtual-wan
author: wellee
ms.service: virtual-wan
ms.topic: include
ms.date: 01/12/2021
ms.author: wellee
ms.custom: include file
ms.openlocfilehash: 67a996dbe1eb7090ea5f9ee9f0880698f4ba74a3
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919061"
---
In deze sectie worden de richt lijnen en vereisten beschreven voor het toewijzen van client adres ruimten waarbij de gekozen punt-naar-site-VPN Gateway schaal eenheid van de virtuele WAN-hub groter is dan of gelijk is aan 40.

### <a name="background"></a>Achtergrond

Punt-naar-site-VPN-gateways in de virtuele WAN-hub worden geïmplementeerd met meerdere exemplaren. Elk exemplaar van een punt-naar-site-VPN-gateway kan Maxi maal 10.000 gelijktijdige punt-naar-site-gebruikers verbindingen ondersteunen. Als gevolg hiervan moeten schaal eenheden groter zijn dan 40, moet er voor Virtual WAN extra capaciteit worden geïmplementeerd. hiervoor moet een minimum aantal adres groepen worden toegewezen voor verschillende schaal eenheden.

Als er bijvoorbeeld een schaal eenheid van 100 is gekozen, worden er 5 exemplaren geïmplementeerd voor de punt-naar-site-VPN Gateway in de virtuele hub. Deze implementatie kan 50.000 gelijktijdige verbindingen en **ten minste** vijf afzonderlijke adres groepen ondersteunen.

**Beschik bare schaal eenheden**

| Schaal eenheid | Maximum aantal ondersteunde clients | Minimum aantal adres groepen |
|--- |--- |--- |
| 40 | 20.000 | 2 |
| 60 | 30.000 | 3 |
| 80 | 40000 | 4 |
| 100 | 50000 | 5 |
| 120 | 60000 | 6 |
| 140 | 70000 | 7 |
| 160 | 80000 | 8 |
| 180 | 90000 | 9 |
| 200 | 100000 | 10 |

### <a name="specifying-address-pools"></a>Adres groepen opgeven

Hieronder vindt u enkele richt lijnen voor het kiezen van adres groepen. Houd er rekening mee dat de toewijzingen van een punt-naar-site-VPN-adres groep automatisch worden uitgevoerd door Virtual WAN.

1. Eén gateway-exemplaar staat Maxi maal 10.000 gelijktijdige verbindingen toe. Daarom moet elke adres groep ten minste 10.000 unieke RFC1918 IP-adressen bevatten.
1. Meerdere adresbereiken van een adres groep worden automatisch gecombineerd en toegewezen aan **één** gateway-exemplaar. Dit proces wordt uitgevoerd in een round-robin methode voor gateway exemplaren met minder dan 10.000 IP-adressen. Een groep met 5.000-adressen kan bijvoorbeeld automatisch worden gecombineerd door een virtueel WAN met een andere pool met 8.000-adressen en wordt toegewezen aan één gateway-exemplaar.
1. Er wordt slechts één adres groep toegewezen aan één gateway-exemplaar door Virtual WAN.
1. Adres groepen moeten verschillend zijn. Er mag geen overlap ping tussen adres groepen bestaan.

> [!NOTE] 
> Als een adres groep is gekoppeld aan een gateway-exemplaar dat onderhoud ondergaat, kan de adres groep niet opnieuw worden toegewezen aan een ander exemplaar.

### <a name="example"></a>Voorbeeld 

In het volgende voor beeld wordt een situatie beschreven waarbij 60 schaal eenheden tot 30.000 verbindingen ondersteunen, maar de toegewezen adres groepen resulteert in minder dan 30.000 gelijktijdige verbindingen.

Het totale aantal gelijktijdige verbindingen dat wordt ondersteund in deze installatie is 28.192. Het eerste gateway-exemplaar ondersteunt 10.000-adressen, de tweede instantie 8.192-verbindingen en de derde instantie ondersteunt ook 10.000 adressen.

| Nummer van adres groep | Adres groep | Ondersteunde verbindingen |
|--- |--- |--- |
| 1 | 10.12.0.0/15 | 10.000 |
| 2 | 10.13.0.0/19 | 8192 |
| 3 | 10.14.0.0/15 | 10.000|

**Aanbeveling #1: Zorg ervoor dat de adres groep #2 ten minste 10.000 afzonderlijke IP-adressen heeft. (voor beeld: 10.13.0.0/15)**

**Aanbeveling #2: Voeg nog één adres groep toe. (voor beeld: adres groep #4 10.15.0.0/21 met 2048 adressen). Adres groepen 2 en 4 worden automatisch gecombineerd en toestaan dat het gateway-exemplaar 10.000 gelijktijdige verbindingen ondersteunt.**
