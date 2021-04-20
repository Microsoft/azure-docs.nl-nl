---
title: Problemen met uitgaande verbindingen in Azure Load Balancer
description: Oplossingen voor veelvoorkomende problemen met uitgaande connectiviteit via de Azure Load Balancer.
services: load-balancer
author: erichrt
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 05/7/2020
ms.author: errobin
ms.openlocfilehash: 1f52900086afef09d69b80bf44474d5514e25235
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748876"
---
# <a name="troubleshooting-outbound-connections-failures"></a><a name="obconnecttsg"></a> Problemen met uitgaande verbindingen oplossen

Dit artikel is bedoeld om oplossingen te bieden voor veelvoorkomende problemen die zich kunnen voordoen met uitgaande verbindingen van een Azure Load Balancer. De meeste problemen met uitgaande connectiviteit die klanten ervaren, worden veroorzaakt door uitputting van de SNAT-poort en verbindings time-outs die leiden tot uitgevallen pakketten. Dit artikel bevat stappen voor het oplossen van al deze problemen.

## <a name="managing-snat-pat-port-exhaustion"></a><a name="snatexhaust"></a> SNAT-poortuitputting (PAT) beheren
[Kortstondige poorten die](load-balancer-outbound-connections.md) worden gebruikt voor [PAT](load-balancer-outbound-connections.md) zijn een uitputtingsresource, zoals beschreven in Zelfstandige VM zonder openbaar [IP-adres](load-balancer-outbound-connections.md) en [VM met load-balanced zonder openbaar IP-adres.](load-balancer-outbound-connections.md) U kunt uw gebruik van kortstondige poorten bewaken en deze vergelijken met uw huidige toewijzing om het risico op SNAT-uitputting te bepalen of te bevestigen met behulp van [deze](./load-balancer-standard-diagnostics.md#how-do-i-check-my-snat-port-usage-and-allocation) handleiding.

Als u weet dat u veel uitgaande TCP- of UDP-verbindingen met hetzelfde doel-IP-adres en dezelfde poort start en u merkt dat uitgaande verbindingen mislukken of u wordt geadviseerd door ondersteuning dat u SNAT-poorten (vooraf toegewezen kortstondige poorten die worden gebruikt door [PAT)](load-balancer-outbound-connections.md)uitputt, hebt u verschillende algemene oplossingsopties. [](load-balancer-outbound-connections.md#preallocatedports) Bekijk deze opties en bepaal wat er beschikbaar en het beste is voor uw scenario. Het is mogelijk dat een of meer kunnen helpen bij het beheren van dit scenario.

Als u problemen hebt met het gedrag van uitgaande verbindingen, kunt u IP-stackstatistieken (netstat) gebruiken. Het kan ook handig zijn om verbindingsgedrag te observeren met behulp van pakketopnamen. U kunt deze pakketopnamen uitvoeren in het gast-besturingssysteem van uw exemplaar of Network Watcher [gebruiken voor pakketopname.](../network-watcher/network-watcher-packet-capture-manage-portal.md) 

## <a name="manually-allocate-snat-ports-to-maximize-snat-ports-per-vm"></a><a name ="manualsnat"></a>Handmatig SNAT-poorten toewijzen om SNAT-poorten per VM te maximaliseren
Zoals gedefinieerd in [vooraf toegewezen poorten,](load-balancer-outbound-connections.md#preallocatedports)wijst load balancer automatisch poorten toe op basis van het aantal VM's in de back-end. Dit wordt standaard voorzichtig gedaan om schaalbaarheid te garanderen. Als u het maximum aantal VM's in de back-end weet, kunt u handmatig SNAT-poorten toewijzen in elke uitgaande regel. Als u bijvoorbeeld weet dat u maximaal 10 VM's hebt, kunt u 6.400 SNAT-poorten per VM toewijzen in plaats van de standaardwaarde 1024. 

## <a name="modify-the-application-to-reuse-connections"></a><a name="connectionreuse"></a>De toepassing wijzigen om verbindingen opnieuw te gebruiken 
U kunt de vraag naar kortstondige poorten die voor SNAT worden gebruikt, verminderen door verbindingen in uw toepassing opnieuw te gebruiken. Hergebruik van verbindingen is vooral relevant voor protocollen zoals HTTP/1.1, waarbij hergebruik van verbindingen de standaardinstelling is. En andere protocollen die HTTP gebruiken als transportmiddel (bijvoorbeeld REST), kunnen daar op zijn beurt van profiteren. 

Hergebruik is altijd beter dan afzonderlijke atomische TCP-verbindingen voor elke aanvraag. Hergebruik resulteert in beter presterende, zeer efficiënte TCP-transacties.

## <a name="modify-the-application-to-use-connection-pooling"></a><a name="connection pooling"></a>De toepassing wijzigen voor het gebruik van verbindingsgroepering
U kunt een schema voor verbindingsgroepering in uw toepassing gebruiken, waarbij aanvragen intern worden verdeeld over een vaste set verbindingen (waar mogelijk opnieuw worden gebruikt). Dit schema beperkt het aantal kortstondige poorten dat in gebruik is en maakt een meer voorspelbare omgeving. Dit schema kan ook de doorvoer van aanvragen verhogen door meerdere gelijktijdige bewerkingen toe te staan wanneer één verbinding wordt geblokkeerd bij het beantwoorden van een bewerking.  

Verbindingsgroepering bestaat mogelijk al binnen het framework dat u gebruikt voor het ontwikkelen van uw toepassing of de configuratie-instellingen voor uw toepassing. U kunt verbindingspooling combineren met hergebruik van verbindingen. Uw meerdere aanvragen verbruiken vervolgens een vast, voorspelbaar aantal poorten naar hetzelfde DOEL-IP-adres en dezelfde poort. De aanvragen profiteren ook van efficiënt gebruik van TCP-transacties die de latentie en het resourcegebruik verminderen. UDP-transacties kunnen ook profiteren, omdat het beheren van het aantal UDP-stromen op zijn beurt uitputtingsomstandigheden kan voorkomen en het gebruik van de SNAT-poort kan beheren.

## <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a><a name="retry logic"></a>Wijzig de toepassing om minder agressieve logica voor opnieuw proberen te gebruiken
Wanneer [vooraf toegewezen](load-balancer-outbound-connections.md#preallocatedports) kortstondige poorten die worden gebruikt voor [PAT](load-balancer-outbound-connections.md) zijn uitgeput of toepassingsfouten optreden, agressieve of brute force nieuwe pogingen zonder verval en logica voor terugval veroorzaken uitputting of persistentie. U kunt de vraag naar kortstondige poorten verminderen met behulp van een minder agressieve logica voor opnieuw proberen. 

Kortstondige poorten hebben een time-out voor inactieve 4 minuten (niet aanpasbaar). Als de nieuwe aanvallen te agressief zijn, kan de uitputting niet zelf worden geweken. Daarom is het een essentieel onderdeel van het ontwerp om na te gaan hoe vaak de toepassing transacties opnieuw moet proberen uit te proberen.

## <a name="assign-a-public-ip-to-each-vm"></a><a name="assignilpip"></a>Een openbaar IP-adres toewijzen aan elke VM
Als u een openbaar IP-adres toewijst, wordt uw scenario gewijzigd [in Openbaar IP-adres in een VM.](load-balancer-outbound-connections.md) Alle kortstondige poorten van het openbare IP-adres die voor elke VM worden gebruikt, zijn beschikbaar voor de VM. (In tegenstelling tot scenario's waarin kortstondige poorten van een openbaar IP-adres worden gedeeld met alle VM's die zijn gekoppeld aan de respectieve back-endpool.) Er zijn afwegingen waar u rekening mee moet houden, zoals de extra kosten van openbare IP-adressen en de mogelijke impact van het filteren van een groot aantal afzonderlijke IP-adressen.

>[!NOTE] 
>Deze optie is niet beschikbaar voor webwerkrollen.

## <a name="use-multiple-frontends"></a><a name="multifesnat"></a>Meerdere frontends gebruiken
Wanneer u openbare Standard Load Balancer, wijst u meerdere [front-end-IP-adressen](load-balancer-outbound-connections.md) toe voor uitgaande verbindingen en vermenigvuldigt u het aantal [beschikbare SNAT-poorten.](load-balancer-outbound-connections.md#preallocatedports)  Maak een front-end-IP-configuratie, -regel en -back-en -pool om het programmeren van SNAT te activeren op het openbare IP-adres van de front-end.  De regel hoeft niet te werken en een statustest hoeft niet te slagen.  Als u ook meerdere frontends gebruikt voor inkomende gegevens (in plaats van alleen voor uitgaand verkeer), moet u aangepaste statustests gebruiken om betrouwbaarheid te garanderen.

>[!NOTE]
>In de meeste gevallen is uitputting van SNAT-poorten een teken van een slecht ontwerp.  Zorg ervoor dat u begrijpt waarom u poorten uitput voordat u meer front-enden gebruikt om SNAT-poorten toe te voegen.  Mogelijk maskert u een probleem dat later tot fouten kan leiden.

## <a name="scale-out"></a><a name="scaleout"></a>Uitschalen
[Vooraf toegewezen poorten](load-balancer-outbound-connections.md#preallocatedports) worden toegewezen op basis van de grootte van de back-endpool en gegroepeerd in lagen om onderbrekingen te minimaliseren wanneer sommige poorten opnieuw moeten worden toegewezen om ruimte te bieden aan de volgende grotere back-endpoollaag.  Mogelijk hebt u een optie om het SNAT-poortgebruik voor een bepaalde front-end te verhogen door uw back-endpool te schalen naar de maximale grootte voor een bepaalde laag.  Houd er rekening mee dat de standaardpoorttoewijzing vereist is om de toepassing efficiënt uit te schalen zonder risico op SNAT-uitputting.

Twee virtuele machines in de back-endpool hebben bijvoorbeeld 1024 SNAT-poorten per IP-configuratie, waardoor er in totaal 2048 SNAT-poorten beschikbaar zijn voor de implementatie.  Als de implementatie zou worden verhoogd tot 50 virtuele machines, hoewel het aantal vooraf toegewezen poorten constant per virtuele machine blijft, kunnen er in totaal 51.200 (50 x 1024) SNAT-poorten worden gebruikt door de implementatie.  Als u uw implementatie wilt uitschalen, controleert u het aantal vooraf toegewezen poorten [per](load-balancer-outbound-connections.md#preallocatedports) laag om ervoor te zorgen dat u de uitschaal naar het maximum voor de respectieve laag vormt.  Als u in het vorige voorbeeld had gekozen om uit te schalen naar 51 in plaats van 50 exemplaren, zou u naar de volgende laag gaan en uiteindelijk minder SNAT-poorten per VM en in totaal hebben.

Als u uitschaalt naar de eerstvolgende grotere back-endpoollaag, is er een mogelijke time-out voor sommige uitgaande verbindingen als de toegewezen poorten opnieuw moeten worden toegewezen.  Als u slechts een deel van uw SNAT-poorten gebruikt, is uitschalen over de volgende grotere back-endpool niet-opeenvolgende.  De helft van de bestaande poorten wordt elke keer dat u naar de volgende back-endpoollaag gaat, opnieuw toegewezen.  Als u niet wilt dat dit wordt gedaan, moet u uw implementatie vormgeven naar de laaggrootte.  Of zorg ervoor dat uw toepassing zo nodig kan detecteren en het opnieuw kan proberen.  TCP-keepalives kunnen helpen bij het detecteren wanneer SNAT-poorten niet meer werken vanwege een nieuwe toewijzing.

## <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a><a name="idletimeout"></a>Keepalives gebruiken om de time-out voor inactieve uitgaande gegevens opnieuw in te stellen
Uitgaande verbindingen hebben een time-out voor inactieve verbindingen van 4 minuten. Deze time-out is aanpasbaar via [uitgaande regels.](outbound-rules.md) U kunt ook transport (bijvoorbeeld TCP-keepalives) of keepalives op de toepassingslaag gebruiken om een niet-actieve stroom te vernieuwen en deze time-out voor inactieve gegevens indien nodig opnieuw in te stellen.  

Wanneer u TCP-keepalives gebruikt, is het voldoende om ze aan één zijde van de verbinding in te stellen. Het is bijvoorbeeld voldoende om ze alleen in te stellen aan de serverzijde om de niet-actieve timer van de stroom opnieuw in te stellen en het is niet nodig dat beide zijden TCP-keepalives starten.  Er bestaan vergelijkbare concepten voor de toepassingslaag, waaronder client-serverconfiguraties voor databases.  Controleer aan de serverzijde welke opties er bestaan voor toepassingsspecifieke keepalives.

## <a name="next-steps"></a>Volgende stappen
We willen altijd de ervaring van onze klanten verbeteren. Als u problemen ondervindt met uitgaande connectiviteit die niet worden vermeld of opgelost in dit artikel, kunt u feedback verzenden via GitHub onder aan deze pagina. Wij zullen uw feedback zo snel mogelijk bespreken.
