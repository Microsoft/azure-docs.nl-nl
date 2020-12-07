---
title: Hoge beschikbaarheid en herstel na noodgevallen
titleSuffix: Azure Digital Twins
description: Hierin worden de Azure-en Azure Digital Apparaatdubbels-functies beschreven waarmee u Maxi maal beschik bare Azure IoT-oplossingen kunt bouwen met mogelijkheden voor herstel na nood gevallen.
author: baanders
ms.author: baanders
ms.date: 10/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: ac75a5b0b59a06855b7ee88d971c269ca915e429
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763160"
---
# <a name="azure-digital-twins-high-availability-and-disaster-recovery"></a>Azure Digital Apparaatdubbels hoge Beschik baarheid en herstel na nood gevallen

Een belang rijk aandachtspunt voor flexibele IoT-oplossingen is bedrijfs continuïteit en herstel na nood gevallen. Ontwerpen voor **hoge Beschik baarheid (ha)** en herstel **na nood gevallen (Dr)** kan u helpen om de juiste uptime-doelen voor uw oplossing te definiëren en te bereiken.

In dit artikel worden de HA-en DR-functies beschreven die specifiek worden aangeboden door de Azure Digital Apparaatdubbels-service.

Azure Digital Apparaatdubbels ondersteunt deze functie opties:
* *Intra regio ha* : ingebouwde redundantie voor het leveren van de uptime van de service
* *Meerdere regio's van Dr* : een failover naar een geografisch gekoppelde Azure-regio in het geval van een onverwachte Data Center-fout

U kunt ook het gedeelte [*Aanbevolen procedures*](#best-practices) voor algemene Azure-richt lijnen over het ontwerpen van ha/Dr bekijken.

## <a name="intra-region-ha"></a>Binnenste regio HA
 
Azure Digital Apparaatdubbels voorziet in een binnenste regio HA door redundantie in de service te implementeren. Dit wordt weer gegeven in de [service-Sla](https://azure.microsoft.com/support/legal/sla/digital-twins) voor de actieve tijds duur. **Voor de ontwikkel aars van een Azure Digital Apparaatdubbels-oplossing is geen extra werk vereist om deze HA-functies te benutten.** Hoewel Azure Digital Apparaatdubbels een redelijk hoge uptime biedt, kunnen tijdelijke fouten nog steeds worden verwacht, net zoals bij een gedistribueerd computing platform. Het juiste beleid voor opnieuw proberen moet zijn ingebouwd in de onderdelen die communiceren met een Cloud toepassing om te kunnen omgaan met tijdelijke fouten.

## <a name="cross-region-dr"></a>DR-regio tussen regio's

Er kunnen zeldzame situaties optreden wanneer een Data Center uitvallende storingen ondervindt als gevolg van stroom storingen of andere gebeurtenissen in de regio. Dergelijke gebeurtenissen zijn zeldzaam, en tijdens dergelijke storingen is het mogelijk dat de mogelijkheden voor de intra regio HA die hierboven worden beschreven, niet kunnen helpen. Azure Digital Apparaatdubbels lost dit op met door micro soft geïnitieerde failover.

Door micro soft **geïnitieerde failover** wordt door micro soft in zeldzame gevallen uitgevoerd om alle Azure Digital apparaatdubbels-exemplaren van een betrokken regio over te gaan naar de overeenkomstige geografische paar regio. Dit proces is een standaard optie (zonder een manier om gebruikers te kiezen) en vereist geen tussen komst van de gebruiker. Micro soft behoudt zich het recht voor om te bepalen wanneer deze optie wordt uitgeoefend. Dit mechanisme heeft geen toestemming van een gebruiker voor het uitvoeren van een failover van het gebruikers exemplaar.

>[!NOTE]
> Sommige Azure-Services bieden ook een extra optie met de door de **klant geïnitieerde failover**, waarmee klanten een failover voor hun exemplaar kunnen initiëren, bijvoorbeeld om een Dr-analyse uit te voeren. Dit mechanisme wordt momenteel **niet ondersteund** door Azure Digital apparaatdubbels. 

## <a name="best-practices"></a>Aanbevolen procedures

Raadpleeg de volgende Azure-richt lijnen in dit onderwerp voor aanbevolen procedures op HA/DR: 
* In het artikel [*technische richt lijnen voor Azure-bedrijfs continuïteit*](/azure/architecture/framework/resiliency/overview) wordt een algemeen framework beschreven waarmee u rekening moet houden met bedrijfs continuïteit en herstel na nood gevallen. 
* Het document [*nood herstel en hoge Beschik baarheid voor Azure-toepassingen*](/azure/architecture/framework/resiliency/backup-and-recovery) biedt architectuur richtlijnen voor strategieën voor Azure-toepassingen om hoge beschik BAARHEID (ha) en herstel na nood gevallen (Dr) te kunnen uitvoeren.

## <a name="next-steps"></a>Volgende stappen 

Lees meer over aan de slag met Azure Digital Apparaatdubbels-oplossingen:
 
* [*Wat is Azure Digital Twins?*](overview.md)
* [*Quick Start: een voorbeeld scenario verkennen*](quickstart-adt-explorer.md)