---
title: Azure Functions betrouw bare gebeurtenis verwerking
description: Voor komen dat Event hub-berichten in Azure Functions ontbreken
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: cshoe
ms.openlocfilehash: fd784bb184ff9432efc569ac9fd40de93eec0b53
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93379584"
---
# <a name="azure-functions-reliable-event-processing"></a>Azure Functions betrouw bare gebeurtenis verwerking

Gebeurtenis verwerking is een van de meest voorkomende scenario's die zijn gekoppeld aan serverloze architectuur. In dit artikel wordt beschreven hoe u een betrouw bare berichten processor maakt met Azure Functions om verlies van berichten te voor komen.

## <a name="challenges-of-event-streams-in-distributed-systems"></a>Uitdagingen van gebeurtenis stromen in gedistribueerde systemen

Overweeg een systeem dat gebeurtenissen verzendt met een constant snelheid van 100 gebeurtenissen per seconde. Op basis van dit aantal minuten kunnen meerdere parallelle functions-instanties elke seconde de binnenkomende 100 gebeurtenissen gebruiken.

De volgende minder optimale voor waarden zijn echter mogelijk:

- Wat gebeurt er als de gebeurtenis-uitgever een beschadigde gebeurtenis verzendt?
- Wat gebeurt er als uw functions-exemplaar niet-verwerkte uitzonde ringen tegen komt?
- Wat gebeurt er als een downstream-systeem offline gaat?

Hoe kunt u deze situaties afhandelen en de door Voer van uw toepassing behouden?

Met wacht rijen is betrouw bare berichten natuurlijk. Bij het koppelen met een functions-trigger maakt de functie een vergren deling van het bericht in de wachtrij. Als de verwerking mislukt, wordt de vergren deling vrijgegeven zodat een andere instantie de verwerking opnieuw kan uitvoeren. De verwerking gaat vervolgens door totdat het bericht is geëvalueerd of het wordt toegevoegd aan een verontreinigde wachtrij.

Hoewel één wachtrij bericht in een nieuwe cyclus kan blijven, blijven andere parallelle uitvoeringen behouden om de resterende berichten uit de wachtrij te verwijderen. Het resultaat is dat de algehele door Voer grotendeels niet wordt beïnvloed door één onjuist bericht. Opslag wachtrijen garanderen echter geen bestellingen en zijn niet geoptimaliseerd voor de hoge doorvoer vereisten die vereist zijn voor Event Hubs.

Azure Event Hubs daarentegen geen vergrendelings concept bevat. Event Hubs-gebeurtenissen werken als een video speler om functies als hoge door Voer, meerdere consumenten groepen en replay-mogelijkheid mogelijk te maken. Gebeurtenissen worden op één punt in de stream per partitie gelezen. Vanaf de wijzer kunt u voorwaarts of achterwaarts lezen vanaf die locatie, maar u moet ervoor kiezen om de aanwijzer te verplaatsen op gebeurtenissen die moeten worden verwerkt.

Wanneer er fouten optreden in een stroom, wordt de verwerking van gebeurtenissen geblokkeerd totdat de aanwijzer Geavanceerd is als u besluit de aanwijzer op dezelfde plaats te houden. Met andere woorden, als de wijzer is gestopt voor het oplossen van problemen met het verwerken van één gebeurtenis, beginnen de niet-verwerkte gebeurtenissen Piling omhoog.

Azure Functions maakt deadlocken ongedaan door de aanwijzer van de stream te verrijken, ongeacht het slagen of mislukken. Omdat de aanwijzer steeds goed gaat, moeten uw functies de juiste problemen oplossen.

## <a name="how-azure-functions-consumes-event-hubs-events"></a>Hoe Azure Functions Event Hubs gebeurtenissen verbruikt?

Azure Functions gebruikt Event hub-gebeurtenissen tijdens het uitvoeren van de volgende stappen:

1. Er wordt een wijzer gemaakt en opgeslagen in Azure Storage voor elke partitie van de Event Hub.
2. Wanneer er nieuwe berichten worden ontvangen (in een batch standaard), probeert de host de functie te activeren met de batch berichten.
3. Als de functie wordt uitgevoerd (met of zonder uitzonde ring), worden de wijzer voorschotten en een controle punt opgeslagen in het opslag account.
4. Als er omstandigheden zijn waardoor de uitvoering van de functie niet kan worden voltooid, mislukt de host de aanwijzer. Als de aanwijzer niet geavanceerd is, controleert later de verwerking van dezelfde berichten.
5. Herhaal stap 2 tot en met 4

Dit gedrag toont enkele belang rijke punten:

- *Onverwerkte uitzonde ringen kunnen ertoe leiden dat berichten verloren gaan.* Uitvoeringen die leiden tot een uitzonde ring, blijven de wijzers volgen.  Als u een [beleid voor opnieuw proberen](./functions-bindings-error-pages.md#retry-policies-preview) instelt, wordt de voortgang van de aanwijzer vertraagd totdat het volledige beleid voor opnieuw proberen is geëvalueerd.
- *Functions garanderen mini maal eenmalige levering.* Uw code en afhankelijke systemen moeten mogelijk worden [verwerkt om het feit dat hetzelfde bericht twee keer kan worden ontvangen](./functions-idempotent.md).

## <a name="handling-exceptions"></a>Afhandeling van uitzonderingen

Als algemene regel moet elke functie een [try/catch-blok](./functions-bindings-error-pages.md) op het hoogste niveau van code bevatten. Met name alle functies die Event Hubs gebeurtenissen gebruiken, moeten een `catch` blok hebben. Op die manier wordt, wanneer er een uitzonde ring wordt gegenereerd, de fout door de blok kering verwerkt voordat de aanwijzer in voortgang is.

### <a name="retry-mechanisms-and-policies"></a>Mechanismen en beleids regels voor opnieuw proberen

Sommige uitzonde ringen zijn tijdelijk van aard en worden niet opnieuw weer gegeven wanneer een bewerking later opnieuw wordt geprobeerd. Daarom is de eerste stap altijd om de bewerking opnieuw uit te voeren.  U kunt gebruikmaken van het [beleid voor opnieuw proberen](./functions-bindings-error-pages.md#retry-policies-preview) van de functie-app of de logica voor het opnieuw proberen van de functie in de uitvoering van

Door het gedrag van fout afhandeling in uw functies te introduceren, kunt u het basis beleid voor opnieuw proberen te definiëren. U kunt bijvoorbeeld een beleid implementeren dat volgt op een werk stroom die wordt geïllustreerd door de volgende regels:

- Probeer een bericht drie keer in te voegen (mogelijk met een vertraging tussen nieuwe pogingen).
- Als het uiteindelijke resultaat van alle nieuwe pogingen een fout is, voegt u een bericht toe aan een wachtrij, zodat de verwerking kan door gaan op de stroom.
- Beschadigde of niet-verwerkte berichten worden later verwerkt.

> [!NOTE]
> [Polly](https://github.com/App-vNext/Polly) is een voor beeld van een bibliotheek met tolerantie en tijdelijke afhandeling voor C#-toepassingen.

## <a name="non-exception-errors"></a>Niet-uitzonderings fouten

Er treden problemen op, zelfs wanneer er geen fout is gevonden. Neem bijvoorbeeld een fout op die in het midden van een uitvoering optreedt. Als de uitvoering van een functie niet is voltooid, wordt de verschuivings verwijzing nooit uitgevoerd. Als de wijzer niet verder gaat, blijft elke instantie die wordt uitgevoerd nadat de uitvoering is mislukt, dezelfde berichten lezen. Deze situatie biedt een garantie van Mini maal één keer.

De zekerheid dat elk bericht ten minste één keer wordt verwerkt, houdt in dat sommige berichten meermaals kunnen worden verwerkt. Uw functie-apps moeten op de hoogte zijn van deze mogelijkheid en moeten worden gebouwd rond de [beginselen van idempotentie](./functions-idempotent.md).

## <a name="stop-and-restart-execution"></a>Uitvoering stoppen en opnieuw starten

Maar een paar fouten kunnen acceptabel zijn, wat als uw app aanzienlijke problemen ondervindt? U kunt het activeren van gebeurtenissen stoppen totdat het systeem een goede status heeft bereikt. De kans om de verwerking te onderbreken, wordt vaak bereikt met een circuit onderbreker patroon. Met het patroon circuit onderbreker kan uw app het circuit van het gebeurtenis proces ' verbreekt ' en op een later tijdstip hervatten.

Er zijn twee onderdelen vereist voor het implementeren van een circuit onderbreker in een gebeurtenis proces:

- Gedeelde status voor alle exemplaren om de status van het circuit bij te houden en te bewaken
- Hoofd proces dat de status van het circuit kan beheren (open of gesloten)

Implementatie details kunnen variëren, maar als u de status wilt delen tussen instanties, hebt u een opslag mechanisme nodig. U kunt ervoor kiezen om de status op te slaan in Azure Storage, een redis-cache of een ander account dat toegankelijk is voor een verzameling functies.

[Azure Logic apps](../logic-apps/logic-apps-overview.md) of [duurzame functies](./durable/durable-functions-overview.md) zijn natuurlijk geschikt voor het beheren van de werk stroom en de status van het circuit. Andere services werken mogelijk net zo goed, maar logische apps worden gebruikt voor dit voor beeld. Met Logic apps kunt u de uitvoering van een functie onderbreken en opnieuw starten, zodat u de controle hebt die vereist is voor het implementeren van het patroon circuit onderbreker.

### <a name="define-a-failure-threshold-across-instances"></a>Een drempel waarde voor fouten voor alle instanties definiëren

Voor het verwerken van meerdere instanties waarbij gebeurtenissen tegelijkertijd worden verwerkt, is de status van het circuit te controleren.

Een regel die u kunt implementeren, kan afdwingen dat:

- Als er binnen 30 seconden meer dan 100 storingen optreden, verbreekt u het circuit en stopt u het activeren van nieuwe berichten.

De implementatie details zijn afhankelijk van uw behoeften, maar in het algemeen kunt u een systeem maken dat:

1. Fouten vastleggen in een opslag account (Azure Storage, redis, enzovoort)
1. Als er een nieuwe fout wordt geregistreerd, inspecteert u het aantal rollen om te zien of aan de drempel waarde is voldaan (bijvoorbeeld meer dan 100 in de afgelopen 30 seconden).
1. Als aan de drempel waarde wordt voldaan, moet u een gebeurtenis verzenden om Azure Event Grid te vertellen dat het systeem het circuit verbreekt.

### <a name="managing-circuit-state-with-azure-logic-apps"></a>De status van een circuit beheren met Azure Logic Apps

De volgende beschrijving geeft een manier waarop u een Azure Logic-app kunt maken om de verwerking van een functions-app te stoppen.

Azure Logic Apps wordt geleverd met ingebouwde connectors voor verschillende services, kenmerken stateful en is een natuurlijke keuze om de status van een circuit te beheren. Nadat het circuit is gedetecteerd dat moet worden onderbroken, kunt u een logische app maken om de volgende werk stroom te implementeren:

1. Een Event Grid werk stroom activeren en de Azure-functie stoppen (met de Azure resource connector)
1. Een e-mail melding verzenden met een optie om de werk stroom opnieuw te starten

De ontvanger van het e-mail bericht kan de status van het circuit onderzoeken en, indien nodig, het circuit opnieuw starten via een koppeling in de e-mail melding. Als de werk stroom de functie opnieuw start, worden berichten van het laatste event hub-controle punt verwerkt.

Wanneer u deze methode gebruikt, gaan er geen berichten verloren, worden alle berichten in de juiste volg orde verwerkt en kunt u het circuit zo lang als nodig verstoren.

## <a name="resources"></a>Resources

- [Voor beelden van betrouw bare gebeurtenis verwerking](https://github.com/jeffhollan/functions-csharp-eventhub-ordered-processing)
- [Circuit onderbreker van de duurzame Azure-entiteit](https://github.com/jeffhollan/functions-durable-actor-circuitbreaker)

## <a name="next-steps"></a>Volgende stappen

Zie de volgende resources voor meer informatie:

- [Fout afhandeling Azure Functions](./functions-bindings-error-pages.md)
- [Formaat van geüploade afbeeldingen automatisch wijzigen met Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json&tabs=dotnet)
- [Een functie maken die kan worden geïntegreerd met Azure Logic Apps](./functions-twitter-email.md)
