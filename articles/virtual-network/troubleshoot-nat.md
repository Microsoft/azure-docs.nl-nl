---
title: Problemen met Azure Virtual Network NAT-verbindingen oplossen
titleSuffix: Azure Virtual Network
description: Problemen met Virtual Network NAT oplossen.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to troubleshoot Virtual Network NAT.
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/20/2020
ms.author: allensu
ms.openlocfilehash: 70410e58acb30c7694e6fe4a6dcaff57bee98607
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98223428"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity"></a>Problemen met Azure Virtual Network NAT-verbindingen oplossen

Dit artikel biedt ondersteuning voor beheerders bij het vaststellen en oplossen van verbindingsproblemen bij het gebruik van Virtual Network NAT.

## <a name="problems"></a>Problemen

* [SNAT-uitputting](#snat-exhaustion)
* [ICMP-ping mislukt](#icmp-ping-is-failing)
* [Verbindingsfouten](#connectivity-failures)
* [Gelijktijdig gebruik van IPv6 en IPv4](#ipv6-coexistence)
* [De verbinding is niet afkomstig van een IP-adres of IP-adressen van een NAT-gateway](#connection-doesnt-originate-from-nat-gateway-ips)

Voer de stappen in de volgende sectie uit om deze problemen op te lossen.

## <a name="resolution"></a>Oplossing

### <a name="snat-exhaustion"></a>SNAT-uitputting

Eén [NAT-gatewayresource](nat-gateway-resource.md) ondersteunt tussen 64.000 en 1.000.000 gelijktijdige stromen.  Elk IP-adres levert 64.000 SNAT-poorten aan de beschikbare voorraad. U kunt maximaal 16 IP-adressen per NAT-gatewayresource gebruiken.  De SNAT-methode wordt [hier](nat-gateway-resource.md#source-network-address-translation) nader beschreven.

De belangrijkste oorzaak van SNAT-uitputting is vaak een antipatroon voor de manier waarop uitgaande verbindingen tot stand worden gebracht, worden beheerd of de standaardwaarden van configureerbare timers worden gewijzigd.  Lees deze sectie aandachtig.

#### <a name="steps"></a>Stappen

1. Controleer of u de standaardinstelling voor de time-out voor inactiviteit hebt gewijzigd in een waarde die hoger is dan 4 minuten.
2. Ga na hoe uw app uitgaande verbindingen maakt (bijvoorbeeld door het bekijken van de code of door pakketregistratie). 
3. Bepaal of deze activiteit verwacht gedrag vertoont of dat de app niet goed werkt.  Gebruik [metrische gegevens](nat-metrics.md) in Azure Monitor om uw bevindingen te onderbouwen. Gebruik de categorie Mislukt voor de metrische gegevens voor SNAT-verbindingen.
4. Evalueer of de juiste patronen worden gevolgd.
5. Evalueer of uitputting van de SNAT-poort moet worden opgelost met extra IP-adressen die aan de NAT-gatewayresource worden toegewezen.

#### <a name="design-patterns"></a>Ontwerppatronen

Gebruik waar mogelijk verbindingen opnieuw en maak gebruik van verbindingspools.  Deze patronen voorkomen dat resources worden uitgeput en leiden tot voorspelbaar gedrag. Primitieven voor deze patronen vindt u in veel ontwikkelbibliotheken en frameworks.

_**Oplossing:**_ Gebruik geschikte patronen en volg aanbevolen procedures

- NAT-gatewayresources hebben een standaardtime-out voor TCP-inactiviteit van 4 minuten.  Als deze instelling wordt gewijzigd in een hogere waarde, houdt NAT stromen langer vast en kan dit leiden tot [overbodige druk op de beschikbare SNAT-poorten](nat-gateway-resource.md#timers).
- Atomische aanvragen (één aanvraag per verbinding) zijn een slechte ontwerpkeuze. Een dergelijk antipatroon beperkt de schaal, verlaagt de prestaties en vermindert de betrouwbaarheid. Hergebruik in plaats daarvan HTTP/S-verbindingen om het aantal verbindingen en de bijbehorende SNAT-poorten te verminderen. De schaal van de app wordt vergroot en de prestaties worden verbeterd als gevolg van minder handshakes, overhead en lagere kosten voor cryptografische bewerkingen wanneer gebruik wordt gemaakt van TLS.
- DNS kan veel afzonderlijke grote stromen verwerken wanneer de client het resultaat van de DNS-omzetters niet in de cache opslaat. Gebruik cacheopslag.
- UDP-stromen (bijvoorbeeld DNS-zoekacties) wijzen SNAT-poorten toe voor de duur van de time-out voor inactiviteit. Hoe langer de time-out voor inactiviteit, hoe hoger de druk op de SNAT-poorten. Gebruik een korte duur voor de time-out voor inactiviteit (bijvoorbeeld 4 minuten).
- Gebruik verbindingspools om vorm te geven aan het volume van uw verbindingen.
- Verlaat nooit zo maar een TCP-stroom en maak gebruik van TCP-timers om de stroom op te schonen. Als u de verbinding niet expliciet door TCP laat sluiten, blijft de status op Toegewezen staan bij tussenliggende systemen en eindpunten en zijn er geen SNAT-poorten beschikbaar voor andere verbindingen. Dit patroon kan fouten in de app en SNAT-uitputting veroorzaken. 
- Wijzig timerwaarden voor het sluiten door TCP op besturingssysteemniveau alleen als u uitgebreide kennis hebt van de gevolgen hiervan. De TCP-stack wordt hersteld, maar de prestaties van uw app kunnen negatief worden beïnvloed wanneer de eindpunten van een verbinding niet-overeenkomende verwachtingen hebben. De wens om timers te wijzigen, is doorgaans een gevolg van een onderliggend ontwerpprobleem. Bestudeer de volgende aanbevelingen.

De SNAT-uitputting kan ook worden versterkt door andere antipatronen in de onderliggende app. Bekijk deze aanvullende patronen en aanbevolen procedures om de schaal en betrouwbaarheid van uw service te verbeteren.

- Ontdek de gevolgen van het verminderen van [TCP-time-out voor inactiviteit](nat-gateway-resource.md#timers) naar lagere waarden, waaronder de standaardtime-out voor inactiviteit van 4 minuten om eerder beschikbare SNAT-poorten vrij te maken.
- U kunt [asynchrone pollingpatronen](/azure/architecture/patterns/async-request-reply) voor langdurige bewerkingen overwegen om verbindingsresources voor andere bewerkingen vrij te maken.
- Stromen met een lange levensduur (bijvoorbeeld hergebruikte TCP-verbindingen) moeten gebruikmaken van TCP-keepalives of toepassingslaag-keepalives om een time-out voor tussenliggende systemen te voorkomen. De duur van de time-out voor inactiviteit verlengen, is een laatste redmiddel en lost de hoofdoorzaak mogelijk niet op. Een lange time-outduur kan fouten veroorzaken wanneer de time-out is verlopen en vertraging en overbodige storingen veroorzaken.
- Er moeten goede [patronen voor nieuwe pogingen](/azure/architecture/patterns/retry) worden gebruikt om agressieve nieuwe pogingen/bursts te voorkomen tijdens een tijdelijke storing of herstel na storingen.
Het maken van een nieuwe TCP-verbinding voor elke HTTP-bewerking (ook wel atomische verbindingen genoemd) is een antipatroon.  Atomische verbindingen voorkomen dat uw toepassing goed wordt geschaald en zorgen voor verspilling van resources.  Voeg altijd meerdere bewerkingen samen in een pijplijn in dezelfde verbinding.  Uw app zal er voordeel bij hebben qua transactiesnelheid en resourcekosten.  Als uw app gebruikmaakt van transportlaagversleuteling (bijvoorbeeld TLS), zijn er aanzienlijke kosten verbonden aan de verwerking van nieuwe verbindingen.  Bekijk [Azure-cloudontwerppatronen](/azure/architecture/patterns/) voor aanvullende aanbevolen patronen.

#### <a name="additional-possible-mitigations"></a>Andere mogelijke oplossingen

_**Oplossing:**_ U kunt uitgaande verbindingen als volgt schalen:

| Scenario | Bewijs |Oplossing |
|---|---|---|
| Er treden conflicten op voor SNAT-poorten en er is sprake van uitputting van SNAT-poorten tijdens perioden van veel gebruik. | In de categorie Mislukt voor de [metrische gegevens](nat-metrics.md) voor SNAT-verbindingen in Azure Monitor worden na verloop van tijd tijdelijke of permanente fouten en een grote hoeveelheid verbindingen vermeld.  | Bepaal of u extra openbare IP-adressen of openbare IP-voorvoegsels kunt toevoegen. In totaal kunnen maximaal 16 IP-adressen aan uw NAT-gateway worden toegevoegd. Met deze toevoeging beschikt u over meer beschikbare SNAT-poorten (64.000 per IP-adres) en kunt u uw scenario verder opschalen.|
| U hebt al zestien IP-adressen opgegeven en er is nog steeds sprake van uitputting van SNAT-poorten. | Er kunnen geen aanvullende IP-adressen worden toegevoegd. Het totale aantal IP-adressen van resources voor openbare IP-adressen of resources voor openbare IP-voorvoegsels overschrijdt het totaal van zestien. | Verdeel uw toepassingsomgeving over meerdere subnetten en geef een NAT-gatewayresource op voor elk subnet.  Evalueer uw ontwerppatronen opnieuw om deze te optimaliseren op basis van de voorafgaande [richtlijnen](#design-patterns). |

>[!NOTE]
>Het is belangrijk om te weten waarom SNAT-uitputting optreedt. Zorg ervoor dat u de juiste patronen voor schaalbare en betrouwbare scenario's gebruikt.  Het toevoegen van meer SNAT-poorten aan een scenario zonder dat de oorzaak van de vraag bekend is, is een laatste redmiddel. Als u niet weet waarom uw scenario een grote belasting vormt voor de beschikbare SNAT-poorten, zal het toevoegen van meer beschikbare SNAT-poorten door het toevoegen van meer IP-adressen dezelfde storing door uitputting alleen vertragen wanneer uw app wordt opgeschaald.  Het kan andere inefficiënties en antipatronen maskeren.

### <a name="icmp-ping-is-failing"></a>ICMP-ping mislukt

[Virtual Network NAT](nat-overview.md) ondersteunt IPv4 UDP- en TCP-protocollen. ICMP wordt niet ondersteund en werkt niet.  

_**Oplossing:**_ Gebruik in plaats daarvan TCP-verbindingstests (bijvoorbeeld 'TCP-ping') en UDP-toepassingslaagtests om end-to-end-verbindingen te valideren.

De volgende tabel kan als uitgangspunt dienen, als u een hulpprogramma wilt kiezen voor het starten van tests.

| Besturingssysteem | Algemene TCP-verbindingstest | TCP-toepassingslaagtest | UDP |
|---|---|---|---|
| Linux | nc (algemene TCP-verbindingstest) | curl (TCP-toepassingslaagtest) | appspecifiek |
| Windows | [PsPing](/sysinternals/downloads/psping) | PowerShell [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) | appspecifiek |

### <a name="connectivity-failures"></a>Verbindingsfouten

Verbindingsproblemen met [Virtual Network NAT](nat-overview.md) kunnen verschillende oorzaken hebben:

* permanente fouten als gevolg van configuratiefouten.
* tijdelijke of permanente [SNAT-uitputting](#snat-exhaustion) van de NAT-gateway,
* tijdelijke fouten in de Azure-infrastructuur, 
* tijdelijke fouten in het pad tussen Azure en de openbare internetbestemming, 
* tijdelijke of permanente fouten op de openbare internetbestemming.

Gebruik de volgende hulpprogramma's voor het valideren van de verbinding. [ICMP-ping wordt niet ondersteund](#icmp-ping-is-failing).

| Besturingssysteem | Algemene TCP-verbindingstest | TCP-toepassingslaagtest | UDP |
|---|---|---|---|
| Linux | nc (algemene TCP-verbindingstest) | curl (TCP-toepassingslaagtest) | appspecifiek |
| Windows | [PsPing](/sysinternals/downloads/psping) | PowerShell [Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) | appspecifiek |

#### <a name="configuration"></a>Configuratie

Controleer uw configuratie:
1. Heeft de NAT gateway-resource ten minste één resource met openbaar IP of openbaar IP-voorvoegsel? Er moet ten minste één IP-adres zijn gekoppeld aan de NAT-gateway om uitgaande connectiviteit mogelijk te maken.
2. Is het subnet van het virtuele netwerk zo geconfigureerd dat de NAT-gateway wordt gebruikt?
3. Gebruikt u UDR (door de gebruiker gedefinieerde route) en overschrijft u de bestemming?  NAT-gatewayresources worden de standaardroute (0/0) in geconfigureerde subnetten.

#### <a name="snat-exhaustion"></a>SNAT-uitputting

Raadpleeg de sectie over [SNAT-uitputting](#snat-exhaustion) in dit artikel.

#### <a name="azure-infrastructure"></a>Azure-infrastructuur

Azure controleert en gebruikt de infrastructuur met grote zorg. Tijdelijke fouten kunnen plaatsvinden en er is geen garantie dat er bij de verzending helemaal geen gegevens verloren gaan.  Gebruik ontwerppatronen waarbij SYN-herverzendingen voor TCP-apps worden toegestaan. Gebruik verbindingstime-outs die lang genoeg zijn zodat TCP SYN-herverzendingen tijdelijke gevolgen kunnen beperken die worden veroorzaakt door een verloren SYN-pakket.

_**Oplossing:**_

* Controleer op [SNAT-uitputting](#snat-exhaustion).
* De configuratieparameter in een TCP-stack waarmee het gedrag van SYN-herverzendingen wordt beheerd, wordt RTO ([Retransmission Time-Out of time-out voor het opnieuw verzenden van gegevens](https://tools.ietf.org/html/rfc793)) genoemd. De RTO-waarde is aanpasbaar maar doorgaans standaard 1 seconde of langer met exponentieel uitstel.  Als de verbindingstime-out van uw app te kort is (bijvoorbeeld 1 seconde), treden er mogelijk sporadische verbindingstime-outs op.  Verhoog de waarde voor de verbindingstime-out van de app.
* Als er langere, onverwachte time-outs optreden bij standaardgedrag van apps, opent u een ondersteuningsaanvraag voor verdere probleemoplossing.

Het wordt niet aanbevolen om de waarde voor de time-out van de TCP-verbinding kunstmatig te verlagen of de RTO-parameter af te stemmen.

#### <a name="public-internet-transit"></a>Verzending van gegevens via openbaar internet

De kans op tijdelijke fouten neemt toe bij een langer pad naar de bestemming en meer tussenliggende systemen. Naar verwachting kunnen tijdelijke fouten in toenemende mate optreden via de [Azure-infrastructuur](#azure-infrastructure). 

Volg de richtlijnen die in de voorafgaande sectie over de [Azure-infrastructuur](#azure-infrastructure) worden beschreven.

#### <a name="internet-endpoint"></a>Interneteindpunt

De vorige secties zijn van toepassing, evenals het interneteindpunt waarmee communicatie tot stand wordt gebracht. Andere factoren die van invloed kunnen zijn op de totstandkoming van de verbinding zijn:

* verkeersbeheer aan bestemmingszijde, waaronder
- Beperking van de API-snelheid die is opgelegd door de bestemmingszijde
- Het tegengaan van grootschalige DDoS-aanvallen of transportlaagverkeersvorming
* firewall of andere onderdelen op de bestemming 

Doorgaans zijn pakketregistraties bij de bron en de bestemming (indien beschikbaar) vereist om te bepalen wat er gaande is.

_**Oplossing:**_

* Controleer op [SNAT-uitputting](#snat-exhaustion). 
* Valideer ter vergelijking de verbinding met een eindpunt in dezelfde regio of elders.  
* Als u tests maakt met grote volumes of hoge transactiesnelheid, bekijk dan of het aantal fouten afneemt als u de snelheid vermindert.
* Als het wijzigen van de snelheid invloed heeft op het aantal fouten, controleert u of limieten voor de API-snelheid of andere beperkingen aan de bestemmingszijde mogelijk zijn bereikt.
* Als u geen eenduidig antwoord vindt voor het probleem, opent u een ondersteuningsaanvraag voor verdere probleemoplossing.

#### <a name="tcp-resets-received"></a>Activering van het opnieuw instellen van TCP

De NAT-gateway activeert het opnieuw instellen van TCP op de bron-VM voor verkeer dat niet wordt herkend als verkeer dat wordt uitgevoerd.

Een mogelijke reden is dat er een time-out voor inactiviteit is opgetreden voor de TCP-verbinding.  U kunt de time-out voor inactiviteit instellen op 4 minuten tot 120 minuten.

Het opnieuw instellen van TCP wordt niet geactiveerd aan de openbare zijde van NAT-gatewayresources. Het opnieuw instellen van TCP aan de bestemmingszijde wordt geactiveerd door de bron-VM, niet de NAT-gatewayresource.

_**Oplossing:**_

* Bekijk aanbevelingen voor [ontwerppatronen](#design-patterns).  
* Open zo nodig een ondersteuningsaanvraag voor verdere probleemoplossing.

### <a name="ipv6-coexistence"></a>Gelijktijdig gebruik van IPv6 en IPv4

[Virtual Network NAT](nat-overview.md) ondersteunt IPv4 UDP- en TCP-protocollen en implementatie in een [subnet met een IPv6-voorvoegsel wordt niet ondersteund](nat-overview.md#limitations).

_**Oplossing:**_ Implementeer een NAT-gateway in een subnet zonder IPv6-voorvoegsel.

U kunt uw interesse voor aanvullende mogelijkheden aangeven via [UserVoice voor Virtual Network NAT](https://aka.ms/natuservoice).

### <a name="connection-doesnt-originate-from-nat-gateway-ips"></a>De verbinding is niet afkomstig van een IP-adres of IP-adressen van een NAT-gateway

U configureert een NAT-gateway, IP-adressen die moeten worden gebruikt en het subnet dat een NAT-gatewayresource moet gebruiken. Verbindingen van VM-instanties die al bestonden voordat de NAT-gateway werd geïmplementeerd, gebruiken echter geen IP-adres(sen).  Ze maken gebruik van IP-adressen die niet worden gebruikt met de NAT-gatewayresource.

_**Oplossing:**_

[Virtual Network NAT](nat-overview.md) vervangt de uitgaande verbinding voor het subnet waarvoor het is geconfigureerd. Wanneer u overstapt van standaard-SNAT of SNAT voor uitgaande verbindingen voor de load balancer naar het gebruik van NAT-gateways, zullen de nieuwe verbindingen direct gebruikmaken van de IP-adressen die zijn gekoppeld aan de NAT-gatewayresource.  Als een virtuele machine echter nog steeds een actieve verbinding heeft tijdens de overstap naar de NAT-gatewayresource, blijft de verbinding het oude IP-adres met SNAT gebruiken dat was toegewezen toen de verbinding tot stand werd gebracht.  Zorg ervoor dat u echt een nieuwe verbinding tot stand brengt en niet een verbinding opnieuw gebruikt die al bestond omdat het besturingssysteem of de browser de verbindingen in een verbindingspool in de cache had opgeslagen.  Als u bijvoorbeeld _curl_ in PowerShell gebruikt, moet u de parameter _-DisableKeepalive_ opgeven om een nieuwe verbinding af te dwingen.  Als u een browser gebruikt, kunnen verbindingen ook in een pool worden opgenomen.

Het is niet nodig om een virtuele machine opnieuw op te starten wanneer u een subnet voor een NAT-gatewayresource configureert.  Als een virtuele machine opnieuw wordt opgestart, wordt de verbindingsstatus echter gewist.  Wanneer de verbindingsstatus is gewist, gaan alle verbindingen de IP-adressen van de NAT-gatewayresource gebruiken.  Dit is echter een neveneffect van het opnieuw opstarten van de virtuele machine en niet een indicator dat de virtuele machine opnieuw moet worden opgestart.

Als u nog steeds problemen ondervindt, opent u een ondersteuningsaanvraag zodat het probleem nader kan worden onderzocht.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Virtual Network NAT](nat-overview.md)
* Meer informatie over [NAT-gatewayresource](nat-gateway-resource.md)
* Meer informatie over [metrische gegevens en waarschuwingen voor NAT-gatewayresources](nat-metrics.md).
* [Vertel ons in UserVoice wat we verder kunnen ontwikkelen voor Virtual Network NAT](https://aka.ms/natuservoice).