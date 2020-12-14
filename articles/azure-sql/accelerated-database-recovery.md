---
title: Versneld databaseherstel
titleSuffix: Azure SQL
description: Versneld database herstel biedt snelle en consistente herstel van de data base, het terugdraaien van momentane trans acties en het afkappen van agressief logboeken voor data bases in Azure SQL Port Folio.
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 05/19/2020
ms.openlocfilehash: bd1b33b2f6b1b0e0bf94639b3991b6507a89f5a9
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400791"
---
# <a name="accelerated-database-recovery-in-azure-sql"></a>Versneld database herstel in Azure SQL 
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

**Versneld database herstel (ADR)** is een functie van SQL server data base-engine waarmee de beschik baarheid van de data base aanzienlijk wordt verbeterd, met name als er langlopende trans acties worden uitgevoerd, door het herstel proces van de SQL server data base-engine opnieuw te ontwerpen. 

ADR is momenteel beschikbaar voor Azure SQL Database, Azure SQL Managed instance, data bases in azure Synapse Analytics en SQL Server op Azure Vm's vanaf SQL Server 2019. 

> [!NOTE] 
> ADR is standaard ingeschakeld in Azure SQL Database en Azure SQL Managed instance en het uitschakelen van ADR voor een van beide producten wordt niet ondersteund. 

## <a name="overview"></a>Overzicht

De belangrijkste voor delen van ADR zijn:

- **Snel en consistent herstel van de data base**

  Met ADR hebben langlopende trans acties geen invloed op de algehele herstel tijd, waardoor snel en consistent herstel van de data base mogelijk is, ongeacht het aantal actieve trans acties in het systeem of de grootte ervan.

- **Momentane trans actie terugdraaien**

  Met ADR is het terugdraaien van trans acties onmiddellijk, ongeacht het tijdstip waarop de trans actie actief is of het aantal updates dat is uitgevoerd.

- **Afkap ping van agressief logboek**

  Met ADR wordt het transactie logboek agressief afgekapt, zelfs in de aanwezigheid van actieve langlopende trans acties, waardoor het niet mogelijk is om de controle te vervolledigen.

## <a name="standard-database-recovery-process"></a>Standaard database herstel proces

Database herstel volgt het [Aries](https://people.eecs.berkeley.edu/~brewer/cs262/Aries.pdf) -herstel model en bestaat uit drie fasen, die in het volgende diagram worden geïllustreerd en uitvoeriger worden beschreven in het diagram.

![huidig herstel proces](./media/accelerated-database-recovery/current-recovery-process.png)

- **Analyse fase**

  Voorwaartse scan van het transactie logboek vanaf het begin van het laatste geslaagde controle punt (of het oudste wegge schreven pagina-LSN) tot aan het eind, om de status van elke trans actie te bepalen op het moment dat de data base is gestopt.

- **Fase opnieuw uitvoeren**

  Voorwaartse scan van het transactie logboek van de oudste niet-doorgevoerde trans actie tot het eind, om de data base te verplaatsen naar de status die het was op het moment van de crash door alle doorgevoerde bewerkingen opnieuw uit te voeren.

- **Fase ongedaan maken**

  Voor elke trans actie die actief was op het moment van de crash, doorloopt het logboek achterwaarts, waardoor de bewerkingen die deze trans actie hebben uitgevoerd, worden ongedaan gemaakt.

Op basis van dit ontwerp is de tijd die het kost om de SQL Server data base-engine te herstellen na een onverwachte herstart (ruwweg) evenredig met de grootte van de langste actieve trans actie in het systeem op het moment van de crash. Voor het herstel moeten alle onvolledige trans acties worden teruggedraaid. De vereiste hoeveelheid tijd is evenredig met het werk dat de trans actie heeft uitgevoerd en de tijd die het is actief. Daarom kan het herstel proces enige tijd in beslag nemen in de aanwezigheid van langlopende trans acties (zoals grote bulksgewijze bewerkingen of het bouwen van index bewerkingen voor een grote tabel).

Het annuleren/terugdraaien van een grote trans actie op basis van dit ontwerp kan ook lang duren omdat deze dezelfde fase voor het ongedaan maken van de herstel bewerking gebruikt, zoals hierboven wordt beschreven.

Daarnaast kan de SQL Server data base-engine het transactie logboek niet afkappen wanneer er langlopende trans acties zijn, omdat de bijbehorende logboek records nodig zijn voor de herstel-en terugdraai processen. Als gevolg van dit ontwerp van de SQL Server data base-engine, hebben sommige klanten gebruikt om het probleem te verhelpen dat de omvang van het transactie logboek erg groot wordt en grote hoeveel heden schijf ruimte verbruikt.

## <a name="the-accelerated-database-recovery-process"></a>Het herstel proces voor versneld data base

ADR behandelt de bovenstaande problemen door het herstel proces van de SQL Server data base-engine volledig opnieuw te ontwerpen:

- Maak deze constant tijd/direct door te voor komen dat u het logboek van/naar het begin van de oudste actieve trans actie moet scannen. Met ADR wordt het transactie logboek alleen verwerkt vanaf het laatste geslaagde controle punt (of het oudste LSN (Dirty page log Sequence Number)). Als gevolg hiervan worden de herstel tijd niet beïnvloed door langlopende trans acties.
- Minimaliseer de vereiste transactie logboek ruimte, omdat het logboek voor de hele trans actie niet meer hoeft te worden verwerkt. Als gevolg hiervan kan het transactie logboek agressief worden afgekapt als controle punten en back-ups worden uitgevoerd.

Op hoog niveau verkrijgt ADR snelle herstel van de data base door versie beheer van alle fysieke database wijzigingen en alleen ongedaan maken van logische bewerkingen, die beperkt zijn en bijna onmiddellijk ongedaan kunnen worden gemaakt. Alle trans acties die actief waren op het moment van een crash, worden gemarkeerd als afgebroken en daarom kunnen alle door deze trans acties gegenereerde versies worden genegeerd door gelijktijdige gebruikers query's.

Het proces voor het herstellen van ADR heeft dezelfde drie fasen als het huidige herstel proces. Hoe deze fasen met ADR worden gebruikt, worden in het volgende diagram geïllustreerd en uitvoerig beschreven in het diagram.

![ADR-herstel proces](./media/accelerated-database-recovery/adr-recovery-process.png)

- **Analyse fase**

  Het proces blijft hetzelfde als vóór het toevoegen van het opnieuw samen stellen van sLog en het kopiëren van logboek records voor niet-versie bewerkingen.
  
- Fase **opnieuw uitvoeren**

  Onderverdeeld in twee fasen (P)
  - Fase 1

      Voer de bewerking opnieuw uit vanaf sLog (oudste niet-doorgevoerde trans actie tot het laatste controle punt). Opnieuw is een snelle bewerking, omdat alleen enkele records uit de sLog moeten worden verwerkt.

  - Fase 2

     Opnieuw uitvoeren vanuit het transactie logboek vanaf het laatste controle punt (in plaats van de oudste niet-doorgevoerde trans actie)

- **Fase ongedaan maken**

   De fase voor het ongedaan maken met ADR wordt bijna onmiddellijk voltooid met behulp van sLog om bewerkingen zonder versie ongedaan te maken en de persistente versie van PVS

## <a name="adr-recovery-components"></a>ADR-herstel onderdelen

De vier belangrijkste onderdelen van ADR zijn:

- **Permanente versie opslag (PVS)**

  De permanente versie opslag is een nieuw SQL Server data base engine-mechanisme voor het persistent maken van de rijdefinities die in de data base zelf zijn gegenereerd, in plaats van naar de traditionele `tempdb` versie opslag. PVS maakt het isoleren van bronnen mogelijk en verbetert de beschik baarheid van Lees bare secundaire zones.

- **Logische terugzet actie**

  Logische herverteren is het asynchrone proces dat verantwoordelijk is voor het uitvoeren van ongedaan maken op basis van een op rijniveau gebaseerde, direct terugdraaiende trans actie en ongedaan maken voor alle bewerkingen met een versie. Logische terugzet actie wordt uitgevoerd door:

  - Alle afgebroken trans acties bijhouden en deze onzichtbaar maken voor andere trans acties. 
  - Terugdraai actie uitvoeren met behulp van PVS voor alle gebruikers transacties in plaats van het transactie logboek fysiek te scannen en wijzigingen een voor een ongedaan te maken.
  - Alle vergren delingen direct na het afbreken van de trans actie worden vrijgegeven. Omdat het afbreken vereist dat wijzigingen in het geheugen worden gemarkeerd, is het proces zeer efficiënt en kunnen vergren delingen gedurende een lange periode niet worden bewaard.

- **sLog**

  sLog is een secundaire logboek stroom in het geheugen waarin logboek records worden opgeslagen voor niet-verwerkte bewerkingen (zoals meta gegevens cache, vergren delen, enzovoort). De sLog is:

  - Laag volume en in-Memory
  - Bewaard op schijf door geserialiseerd tijdens het controlepunt proces
  - Periodiek afgekapt tijdens het door voeren van trans acties
  - Versnelt opnieuw en ongedaan maken door alleen de niet-geversiete bewerkingen te verwerken  
  - Maakt agressieve afkap ping van transactie logboeken mogelijk door alleen de vereiste logboek records te behouden

- **Schone**

  De Removal is het asynchrone proces dat periodiek wordt geactiveerd en dat er geen pagina versies worden opgeschoond die niet nodig zijn.

## <a name="accelerated-database-recovery-patterns"></a>Versnelde database herstel patronen

De volgende typen werk belastingen komen het meest voor in ADR:

- Werk belastingen met langlopende trans acties.
- Werk belastingen die gevallen hebben met de melding dat het transactie logboek aanzienlijk groeit door actieve trans acties.  
- Werk belastingen die lange Peri Oden niet beschik baarheid hebben vanwege langdurige herstel bewerking (zoals het onverwacht opnieuw opstarten van de service of het terugdraaien van hand matige trans acties).
