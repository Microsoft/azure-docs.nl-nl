---
title: Implementatie architectuur-Azure dedicated HSM | Microsoft Docs
description: Basis overwegingen bij het ontwerpen van het gebruik van een specifieke HSM van Azure als onderdeel van een toepassings architectuur
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 6a0767b077886337331f24b15715247006f3fe2c
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888892"
---
# <a name="azure-dedicated-hsm-deployment-architecture"></a>Implementatiearchitectuur van Azure Dedicated HSM

Azure dedicated HSM biedt cryptografische-sleutel opslag in Azure. Het voldoet aan strenge beveiligings vereisten. Klanten profiteren van het gebruik van een speciale HSM van Azure, als ze:

* Moet voldoen aan de FIPS 140-2-certificering van niveau 3
* Vereisen dat ze exclusieve toegang tot de HSM hebben
* moeten volledige controle over hun apparaten hebben

De Hsm's worden gedistribueerd in de data centers van micro soft en kunnen eenvoudig worden ingericht als een combi natie van apparaten als basis voor een Maxi maal beschik bare oplossing. Ze kunnen ook worden geïmplementeerd in verschillende regio's voor een nood herstel oplossing. De regio's met een toegewezen HSM kunnen momenteel worden gecontroleerd met behulp [van de pagina producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=azure-dedicated-hsm). 

Elk van de regio's heeft HSM-racks die zijn geïmplementeerd in twee onafhankelijke data centers of ten minste twee onafhankelijke beschikbaarheids zones. Zuid-Azië-oost heeft bijvoorbeeld drie beschikbaarheids zones en VS-Oost 2 heeft twee. Er zijn in totaal acht regio's in Europa, Azië en de Verenigde Staten die de toegewezen HSM-service bieden en deze wijzigingen wanneer we nieuwe HSM-racks toevoegen in nieuwe regio's. Zie de officiële  [informatie over Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/)voor meer informatie over Azure-regio's.
Enkele ontwerp factoren voor een specifieke op HSM gebaseerde oplossing zijn locatie/latentie, hoge Beschik baarheid en ondersteuning voor andere gedistribueerde toepassingen.

## <a name="device-location"></a>Locatie apparaat

De optimale locatie van een HSM-apparaat bevindt zich in de buurt van de toepassingen die cryptografische bewerkingen uitvoeren. In de regio latentie wordt verwacht dat het enkele milliseconden is. De latentie van de andere regio kan 5 tot 10 keer hoger zijn dan dit.

## <a name="high-availability"></a>Hoge beschikbaarheid

Voor een hoge Beschik baarheid moet een klant twee HSM-apparaten gebruiken in een regio die is geconfigureerd met behulp van Thanles-software als een paar met hoge Beschik baarheid. Dit type implementatie zorgt voor de beschik baarheid van sleutels als één apparaat een probleem ondervindt waardoor het niet mogelijk is om sleutel bewerkingen te verwerken. Het vermindert ook aanzienlijk risico bij het uitvoeren van onderhouds-en reparatie onderhoud, zoals het vervangen van een voeding. Het is belang rijk om te voor komen dat u rekening moet doen met een soort fout op regionaal niveau. Er kunnen regionale storingen optreden wanneer er sprake is van natuur rampen, zoals orkanen, overstroming of aard bevingen. Dit type gebeurtenissen moet worden verholpen door HSM-apparaten in een andere regio in te richten. Apparaten die zijn geïmplementeerd in een andere regio kunnen samen worden gekoppeld via de Thales-software configuratie. Dit betekent dat de minimale implementatie voor een Maxi maal beschik bare en nood herstel bare oplossing vier HSM-apparaten zijn tussen twee regio's. Lokale redundantie en redundantie over regio's kunnen worden gebruikt als basis lijn voor het toevoegen van andere implementaties van HSM-apparaten voor de ondersteuning van latentie, capaciteit of om te voldoen aan andere toepassingsspecifieke vereisten.

## <a name="distributed-application-support"></a>Ondersteuning voor gedistribueerde toepassingen

Toegewezen HSM-apparaten worden meestal geïmplementeerd ter ondersteuning van toepassingen die sleutel opslag en het ophalen van sleutels moeten uitvoeren. Toegewezen HSM-apparaten hebben 10 partities voor onafhankelijke toepassings ondersteuning. De locatie van het apparaat moet worden gebaseerd op een holistische weer gave van alle toepassingen die de service moeten gebruiken.

## <a name="next-steps"></a>Volgende stappen

Zodra de implementatie architectuur is vastgesteld, worden de meeste configuratie activiteiten voor het implementeren van die architectuur verschaft door Thales. Dit omvat ook apparaatconfiguratie en scenario's voor de integratie van toepassingen. Voor meer informatie gebruikt u de Thales-portal voor [klanten ondersteuning](https://supportportal.gemalto.com/csm/) en downloadt u beheer-en configuratie handleidingen. De micro soft-partner site heeft verschillende integratie handleidingen.
Het wordt aanbevolen om alle belang rijke concepten van de service, zoals hoge Beschik baarheid en beveiliging, bijvoorbeeld goed te begrijpen vóór het inrichten van apparaten of het ontwerpen en implementeren van toepassingen.
Meer onderwerpen over het concept niveau:

* [Hoge beschikbaarheid](high-availability.md)
* [Fysieke beveiliging](physical-security.md)
* [Netwerken](networking.md)
* [Ondersteuning](supportability.md)
* [Controle](monitoring.md)
