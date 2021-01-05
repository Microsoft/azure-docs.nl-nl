---
title: Hoge beschikbaarheid en herstel na noodgevallen
description: Meer informatie over het ontwerpen van uw batch-toepassing voor een regionale storing.
ms.topic: how-to
ms.date: 12/30/2020
ms.openlocfilehash: 51bcb0cfa35aacd24c0f79082491ef1fc7040889
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2020
ms.locfileid: "97831003"
---
# <a name="design-your-batch-application-for-high-availability"></a>Uw batch-toepassing ontwerpen voor hoge Beschik baarheid

Azure Batch is beschikbaar in alle Azure-regio's, maar wanneer een batch-account wordt gemaakt, moet het worden gekoppeld aan één specifieke regio. Alle bewerkingen voor het batch-account worden toegepast op die regio. Pools en gekoppelde virtuele machines (Vm's) worden bijvoorbeeld gemaakt in dezelfde regio als het batch-account.

Bij het ontwerpen van een toepassing die gebruikmaakt van batch, moet u rekening houden met de mogelijkheid van een batch die niet beschikbaar is in een regio. Het is mogelijk om een zeldzame situatie te ondervinden waarbij er sprake is van een probleem met de regio als geheel, de volledige batch-service in de regio of uw specifieke batch-account.

Als de toepassing of oplossing die gebruikmaakt van batch altijd beschikbaar moet zijn, moet deze zijn ontworpen om een failover naar een andere regio te kunnen uitvoeren of de werk belasting over twee of meer regio's te splitsen. Beide benaderingen vereisen ten minste twee batch-accounts, waarbij elk account zich in een andere regio bevindt.

## <a name="multiple-batch-accounts-in-multiple-regions"></a>Meerdere batch-accounts in meerdere regio's

Door meerdere batch-accounts in verschillende regio's te gebruiken, kan uw toepassing worden uitgevoerd als een batch-account in één regio niet meer beschikbaar is. Als uw toepassing Maxi maal beschikbaar moet zijn, heeft meerdere accounts vooral belang rijk.

In sommige gevallen kunnen toepassingen worden ontworpen om opzettelijk twee of meer regio's te gebruiken. Als u bijvoorbeeld een aanzienlijke hoeveelheid capaciteit nodig hebt, moet u mogelijk meerdere regio's gebruiken om een grootschalige toepassing te verwerken of voor toekomstige groei. Voor deze toepassingen zijn ook meerdere batch-accounts vereist (één per regio gebruikt).

## <a name="design-considerations-for-providing-failover"></a>Ontwerp overwegingen voor het leveren van failover

Wanneer u de mogelijkheid biedt om een failover naar een andere regio uit te geven, moeten alle onderdelen in een oplossing worden overwogen; het is niet voldoende om simpelweg een tweede batch-account te hebben. In de meeste batch-toepassingen is bijvoorbeeld een Azure-opslag account vereist, waarbij het opslag account en het batch-account moeten worden opgeslagen in dezelfde regio voor aanvaard bare prestaties.

Houd rekening met de volgende punten bij het ontwerpen van een oplossing die failover kan:

- Maak vooraf alle vereiste accounts in elke regio, zoals het batch-account en het opslag account. Er worden vaak geen kosten in rekening gebracht voor het maken van accounts en kosten worden alleen toegerekend wanneer het account wordt gebruikt of wanneer gegevens worden opgeslagen.
- Zorg ervoor dat de juiste [quota](batch-quota-limit.md) worden ingesteld op alle accounts van tevoren, zodat u het vereiste aantal kernen kunt toewijzen met behulp van het batch-account.
- Gebruik sjablonen en/of scripts voor het automatiseren van de implementatie van de toepassing in een regio.
- Binaire toepassings bestanden en referentie gegevens up-to-date houden in alle regio's. Als u up-to-date blijft, kunt u ervoor zorgen dat de regio snel online kan worden gebracht zonder te hoeven wachten op het uploaden en implementeren van bestanden. Als een aangepaste toepassing bijvoorbeeld op groeps knooppunten wordt opgeslagen en ernaar wordt verwezen met behulp van batch-toepassings pakketten, moet de nieuwe versie van de toepassing worden geüpload naar elk batch-account en wordt verwezen door de groeps configuratie (of de nieuwe versie de standaard versie maken).
- In de toepassing die batch, opslag en andere services aanroept, kunt u eenvoudig overschakelen naar clients of de belasting naar verschillende regio's.
- Overweeg regel matig over te scha kelen naar een alternatieve regio als onderdeel van de normale werking. Bijvoorbeeld, met twee implementaties in afzonderlijke regio's, schakelt u elke maand over naar de alternatieve regio.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het maken van batch-accounts met de [Azure Portal](batch-account-create-portal.md), de [Azure cli](./scripts/batch-cli-sample-create-account.md), [Power shell](batch-powershell-cmdlets-get-started.md)of de [batch-beheer-API](batch-management-dotnet.md).
- Meer informatie over de [standaard quota's die zijn gekoppeld aan een batch-account](batch-quota-limit.md) en hoe quota kunnen worden verhoogd.
