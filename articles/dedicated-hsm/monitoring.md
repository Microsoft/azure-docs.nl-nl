---
title: Bewakings opties-in azure toegewezen HSM | Microsoft Docs
description: Overzicht van de specifieke HSM-bewakings opties en bewakings verantwoordelijkheden van Azure
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: mbaldwin
ms.openlocfilehash: 4c3d062d615208177fcb9c7df511f598613bf6a6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97092700"
---
# <a name="azure-dedicated-hsm-monitoring"></a>Specifieke HSM-bewaking voor Azure

De speciale HSM-service van Azure biedt een fysiek apparaat voor exclusief gebruik door de klant met volledige administratieve beheer-en beheer verantwoordelijkheid. Het apparaat dat beschikbaar is gemaakt is een [Thales Luna 7 HSM model A790](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms).  Micro soft heeft geen administratieve toegang meer nadat een klant, buiten de fysieke seriële poort van de bijlage, is ingericht als een bewakings functie. Als gevolg hiervan zijn klanten verantwoordelijk voor typische operationele activiteiten, waaronder uitgebreide bewakings-en logboek analyse.
Klanten zijn volledig verantwoordelijk voor toepassingen die gebruikmaken van de Hsm's en moeten samen werken met Thales voor ondersteuning of advies. Vanwege de omvang van de eigendom van de operationele hygiëne is het niet mogelijk dat micro soft een gegarandeerde garantie voor hoge Beschik baarheid biedt voor deze service. Het is de verantwoordelijkheid van de klant om ervoor te zorgen dat hun toepassingen op de juiste wijze zijn geconfigureerd voor maximale Beschik baarheid. Micro soft controleert en beheert de status van het apparaat en de netwerk verbinding.

## <a name="microsoft-monitoring"></a>Micro soft monitoring

Het Thales Luna-apparaat in gebruik heeft standaard SNMP en seriële poort als opties voor het bewaken van het apparaat. Micro soft heeft de seriële poort verbinding als een fysieke manier gebruikt om verbinding te maken met het apparaat om een eenvoudige telemetrie op Apparaatstatus op te halen. Dit omvat items zoals de Tempe ratuur en onderdeel status, zoals voedingen en ventilatoren.
Micro soft gebruikt hiervoor een niet-beheerders functie die is ingesteld op het Thales-apparaat. Deze rol biedt de mogelijkheid om de telemetrie op te halen, maar geeft geen toegang tot het apparaat op basis van een beheer taak of een manier om cryptografische informatie weer te geven. Onze klanten kunnen er zeker van zijn dat hun apparaat echt eigen is voor het beheren, beheren en gebruiken van gevoelige cryptografische-sleutel opslag. Als een klant niet tevreden is over deze minimale toegang voor de basis status controle, hebben ze de mogelijkheid om het bewakings account uit te scha kelen. Het meest voor de hand liggende gevolg hiervan is dat micro soft geen enige informatie kan geven over de status van een proactieve melding van Apparaatstatus. In dit geval is de klant verantwoordelijk voor de status van het apparaat.
De functie monitor zelf is zo ingesteld dat het apparaat om de 10 minuten wordt gecontroleerd om status gegevens op te halen. Als gevolg van de fout gevoelige aard van seriële communicatie, wordt er pas een waarschuwing gegenereerd als er gedurende een periode van één uur meerdere negatieve status indicatoren zijn. Deze waarschuwing leidt uiteindelijk tot een proactieve klant communicatie om het probleem op te lossen.
Afhankelijk van de aard van het probleem, wordt de juiste actie ondernomen om de impact te verminderen en te zorgen voor een lage risico herstel. Zo is een stroom storing bijvoorbeeld een hot swap-procedure zonder resulterende knoei gebeurtenis, zodat deze kan worden uitgevoerd met weinig impact en minimale Risico's voor de bewerking. Andere procedures kunnen vereisen dat een apparaat wordt vrijgemaakt en de inrichting ongedaan wordt gemaakt om beveiligings Risico's voor de klant te minimaliseren. In deze situatie zou een klant een alternatief apparaat inrichten, opnieuw lid worden van een koppeling met hoge Beschik baarheid, waardoor synchronisatie van apparaten wordt geactiveerd. De normale werking wordt in minimale tijd hervat, met een minimale onderbreking en een laag beveiligings risico.  

## <a name="customer-monitoring"></a>Klant bewaking

Een toegevoegde waarde van de speciale HSM-service is het besturings element dat de klant ontvangt van het apparaat, met name overwegend als het een door de Cloud geleverde apparaat is. Een gevolg van dit besturings element is de verantwoordelijkheid om de status van het apparaat te controleren en te beheren. Het Thales Luna 7 HSM-apparaat wordt geleverd met richt lijnen voor de implementatie van SNMP en syslog. Klanten van de speciale HSM-service wordt aangeraden dit zelfs te gebruiken wanneer het micro soft-controle account actief blijft en het verplicht acht te beschouwen als het micro soft-controle account wordt uitgeschakeld.
Een van de beschik bare technieken kan een klant toestaan problemen te identificeren en micro soft-ondersteuning aan te roepen om de juiste herstel werkzaamheden te initiëren.

## <a name="next-steps"></a>Volgende stappen

Het wordt aanbevolen om alle belang rijke concepten van de service, zoals hoge Beschik baarheid en beveiliging, bijvoorbeeld goed te begrijpen vóór het inrichten van apparaten en het ontwerpen of implementeren van toepassingen. Meer onderwerpen over het concept niveau:

* [Hoge beschikbaarheid](high-availability.md)
* [Fysieke beveiliging](physical-security.md)
* [Netwerken](networking.md)
* [Ondersteuning](supportability.md)
