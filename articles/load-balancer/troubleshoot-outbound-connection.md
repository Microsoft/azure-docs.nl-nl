---
title: Problemen met uitgaande verbindingen in Azure Load Balancer oplossen
description: Oplossingen voor veelvoorkomende problemen met uitgaande verbindingen via de Azure Load Balancer.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 05/7/2020
ms.author: errobin
ms.openlocfilehash: 516576f4e005cc9fe2303945ecb1a13489908a5d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94696350"
---
# <a name="troubleshooting-outbound-connections-failures"></a><a name="obconnecttsg"></a> Problemen met mislukte uitgaande verbindingen oplossen

Dit artikel is bedoeld voor het bieden van oplossingen voor veelvoorkomende problemen die kunnen optreden bij uitgaande verbindingen van een Azure Load Balancer. De meeste problemen met een uitgaande verbinding die klanten ervaren, worden veroorzaakt door de verval tijd van de SNAT-poort en verbindingstime-outs voor verwijderde pakketten. In dit artikel worden de stappen beschreven voor het beperken van elk van deze problemen.

## <a name="managing-snat-pat-port-exhaustion"></a><a name="snatexhaust"></a> Poort uitputting van SNAT (PAT) beheren
[Tijdelijke poorten](load-balancer-outbound-connections.md) die worden gebruikt voor [Pat](load-balancer-outbound-connections.md) zijn een exhaustible-resource, zoals beschreven in [zelfstandige virtuele machine zonder openbaar IP-adres](load-balancer-outbound-connections.md) en [VM met gelijke taak verdeling zonder openbaar IP-adres](load-balancer-outbound-connections.md). Met [deze](./load-balancer-standard-diagnostics.md#how-do-i-check-my-snat-port-usage-and-allocation) hand leiding kunt u uw gebruik van tijdelijke poorten bewaken en vergelijken met uw huidige toewijzing om het risico van of te bevestigen dat de SNAT-uitputting wordt bevestigd.

Als u weet dat u een groot aantal uitgaande TCP-of UDP-verbindingen met hetzelfde doel-IP-adres en dezelfde poort wilt initiëren, kunt u zien dat uitgaande verbindingen mislukken of worden aanbevolen door ondersteuning te bieden voor de SNAT-poorten (vooraf toegewezen [tijdelijke poorten](load-balancer-outbound-connections.md#preallocatedports) die worden gebruikt door [Pat](load-balancer-outbound-connections.md)), hebt u verschillende algemene opties voor risico beperking. Bekijk deze opties en beslis wat er beschikbaar en beste is voor uw scenario. Het is mogelijk dat een of meer informatie kan helpen bij het beheren van dit scenario.

Als u problemen ondervindt met het gedrag van uitgaande verbindingen, kunt u IP-stack statistieken (netstat) gebruiken. Het kan ook handig zijn om verbindings gedrag te observeren met behulp van pakket opnamen. U kunt deze pakket opnames uitvoeren in het gast besturingssysteem van uw exemplaar of gebruikmaken van [Network Watcher voor pakket opname](../network-watcher/network-watcher-packet-capture-manage-portal.md). 

## <a name="manually-allocate-snat-ports-to-maximize-snat-ports-per-vm"></a><a name ="manualsnat"></a>Hand matig SNAT-poorten toewijzen om de SNAT-poorten per VM te maximaliseren
Zoals gedefinieerd in [vooraf toegewezen poorten](load-balancer-outbound-connections.md#preallocatedports), wijst de Load Balancer automatisch poorten toe op basis van het aantal virtuele machines in de back-end. Standaard wordt dit zo conservatief gedaan om te zorgen voor schaal baarheid. Als u het maximum aantal Vm's weet dat u in de back-end zult hebben, kunt u de SNAT-poorten hand matig toewijzen in elke uitgaande regel. Als u bijvoorbeeld weet dat u een maximum van 10 virtuele machines hebt, kunt u in plaats van de standaard 1.024 6.400 SNAT-poorten per VM toewijzen. 

## <a name="modify-the-application-to-reuse-connections"></a><a name="connectionreuse"></a>Wijzig de toepassing om verbindingen opnieuw te gebruiken 
U kunt de vraag beperken voor tijdelijke poorten die worden gebruikt voor SNAT door verbindingen in uw toepassing opnieuw te gebruiken. Hergebruik van verbinding is vooral relevant voor protocollen zoals HTTP/1.1, waarbij hergebruik van verbindingen de standaard instelling is. En andere protocollen die gebruikmaken van HTTP als Trans Port (bijvoorbeeld REST), kunnen op hun beurt profiteren van. 

Hergebruik is altijd beter dan afzonderlijke, atomische TCP-verbindingen voor elke aanvraag. Het opnieuw gebruiken van resultaten in meer uitvoerende, zeer efficiënte TCP-trans acties.

## <a name="modify-the-application-to-use-connection-pooling"></a><a name="connection pooling"></a>De toepassing wijzigen voor het gebruik van groepsgewijze verbindingen
U kunt een schema voor groepsgewijze verbindingen gebruiken in uw toepassing, waarbij aanvragen intern worden gedistribueerd via een vaste set verbindingen (waarbij elk gebruik waar mogelijk wordt gebruikt). Dit schema beperkt het aantal tijdelijke poorten dat in gebruik is en maakt een meer voorspel bare omgeving. Dit schema kan ook de door Voer van aanvragen verhogen door meerdere gelijktijdige bewerkingen toe te staan wanneer één verbinding wordt geblokkeerd bij het beantwoorden van een bewerking.  

Verbindings groeperingen kunnen al bestaan in het Framework dat u gebruikt voor het ontwikkelen van uw toepassing of de configuratie-instellingen voor uw toepassing. U kunt groepsgewijze verbindingen combi neren met het opnieuw gebruiken van verbindingen. Uw meerdere aanvragen verbruiken vervolgens een vast, voorspelbaar aantal poorten op hetzelfde doel-IP-adres en dezelfde poort. De aanvragen profiteren ook van efficiënt gebruik van TCP-trans acties waarbij de latentie en het resource gebruik worden verminderd. UDP-trans acties kunnen ook profiteren van het voor deel, omdat het beheer van het aantal UDP-stromen mogelijk is om de uitlaat omstandigheden te voor komen en het SNAT-poort gebruik te beheren.

## <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a><a name="retry logic"></a>De toepassing wijzigen voor het gebruik van minder agressieve nieuwe logica
Wanneer [tijdelijke poorten](load-balancer-outbound-connections.md#preallocatedports) die worden gebruikt voor [Pat](load-balancer-outbound-connections.md) , worden uitgeput of er fouten in de toepassing optreden, worden pogingen voor agressieve of brute Forces zonder onverwachting en uitstel Logic uitputting veroorzaakt of blijven ze behouden. U kunt de vraag naar tijdelijke poorten verminderen door een minder agressieve pogings logica te gebruiken. 

Tijdelijke poorten hebben een time-out van 4 minuten (niet aanpasbaar). Als de nieuwe pogingen te agressief zijn, heeft de uitputting geen mogelijkheid om zichzelf zelf te wissen. Daarom is het een belang rijk onderdeel van het ontwerp om te overwegen hoe--en hoe vaak uw toepassingen nieuwe pogingen doen.

## <a name="assign-a-public-ip-to-each-vm"></a><a name="assignilpip"></a>Een openbaar IP-adres toewijzen aan elke VM
Als u een openbaar IP-adres toewijst, wordt uw scenario gewijzigd in [een open bare IP voor een VM](load-balancer-outbound-connections.md). Alle tijdelijke poorten van het open bare IP-adres dat voor elke VM worden gebruikt, zijn beschikbaar voor de VM. (In tegens telling tot scenario's waarbij tijdelijke poorten van een openbaar IP-adres worden gedeeld met alle virtuele machines die zijn gekoppeld aan de bijbehorende back-end-groep) Er zijn zaken die u moet overwegen, zoals de extra kosten van open bare IP-adressen en de mogelijke gevolgen van het filteren van een groot aantal afzonderlijke IP-adressen.

>[!NOTE] 
>Deze optie is niet beschikbaar voor Web Worker-rollen.

## <a name="use-multiple-frontends"></a><a name="multifesnat"></a>Meerdere frontends gebruiken
Wanneer u open bare Standard Load Balancer gebruikt, wijst u [meerdere frontend-IP-adressen toe voor uitgaande verbindingen](load-balancer-outbound-connections.md) en [vermenigvuldigt u het aantal beschik bare SNAT-poorten](load-balancer-outbound-connections.md#preallocatedports).  Een front-end-IP-configuratie, regel en back-end-pool maken om de programmering van SNAT aan het open bare IP-adres van de frontend te activeren.  De regel hoeft niet te functioneren en een status test hoeft niet te slagen.  Als u meerdere front-ends gebruikt voor inkomend verkeer (in plaats van alleen voor uitgaand verkeer), moet u aangepaste status tests gebruiken om de betrouw baarheid te garanderen.

>[!NOTE]
>In de meeste gevallen is de uitputting van de SNAT-poorten een teken van een slecht ontwerp.  Zorg ervoor dat u weet waarom u poorten uitgeput voordat u meer frontends gebruikt om SNAT-poorten toe te voegen.  Het kan zijn dat u een probleem maskeert dat later kan optreden.

## <a name="scale-out"></a><a name="scaleout"></a>Uitschalen
Vooraf [toegewezen poorten](load-balancer-outbound-connections.md#preallocatedports) worden toegewezen op basis van de grootte van de back-endserver en gegroepeerd in lagen om onderbrekingen te minimaliseren wanneer een aantal poorten opnieuw moet worden toegewezen om de eerstvolgende grotere laag van de back-endadresgroep te maken.  Mogelijk hebt u een optie om het SNAT-poort gebruik voor een bepaalde frontend te verhogen door de back-endadresgroep te schalen naar de maximale grootte voor een bepaalde laag.  Houd er rekening mee dat de toewijzing van de standaard poort is vereist voor het efficiënt schalen van de toepassing zonder dat er risico SNAT ontstaat.

Twee virtuele machines in de back-end-pool zouden bijvoorbeeld 1024 SNAT-poorten beschikbaar hebben per IP-configuratie, waardoor er een totaal van 2048 SNAT-poorten voor de implementatie mogelijk is.  Als de implementatie moet worden verhoogd tot 50 virtuele machines, hoewel het aantal vooraf toegewezen poorten constant per virtuele machine blijft, kunnen er in totaal 51.200 (50 x 1024) SNAT-poorten worden gebruikt door de implementatie.  Als u uw implementatie wilt uitbreiden, controleert u het aantal [vooraf toegewezen poorten](load-balancer-outbound-connections.md#preallocatedports) per laag om ervoor te zorgen dat u uw uitschalen afshapet naar het maximum voor de desbetreffende laag.  Als u in het vorige voor beeld hebt gekozen om uit te schalen naar 51 in plaats van met 50-instanties, gaat u naar de volgende laag en eindigt u met minder SNAT-poorten per VM en in totaal.

Als u uitbreidt naar de volgende grotere laag van de back-endadresgroep, is er mogelijk een time-out opgestaan voor sommige van uw uitgaande verbindingen als toegewezen poorten opnieuw moeten worden toegewezen.  Als u slechts een aantal SNAT-poorten gebruikt, kunt u uitschalen over de volgende grotere back-inconsequential.  De helft van de bestaande poorten wordt opnieuw toegewezen telkens wanneer u overstapt naar de volgende back-endadresgroep.  Als u dit niet wilt doen, moet u uw implementatie vorm geven in de laag grootte.  Of zorg ervoor dat uw toepassing zo nodig kan detecteren en probeer het opnieuw.  TCP-keepalives kunnen worden gebruikt om te detecteren wanneer de SNAT-poorten niet meer werken omdat ze opnieuw moeten worden toegewezen.

## <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a><a name="idletimeout"></a>Keepalives gebruiken om de uitgaande time-out voor inactiviteit opnieuw in te stellen
Uitgaande verbindingen hebben een time-out van 4 minuten. Deze time-out is aanpasbaar via [Uitgaande regels](outbound-rules.md). U kunt ook Trans Port (bijvoorbeeld TCP-keepalives) of keepalives op toepassings niveau gebruiken om een niet-actieve stroom te vernieuwen en deze time-out voor inactiviteit zo nodig opnieuw in te stellen.  

Bij het gebruik van TCP-keepalives is het voldoende om deze in te scha kelen aan één zijde van de verbinding. Het is bijvoorbeeld voldoende om ze alleen in te scha kelen op de server om de niet-actieve timer van de stroom opnieuw in te stellen en het is niet nodig voor beide zijden voor het starten van TCP-keepalives.  Er bestaan soort gelijke concepten voor Application Layer, waaronder client-server configuraties voor data bases.  Controleer de server zijde voor welke opties bestaan voor toepassingsspecifieke keepalives.

## <a name="next-steps"></a>Volgende stappen
We zijn altijd bezig met het verbeteren van de ervaring van onze klanten. Als u problemen ondervindt met uitgaande verbindingen die niet worden vermeld of opgelost door dit artikel, kunt u feedback via GitHub indienen via de onderkant van deze pagina. Wij zullen uw feedback zo snel mogelijk verhelpen.