---
title: Automatische prestaties, kosten, beveiligings aanbevelingen voor Azure Cosmos DB
description: Meer informatie over het weer geven van aangepaste prestaties, kosten, beveiliging en andere aanbevelingen voor Azure Cosmos DB op basis van uw werkbelasting patronen.
author: ThomasWeiss
ms.author: thweiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/28/2020
ms.reviewer: sngun
ms.openlocfilehash: d9eb55030c7ec52f9b2ac79fbab19944f0a3e190
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93087822"
---
# <a name="automated-recommendations-for-azure-cosmos-db"></a>Automatische aanbevelingen voor Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Alle Cloud Services met inbegrip van Azure Cosmos DB frequente updates ontvangen met nieuwe functies, mogelijkheden en verbeteringen. Het is belang rijk om ervoor te zorgen dat uw toepassing de nieuwste prestatie-en beveiligings updates bijwerkt. De Azure Portal biedt aangepaste aanbevelingen waarmee u de prestaties van uw toepassing kunt maximaliseren. De Advisor-engine van Azure Cosmos DB analyseert voortdurend de gebruiks geschiedenis van uw Azure Cosmos DB resources en biedt aanbevelingen op basis van uw werkbelasting patronen. Deze aanbevelingen komen overeen met gebieden zoals partitioneren, indexeren, netwerk, beveiliging, enzovoort. Deze aangepaste aanbevelingen helpen u bij het verbeteren van de prestaties van uw toepassing.

## <a name="view-recommendations"></a>Aanbevelingen bekijken

U kunt op de volgende manieren aanbevelingen voor Azure Cosmos DB bekijken:

- Een manier om de aanbevelingen weer te geven, vindt u op het tabblad meldingen. Als er nieuwe aanbevelingen zijn, wordt er een berichten balk weer gegeven. Meld u aan bij uw [Azure Portal](https://portal.azure.com) en navigeer naar uw Azure Cosmos-account. Open in uw Azure Cosmos-account het deel venster **meldingen** en selecteer vervolgens het tabblad **aanbevelingen** . U kunt het bericht selecteren en aanbevelingen weer geven.  

   :::image type="content" source="./media/automated-recommendations/cosmos-db-pane-recommendations.png" alt-text="Aanbevelingen uit Azure Cosmos DB deel venster weer geven":::

- U kunt ook de aanbevelingen vinden via [Azure Advisor](../advisor/advisor-overview.md) in gecategoriseerd door verschillende buckets, zoals kosten, beveiliging, betrouw baarheid, prestaties en bedrijfs kwaliteit. U kunt specifieke abonnementen selecteren en filteren op het resource type. Dit is **Azure Cosmos DB accounts**.  Wanneer u een specifieke aanbeveling selecteert, worden de acties weer gegeven die u kunt uitvoeren om uw werk belastingen te voors delen.

   :::image type="content" source="./media/automated-recommendations/advisor-pane-recommendations.png" alt-text="Aanbevelingen uit Azure Advisor deel venster weer geven":::

Niet alle aanbevelingen die worden weer gegeven in het deel venster Azure Cosmos DB, zijn beschikbaar in de Azure Advisor en vice versa. Dit komt doordat, op basis van het type aanbeveling dat ze in het deel venster Azure Advisor, Azure Cosmos DB deel venster of beide.

Momenteel Azure Cosmos DB de aanbevelingen voor de volgende gebieden worden ondersteund. Elk van deze aanbevelingen bevat een koppeling naar de relevante sectie van de documentatie, zodat u gemakkelijk de volgende stappen kunt uitvoeren.

## <a name="sdk-usage-recommendations"></a>Aanbevelingen voor SDK-gebruik

In deze categorie detecteert de Advisor het gebruik van een oude versie van Sdk's en raadt u aan om een upgrade naar een nieuwere versie uit te kunnen zetten om gebruik te maken van de nieuwste oplossingen voor problemen en prestatie verbeteringen. Momenteel zijn de volgende SDK-specifieke aanbevelingen beschikbaar:

|Naam  |Beschrijving  |
|---------|---------|
| Oude Spark-connector | Detecteert het gebruik van oude versies van de Spark-connector en raadt u aan om bij te werken. |
| Oude .NET SDK | Detecteert het gebruik van oude versies van de .NET SDK en raadt u aan om bij te werken. |
| Oude Java-SDK | Hiermee wordt het gebruik van oude versies van de Java-Connector gedetecteerd en wordt aanbevolen om bij te werken. |

## <a name="indexing-recommendations"></a>Aanbevelingen indexeren

In deze categorie detecteert de Advisor de indexerings modus, het indexerings beleid, de geïndexeerde paden en wordt aanbevolen om te wijzigen als de huidige configuratie van invloed is op de query prestaties. Momenteel zijn de volgende indexerings-specifieke aanbevelingen beschikbaar:

|Naam  |Beschrijving  |
|---------|---------|
| Luie indexering | Detecteert het gebruik van de vertraagde indexerings modus en beveelt in plaats daarvan een consistente indexerings modus aan. Het doel van de vertraagde indexerings modus van Azure Cosmos DB is beperkt en kan van invloed zijn op de versheid van query resultaten in bepaalde situaties, zodat de consistente indexerings modus wordt aanbevolen. |
| Samengestelde indexering| Detecteert de accounts waarop query's kunnen profiteren van samengestelde indexen en om deze te gebruiken. Samengestelde indexen kunnen het verbruik van de prestaties en de door Voer van sommige query's aanzienlijk verbeteren.|
| Standaard indexerings beleid met veel geïndexeerde paden | Detecteert containers die worden uitgevoerd op standaard indexering met veel geïndexeerde paden en raadt aan het indexerings beleid aan te passen.|
| Volg orde van query's met hoge RU/s-kosten| Detecteert containers die worden uitgegeven door query's met hoge RU/s-kosten en raadt aan om samengestelde indexen te verkennen.|
| MongoDB 3,6-accounts zonder index en hoge RU/s-verbruik| Detecteert de API van Azure Cosmos DB voor MongoDB met 3,6-versie van containers die query's met hoge RU/s-kosten uitgeven en aanbeveelt om indexen toe te voegen.|

## <a name="cost-optimization-recommendations"></a>Aanbevelingen voor kosten optimalisatie

In deze categorie detecteert de Advisor het gebruik van RU/s en bepaalt u of u de prijs kunt optimaliseren door enkele wijzigingen aan te brengen in uw resources of door gebruik te maken van een ander prijs model. Momenteel zijn de volgende specifieke aanbevelingen voor kosten optimalisatie beschikbaar:

|Naam  |Beschrijving  |
|---------|---------|
| Gereserveerde capaciteit | Detecteert uw gebruik van RU/s en beveelt gereserveerde instanties aan voor gebruikers die er van kunnen profiteren. |
| Inactieve containers | Detecteert de containers die meer dan 30 dagen niet zijn gebruikt en raadt aan de door Voer voor dergelijke containers te verminderen of ze te verwijderen.|
| Nieuwe abonnementen met hoge door Voer | Detecteert nieuwe abonnementen met accounts die ongebruikelijk hoge RU/s per dag best Eden en biedt hen een melding. Deze melding is specifiek bedoeld om nieuwe klanten te laten weten dat Azure Cosmos DB actief is op het ingerichte model op basis van door Voer en niet op basis van verbruik. |

## <a name="migration-recommendations"></a>Aanbevelingen voor migratie

In deze categorie detecteert de Advisor dat u gebruikmaakt van verouderde functies, wordt gemigreerd, zodat u kunt profiteren van de grote schaal baarheid en andere voor delen van Azure Cosmos DB. Momenteel zijn de volgende migratie-specifieke aanbevelingen beschikbaar:

|Naam  |Beschrijving  |
|---------|---------|
| Niet-gepartitioneerde containers | Detecteert containers met een vaste grootte die de maximale opslag limiet nadert en raadt u aan om ze te migreren naar gepartitioneerde containers.|

## <a name="query-usage-recommendations"></a>Aanbevelingen voor query gebruik

In deze categorie detecteert de Advisor de uitvoering van de query en wordt geïdentificeerd dat de query prestaties kunnen worden afgestemd op een aantal wijzigingen. Momenteel zijn de volgende aanbevelingen voor query gebruik beschikbaar:

|Naam  |Beschrijving  |
|---------|---------|
| Query's met vaste pagina grootte | Detecteert query's die zijn uitgegeven met een vaste pagina grootte en raadt aan om-1 (geen limiet voor de pagina grootte) te gebruiken in plaats van een specifieke waarde te definiëren. Deze optie vermindert het aantal netwerk round trips dat nodig is om alle resultaten op te halen. |

## <a name="next-steps"></a>Volgende stappen

* [Query prestaties afstemmen in Azure Cosmos DB](sql-api-query-metrics.md)
* [Problemen met Query's oplossen](troubleshoot-query-performance.md) bij het gebruik van Azure Cosmos db
