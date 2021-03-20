---
title: Serverloze data base Computing met Azure Cosmos DB en Azure Functions
description: Meer informatie over hoe Azure Cosmos DB en Azure Functions kunnen worden gebruikt voor het maken van op gebeurtenissen gebaseerde serverloze computing-apps.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 07/17/2019
ms.author: sngun
ms.openlocfilehash: 73a34cc27eaba33d04f4d31585c7f494f58e7274
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93334069"
---
# <a name="serverless-database-computing-using-azure-cosmos-db-and-azure-functions"></a>Serverloze database Computing met Azure Cosmos DB en Azure Functions
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Serverloze computing is de mogelijkheid om te focussen op afzonderlijke stukjes logica die herhaalbaar en stateless zijn. Voor deze onderdelen is geen infrastructuur beheer vereist en worden bronnen alleen gebruikt voor de seconden of milliseconden waarvoor ze worden uitgevoerd. De kern van de serverloze computing bewegingen zijn functies die beschikbaar worden gesteld in het Azure-ecosysteem door [Azure functions](https://azure.microsoft.com/services/functions). Zie [serverloze op Azure](https://azure.microsoft.com/solutions/serverless/) -pagina voor meer informatie over andere serverloze uitvoerings omgevingen in Azure. 

Met de systeem eigen integratie tussen [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db) en Azure functions kunt u database triggers, invoer bindingen en uitvoer bindingen rechtstreeks vanuit uw Azure Cosmos DB-account maken. Met behulp van Azure Functions en Azure Cosmos DB kunt u op gebeurtenissen gebaseerde apps zonder latentie maken en implementeren met toegang met lage wacht tijd tot uitgebreide gegevens voor een globale gebruikers database.

## <a name="overview"></a>Overzicht

Met Azure Cosmos DB en Azure Functions kunt u uw data bases en serverloze apps op de volgende manieren integreren:

* Maak een gebeurtenis gerichte **Azure functions trigger voor Cosmos DB**. Deze trigger is afhankelijk van de wijzigingen in de [feed](change-feed.md) voor het bewaken van de Azure Cosmos-container. Wanneer er wijzigingen worden aangebracht in een container, wordt de wijzigings stroom van de feed verzonden naar de trigger, waarmee de Azure-functie wordt aangeroepen.
* U kunt ook een Azure-functie binden aan een Azure Cosmos-container met behulp van een **invoer binding**. Invoer bindingen lezen gegevens uit een container wanneer een functie wordt uitgevoerd.
* Een functie binden aan een Azure Cosmos-container met behulp van een **uitvoer binding**. Met uitvoer bindingen worden gegevens naar een container geschreven wanneer een functie is voltooid.

> [!NOTE]
> Momenteel worden Azure Functions trigger, invoer bindingen en uitvoer bindingen voor Cosmos DB alleen ondersteund voor gebruik met de SQL-API. Voor alle andere Azure Cosmos DB Api's moet u de data base vanuit uw functie openen met behulp van de statische client voor uw API.


Het volgende diagram illustreert elk van deze drie integraties: 

:::image type="content" source="./media/serverless-computing-database/cosmos-db-azure-functions-integration.png" alt-text="Hoe Azure Cosmos DB en Azure Functions worden geïntegreerd" border="false":::

De Azure Functions trigger, invoer binding en uitvoer binding voor Azure Cosmos DB kunnen worden gebruikt in de volgende combi Naties:

* Een Azure Functions trigger voor Cosmos DB kan worden gebruikt met een uitvoer binding naar een andere Azure Cosmos-container. Nadat een functie een actie heeft uitgevoerd op een item in de wijzigings feed, kunt u deze naar een andere container schrijven (het schrijven naar de container van waaruit deze afkomstig is en een recursieve lus maken). U kunt ook een Azure Functions trigger gebruiken voor Cosmos DB om alle gewijzigde items van de ene container naar een andere container te migreren, met het gebruik van een uitvoer binding.
* Invoer bindingen en uitvoer bindingen voor Azure Cosmos DB kunnen worden gebruikt in dezelfde Azure-functie. Dit werkt goed in gevallen waarin u bepaalde gegevens wilt zoeken met de invoer binding, deze wilt wijzigen in de Azure-functie en deze vervolgens opslaat in dezelfde container of een andere container nadat de wijziging is aangebracht.
* Een invoer binding met een Azure Cosmos-container kan worden gebruikt in dezelfde functie als een Azure Functions trigger voor Cosmos DB en kan ook worden gebruikt met of zonder een uitvoer binding. U kunt deze combi natie gebruiken om actuele informatie over valuta uitwisseling toe te passen (met een invoer binding aan een Exchange-container) aan de wijzigings feed van nieuwe orders in uw winkel wagen-service. Het bijgewerkte winkel wagen totaal, met de huidige valuta conversie, kan naar een derde container worden geschreven met behulp van een uitvoer binding.

## <a name="use-cases"></a>Gebruiksvoorbeelden

In de volgende use-cases ziet u een aantal manieren waarop u het meren deel van uw Azure Cosmos DB gegevens kunt maken door uw gegevens te koppelen aan gebeurtenis gerichte Azure Functions.

### <a name="iot-use-case---azure-functions-trigger-and-output-binding-for-cosmos-db"></a>IoT use case-Azure Functions-trigger en uitvoer binding voor Cosmos DB

In IoT-implementaties kunt u een functie aanroepen wanneer het lampje van de controle machine wordt weer gegeven in een verbonden auto.

**Implementatie:** Een Azure Functions trigger en uitvoer binding gebruiken voor Cosmos DB

1. Een **Azure functions trigger voor Cosmos DB** wordt gebruikt voor het activeren van gebeurtenissen die zijn gerelateerd aan auto waarschuwingen, zoals het lampje van de controle-engine in een verbonden auto.
2. Wanneer het lampje van de controle machine aankomt, worden de sensor gegevens verzonden naar Azure Cosmos DB.
3. Azure Cosmos DB nieuwe sensor gegevens documenten maakt of bijwerkt, worden deze wijzigingen gestreamd naar de Azure Functions trigger voor Cosmos DB.
4. De trigger wordt aangeroepen bij elke gegevens-wijziging van de sensor gegevens verzameling, omdat alle wijzigingen worden gestreamd via de wijzigings feed.
5. Er wordt een drempel waarde gebruikt in de functie om de sensor gegevens naar de garantie afdeling te verzenden.
6. Als de Tempe ratuur ook een bepaalde waarde heeft, wordt er ook een waarschuwing verzonden naar de eigenaar.
7. De **uitvoer binding** voor de functie werkt de auto-record in een andere Azure Cosmos-container bij om informatie over de controle-engine gebeurtenis op te slaan.

In de volgende afbeelding ziet u de code die is geschreven in de Azure Portal voor deze trigger.

:::image type="content" source="./media/serverless-computing-database/cosmos-db-trigger-portal.png" alt-text="Een Azure Functions trigger maken voor Cosmos DB in de Azure Portal":::

### <a name="financial-use-case---timer-trigger-and-input-binding"></a>Financiële gebruiks Case-timer trigger en invoer binding

In financiële implementaties kunt u een functie aanroepen wanneer een saldo van een bank account onder een bepaald bedrag valt.

**Implementatie:** Een timer trigger met een Azure Cosmos DB invoer binding

1. Met een [Timer trigger](../azure-functions/functions-bindings-timer.md)kunt u de gegevens van het bankrekening saldo ophalen die zijn opgeslagen in een Azure Cosmos-container met behulp van een **invoer binding**.
2. Als het saldo lager is dan de drempel waarde voor het laagste saldo dat door de gebruiker is ingesteld, voert u een actie uit vanuit de Azure-functie.
3. De uitvoer binding kan een [SendGrid-integratie](../azure-functions/functions-bindings-sendgrid.md) zijn die een e-mail verzendt van een service account naar de e-mail adressen die voor elk van de accounts met een lage balans worden geïdentificeerd.

De volgende afbeeldingen tonen de code in de Azure Portal voor dit scenario.

:::image type="content" source="./media/serverless-computing-database/cosmos-db-functions-financial-trigger.png" alt-text="Index.js-bestand voor een timer trigger voor een financieel scenario":::

:::image type="content" source="./media/serverless-computing-database/azure-function-cosmos-db-trigger-run.png" alt-text="Het. CSX-bestand uitvoeren voor een timer trigger voor een financieel scenario":::

### <a name="gaming-use-case---azure-functions-trigger-and-output-binding-for-cosmos-db"></a>Gebruiks voorbeeld van games-Azure Functions trigger-en uitvoer binding voor Cosmos DB 

Wanneer een nieuwe gebruiker is gemaakt in gaming, kunt u zoeken naar andere gebruikers die deze kunnen kennen met behulp van de [Azure Cosmos DB GREMLIN API](graph-introduction.md). U kunt de resultaten vervolgens naar een Azure Cosmos DB of SQL database schrijven om het eenvoudig op te halen.

**Implementatie:** Een Azure Functions trigger en uitvoer binding gebruiken voor Cosmos DB

1. Een Azure Cosmos DB [Graph-data base](graph-introduction.md) gebruiken om alle gebruikers op te slaan, kunt u een nieuwe functie maken met een Azure functions trigger voor Cosmos db. 
2. Wanneer een nieuwe gebruiker wordt ingevoegd, wordt de functie aangeroepen en wordt het resultaat opgeslagen met behulp van een **uitvoer binding**.
3. De functie voert een query uit op de Graph-data base om te zoeken naar alle gebruikers die rechtstreeks zijn gerelateerd aan de nieuwe gebruiker en die DataSet retourneert naar de functie.
4. Deze gegevens worden vervolgens opgeslagen in een Azure Cosmos DB die vervolgens eenvoudig kunnen worden opgehaald door een front-end-toepassing die de nieuwe gebruiker hun verbonden vrienden weergeeft.

### <a name="retail-use-case---multiple-functions"></a>Use-case-meerdere functies

Wanneer een gebruiker in een retail-implementatie een item toevoegt aan hun mandje, beschikt u nu over de flexibiliteit om functies te maken en aan te roepen voor optionele onderdelen van de bedrijfs pijplijn.

**Implementatie:** Meerdere Azure Functions triggers voor het Cosmos DB naar een container Luis teren

1. U kunt meerdere Azure Functions maken door Azure Functions triggers voor Cosmos DB toe te voegen aan alle-alle, waarmee wordt geluisterd naar dezelfde wijzigings feed van de gegevens van de winkel wagen. Houd er rekening mee dat wanneer meerdere functies naar dezelfde feed worden geluisterd, een nieuwe lease-verzameling is vereist voor elke functie. Zie [informatie over het wijzigen van de processor bibliotheek voor feeds](change-feed-processor.md)voor meer informatie over lease verzamelingen.
2. Wanneer een nieuw item wordt toegevoegd aan de winkel wagen van een gebruiker, wordt elke functie onafhankelijk aangeroepen door de wijzigings feed van de winkel wagen.
   * Een functie kan de inhoud van de huidige mand gebruiken om de weer gave van andere items te wijzigen die de gebruiker mogelijk wil.
   * Een andere functie kan voorraad totalen bijwerken.
   * Een andere functie kan klant informatie voor bepaalde producten naar de marketing afdeling verzenden, die hen een promotie Mailer verzendt. 

     Elke afdeling kan een Azure Functions maken voor Cosmos DB door naar de wijzigings feed te Luis teren en ervoor te zorgen dat ze geen essentiële verwerking van gebeurtenissen in het proces vertragen.

In al deze use cases, omdat de functie de app zelf heeft losgekoppeld, hoeft u niet altijd nieuwe app-exemplaren in te stellen. In plaats daarvan Azure Functions worden afzonderlijke functies gerug om zo nodig discrete processen te volt ooien.

## <a name="tooling"></a>Hulpprogramma's

Systeem eigen integratie tussen Azure Cosmos DB en Azure Functions is beschikbaar in de Azure Portal en in Visual Studio 2019.

* In de Azure Functions Portal kunt u een trigger maken. Zie voor instructies voor Snelstartgids [een Azure functions trigger maken voor Cosmos db in de Azure Portal](../azure-functions/functions-create-cosmos-db-triggered-function.md).
* In de Azure Cosmos DB Portal kunt u een Azure Functions trigger voor Cosmos DB toevoegen aan een bestaande Azure function-app in dezelfde resource groep.
* In Visual Studio 2019 kunt u de trigger maken met behulp van de [Azure functions-Hulpprogram ma's](../azure-functions/functions-develop-vs.md):

    >[!VIDEO https://www.youtube.com/embed/iprndNsUeeg]

## <a name="why-choose-azure-functions-integration-for-serverless-computing"></a>Waarom Azure Functions integratie voor serverloze computing kiezen?

Azure Functions biedt de mogelijkheid om schaal bare werk eenheden of beknopte stukjes logica te maken die op aanvraag kunnen worden uitgevoerd, zonder dat u de infra structuur hoeft in te richten of te beheren. Als u Azure Functions gebruikt, hoeft u geen volledige, gepaarde app te maken om te reageren op wijzigingen in uw Azure Cosmos-data base. u kunt kleine herbruikbare functies maken voor specifieke taken. Daarnaast kunt u ook Azure Cosmos DB gegevens gebruiken als de invoer-of uitvoer naar een Azure-functie als reactie op gebeurtenis zoals een HTTP-aanvraag of een getimede trigger.

Azure Cosmos DB is de aanbevolen Data Base voor uw serverloze computer architectuur om de volgende redenen:

* **Directe toegang tot al uw gegevens**: u hebt gedetailleerde toegang tot elke opgeslagen waarde, omdat Azure Cosmos DB standaard alle gegevens [automatisch indexeert](index-policy.md) en deze indexen onmiddellijk beschikbaar maakt. Dit betekent dat u voortdurend in staat bent om nieuwe items te zoeken, bij te werken en toe te voegen aan uw data base en direct toegang te hebben via Azure Functions.

* **Schemaloos**. Azure Cosmos DB is zonder schema, zodat de gegevens uitvoer van een Azure-functie uniek kan worden verwerkt. Met deze aanpak van ' alles afhandelen ' maakt u het eenvoudig om allerlei functies te maken die alle uitvoer naar Azure Cosmos DB.

* **Schaal bare door Voer**. De door Voer kan direct omhoog en omlaag worden geschaald in Azure Cosmos DB. Als er honderden of duizenden functies zijn die query's uitvoeren en schrijven naar dezelfde container, kunt u uw [ru/s](request-units.md) omhoog schalen om de belasting te verwerken. Alle functies kunnen parallel werken met behulp van uw toegewezen RU/s en uw gegevens worden gegarandeerd [consistent](consistency-levels.md).

* **Globale replicatie**. U kunt Azure Cosmos DB gegevens [over de hele wereld](distribute-data-globally.md) repliceren om de latentie te verminderen, waardoor uw gegevens zich in de buurt bevinden waar uw gebruikers zich bevinden. Net als bij alle Azure Cosmos DB query's worden gegevens van door gebeurtenis gestuurde triggers gegevens gelezen van de Azure Cosmos DB die het dichtst bij de gebruiker ligt.

Als u wilt integreren met Azure Functions om gegevens op te slaan en geen diepe indexering nodig hebt of als u bijlagen en media bestanden wilt opslaan, is de [Azure Blob Storage-trigger](../azure-functions/functions-bindings-storage-blob.md) mogelijk een betere optie.

Voor delen van Azure Functions: 

* **Gebeurtenis-gestuurd**. Azure Functions zijn op gebeurtenissen gebaseerd en kunnen naar een wijzigings feed Luis teren vanuit Azure Cosmos DB. Dit betekent dat u geen luisterende logica hoeft te maken. u hoeft alleen maar een ogen effect te hebben voor de wijzigingen die u hebt geluisterd. 

* **Geen limieten**. Functies worden parallel uitgevoerd en de service draait zo veel als u wilt. U stelt de para meters in.

* **Goed voor snelle taken**. De service draait nieuwe instanties van functies op wanneer een gebeurtenis wordt geactiveerd en wordt gesloten zodra de functie is voltooid. U betaalt alleen voor de tijd dat uw functies worden uitgevoerd.

Als u niet zeker weet of flow, Logic Apps, Azure Functions of webjobs het meest geschikt zijn voor uw implementatie, raadpleegt u [kiezen tussen flow, Logic apps, functions en webjobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="next-steps"></a>Volgende stappen

We gaan nu verbinding maken Azure Cosmos DB en Azure Functions voor Real: 

* [Een Azure Functions trigger maken voor Cosmos DB in de Azure Portal](../azure-functions/functions-create-cosmos-db-triggered-function.md)
* [Een Azure Functions HTTP-trigger maken met een Azure Cosmos DB-invoerbinding](../azure-functions/functions-bindings-cosmosdb.md?tabs=csharp)
* [Bindingen en triggers Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md)
