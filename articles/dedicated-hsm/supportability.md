---
title: Ondersteunings-Azure-specifieke HSM | Microsoft Docs
description: Ondersteunings opties en verantwoordelijkheids gebieden voor specifieke HSM van Azure in verschillende scenario's
services: dedicated-hsm
author: johndaw
manager: rkarlin
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: seodec18
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: cd949bdb7c489478df6a16d6dccd0bf358637604
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606926"
---
# <a name="azure-dedicated-hsm-supportability"></a>Exclusieve HSM-ondersteuning van Azure

De speciale HSM-service van Azure biedt een fysiek apparaat voor exclusief gebruik door de klant met volledige administratieve beheer-en beheer verantwoordelijkheid. Het apparaat dat beschikbaar is gemaakt is een [Thales Luna 7 HSM model A790](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms). Micro soft heeft geen administratieve toegang meer nadat een klant, buiten de fysieke seriële poort van de bijlage, is ingericht als een bewakings functie.  Zonder toegang kan micro soft geen voortdurende onderhouds-en systeem beheer verantwoordelijkheden hebben. Als gevolg hiervan zijn klanten verantwoordelijk voor typische operationele activiteiten.
Klanten zijn volledig verantwoordelijk voor toepassingen die gebruikmaken van de Hsm's en moeten samen werken met Thales voor ondersteuning of advies. Vanwege de omvang van de eigendom van de operationele hygiëne is het niet mogelijk dat micro soft een gegarandeerde garantie voor hoge Beschik baarheid biedt voor deze service. Het is de verantwoordelijkheid van de klant om ervoor te zorgen dat hun toepassingen op de juiste wijze zijn geconfigureerd om hoge Beschik baarheid te garanderen. Micro soft controleert en beheert de status van het apparaat en de netwerk verbinding.

## <a name="getting-support"></a>Ondersteuning

Klanten ondersteuning voor toegewezen HSM is een gezamenlijke inspanning tussen micro soft en Thales. Eventuele hardwareproblemen of problemen met het netwerkpad worden door micro soft verholpen, en alles wat u kunt doen met de daad werkelijke HSM, zoals configuratie, software, firmware en toepassings ontwikkeling, wordt geadresseerd door Thales. Dit ondersteunings model zorgt voor een snelle route naar de meest efficiënte ondersteuning. Als u twijfelt met een bepaald probleem, verhoogt u een ondersteunings aanvraag met micro soft en zorgt u ervoor dat u op de juiste wijze omgaat. Micro soft blijft in de ondersteunings scenario's werken en streeft naar de beste ondersteunings ervaring voor onze klanten.

## <a name="thales-support"></a>Thales-ondersteuning

Klanten die gebruikmaken van de speciale HSM-service, komen in aanmerking voor ondersteuning van Thales conform hun plus ondersteunings plan. Hiervoor hebt u alleen een registratie proces nodig met behulp van de Thales-ondersteunings portal. Er wordt een klant-ID en instructies voor dit gegeven als onderdeel van de eerste betrokkenheid bij micro soft om toegang te krijgen tot de toegewezen HSM-service. Het mechanisme voor het verkrijgen van ondersteuning van Thales is via hun [Portal voor klant ondersteuning](https://supportportal.thalesgroup.com/csm).
Een belang rijk punt van de opmerking is dat Thales alle software en documentatie biedt die nodig zijn om de HSM (bijvoorbeeld Client Access software en Sdk's) te gebruiken via down load op de portal voor klant ondersteuning.

### <a name="software-components"></a>Software onderdelen

Diverse software onderdelen worden gebruikt in de configuratie van HSM-apparaten:

* Client software
* SDK
* Hulpprogramma's

### <a name="guidance"></a>Hulp

Thales maakt beschik bare beheer-en configuratie richtlijnen via de [Thales-portal voor klant ondersteuning](https://supportportal.thalesgroup.com/csm). Nadat u zich hebt aangemeld met een geldige klant-ID, zijn deze documenten beschikbaar voor downloaden. Thales biedt ook een reeks integratie handleidingen om klanten te helpen met verschillende scenario's en software-integraties. Zie de [Thales-partner site voor micro soft](https://cpl.thalesgroup.com/partners/overview)voor meer informatie.

### <a name="support"></a>Ondersteuning

Een probleem met het software niveau of vraag in verband met het gebruik van de Hsm's als onderdeel van de speciale HSM-service moet rechtstreeks worden geadresseerd aan Thales-ondersteuning. Alle software onderdelen die hierboven worden vermeld en een aangepaste HSM-configuratie die na inrichting is, worden verzorgd door Thales. Zie de Thales-portal voor [klanten ondersteuning](https://supportportal.thalesgroup.com/csm)voor meer informatie.

### <a name="consulting-services"></a>Adviesservices

Neem contact op met de vertegenwoordiger van uw Thales-account voor hulp bij het ontwerpen, ontwikkelen en implementeren van aangepaste toepassingen die gebruikmaken van de HSM.

## <a name="microsoft-support"></a>Microsoft Ondersteuning

Micro soft zorgt ervoor dat fysieke HSM-apparaten toegankelijk zijn via het netwerk en in een operationele status voor exclusief gebruik van één klant. Klanten zijn verantwoordelijk voor de configuratie, het beheer en het beheer van het apparaat. De verantwoordelijkheden van micro soft zijn:

* Controleren of het apparaat stroom en koeling heeft
* Het onderhouden van een operationele status van de HSM (bijvoorbeeld scenario's voor beëindigen/herstellen)
* Het apparaat is toegankelijk via het netwerk.

De volgende problemen moeten worden gerapporteerd aan micro soft:

* Onderdeel fouten
* Volledige apparaatfout
* Problemen met netwerk toegang
* Problemen met inrichten en ongedaan maken van de inrichting.

Micro soft heeft fysieke seriële poort toegang tot het apparaat via een bewakings functie (die een niet-administratieve rol is) die basis status telemetrie mogelijk maakt.  Hierdoor kan micro soft proactieve meldingen over problemen aan de klant aanbieden, tenzij de klant kiest om deze machtiging te beperken. 

### <a name="provisioning-and-decommissioning"></a>Inrichting en buiten gebruik stellen

Nadat een klant een goedgekeurde registratie heeft voor de toegewezen HSM-service, kunnen ze HSM-bronnen maken (momenteel via Power shell of de opdracht regel interface en niet de Azure Portal). De resource gaat via een toewijzings proces dat een fysiek apparaat in een opgegeven regio toewijst aan een vooraf gedefinieerd virtueel netwerk (VNet) van een klant. Nadat de klant op een VNet is weer gegeven, heeft deze toegang tot het apparaat en wordt het opnieuw geconfigureerd volgens de vereisten. Klanten hebben toegang tot hun specifieke Hsm's met Thales-client software en-hulpprogram ma's. Het proces voor het maken van resources wordt ondersteund door micro soft. Het aangepaste configuratie proces en daarbuiten worden ondersteund door Thales. (Zie Thales-ondersteuning hierboven). Wanneer een klant klaar is met het gebruik van een HSM, moet deze opnieuw worden ingesteld (of nul) om ervoor te zorgen dat er geen gegevens persistentie is. Het proces van het opnieuw instellen van het apparaat verwijdert alle aangepaste configuratie en gegevens. Micro soft wijst het apparaat toe en retourneert het naar de groep in een pristine-status. Dit betekent dat wanneer het apparaat wordt geretourneerd naar de groep, er geen bewijs is van de vorige klant activiteit. 

### <a name="hardware-issues"></a>Hardwareproblemen

Het HSM-apparaat heeft redundante en vervangbaar voedingen en ventilator eenheden.  Het verwijderen van een ventilator eenheid heeft echter nog steeds een onrecht matige gebeurtenis. Als er een fout optreedt in een onderdeel, gebruikt micro soft het meest geschikte proces om het probleem op onderdeel niveau op een manier te verhelpen, waardoor de beschik baarheid van de klanten service mini maal wordt onderbroken.
Een ernstige storing van het apparaat leidt ertoe dat het apparaat wordt vervangen door een nieuw apparaat uit de gratis groep. De klant bevat gewoon het nieuwe apparaat in het bestaande HA-paar voor het synchroniseren en keert u terug naar de volledige operationele status. Op het apparaat kan de gegevens apparaten worden verwijderd en vernietigd op de site in het Data Center. 

### <a name="networking-issues"></a>Netwerk problemen

Als klanten problemen met netwerk toegang tot het HSM-apparaat ondervinden, moeten ze contact opnemen met micro soft ondersteuning. Een eenvoudige test voor netwerk toegang is het gebruik van SSH om verbinding te maken met het HSM-apparaat. Als dit mislukt, neemt u contact op met micro soft ondersteuning.

## <a name="service-level-expectations-for-support"></a>Service Level verwachtingen voor ondersteuning

Raadpleeg het [ondersteunings plan voor Azure](https://azure.microsoft.com/support/plans/)voor micro soft Support service niveaus.
Raadpleeg voor Thales-ondersteunings service niveaus de [Thales-ondersteuning](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Volgende stappen

Het is raadzaam om de belangrijkste concepten, zoals hoge Beschik baarheid en beveiliging, goed te begrijpen voor het inrichten van apparaten en het ontwerpen of implementeren van toepassingen.

* [Implementatie architectuur](deployment-architecture.md)
* [Hoge beschikbaarheid](high-availability.md)
* [Fysieke beveiliging](physical-security.md)
* [Netwerken](networking.md)

