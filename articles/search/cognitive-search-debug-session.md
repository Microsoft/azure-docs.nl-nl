---
title: Concepten van debug-sessies (preview-versie)
titleSuffix: Azure Cognitive Search
description: Het opsporen van sessies, die toegankelijk zijn via de Azure Portal, biedt een IDE-achtige omgeving waarin u fouten kunt identificeren en herstellen, wijzigingen moet valideren en wijzigingen in de vaardig heden in de AI-verrijkings pijplijn naar een andere pusht. Debug-sessies is in preview.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: f666cc2f9d11b1e05d11ba266897e6fca3cd287e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92057617"
---
# <a name="debug-sessions-in-azure-cognitive-search"></a>Sessies voor fout opsporing in azure Cognitive Search

Debug-sessies is een visuele editor die werkt met een bestaande vaardig heden in de Azure Portal. Binnen een foutopsporingssessie kunt u fouten identificeren en oplossen, wijzigingen valideren en wijzigingen naar een productie vaardighedenset pushen in de AI-verrijkings pijplijn.

> [!Important]
> Foutopsporingssessies is een preview-functie die beschikbaar is service level agreement en niet aanbevolen wordt voor productieworkloads. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.
>

## <a name="using-debug-sessions"></a>Foutopsporingssessie gebruiken

Wanneer u een sessie start, maakt de service een kopie van de vaardig heden, Indexeer functie en index, waarbij één document wordt gebruikt om de vaardig heden te testen. Wijzigingen die in de sessie zijn aangebracht, worden opgeslagen in de sessie. De wijzigingen die zijn aangebracht in de foutopsporingssessie, hebben geen invloed op de productie-vaardigheids, tenzij de wijzigingen worden doorgevoerd. Als u wijzigingen doorvoert, wordt de productie-vaardigheidset overschreven.

Tijdens een foutopsporingssessie kunt u een vaardig heden bewerken, inspecteren en valideren elk knoop punt in de verrijkings structuur in de context van een specifiek document. Zodra de problemen met de verrijkings pijplijn zijn opgelost, kunnen wijzigingen worden opgeslagen in de sessie en worden vastgelegd in de vaardig heden in de productie omgeving. 

Als de verrijkings pijplijn geen fouten bevat, kan een foutopsporingssessie worden gebruikt om een document stapsgewijs te verrijken, elke wijziging te testen en te valideren voordat de wijzigingen worden doorgevoerd.

## <a name="creating-a-debug-session"></a>Een foutopsporingssessie maken

Als u een foutopsporingssessie wilt starten, moet u een bestaande AI-verrijkings pijplijn hebben, waaronder; een gegevens bron, een vaardig heden, een indexer en een index. Als u een foutopsporingssessie wilt configureren, moet u de sessie een naam geven en een opslag account voor algemeen gebruik opgeven dat wordt gebruikt om de vaardigheids uitvoeringen in de cache op te slaan tijdens de uitvoering van de Indexeer functie. U moet ook de Indexeer functie selecteren die wordt uitgevoerd. De Indexeer functie heeft verwijzingen die zijn opgeslagen in de gegevens bron, de vaardig heden en de index. De foutopsporingssessie wordt standaard ingesteld op het eerste document in de gegevens bron of u kunt een document opgeven in de gegevens bron om door te gaan.

> :::image type="content" source="media/cognitive-search-debug/debug-session-new.png" alt-text="Een foutopsporingssessie maken":::

## <a name="debug-session-features"></a>Probleemoplossings sessie functies

De foutopsporingssessie begint met het uitvoeren van de vaardig heden op het geselecteerde document. In de sessie fout opsporing worden aanvullende meta gegevens vastgelegd die zijn gekoppeld aan elke bewerking in de vaardig heden. De meta gegevens die door de vaardigheids uitvoering van de pijp lijn zijn gemaakt, informeert de volgende set functies die vervolgens worden gebruikt om problemen met de vaardig heden te identificeren en op te lossen.

## <a name="ai-enrichments"></a>AI-verrijkingen

Naarmate vaardig heden een structuur van verrijkingen uitvoeren, die het document vertegenwoordigen, groeit. Het gebruik van een structuur voor het visualiseren van de uitvoer van vaardig heden of verrijkingen biedt een uitgebreid overzicht van de uitgevoerde verrijkingen. U kunt in het hele document zoeken en elk knoop punt van de verrijkings structuur controleren. Dit perspectief maakt het eenvoudiger om objecten vorm te geven. Deze indeling biedt ook visuele aanwijzingen voor het type, het pad en de inhoud van elk knoop punt in de structuur.

## <a name="skill-graph"></a>Vaardigheids grafiek

De weer gave **vaardigheids grafiek** biedt een hiërarchische, visuele representatie van de vaardig heden. De grafiek is een weer gave van boven naar beneden van de volg orde waarin de vaardig heden worden uitgevoerd. De vaardig heden die afhankelijk zijn van de uitvoer van andere vaardig heden, worden lager weer gegeven in de grafiek. De vaardig heden op hetzelfde niveau in de hiërarchie kunnen parallel worden uitgevoerd. 

Als u een vaardigheid in de grafiek selecteert, worden de bijbehorende vaardig heden gemarkeerd, de knoop punten die de invoer hebben en de knoop punten die de uitvoer ervan accepteren. Elk vaardigheids knooppunt geeft het type, de fouten of waarschuwingen en het aantal uitvoeringen weer. In de **vaardigheids grafiek** selecteert u welke vaardigheid u wilt opsporen of uitbreiden. Wanneer u een vaardigheid selecteert, worden de bijbehorende gegevens weer gegeven in het deel venster vaardigheids Details rechts van de grafiek.

> :::image type="content" source="media/cognitive-search-debug/skills-graph.png" alt-text="Vaardigheids grafiek":::

## <a name="skill-details"></a>Details van vaardigheid

In het deel venster vaardigheids Details wordt een set met gebieden weer gegeven voor het werken met een specifieke vaardigheid, wanneer die kwalificatie is gemarkeerd in de **vaardigheids grafiek**. U kunt de details van de instellingen van de vaardigheid bekijken en bewerken. De JSON-definitie van de vaardigheid wordt weer gegeven. De details van de uitvoering van de vaardigheid en de fouten en waarschuwingen worden ook weer gegeven. Op het tabblad **vaardigheids instellingen** & **VAARDIGHEIDs-JSON-editor** kunt u de vaardigheid direct bewerken. [`</>`](#expression-evaluator)Hiermee opent u een venster voor het weer geven en bewerken van de expressies van de invoer en uitvoer van de vaardig heden.

Geneste invoer besturings elementen in het venster vaardigheids instellingen kunnen worden gebruikt voor het bouwen van complexe vormen voor projecties, uitvoer veld toewijzingen voor een complex type veld of een invoer voor een kwalificatie. Bij gebruik in combi natie met de **expressie-evaluator** bieden geneste invoer een eenvoudige test en validatie van de opbouw functie voor expressies.

## <a name="skill-execution-history"></a>Vaardigheids uitvoerings geschiedenis

Een vaardigheid kan meerdere keren in een vaardighedenset voor één document worden uitgevoerd. De OCR-vaardigheid wordt bijvoorbeeld één keer uitgevoerd voor elke afbeelding die uit elk document is geëxtraheerd. In het deel venster vaardigheids Details op het tabblad **uitvoeringen** wordt de uitvoerings geschiedenis van de vaardigheid weer gegeven met een diep gaande blik op elke aanroep van de vaardigheid. 

Met de uitvoerings geschiedenis kunt u een specifieke verrijking terugvolgen op de vaardigheid waarmee deze is gegenereerd. Als u op een vaardigheids invoer klikt, gaat u naar de kwalificatie die deze invoer heeft gegenereerd. Dit maakt het mogelijk om de hoofd oorzaak van een probleem te identificeren dat in een downstream-vaardigheid kan worden gemanifesteerd. 

Wanneer een mogelijk probleem wordt geïdentificeerd, worden in de uitvoerings geschiedenis koppelingen naar de vaardig heden weer gegeven die de specifieke invoer hebben gegenereerd, waardoor een stack-trace lijkt op een functie. Door te klikken op de vaardigheid die aan een invoer is gekoppeld, gaat u naar de vaardigheid om eventuele fouten op te lossen of gaat u door met het traceren van het specifieke probleem.

Wanneer u een aangepaste vaardigheid bouwt of een fout opspoort met een aangepaste vaardigheid, is er een optie voor het genereren van een aanvraag voor het aanroepen van een kwalificatie in de uitvoerings geschiedenis.

## <a name="enriched-data-structure"></a>Verrijkte gegevensstructuur

In het deel venster **verrijkte gegevens structuur** worden de verrijkingen van het document via de vaardig heden weer gegeven, met een gedetailleerde beschrijving van de context voor elke verrijking en de oorspronkelijke vaardigheid. De **expressie-evaluator** kan ook worden gebruikt om de inhoud van elke verrijking weer te geven.

> :::image type="content" source="media/cognitive-search-debug/enriched-data-structure-display.png" alt-text="Verrijkte gegevensstructuur":::

## <a name="expression-evaluator"></a>Expressie-Evaluator

De **expressie-evaluator** geeft een kort overzicht van de waarde van een pad. Hiermee kunt u het pad bewerken en de resultaten testen voordat u een van de invoer of context voor een vaardigheid of projectie bijwerkt.

## <a name="errorswarnings"></a>Fouten/waarschuwingen

In dit venster worden alle fouten en waarschuwingen weer gegeven die door de vaardigy worden gegenereerd, zoals deze worden uitgevoerd op basis van het document in de foutopsporingssessie.

## <a name="limitations"></a>Beperkingen

Debug sesisons werkt met alle algemeen beschik bare gegevens bronnen AMD de meeste preview-gegevens bronnen. De MongoDB-API (preview) en Cassandra-API (preview) van Cosmos DB worden momenteel niet ondersteund.

## <a name="next-steps"></a>Volgende stappen

Nu u inzicht hebt in de onderdelen van debug-sessies, voert u de zelf studie uit voor een praktijk ervaring.

> [!div class="nextstepaction"]
> [Zelf studie fout opsporings sessies verkennen](./cognitive-search-tutorial-debug-sessions.md)