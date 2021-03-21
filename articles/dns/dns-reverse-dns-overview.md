---
title: Overzicht van omgekeerde DNS in azure-Azure DNS
description: Aan de slag in dit leer traject leert u hoe omgekeerde DNS werkt en hoe dit kan worden gebruikt in azure
services: dns
documentationcenter: na
author: rohinkoul
manager: KumuD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: 8af9549efc3e8dab54f55dd404346d87201dee2c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94965609"
---
# <a name="overview-of-reverse-dns-and-support-in-azure"></a>Overzicht van omgekeerde DNS en ondersteuning in azure

Dit artikel bevat een overzicht van de werking van omgekeerde DNS en de omgekeerde DNS-scenario's die worden ondersteund in Azure.

## <a name="what-is-reverse-dns"></a>Wat is omgekeerde DNS?

Met conventionele DNS-records kan een toewijzing van een DNS-naam (zoals ' www.contoso.com ') worden toegewezen aan een IP-adres (zoals 64.4.6.100).  Omgekeerde DNS maakt de omzetting van een IP-adres (64.4.6.100) terug naar een naam (' www.contoso.com ').

Omgekeerde DNS-records worden gebruikt in verschillende situaties. Omgekeerde DNS-records worden bijvoorbeeld veel gebruikt voor het bestrijden van e-mail-spam door de afzender van een e-mail bericht te verifiëren.  De ontvangende e-mail server haalt de omgekeerde DNS-record van het IP-adres van de verzendende server op en controleert of deze host gemachtigd is om e-mail te verzenden vanaf het oorspronkelijke domein. 

## <a name="how-reverse-dns-works"></a>Hoe omgekeerde DNS werkt

Omgekeerde DNS-records worden gehost in speciale DNS-zones, ook wel ' ARPA-zones ' genoemd.  Deze zones vormen een afzonderlijke DNS-hiërarchie, parallel met de normale hiërarchie die als host fungeert voor domeinen als ' contoso.com '.

De DNS-record www.contoso.com bijvoorbeeld wordt geïmplementeerd met behulp van een DNS A-record met de naam ' www ' in de zone ' contoso.com '.  Deze record verwijst naar het bijbehorende IP-adres, in dit geval 64.4.6.100.  De reverse lookup wordt afzonderlijk geïmplementeerd, met behulp van een PTR-record met de naam ' 100 ' in de zone ' 6.4.64.in-addr. arpa ' (Houd er rekening mee dat IP-adressen worden omgekeerd in ARPA-zones.)  Deze PTR-record, als deze correct is geconfigureerd, verwijst naar de naam ' www.contoso.com '.

Wanneer aan een organisatie een IP-adres blok is toegewezen, krijgen ze ook het recht om de bijbehorende ARPA-zone te beheren. De ARPA-zones die overeenkomen met de IP-adres blokken die door Azure worden gebruikt, worden gehost en beheerd door micro soft. Uw Internet provider kan de ARPA-zone hosten voor uw eigen IP-adressen voor u, of u kunt de ARPA-zone hosten in een DNS-service van uw keuze, zoals Azure DNS.

> [!NOTE]
> Forward DNS-Zoek opdrachten en reverse DNS-Zoek opdrachten worden geïmplementeerd in afzonderlijke, parallelle DNS-hiërarchieën. De reverse lookup voor ' www.contoso.com ' wordt **niet** gehost in de zone ' contoso.com ', maar wordt gehost in de arpa-zone voor het bijbehorende IP-adres blok. Er worden afzonderlijke zones gebruikt voor IPv4-en IPv6-adres blokken.

### <a name="ipv4"></a>IPv4

De naam van een IPv4-zone voor reverse lookup moet de volgende indeling hebben: `<IPv4 network prefix in reverse order>.in-addr.arpa` .

Wanneer u bijvoorbeeld een reverse zone maakt voor het hosten van records voor hosts met IP-adressen die zich in het voor voegsel 192.0.2.0/24 bevinden, wordt de zone naam gemaakt door het netwerk voorvoegsel van het adres (192.0.2) te isoleren en vervolgens de order (2.0.192) om te zetten en het achtervoegsel toe te voegen `.in-addr.arpa` .

|Klasse subnet|Netwerk voorvoegsel  |Omgekeerd netwerk voorvoegsel  |Standaard achtervoegsel  |Zone naam omkeren |
|-------|----------------|------------|-----------------|---------------------------|
|Klasse A|203.0.0.0/8     | 203        | . in-addr. arpa   | `203.in-addr.arpa`        |
|Klasse B|198.51.0.0/16   | 51,198     | . in-addr. arpa   | `51.198.in-addr.arpa`     |
|Klasse C|192.0.2.0/24    | 2.0.192    | . in-addr. arpa   | `2.0.192.in-addr.arpa`    |

### <a name="classless-ipv4-delegation"></a>Klasseloze IPv4-overdracht

In sommige gevallen is het IP-adres bereik dat aan een organisatie is toegewezen, kleiner dan een Class C (/24)-bereik. In dit geval valt het IP-bereik niet op een zone grens binnen de `.in-addr.arpa` zone hiërarchie en kan daarom niet worden gedelegeerd als onderliggende zone.

In plaats daarvan wordt een ander mechanisme gebruikt voor het overdragen van de controle over afzonderlijke records voor reverse lookup (PTR) naar een toegewezen DNS-zone. Dit mechanisme delegeert een onderliggende zone voor elk IP-bereik en wijst vervolgens elk IP-adres in het bereik afzonderlijk toe aan die onderliggende zone met behulp van CNAME-records.

Stel bijvoorbeeld dat een organisatie het IP-bereik 192.0.2.128/26 aan de Internet provider heeft toegekend. Dit vertegenwoordigt 64 IP-adressen van 192.0.2.128 tot 192.0.2.191. Omgekeerde DNS voor dit bereik wordt als volgt geïmplementeerd:
- De organisatie maakt een zone voor reverse lookup met de naam 128-26.2.0.192.in-addr. arpa. Het voor voegsel ' 128-26 ' vertegenwoordigt het netwerk segment dat is toegewezen aan de organisatie binnen het klasse C (/24)-bereik.
- De provider maakt NS-records voor het instellen van de DNS-delegering voor de bovenstaande zone vanuit de bovenliggende zone van klasse C. Er worden ook CNAME-records gemaakt in de bovenliggende zone (klasse C) voor reverse lookup, waarbij elk IP-adres in het IP-bereik wordt toegewezen aan de nieuwe zone die is gemaakt door de organisatie:

```
$ORIGIN 2.0.192.in-addr.arpa
; Delegate child zone
128-26    NS       <name server 1 for 128-26.2.0.192.in-addr.arpa>
128-26    NS       <name server 2 for 128-26.2.0.192.in-addr.arpa>
; CNAME records for each IP address
129       CNAME    129.128-26.2.0.192.in-addr.arpa
130       CNAME    130.128-26.2.0.192.in-addr.arpa
131       CNAME    131.128-26.2.0.192.in-addr.arpa
; etc
```
- De organisatie beheert vervolgens de afzonderlijke PTR-records in hun onderliggende zone.

```
$ORIGIN 128-26.2.0.192.in-addr.arpa
; PTR records for each UIP address. Names match CNAME targets in parent zone
129      PTR    www.contoso.com
130      PTR    mail.contoso.com
131      PTR    partners.contoso.com
; etc
```
Een reverse lookup voor het IP-adres ' 192.0.2.129 ' query's voor een PTR-record met de naam ' 129.2.0.192.in-addr. arpa '. Met deze query wordt de CNAME in de bovenliggende zone omgezet in de PTR-record in de onderliggende zone.

### <a name="ipv6"></a>IPv6

De naam van een IPv6-zone voor reverse lookup moet de volgende vorm hebben: `<IPv6 network prefix in reverse order>.ip6.arpa`

Bijvoorbeeld. Bij het maken van een reverse zone voor het hosten van records voor hosts met IP-adressen in het voor voegsel 2001: db8:1000: abdc::/64, wordt de zone naam gemaakt door het netwerk voorvoegsel van het adres (2001: db8: abdc::)) te isoleren. Vouw vervolgens het voor voegsel van het IPv6-netwerk uit om [geen compressie van nul](/previous-versions/windows/it-pro/windows-server-2003/cc781672(v=ws.10))te verwijderen, als dit is gebruikt om het IPv6-adres voorvoegsel (2001:0db8: abdc: 0000::)) te verkorten. Keer de volg orde om met een punt als scheidings teken tussen elk hexadecimaal getal in het voor voegsel om het omgekeerde netwerk voorvoegsel ( `0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2` ) te maken en het achtervoegsel toe te voegen `.ip6.arpa` .


|Netwerk voorvoegsel  |Uitgevouwen en omgekeerd netwerk voorvoegsel |Standaard achtervoegsel |Zone naam omkeren  |
|---------|---------|---------|---------|
|2001: db8: abdc::/64    | 0.0.0.0. c. d. b. a. 8. b. d. 0.1.0.0.2        | . ip6. arpa        | `0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa`       |
|2001: db8:1000:9102::/64    | 2.0.1.9.0.0.0.1.8. b. d. 0.1.0.0.2        | . ip6. arpa        | `2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2.ip6.arpa`        |


## <a name="azure-support-for-reverse-dns"></a>Ondersteuning voor reverse DNS voor Azure

Azure ondersteunt twee afzonderlijke scenario's met betrekking tot omgekeerde DNS:

**Host de zone voor reverse lookup die overeenkomt met uw IP-adres blok.**
Azure DNS kunnen worden gebruikt voor [het hosten van uw zones voor reverse lookup en voor het beheren van de PTR-records voor elke achterwaartse DNS-zoek opdracht](dns-reverse-dns-hosting.md)voor zowel IPv4 als IPv6.  Het proces voor het maken van de zone voor reverse lookup (ARPA), het instellen van de overdracht en het configureren van PTR-records is hetzelfde als voor reguliere DNS-zones.  De enige verschillen zijn dat de overdracht moet worden geconfigureerd via uw Internet provider in plaats van met uw DNS-REGI ster en alleen het PTR-record type moet worden gebruikt.

**Configureer de omgekeerde DNS-record voor het IP-adres dat is toegewezen aan uw Azure-service.** Met Azure kunt u [de reverse lookup configureren voor de IP-adressen die zijn toegewezen aan uw Azure-service](dns-reverse-dns-for-azure-services.md).  Deze reverse lookup wordt door Azure geconfigureerd als een PTR-record in de overeenkomende ARPA-zone.  Deze ARPA-zones, die overeenkomen met alle IP-bereiken die worden gebruikt door Azure, worden gehost door micro soft

## <a name="next-steps"></a>Volgende stappen

Zie [Reverse DNS-zoek opdracht op Wikipedia](https://en.wikipedia.org/wiki/Reverse_DNS_lookup)voor meer informatie over omgekeerde DNS.
<br>
Informatie over [het hosten van de zone voor reverse lookup voor het IP-adres bereik dat is toegewezen aan uw Internet provider in azure DNS](dns-reverse-dns-for-azure-services.md).
<br>
Meer informatie over het [beheren van reverse DNS-records voor uw Azure-Services](dns-reverse-dns-for-azure-services.md).