---
title: Wereld wijde distributie met Azure Cosmos DB-onder de motorkap
description: In dit artikel vindt u technische gegevens over de globale distributie van Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/02/2020
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 1b47ad27abbe59eceabd15d091f88f4659d8dad6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102486383"
---
# <a name="global-data-distribution-with-azure-cosmos-db---under-the-hood"></a>Distributie van globale gegevens met Azure Cosmos DB-onder de motorkap
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB is een Foundational service in azure, zodat deze wereld wijd wordt geïmplementeerd in alle Azure-regio's, inclusief de open bare, soevereine, ministerie van defensie (DoD) en overheids Clouds. Binnen een Data Center implementeren en beheren we de Azure Cosmos DB op enorme stem pels computers, elk met speciale lokale opslag. Binnen een Data Center wordt Azure Cosmos DB geïmplementeerd in verschillende clusters, waardoor er mogelijk meerdere hardware-generaties worden uitgevoerd. Computers in een cluster zijn doorgaans verdeeld over 10-20-fout domeinen voor hoge Beschik baarheid binnen een regio. In de volgende afbeelding ziet u de Cosmos DB globale distributie systeem topologie:

:::image type="content" source="./media/global-dist-under-the-hood/distributed-system-topology.png" alt-text="Systeem topologie" border="false":::

**Wereld wijde distributie in azure Cosmos DB is kant** -en-klare: U kunt op elk gewenst moment, met enkele klikken of programmatisch met één API-aanroep, de geografische regio's toevoegen of verwijderen die zijn gekoppeld aan uw Cosmos-data base. Een Cosmos-data base bestaat op zijn beurt uit een set Cosmos-containers. In Cosmos DB fungeren containers als logische eenheden van distributie en schaal baarheid. De verzamelingen, tabellen en grafieken die u maakt, zijn (intern) slechts Cosmos containers. Containers zijn volledig schema-neutraal en bieden een bereik voor een query. Gegevens in een Cosmos-container worden automatisch geïndexeerd bij opname. Met automatisch indexeren kunnen gebruikers de gegevens opvragen zonder dat er problemen zijn met schema-en index beheer, met name in een wereld wijd gedistribueerde configuratie.  

- In een bepaalde regio worden gegevens in een container gedistribueerd met behulp van een partitie sleutel, die u opgeeft en die transparant wordt beheerd door de onderliggende fysieke partities (*lokale distributie*).  

- Elke fysieke partitie wordt ook gerepliceerd tussen geografische regio's (*globale distributie*). 

Wanneer een app die Cosmos DB, elastisch de door Voer op een Cosmos-container schaalt of meer opslag gebruikt, Cosmos DB transparante bewerkingen voor partitie beheer (splitsen, klonen, verwijderen) in alle regio's transparant afhandelen. Afhankelijk van de schaal, distributie of fouten, blijft Cosmos DB één systeem kopie van de gegevens in de containers opgeven, die wereld wijd worden gedistribueerd over een wille keurig aantal regio's.  

Zoals in de volgende afbeelding wordt weer gegeven, worden de gegevens binnen een container verdeeld over twee dimensies: binnen een regio en in verschillende regio's, over het hele land.  

:::image type="content" source="./media/global-dist-under-the-hood/distribution-of-resource-partitions.png" alt-text="fysieke partities" border="false":::

Een fysieke partitie wordt geïmplementeerd met een groep replica's, een zogenaamde *replicaset*. Elke machine fungeert als host voor honderden replica's die overeenkomen met verschillende fysieke partities binnen een vaste set processen, zoals wordt weer gegeven in de bovenstaande afbeelding. Replica's die overeenkomen met de fysieke partities worden dynamisch geplaatst en gelijkmatig verdeeld over de computers binnen een cluster en data centers binnen een regio.  

Een replica maakt uniek deel uit van een Azure Cosmos DB-Tenant. Elke replica fungeert als host voor een exemplaar van de [Data base-engine](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)van Cosmos DB, waarmee de resources en de bijbehorende indexen worden beheerd. De Cosmos-data base-engine wordt uitgevoerd op een ARS-systeem (Atom-record-Sequence). De engine is neutraal met het concept van een schema, waardoor de grens tussen de waarden van de structuur en het exemplaar van records wordt vervaagd. Cosmos DB behaalt volledige schema AGNOSTICISM door automatisch alles te indexeren bij opname op een efficiënte manier, zodat gebruikers hun wereld wijd gedistribueerde gegevens kunnen doorzoeken zonder schema-of index beheer.

De Cosmos-data base-engine bestaat uit onderdelen, zoals de implementatie van verschillende coördinatie primitieven, taal runtimes, de query processor en de opslag-en indexerings subsystemen die verantwoordelijk zijn voor transactionele opslag en het indexeren van gegevens. Om duurzaamheid en hoge Beschik baarheid te kunnen bieden, behoudt de data base-engine de gegevens en index op Ssd's en repliceert deze naar de data base engine-instanties binnen de respectievelijk replicaset (en). Grotere tenants komen overeen met een hogere schaal van door Voer en opslag en hebben een grotere of meer replica's of beide. Elk onderdeel van het systeem is volledig asynchroon: er zijn geen threads die ooit worden geblokkeerd, en elke thread heeft korte tijdige werkzaamheden zonder enige overbodige thread switches. De limiet voor de frequentie en de back-updruk wordt in de hele stack van de toegangs beheer koppeling naar alle I/O-paden. De Cosmos-data base-engine is ontworpen om gebruik te maken van nauw keurige gelijktijdigheid en om hoge door voer te bieden terwijl u binnen frugale hoeveel heden systeem bronnen werkt.

De wereld wijde distributie van Cosmos DB is afhankelijk van twee belang rijke abstracties: *replica sets* en *partitie sets*. Een replicaset is een modulair galerie bouwer-blok voor coördinatie en een partitieset is een dynamische overlay van een of meer geografisch gedistribueerde fysieke partities. Om te begrijpen hoe globale distributie werkt, moeten we deze twee belang rijke abstracties begrijpen. 

## <a name="replica-sets"></a>Replica sets

Een fysieke partitie wordt gematerialeerd als een met zichzelf beheerde en dynamisch taak verdeling van de gespreide groep replica's over meerdere fout domeinen, een zogenaamde replicaset. Deze verzameling implementeert gezamenlijk het computer Protocol van de gerepliceerde status om de gegevens in de fysieke partitie Maxi maal beschikbaar, duurzaam en consistent te maken. Het lidmaatschap van de replicaset *N* is dynamisch; het zorgt voor schommeling tussen *Nmin.* en *NMAX.* op basis van de fouten, administratieve bewerkingen en de tijd voor mislukte replica's om opnieuw te genereren/te herstellen. Op basis van de wijzigingen in het lidmaatschap configureert het replicatie protocol ook de grootte van de lees-en schrijf quorums. Om de door Voer die is toegewezen aan een bepaalde fysieke partitie uniform te distribueren, hebben we twee ideeën ondergaan: 

- Ten eerste zijn de kosten voor het verwerken van de schrijf aanvragen op de leider hoger dan de kosten voor het Toep assen van de updates op de volger. De leider heeft dan een budget van meer systeem bronnen dan de volgers. 

- Ten tweede, voor zover mogelijk, is het Lees quorum voor een bepaald consistentie niveau uitsluitend samengesteld uit de opvolg replica's. Voor komt u dat u contact opneemt met de leider voor lees bewerkingen, tenzij dit vereist is. We hebben een aantal ideeën ondergaan van het onderzoek dat is uitgevoerd op de relatie van [belasting en capaciteit](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) in de op quorum gebaseerde systemen voor de [vijf consistentie modellen](consistency-levels.md) die Cosmos DB ondersteunt.  

## <a name="partition-sets"></a>Partitie sets

Een groep fysieke partities, één van elk geconfigureerd met de Cosmos-database regio's, bestaat uit het beheren van dezelfde set sleutels die worden gerepliceerd in alle geconfigureerde regio's. Deze hogere coördinatie primitieve wordt een *partitieset* genoemd: een geografisch gedistribueerde dynamische overlay van fysieke partities die een bepaalde set sleutels beheert. Terwijl een bepaalde fysieke partitie (een replicaset) binnen een cluster ligt, kan een partitieset clusters, data centers en geografische regio's omvatten, zoals wordt weer gegeven in de onderstaande afbeelding:  

:::image type="content" source="./media/global-dist-under-the-hood/dynamic-overlay-of-resource-partitions.png" alt-text="Partitie sets" border="false":::

U kunt een partitie instellen als een geografisch verspreide ' superreplicaset ', die bestaat uit meerdere replica sets die eigenaar zijn van dezelfde set sleutels. Net als bij een replicaset is het lidmaatschap van een partitieset ook dynamisch. Dit kan worden geschommeld op basis van de impliciete bewerkingen voor fysieke partitie beheer om nieuwe partities toe te voegen aan of te verwijderen uit een bepaalde partitieset (bijvoorbeeld wanneer u de door Voer voor een container uitschaalt, een regio aan uw Cosmos-Data Base toevoegt of eruit verwijdert). Omdat elk van de partities (van een partitieset) het lidmaatschap van de partitieset binnen een eigen replicaset beheert, wordt het lidmaatschap volledig gedecentraliseerd en Maxi maal beschikbaar. Tijdens de herconfiguratie van een partitieset wordt ook de topologie van de overlay tussen fysieke partities tot stand gebracht. De topologie wordt dynamisch geselecteerd op basis van het consistentie niveau, de geografische afstand en de beschik bare netwerk bandbreedte tussen de bron-en doel-fysieke partities.  

Met de service kunt u uw Cosmos-data bases configureren met één schrijf regio of meerdere schrijf regio's, en afhankelijk van de keuze, worden partitie sets geconfigureerd om schrijf bewerkingen in precies één of alle regio's te accepteren. Het systeem maakt gebruik van een geneste consensus-protocol op twee niveaus: één niveau werkt binnen de replica's van een fysieke partitie die de schrijf bewerkingen accepteert, en de andere werkt op het niveau van een partitie, zodat alle vastgelegde schrijf acties binnen de partitieset volledig worden gegarandeerd. Deze meerlaagse, geneste consensus is van cruciaal belang voor de implementatie van onze strikte Sla's voor hoge Beschik baarheid, evenals de implementatie van de consistentie modellen, die Cosmos DB aan haar klanten aanbieden.  

## <a name="conflict-resolution"></a>Conflictoplossing

Het ontwerp voor de update doorgifte, conflict oplossing en het bijhouden van causality is geïnspireerd op het vorige werk op de [epidemie-algoritmen](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) en het [Bayou](https://zoo.cs.yale.edu/classes/cs422/2013/bib/terry95managing.pdf) -systeem. Hoewel de kernels van de ideeën geruime zijn en een handig referentie kader bieden voor het communiceren van het systeem ontwerp van de Cosmos DB, hebben ze ook aanzienlijke trans formatie ondergaan toen we ze op het Cosmos DB systeem hebben toegepast. Dit was nodig omdat de vorige systemen niet zijn ontworpen met de resource governance, noch met de schaal waarop Cosmos DB moet worden uitgevoerd, noch om de mogelijkheden te bieden (bijvoorbeeld gebonden verouderde consistentie) en de strikte en uitgebreide Sla's die Cosmos DB aan zijn klanten.  

Terughalen dat een partitieset wordt gedistribueerd over meerdere regio's en volgt het replicatie protocol Cosmos Db's (Multi-Region Writes) om de gegevens te repliceren tussen de fysieke partities waaruit een bepaalde partitieset bestaat. Elke fysieke partitie (van een partitieset) accepteert schrijf bewerkingen en verzendt normaal gesp roken Lees bewerkingen naar de clients die lokaal zijn voor die regio. Schrijf bewerkingen die worden geaccepteerd door een fysieke partitie binnen een regio worden blijvend vastgelegd en Maxi maal beschikbaar gemaakt in de fysieke partitie voordat ze worden bevestigd aan de client. Dit zijn voorlopige schrijf bewerkingen en worden door gegeven aan andere fysieke partities in de partitieset met een anti-entropie kanaal. Clients kunnen voorlopige of doorgevoerde schrijf bewerkingen aanvragen door een aanvraag header door te geven. De anti-entropie doorgifte (met inbegrip van de frequentie van doorgifte) is dynamisch, op basis van de topologie van de partitieset, de regionale nabijheid van de fysieke partities en het geconfigureerde consistentie niveau. Binnen een partitieset volgt Cosmos DB een primair doorvoer schema met een dynamisch geselecteerde arbiter-partitie. De selectie van arbiter is dynamisch en vormt een integraal onderdeel van de herconfiguratie van de partitieset op basis van de topologie van de overlay. De vastgelegde schrijf bewerkingen (inclusief meerdere rijen/batch-updates) zijn gegarandeerd. 

We hebben gebruikgemaakt van gecodeerde vector klokken (die de regio-ID en logische klokken bevatten die overeenkomen met elk niveau van consensus in de replicaset en de partitieset) voor causality tracking en versie vectoren om update conflicten te detecteren en op te lossen. De topologie en het selectie algoritme voor peers zijn ontworpen om te zorgen voor vaste en minimale opslag en minimale netwerk overhead van versie vectoren. Met het algoritme wordt de strikte convergentie-eigenschap gegarandeerd.  

Voor de Cosmos-data bases die zijn geconfigureerd met meerdere schrijf regio's, biedt het systeem een aantal flexibele beleids regels voor het automatisch oplossen van conflicten voor de ontwikkel aars waaruit kan worden gekozen, waaronder: 

- **Last-write-WINS (LWW)**, die standaard gebruikmaakt van een door het systeem gedefinieerde tijds tempel eigenschap (die is gebaseerd op het klok protocol voor tijd synchronisatie). Cosmos DB kunt u ook een aangepaste numerieke eigenschap opgeven die moet worden gebruikt voor het oplossen van conflicten.  
- Door de **toepassing gedefinieerde (aangepast) beleid voor conflict oplossing** (uitgedrukt in samenvoeg procedures), dat is ontworpen voor het afstemmen van een door de toepassing gedefinieerde semantiek van conflicten. Deze procedures worden opgeroepen wanneer de Write-write-conflicten worden gedetecteerd onder de verantwoordelijkheid van een database transactie aan de server zijde. Het systeem biedt precies eenmaal garantie voor het uitvoeren van een samenvoeg procedure als onderdeel van het toezeggings protocol. Er zijn [verschillende voor beelden van conflict oplossing](how-to-manage-conflicts.md) beschikbaar waarmee u kunt spelen met.  

## <a name="consistency-models"></a>Consistentie modellen

Of u uw Cosmos-data base met één of meerdere schrijf regio's configureert, u kunt kiezen uit de vijf goed gedefinieerde consistentie modellen. Bij meerdere schrijf regio's zijn het volgende enkele van de belangrijkste aspecten van de consistentie niveaus:  

Met de consistentie van de gebonden veroudering wordt gegarandeerd dat alle Lees bewerkingen binnen *K* -voor voegsels vallen of *T* seconden van de laatste schrijf bewerking in een van de regio's. Bovendien zijn Lees bewerkingen met gebonden verouderde consistentie gegarandeerd een monotone en met consistente voorvoegsel garanties. Het anti-entropie protocol werkt op een frequentie-beperkte manier en zorgt ervoor dat de voor voegsels niet worden geaccumuleerd en dat de druk op de schrijf bewerkingen niet moet worden toegepast. Met sessie consistentie wordt de monotone Lees-, monotone schrijf-, lees-en schrijf bewerkingen gegarandeerd. Voor de data bases die zijn geconfigureerd met sterke consistentie, zijn de voor delen (lage schrijf latentie, hoge schrijf Beschik baarheid) van meerdere schrijf regio's niet van toepassing, vanwege synchrone replicatie tussen regio's.

De semantiek van de vijf consistentie modellen in Cosmos DB worden [hier](consistency-levels.md)beschreven en mathematisch beschreven met behulp van een TLA [-specificatie op](https://github.com/Azure/azure-cosmos-tla)hoog niveau.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het configureren van globale distributie met behulp van de volgende artikelen:

* [Regio's toevoegen aan of verwijderen uit uw databaseaccount](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Een aangepast beleid voor conflict oplossing maken](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
