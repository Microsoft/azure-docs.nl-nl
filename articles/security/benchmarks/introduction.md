---
title: Inleiding tot Azure Security-benchmark
description: Inleiding tot Security Bench Mark
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 32aa64a9343a8e3c62af4322b3320b28ce805064
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97369188"
---
# <a name="azure-security-benchmark-introduction"></a>Inleiding tot Azure Security Bench Mark

Nieuwe services en functies worden dagelijks uitgebracht in azure, ontwikkel aars publiceren snel nieuwe Cloud toepassingen die zijn gebouwd op deze services, en aanvallers willen altijd nieuwe manieren om onjuist geconfigureerde resources te misbruiken. De Cloud gaat snel, ontwikkel aars verplaatsen snel en aanvallers zijn altijd onderweg. Hoe weet u zeker dat u uw Cloud implementaties veilig wilt stellen? Hoe verschillen de beveiligings procedures voor Cloud systemen van on-premises systemen? Hoe kunt u controleren op consistentie in veel onafhankelijke ontwikkel teams?

Micro soft heeft geconstateerd dat met behulp van *beveiligings benchmarks* u snel Cloud implementaties kunt beveiligen. Bench Mark-aanbevelingen van uw Cloud serviceprovider bieden u een start punt voor het selecteren van specifieke beveiligings configuratie-instellingen in uw omgeving, zodat u snel Risico's kunt verminderen voor uw organisatie.

De Azure Security Bench Mark bevat een verzameling essentiële aanbevelingen voor beveiliging die u kunt gebruiken om de services te beveiligen die u in azure gebruikt:

- **Beveiligings controles**: deze aanbevelingen zijn in het algemeen van toepassing op uw Azure-Tenant en Azure-Services. Elke aanbeveling identificeert een lijst met belanghebbenden die doorgaans betrokken zijn bij het plannen, goed keuren of implementeren van de Bench Mark. 
- **Service basislijnen**: Hiermee worden de besturings elementen toegepast op afzonderlijke Azure-Services om aanbevelingen te geven over de beveiligings configuratie van die service.

## <a name="implement-the-azure-security-benchmark"></a>De Azure Security-Bench Mark implementeren
- **Plan** de implementatie van uw Azure Security-benchmark procedure door de [documentatie](overview.md) te bekijken voor de Enter prise-besturings elementen en servicespecifieke basislijnen om uw beheer raamwerk te plannen en te bepalen hoe IT wordt toegewezen aan richt lijnen zoals CIS (Controls v 7.1) en het NIST (SP 800-53)-Framework.
- **Bewaak** uw naleving met de Azure Security Bench Mark-status (en andere controle sets) met behulp van het [dash board voor nalevings vereisten](../../security-center/security-center-compliance-dashboard.md)van Azure Security Center.
- **Stel Guardrails** in voor het automatiseren van beveiligde configuraties en het afdwingen van naleving met Azure Security Bench Mark (en andere vereisten in uw organisatie) met Azure blauw drukken en Azure Policy.
 
Houd er rekening mee dat de Azure Security Bench Mark v2 is afgestemd op de [Best practices van micro soft-beveiliging](/security/compass/microsoft-security-compass-introduction) (voorheen Azure Security kompas), zodat de Azure Security Bench Mark een geconsolideerde weer gave biedt van de aanbevelingen voor Azure-beveiliging van micro soft.

## <a name="common-use-cases"></a>Veelvoorkomende use cases

Azure Security Bench Mark wordt vaak gebruikt om deze veelvoorkomende uitdagingen te verhelpen voor klanten of service partners die:
- Nieuw in Azure en zoeken naar aanbevolen procedures voor beveiliging om te zorgen voor een veilige implementatie.
- Verbetering van de beveiligings postuur van bestaande Azure-implementaties om de belangrijkste Risico's en oplossingen te bepalen.
- Het goed keuren van Azure-Services voor gebruik door technologie en bedrijfs gebruik om te voldoen aan specifieke beveiligings richtlijnen.
- Voldoen aan wettelijke voor schriften voor klanten die afkomstig zijn van een overheid of een in hoge mate gereglementeerde branches, zoals financiën en gezondheids zorg (of service leveranciers die systemen voor deze klanten moeten bouwen). Deze klanten moeten ervoor zorgen dat hun configuratie van Azure voldoet aan de beveiligings mogelijkheden die zijn opgegeven in een branche raamwerk zoals CIS, NIST of PCI. Azure Security Bench Mark biedt een efficiënte benadering van de besturings elementen die reeds vooraf zijn toegewezen aan deze branche benchmarks.

## <a name="terminology"></a>Terminologie

De termen "besturings element", "bench Mark" en "baseline" worden vaak gebruikt in de documentatie over Azure Security Bench Mark. het is belang rijk om te begrijpen hoe Azure deze voor waarden gebruikt.


| Termijn | Beschrijving | Voorbeeld |
|--|--|--|
| Controle | Een besturings element is een hoog niveau van een functie of activiteit die moet worden geadresseerd en niet specifiek is voor een technologie of implementatie. | Gegevens beveiliging is een van de beveiligings besturings elementen. Dit besturings element bevat specifieke acties die moeten worden geadresseerd om ervoor te zorgen dat gegevens beveiligd zijn. |
| Benchmark | Een bench Mark bevat beveiligings aanbevelingen voor een specifieke technologie, zoals Azure. De aanbevelingen worden gecategoriseerd door het besturings element waarvan ze deel uitmaken. | De Security Bench Mark van Azure bestaat uit de beveiligings aanbevelingen die specifiek zijn voor het Azure-platform |
| Basislijn | Een basis lijn is de implementatie van de Bench Mark voor de afzonderlijke Azure-service. Elke organisatie beslist de benchmark aanbeveling en de bijbehorende configuraties zijn vereist in het Azure-implementatie bereik. | De contoso-onderneming kijkt naar Azure SQL-beveiligings functies door de configuratie te volgen die wordt aanbevolen in Azure SQL-beveiligings basislijn.

We stellen uw feedback op de Security Bench Mark van Azure. We raden u aan opmerkingen te geven in het onderstaande feedback gebied. Als u uw invoer liever persoonlijk wilt delen met het Azure Security Bench Mark-team, vult u het formulier in op https://aka.ms/AzSecBenchmark
