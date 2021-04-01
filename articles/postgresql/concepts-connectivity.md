---
title: Problemen met de tijdelijke verbinding verwerken-Azure Database for PostgreSQL-één server
description: Meer informatie over het afhandelen van problemen met de tijdelijke connectiviteit voor Azure Database for PostgreSQL-één server.
keywords: postgresql-verbinding, connection string, connectiviteits problemen, tijdelijke fout, verbindings fout
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 7c70c82615df111f265604ff0984aa452d68565f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91704347"
---
# <a name="handling-transient-connectivity-errors-for-azure-database-for-postgresql---single-server"></a>Tijdelijke connectiviteitsfouten voor Azure Database for PostgreSQL verwerken: één server

In dit artikel wordt beschreven hoe u tijdelijke fouten kunt afhandelen die verbinding maken met Azure Database for PostgreSQL.

## <a name="transient-errors"></a>Tijdelijke fouten

Een tijdelijke fout, ook wel bekend als tijdelijke fout, is een fout die zichzelf zal oplossen. Meestal worden deze fouten manifesten beschouwd als een verbinding met de database server die wordt verwijderd. Er kunnen ook geen nieuwe verbindingen met een server worden geopend. Tijdelijke fouten kunnen bijvoorbeeld optreden als er een hardware-of netwerk fout optreedt. Een andere reden kan een nieuwe versie zijn van een PaaS-service die wordt geïmplementeerd. De meeste van deze gebeurtenissen worden in minder dan 60 seconden automatisch door het systeem beperkt. Een best practice voor het ontwerpen en ontwikkelen van toepassingen in de Cloud is het verwachten van tijdelijke fouten. We gaan ervan uit dat ze op elk gewenst moment in elk onderdeel kunnen plaatsvinden en dat ze de juiste logica hebben om deze situaties af te handelen.

## <a name="handling-transient-errors"></a>Tijdelijke fouten verwerken

Tijdelijke fouten moeten worden afgehandeld met behulp van logica opnieuw proberen. Situaties die in overweging moeten worden genomen:

* Er treedt een fout op wanneer u een verbinding probeert te openen
* Er wordt een niet-actieve verbinding aan de server zijde verwijderd. Wanneer u een opdracht probeert uit te geven, kan deze niet worden uitgevoerd
* Een actieve verbinding die momenteel een opdracht uitvoert, wordt verwijderd.

Het eerste en tweede geval zijn redelijk direct in de richting van de hand. Probeer de verbinding opnieuw te openen. Wanneer u dit hebt gedaan, wordt de tijdelijke fout door het systeem verholpen. U kunt uw Azure Database for PostgreSQL opnieuw gebruiken. We raden u aan te wachten op het opnieuw proberen van de verbinding. Back-ups maken als de eerste pogingen mislukken. Op deze manier kan het systeem alle bronnen gebruiken die beschikbaar zijn om de fout situatie op te lossen. Een goed patroon dat u moet volgen:

* Wacht vijf seconden vóór uw eerste nieuwe poging.
* Voor elke volgende poging wordt de wacht tijd exponentieel verhoogd, tot 60 seconden.
* Stel een maximum aantal nieuwe pogingen in op het moment dat de bewerking door uw toepassing wordt beschouwd.

Wanneer een verbinding met een actieve trans actie mislukt, is het moeilijker om het herstel af te handelen. Er zijn twee gevallen: als de trans actie alleen-lezen is, is het veilig om de verbinding opnieuw te openen en de trans actie opnieuw uit te voeren. Als de trans actie echter ook is geschreven naar de data base, moet u bepalen of de trans actie is teruggedraaid of dat deze is geslaagd voordat de tijdelijke fout is opgetreden. In dat geval is het mogelijk dat u alleen de bevestiging van de door Voer van de database server hebt ontvangen.

Een manier om dit te doen, is door een unieke ID te genereren op de client die wordt gebruikt voor alle nieuwe pogingen. U geeft deze unieke ID door als onderdeel van de trans actie op de server en om deze op te slaan in een kolom met een unieke beperking. Op deze manier kunt u de trans actie veilig opnieuw proberen. Dit gebeurt als de vorige trans actie is teruggedraaid en de door de client gegenereerde unieke ID nog niet in het systeem bestaat. Deze fout kan optreden als de unieke ID eerder is opgeslagen, omdat de vorige trans actie is voltooid.

Wanneer uw programma communiceert met Azure Database for PostgreSQL via middleware van een derde partij, vraagt u de leverancier of de middleware logica voor tijdelijke fouten bevat.

Zorg ervoor dat u de logica probeert te testen. Probeer bijvoorbeeld uw code uit te voeren terwijl u de reken resources van Azure Database for PostgreSQL server omhoog of omlaag kunt schalen. Uw toepassing moet de korte downtime die tijdens deze bewerking wordt aangetroffen zonder problemen verwerken.

## <a name="next-steps"></a>Volgende stappen

* [Verbindingsproblemen met Azure Databases for PostgreSQL oplossen](howto-troubleshoot-common-connection-issues.md)
