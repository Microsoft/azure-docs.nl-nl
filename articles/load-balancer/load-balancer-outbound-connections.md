---
title: Source Network Address Translation (SNAT) voor uitgaande verbindingen
titleSuffix: Azure Load Balancer
description: Meer informatie Azure Load Balancer wordt gebruikt voor uitgaande internetverbinding (SNAT).
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 3b92ef3ce195a2eee9bce53e08d977593a9f1fc2
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477703"
---
# <a name="using-source-network-address-translation-snat-for-outbound-connections"></a>Source Network Address Translation (SNAT) gebruiken voor uitgaande verbindingen

De front-en-vip's van een openbare Azure load balancer kunnen worden gebruikt om uitgaande connectiviteit met internet te bieden voor back-end-exemplaren. Deze configuratie maakt gebruik **van SNAT (Source Network Address Translation).** SNAT herschrijft het IP-adres van de back-end naar het openbare IP-adres van uw load balancer. 

SNAT maakt **ip-adressering van** het back-end-exemplaar mogelijk. Deze masquerading voorkomt dat externe bronnen een direct adres hebben voor de back-end-exemplaren. Een IP-adres dat wordt gedeeld tussen back-end-exemplaren vermindert de kosten van statische openbare IP-adressen. Een bekend IP-adres ondersteunt scenario's zoals het vereenvoudigen van ip-allowlist met verkeer van bekende openbare IP-adressen. 

>[!Note]
> Voor toepassingen waarvoor een groot aantal uitgaande verbindingen is vereist of zakelijke klanten waarvoor één set VAN DEP's moet worden gebruikt vanuit een bepaald virtueel netwerk, is [Virtual Network NAT](../virtual-network/nat-overview.md) de aanbevolen oplossing. De dynamische toewijzing maakt eenvoudige configuratie en het meest efficiënte gebruik van SNAT-poorten vanaf elk IP-adres mogelijk. Hiermee kunnen alle resources in het virtuele netwerk een set IP-adressen delen zonder dat ze een load balancer.

>[!Important]
> Zelfs zonder uitgaande SNAT geconfigureerd, zijn Azure-opslagaccounts binnen dezelfde regio nog steeds toegankelijk en hebben back-Microsoft-services nog steeds toegang tot Microsoft-services zoals Windows Updates.

>[!NOTE] 
>In dit artikel worden Azure Resource Manager implementaties beschreven. Bekijk [Uitgaande verbindingen (klassiek) voor](/previous-versions/azure/load-balancer/load-balancer-outbound-connections-classic) alle klassieke implementatiescenario's in Azure.

## <a name="sharing-frontend-ip-address-across-backend-resources"></a><a name ="snat"></a> Front-end-IP-adres delen tussen back-end-resources

Als de back-load balancer van een load balancer geen openbare IP-adressen (ILPIP) op exemplaarniveau hebben, brengen ze uitgaande connectiviteit tot stand via het front-end-IP-adres van de openbare load balancer. Poorten worden gebruikt voor het genereren van unieke id's die worden gebruikt voor het onderhouden van afzonderlijke stromen. Internet maakt gebruik van een vijf-tuple om dit onderscheid te maken.

De vijf tuples bestaan uit:

* Doel-IP
* Doelpoort
* Bron-IP
* Bronpoort en -protocol om dit onderscheid te maken.

Als een poort wordt gebruikt voor binnenkomende verbindingen, heeft deze een **listener** voor binnenkomende verbindingsaanvragen op die poort. Deze poort kan niet worden gebruikt voor uitgaande verbindingen. Als u een uitgaande verbinding tot stand wilt brengen, wordt een **kortstondige** poort gebruikt om de bestemming een poort te bieden waarmee een afzonderlijke verkeersstroom kan worden gecommuniceerd en onderhouden. Wanneer deze kortstondige poorten worden gebruikt voor SNAT, worden ze **SNAT-poorten genoemd** 

Elk IP-adres heeft per definitie 65.535 poorten. Elke poort kan worden gebruikt voor binnenkomende of uitgaande verbindingen voor TCP(Transmission Control Protocol) en UDP (User Datagram Protocol). 

Wanneer een openbaar IP-adres wordt toegevoegd als een front-end-IP-adres aan een load balancer, biedt Azure 64.000 poorten die in aanmerking komen voor SNAT.

>[!NOTE]
> Elke poort die wordt gebruikt voor een load balancing- of binnenkomende NAT-regel verbruikt een bereik van acht poorten van deze 64.000 poorten, waardoor het aantal poorten dat in aanmerking komt voor SNAT, wordt verkleind. Als een taakverdelings- of nat-regel zich in hetzelfde bereik van acht als een andere ligt, worden er geen extra poorten gebruikt. 

Via [uitgaande regels](./outbound-rules.md) en taakverdelingsregels kunnen deze SNAT-poorten worden gedistribueerd naar back-end-exemplaren, zodat ze de openbare IP's van de load balancer kunnen delen voor uitgaande verbindingen.

Wanneer [scenario 2 hieronder](#scenario2) is geconfigureerd, zal de host voor elk back-end-exemplaar SNAT-pakketten zijn die deel uitmaken van een uitgaande verbinding. 

Wanneer SNAT wordt uitgevoerd op een uitgaande verbinding van een back-end-exemplaar, herschrijft de host het bron-IP-adres naar een van de front-end-IP-adressen. 

Om unieke stromen te behouden, herschrijft de host de bronpoort van elk uitgaand pakket naar een SNAT-poort op het back-end-exemplaar.

## <a name="outbound-connection-behavior-for-different-scenarios"></a>Gedrag van uitgaande verbindingen voor verschillende scenario's
  * Virtuele machine met openbaar IP-adres.
  * Virtuele machine zonder openbaar IP-adres.
  * Virtuele machine zonder openbaar IP-adres en zonder load balancer.
        
 ### <a name="scenario-1-virtual-machine-with-public-ip-either-with-or-without-a-load-balancer"></a><a name="scenario1"></a> Scenario 1: Virtuele machine met een openbaar IP-adres met of zonder load balancer.

 | Verenigingen | Methode | IP-protocollen |
 | ---------- | ------ | ------------ |
 | Openbare load balancer of op zichzelf staan | [SNAT (Source Network Address Translation)](#snat) </br> wordt niet gebruikt. | TCP (Transmission Control Protocol) </br> UDP (User Datagram Protocol) </br> ICMP (Internet Control Message Protocol) </br> ESP (inkapselde nettolading van beveiliging) |

 #### <a name="description"></a>Beschrijving

 Al het verkeer keert terug naar de aanvragende client vanaf het openbare IP-adres van de virtuele machine (IP op exemplaarniveau).
 
 Azure gebruikt het openbare IP-adres dat is toegewezen aan de IP-configuratie van de NIC van het exemplaar voor alle uitgaande stromen. Het exemplaar heeft alle kortstondige poorten beschikbaar. Het maakt niet uit of de VM al dan niet is verdeeld over de belasting. Dit scenario heeft voorrang op de andere scenario's. 

 Een openbaar IP-adres dat is toegewezen aan een VM is een 1:1-relatie (in plaats van 1: veel) en geïmplementeerd als een staatloze 1:1 NAT.

 ### <a name="scenario-2-virtual-machine-without-public-ip-and-behind-standard-public-load-balancer"></a><a name="scenario2"></a>Scenario 2: Virtuele machine zonder openbaar IP-adres en achter standaard openbare Load Balancer

 | Verenigingen | Methode | IP-protocollen |
 | ------------ | ------ | ------------ |
 | Standaard openbare load balancer | Gebruik van load balancer front-end-VIP's voor [SNAT](#snat).| TCP </br> UDP |

 #### <a name="description"></a>Beschrijving

 De load balancer is geconfigureerd met een uitgaande regel of een taakverdelingsregel waarmee SNAT wordt mogelijk. Deze regel wordt gebruikt om een koppeling te maken tussen de openbare IP-front-end met de back-endpool. 

 Als u deze regelconfiguratie niet voltooit, is het gedrag zoals beschreven in scenario 3. 

 Een regel met een listener is niet vereist om de statustest te laten slagen.

 Wanneer een VM een uitgaande stroom maakt, vertaalt Azure het bron-IP-adres naar het openbare IP-adres van de front-load balancer openbare VM. Deze vertaling wordt uitgevoerd via [SNAT](#snat). 

 Kortstondige poorten van het load balancer openbare IP-adres van de front-load balancer worden gebruikt om afzonderlijke stromen te onderscheiden die afkomstig zijn van de VM. SNAT gebruikt dynamisch [vooraf toegewezen kortstondige poorten](#preallocatedports) wanneer uitgaande stromen worden gemaakt. 

 In deze context worden de kortstondige poorten die worden gebruikt voor SNAT SNAT-poorten genoemd. Het wordt ten zeerste aanbevolen om [een uitgaande regel](./outbound-rules.md) expliciet te configureren. Als u standaard-SNAT gebruikt via een taakverdelingsregel, worden SNAT-poorten vooraf toegewezen, zoals beschreven in de toewijzingstabel [standaard-SNAT-poorten.](#snatporttable)

> [!NOTE]
> **Azure Virtual Network NAT** kunt uitgaande connectiviteit bieden voor virtuele machines zonder dat er een load balancer. Zie [Wat is Azure Virtual Network NAT?](../virtual-network/nat-overview.md) voor meer informatie.

 ### <a name="scenario-3-virtual-machine-without-public-ip-and-behind-standard-internal-load-balancer"></a><a name="scenario3"></a>Scenario 3: Virtuele machine zonder openbaar IP-adres en achter standaard interne Load Balancer

 | Verenigingen | Methode | IP-protocollen |
 | ------------ | ------ | ------------ |
 | Standaard interne load balancer | Geen internetverbinding.| Geen |

 #### <a name="description"></a>Beschrijving
 
Wanneer u een standaard interne load balancer, is er geen gebruik van kortstondige IP-adressen voor SNAT. Deze functie ondersteunt standaard beveiliging. Deze functie zorgt ervoor dat alle IP-adressen die door resources worden gebruikt, kunnen worden geconfigureerd en kunnen worden gereserveerd. 

Als u uitgaande connectiviteit met internet wilt bereiken wanneer u een standaard interne load balancer, configureert u een openbaar IP-adres op exemplaarniveau om het gedrag in [scenario 1 te volgen.](#scenario1) 

Een andere optie is om de back-end-exemplaren toe te voegen aan een standaard openbare load balancer met een geconfigureerde uitgaande regel. De back-end-exemplaren worden toegevoegd aan een interne load balancer voor interne taakverdeling. Deze implementatie volgt het gedrag in [scenario 2.](#scenario2) 

> [!NOTE]
> **Azure Virtual Network NAT** kunt uitgaande connectiviteit bieden voor virtuele machines zonder dat er een load balancer. Zie [Wat is Azure Virtual Network NAT?](../virtual-network/nat-overview.md) voor meer informatie.

 ### <a name="scenario-4-virtual-machine-without-public-ip-and-behind-basic-load-balancer"></a><a name="scenario4"></a>Scenario 4: Virtuele machine zonder openbaar IP-adres en achter basic Load Balancer

 | Verenigingen | Methode | IP-protocollen |
 | ------------ | ------ | ------------ |
 |Geen </br> Basis load balancer | [SNAT met](#snat) dynamisch IP-adres op exemplaarniveau| TCP </br> UDP | 

 #### <a name="description"></a>Beschrijving

 Wanneer de VM een uitgaande stroom maakt, vertaalt Azure het bron-IP-adres naar een dynamisch opgegeven openbaar bron-IP-adres. Dit openbare **IP-adres kan niet worden geconfigureerd** en kan niet worden gereserveerd. Dit adres telt niet mee voor de openbare IP-resourcelimiet van het abonnement. 

Het openbare IP-adres wordt vrijgegeven en er wordt een nieuw openbaar IP-adres aangevraagd als u het volgende opnieuw hebt uitgevoerd: 

 * Virtuele machine
 * Beschikbaarheidsset
 * Schaalset voor virtuele machines 

 Gebruik dit scenario niet voor het toevoegen van DEP's aan een allowlist. Gebruik scenario 1 of 2 waarin u uitgaand gedrag expliciet declareert. [SNAT-poorten](#snat) zijn vooraf toegewezen, zoals beschreven in de toewijzingstabel [standaard-SNAT-poorten.](#snatporttable)

## <a name="exhausting-ports"></a><a name="scenarios"></a> Poorten uitputten

Elke verbinding met hetzelfde doel-IP-adres en dezelfde doelpoort gebruikt een SNAT-poort. Deze verbinding onderhoudt een afzonderlijke **verkeersstroom van** het back-end-exemplaar of **de client** naar een **server**. Dit proces geeft de server een afzonderlijke poort waarop het verkeer moet worden aangepakt. Zonder dit proces weet de clientmachine niet van welke stroom een pakket deel uitmaakt.

Stel dat er meerdere browsers naar https://www.microsoft.com gaan, dat wil zeggen:

* Doel-IP = 23.53.254.142
* Doelpoort = 443
* Protocol = TCP

Zonder verschillende doelpoorten voor het retourverkeer (de SNAT-poort die wordt gebruikt om de verbinding tot stand te brengen), kan de client het ene queryresultaat niet van de andere scheiden.

Uitgaande verbindingen kunnen bursts hebben. Aan een back-end-exemplaar kunnen onvoldoende poorten worden toegewezen. Als **de verbinding niet opnieuw** wordt gebruikt, is het risico op uitputting van de SNAT-poort groter. 

Nieuwe uitgaande verbindingen naar een doel-IP-adres mislukken wanneer poortuitputting optreedt. Verbindingen slagen wanneer een poort beschikbaar komt. Deze uitputting treedt op wanneer de 64.000 poorten van een IP-adres thin zijn verspreid over veel back-end-exemplaren. Zie de gids voor probleemoplossing voor hulp bij het beperken van [SNAT-poortuitputting.](./troubleshoot-outbound-connection.md)  

Voor TCP-verbindingen gebruikt de load balancer één SNAT-poort voor elk doel-IP en elke poort. Deze multi-use maakt meerdere verbindingen met hetzelfde doel-IP met dezelfde SNAT-poort mogelijk. Deze multi-use is beperkt als de verbinding niet naar verschillende doelpoorten is.

Voor UDP-verbindingen gebruikt de load balancer een nat-algoritme met beperkte poortverbinding, dat één SNAT-poort per **doel-IP** gebruikt, ongeacht de doelpoort. 

Een poort wordt opnieuw gebruikt voor een onbeperkt aantal verbindingen. De poort wordt alleen opnieuw gebruikt als het doel-IP-adres of de doelpoort anders is.

## <a name="default-port-allocation"></a><a name="preallocatedports"></a> Standaardpoorttoewijzing

Elk openbaar IP-adres dat is toegewezen als een front-end-IP van uw load balancer krijgt 64.000 SNAT-poorten voor de leden van de back-endpool. Poorten kunnen niet worden gedeeld met leden van de back-endpool. Een bereik van SNAT-poorten kan alleen worden gebruikt door één back-end-instantie om ervoor te zorgen dat retourpakketten correct worden gerouteerd. 

Het is raadzaam om een expliciete uitgaande regel te gebruiken om SNAT-poorttoewijzing te configureren. Met deze regel wordt het aantal SNAT-poorten dat beschikbaar is voor uitgaande verbindingen, gemaximaliseerd. 

Als u de automatische toewijzing van uitgaande SNAT via een taakverdelingsregel gebruikt, definieert de toewijzing van de poort in de toewijzingstabel.

In de volgende tabel ziet u de vooraf toegewezen <a name="snatporttable"></a> SNAT-poort voor lagen van back-endpoolgrootten:

| Poolgrootte (VM-exemplaren) | Vooraf toegewezen SNAT-poorten per IP-configuratie |
| --- | --- |
| 1-50 | 1\.024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1,000 | 32 | 

>[!NOTE]
> Als u een back-endpool hebt met een maximale grootte van 10, kan elk exemplaar 64.000/10 = 6400 poorten hebben als u een expliciete uitgaande regel definieert. Volgens de bovenstaande tabel heeft elke tabel slechts 1024 als u automatische toewijzing kiest.

## <a name="outbound-rules-and-virtual-network-nat"></a><a name="outboundrules"></a> Uitgaande regels en Virtual Network NAT

Azure Load Balancer uitgaande regels en Virtual Network NAT zijn opties beschikbaar voor uitgaand verkeer vanuit een virtueel netwerk.

Zie Regels voor uitgaand verkeer voor meer informatie [over uitgaande regels.](outbound-rules.md)

Zie Wat is Azure Virtual Network NAT voor [meer informatie over Azure Virtual Network NAT.](../virtual-network/nat-overview.md)

## <a name="constraints"></a>Beperkingen

*   Wanneer een verbinding niet actief is en er geen nieuwe pakketten worden verzonden, worden de poorten na 4 tot 120 minuten vrijgegeven.
  * Deze drempelwaarde kan worden geconfigureerd via uitgaande regels.
*   Elk IP-adres biedt 64.000 poorten die kunnen worden gebruikt voor SNAT.
*   Elke poort kan worden gebruikt voor zowel TCP- als UDP-verbindingen met een doel-IP-adres
  * Er is een UDP SNAT-poort nodig, ongeacht of de doelpoort uniek is of niet. Voor elke UDP-verbinding met een doel-IP wordt één UDP SNAT-poort gebruikt.
  * Een TCP SNAT-poort kan worden gebruikt voor meerdere verbindingen met hetzelfde doel-IP- mits de doelpoorten verschillend zijn.
*   SNAT-uitputting treedt op wanneer een back-end-exemplaar geen SNAT-poorten meer heeft. Een load balancer kan nog steeds ongebruikte SNAT-poorten hebben. Als de gebruikte SNAT-poorten van een back-exemplaar de opgegeven SNAT-poorten overschrijden, kan het geen nieuwe uitgaande verbindingen tot stand brengen.

## <a name="next-steps"></a>Volgende stappen

*   [Uitgaande verbindingsfouten vanwege SNAT-uitputting oplossen](./troubleshoot-outbound-connection.md)
*   [Controleer de metrische gegevens van SNAT](./load-balancer-standard-diagnostics.md#how-do-i-check-my-snat-port-usage-and-allocation) en lees hoe u ze op de juiste manier kunt filteren, splitsen en weergeven.
