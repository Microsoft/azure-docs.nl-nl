---
title: 'Zelfstudie: Een Java-web-app maken met Azure Cosmos DB en de SQL-API'
description: 'Zelfstudie: In deze zelfstudie over Java-webtoepassingen wordt uitgelegd hoe u Azure Cosmos DB en de SQL-API gebruikt voor het opslaan van en de toegang tot een Java-toepassing die wordt gehost op Azure Websites.'
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 01c9a8b62190f3b545169bb5ba6732e6799ff9f2
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105559788"
---
# <a name="tutorial-build-a-java-web-application-using-azure-cosmos-db-and-the-sql-api"></a>Zelfstudie: Een Java-webtoepassing maken met Azure Cosmos DB en de SQL-API
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](./create-sql-api-python.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

In deze zelfstudie over Java-webtoepassingen wordt uitgelegd hoe u de [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/)-service gebruikt voor het opslaan van en de toegang tot een Java-toepassing die wordt gehost in Azure App Service Web Apps. In dit artikel leert u het volgende:

* Een eenvoudige JSP-toepassing (JavaServer Pages) maken in Eclipse.
* Werken met de Azure Cosmos DB-service met behulp van de [Azure Cosmos DB Java SDK](https://github.com/Azure/azure-documentdb-java).

In deze zelfstudie over het maken van een Java-toepassing wordt uitgelegd hoe u een webtoepassing voor taakbeheer maakt waarmee u taken kunt maken, ophalen en als voltooid kunt markeren, zoals in de volgende afbeelding. Alle taken in de ToDo-lijst worden als JSON-documenten opgeslagen in Azure Cosmos DB.

:::image type="content" source="./media/sql-api-java-application/image1.png" alt-text="De Java-toepassing My ToDo List":::

> [!TIP]
> In deze zelfstudie voor het ontwikkelen van toepassingen wordt ervan uitgegaan dat u ervaring met Java hebt. Als u niet bekend bent met Java of de [vereiste hulpprogram ma's](#Prerequisites), raden we u aan het volledige project [TODO] https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app) te downloaden van github en het te bouwen met behulp van [de instructies aan het einde van dit artikel](#GetProject). Zodra u klaar bent, kunt u het artikel lezen voor meer informatie over de code in de context van het project.  
>

## <a name="prerequisites-for-this-java-web-application-tutorial"></a><a id="Prerequisites"></a>Vereisten voor deze zelfstudie over Java-webtoepassingen

Voordat u met deze zelfstudie over het ontwikkelen van toepassingen aan de slag gaat, moet u beschikken over het volgende:

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Java Development Kit (JDK) 7+](/java/azure/jdk/).
* [Eclipse IDE voor Java EE-ontwikkelaars.](https://www.eclipse.org/downloads/packages/release/luna/sr1/eclipse-ide-java-ee-developers)
* [Azure Web Sites waarvoor een Java Runtime Environment (bijvoorbeeld Tomcat of Jetty) is ingeschakeld.](../app-service/quickstart-java.md)

Als u deze hulpprogramma's voor het eerst installeert, kunt u op coreservlets.com in de quickstart-sectie van het artikel [Tutorial: Installing TomCat7 and Using it with Eclipse](https://www.youtube.com/watch?v=jOdCfW7-ybI&t=2s) (Zelfstudie: TomCat7 installeren en gebruiken met Eclipse) een overzicht van het installatieproces vinden.

## <a name="create-an-azure-cosmos-db-account"></a><a id="CreateDB"></a>Een Azure Cosmos DB-account maken

Begin met het maken van een Azure Cosmos DB-account. Als u al een account hebt of de Azure Cosmos DB-emulator gebruikt voor deze zelfstudie, kunt u direct doorgaan naar [Stap 2: De Java JSP-toepassing maken](#CreateJSP).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

## <a name="create-the-java-jsp-application"></a><a id="CreateJSP"></a>De Java JSP-toepassing maken

De JSP-toepassing maken:

1. Als eerste moet u een Java-project maken. Start Eclipse en klik achtereenvolgens op **File** (Bestand), **New** (Nieuw) en **Dynamic Web Project** (Dynamisch webproject). Als er geen **dynamisch webproject** beschikbaar is, gaat u als volg te werk: klik achtereenvolgens op **File** (Bestand), **New** (Nieuw), **Project**, vouw **Web** uit en klik op **Dynamic Web Project** (Dynamische webproject) en **Next** (Volgende).
   
    :::image type="content" source="./media/sql-api-java-application/image10.png" alt-text="JSP Java-toepassing ontwikkelen":::

1. Voer in het vak **Project name** (Projectnaam) een projectnaam in en selecteer in de vervolgkeuzelijst **Target Runtime** (Doelruntime) eventueel een waarde (bijvoorbeeld Apache Tomcat v7.0) en klik vervolgens op **Finish** (Voltooien). Door een doelruntime te selecteren, kunt u het project lokaal via Eclipse uitvoeren.

1. Vouw in de weergave Project Explorer (Projectverkenner) van Eclipse uw project uit. Klik met de rechtermuisknop op **WebContent**(Webinhoud) en klik vervolgens op **New** (Nieuw) en **JSP File** (JSP-bestand).

1. Geef in het dialoogvenster **New JSP File** (Nieuw JSP-bestand) de naam **index.jsp** voor het bestand op. Bewaar de bovenliggende map als **WebContent**, zoals weergegeven in de volgende afbeelding, en klik vervolgens op **Next** (Volgende).
   
    :::image type="content" source="./media/sql-api-java-application/image11.png" alt-text="Een nieuw JSP-bestand maken - Zelfstudie Java-webtoepassing":::

1. Selecteer voor deze zelfstudie in het dialoogvenster **Select JSP Template** (JSP-sjabloon selecteren) de optie **New JSP File (html)** (Nieuw JSP-bestand (html)) en klik vervolgens op **Finish** (Voltooien).

1. Wanneer het bestand *index.jsp* wordt geopend in Eclipse, voegt u tekst toe om **Hallo wereld!** weer te geven. in het bestaande `<body>`-element. De bijgewerkte `<body>`-inhoud moet eruitzien als de volgende code:

   ```html
   <body>
     <% out.println("Hello World!"); %>
   </body>
   ```

1. Sla het bestand *index.jsp* op.

1. Als u een doelruntime in stap 2 hebt ingesteld, kunt u achtereenvolgens op **Project** en **Run** klikken om uw JSP-toepassing lokaal uit te voeren:

   :::image type="content" source="./media/sql-api-java-application/image12.png" alt-text="Hello World: zelfstudie Java-toepassing":::

## <a name="install-the-sql-java-sdk"></a><a id="InstallSDK"></a>De SQL Java SDK installeren

De eenvoudigste manier om de SQL Java SDK en de bijbehorende afhankelijkheden op te halen, is via [Apache Maven](https://maven.apache.org/). Hiervoor moet u met behulp van de volgende stappen het project converteren naar een Maven-project:

1. Klik met de rechtermuisknop in de Projectverkenner, klik op **Configure** (Configureren) en klik vervolgens op **Convert to Maven Project** (Configureren naar een Maven-project).

1. Accepteer in het venster **Nieuwe POM maken** de standaardinstellingen en klik op **Voltooien**.

1. Ga naar de **Projectverkenner** en open het bestand pom.xml.

1. Klik op het tabblad **Dependencies** (Afhankelijkheden) van het deelvenster **Dependencies** (Afhankelijkheden) op **Add** (Toevoegen).

1. Ga in het venster **Select Dependency** (Afhankelijkheid selecteren) als volgt te werk:
   
   * Voer `com.azure` in het vak **Groeps-id** in.
   * Voer `azure-cosmos` in het vak **Artefact-id** in.
   * Voer `4.11.0` in het vak **Versie** in.
  
   Of voeg de afhankelijkheids-XML voor de groeps- en artefact-id rechtstreeks toe aan het bestand *pom.xml*:

   ```xml
   <dependency>
     <groupId>com.azure</groupId>
     <artifactId>azure-cosmos</artifactId>
     <version>4.11.0</version>
   </dependency>
   ```

1. Klik op **OK** zodat Maven de SQL Java SDK installeert of het bestand pom.xml opslaat.

## <a name="use-the-azure-cosmos-db-service-in-your-java-application"></a><a id="UseService"></a>De Azure Cosmos DB-service in uw Java-toepassing gebruiken

U kunt nu de modellen, weergaven en controllers toevoegen aan uw webtoepassing.

### <a name="add-a-model"></a>Een model toevoegen

Eerst gaan we een model definiëren in een nieuw bestand *TodoItem.java*. Met de `TodoItem`-klasse wordt het schema van een item gedefinieerd, samen met de ophaal- en installatiemethoden:

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/model/TodoItem.java":::

### <a name="add-the-data-access-objectdao-classes"></a>De DAO-klassen (Data Access Object) toevoegen

Maak een Data Access-object (DAO) om de ToDo-items naar Azure Cosmos DB te abstraheren. De client moet weten welke database en verzameling moeten worden gebruikt (waarnaar wordt verwezen via self link-elementen) om de ToDo-items op te kunnen slaan naar een verzameling. Indien mogelijk slaat u de database en verzameling op in het cachegeheugen om extra retouren naar de database te voorkomen.

1. Als u de Azure Cosmos DB-service wilt aanroepen, moet u een nieuw `cosmosClient`-object maken. Doorgaans kunt u het `cosmosClient`-object het best opnieuw gebruiken, zodat u het niet voor elke volgende aanvraag en nieuwe client hoeft te maken. U kunt de client opnieuw gebruiken door deze te definiëren in de `cosmosClientFactory`-klasse. Werk de HOST- en MASTER_KEY-waarden die u hebt opgeslagen in [stap 1](#CreateDB) bij. Vervang de HOST-variabele door uw URI en vervang de MASTER_KEY door uw PRIMARY KEY. Gebruik de volgende code om de `CosmosClientFactory`-klasse te maken in het bestand *CosmosClientFactory.java*:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/CosmosClientFactory.java":::

1. Maak een nieuw *TodoDao.java*-bestand en voeg de `TodoDao`-klasse toe om de TODO-items te maken, bij te werken, te lezen en te verwijderen:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/TodoDao.java":::

1. Maak een nieuw *MockDao.java*-bestand en voeg de `MockDao`-klasse toe; met deze klasse wordt de `TodoDao`-klasse geïmplementeerd om CRUD-bewerkingen op de items uit te voeren:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/MockDao.java":::

1. Maak een nieuw *DocDbDao.java*-bestand en voeg de `DocDbDao`-klasse toe. In deze klasse wordt code gedefinieerd om de TodoItems in de container op te slaan, worden uw database en verzameling opgehaald, indien aanwezig, of wordt een nieuwe gemaakt als deze nog niet bestaat. In dit voorbeeld wordt [Gson](https://code.google.com/p/google-gson/) gebruikt om TodoItem POJO's (Plain Old Java Objects) naar JSON-documenten te serialiseren en te deserialiseren. De client moet weten welke database en verzameling moeten worden gebruikt (waarnaar wordt verwezen via self link-elementen) om de ToDo-items op te kunnen slaan naar een verzameling. In deze klasse wordt ook de Help-functie gedefinieerd om de documenten op te halen door een ander kenmerk (bijvoorbeeld id) in plaats van self link. U kunt de Help-methode gebruiken om een TodoItem JSON-document op te halen op basis van de id om het document vervolgens te deserialiseren naar een POJO.

   U kunt ook het clientobject `cosmosClient` gebruiken om een verzameling of lijst met TodoItems op te halen via een SQL-query. Ten slotte definieert u de verwijderingsmethode voor het verwijderen van een TodoItem uit uw lijst. De volgende code toont de inhoud van de `DocDbDao`-klasse:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/DocDbDao.java":::

1. Maak vervolgens een nieuw *TodoDaoFactory.java*-bestand en voeg de `TodoDaoFactory`-klasse toe, waarmee een nieuw DocDbDao-object wordt gemaakt:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/dao/TodoDaoFactory.java":::

### <a name="add-a-controller"></a>Een controller toevoegen

Voeg de *TodoItemController*-controller toe aan uw toepassing. In dit project, gebruikt u [Project Lombok](https://projectlombok.org/) om de constructor, getters, setters en een opbouwfunctie te genereren. U kunt u deze code eventueel ook handmatig schrijven of door de IDE laten genereren:

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/controller/TodoItemController.java":::

### <a name="create-a-servlet"></a>Een servlet maken

Maak vervolgens een servlet om HTTP-aanvragen naar de controller te sturen. Maak het bestand *ApiServlet. java* en definieer hieronder de volgende code:

:::code language="java" source="~/samples-cosmosdb-java-v4-web-app/src/com/microsoft/azure/cosmos/sample/ApiServlet.java":::

## <a name="wire-the-rest-of-the-of-java-app-together"></a><a id="Wire"></a>Zet de rest van de Java-app in elkaar

Nu het merendeel van het werk is voltooid, hoeven we alleen nog maar een gebruikersinterface te maken en deze te verbinden met uw DAO.

1. U hebt een online gebruikersinterface nodig die voor de gebruiker kan worden weergegeven. Laten we het bestand *index.jsp* dat we eerder hebben gemaakt, herschrijven met de volgende code:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/WebContent/index.jsp":::

1. Schrijf tot slot wat JavaScript aan de clientzijde om de onlinegebruikersinterface en de servlet te koppelen:

   :::code language="java" source="~/samples-cosmosdb-java-v4-web-app/WebContent/assets/todo.js":::

1. Nu hoeft de toepassing alleen nog maar te worden getest. Voer de toepassing lokaal uit en voeg enkele Todo-items toe door de itemnaam en categorie in te vullen en op **Taak toevoegen** te klikken. Wanneer het item wordt weergegeven, kunt u bijwerken of het item is voltooid door het selectievakje in of uit te schakelen en op **Taken bijwerken** te klikken.

## <a name="deploy-your-java-application-to-azure-web-sites"></a><a id="Deploy"></a>Uw Java-toepassing implementeren op Azure Web Sites

Met Azure Web Sites kunt u heel eenvoudig, alsof u de toepassing als een WAR-bestand exporteert, Java-toepassingen implementeren en uploaden via broncodebeheer (bijvoorbeeld Git) of FTP.

1. Als u de toepassing wilt exporteren als een WAR-bestand, klikt u met de rechtermuisknop op uw project in **Projectverkenner** en klikt u achtereenvolgens op **Exporteren** en **WAR-bestand**.

1. Ga in het venster **WAR exporteren** als volgt te werk:
   
   * Voer in het vak webproject Azure-Cosmos-Java-sample in.
   * Kies in het vak Doel de bestemming waarnaar u het WAR-bestand wilt opslaan.
   * Klik op **Voltooien**.

1. Nu u over een WAR-bestand beschikt, kunt u dit uploaden naar de map **webapps** voor Azure Web Sites. Zie [Een Java-toepassing aan Azure App Service Web Apps toevoegen](../app-service/quickstart-java.md) voor instructies over het uploaden van het bestand. Wanneer het WAR-bestand is geüpload naar de map webapps, detecteert de runtime-omgeving dat u het bestand hebt toegevoegd en wordt het bestand automatisch geladen.

1. Als u het voltooide product wilt bekijken, gaat u naar `http://YOUR\_SITE\_NAME.azurewebsites.net/azure-cosmos-java-sample/` en begint u met het toevoegen van uw taken.

## <a name="get-the-project-from-github"></a><a id="GetProject"></a>Het project ophalen van GitHub

Alle voorbeelden in deze zelfstudie zijn opgenomen in het [todo](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)-project op GitHub. Als u het todo-project wilt importeren in Eclipse, moet u over de software en resources beschikken die worden vermeld in de sectie [Vereisten](#Prerequisites) en gaat u als volgt te werk:

1. Installeer [Project Lombok](https://projectlombok.org/). Lombok wordt gebruikt om de constructors, getters, setters in het project te genereren. Zodra u het bestand lombok.jar hebt gedownload, dubbelklikt u op het bestand om het te installeren of installeert u het bestand vanaf de opdrachtregel.

1. Als Eclipse is geopend, sluit en opent u Eclipse opnieuw om Lombok te laden.

1. Klik in Eclipse in het menu **File** (Bestand) op **Import** (Importeren).

1. Klik in het venster the **Import** (Importeren) achtereenvolgens op **Git**, **Projects from Git** (Projecten van Git) en **Next** (Volgende).

1. Klik in het venster **Select Repository Source** (Opslagplaatsbron selecteren) op **Clone URI** (URI klonen).

1. Voer op het scherm **Git-opslagplaats voor broncode** in het vak **URI** het webadres https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app in en klik vervolgens op **Volgende**.

1. Zorg er in het scherm **Vertakking selecteren** voor dat **master** is geselecteerd en klik op **Volgende**.

1. Klik in het scherm **Local Destination** (Lokale bestemming) op **Browse** (Bladeren) om een map te selecteren waarnaar de opslag kan worden gekopieerd en klik op **Next** (Volgende).

1. Zorg er in het scherm **Select a wizard to use for importing projects** (Een wizard selecteren waarmee projecten worden geïmporteerd) voor dat **Import existing projects** (Bestaande projecten selecteren) is geselecteerd en klik op **Next** (Volgende).

1. Schakel in het scherm **Import Projects** (Projecten importeren) het selectievakje uit voor het **DocumentDB-project** en klik op **Finish** (Voltooien). Het DocumentDB-project bevat de Azure Cosmos DB Java SDK die we in plaats daarvan als een afhankelijkheid zullen toevoegen.

1. Ga in **project Verkenner** naar Azure-Cosmos-Java-sample\src\com.Microsoft.Azure.Cosmos.sample.dao\DocumentClientFactory.Java, vervang de HOST en MASTER_KEY waarden door de URI en primaire sleutel voor uw Azure Cosmos DB-account en sla het bestand op. Zie [Stap 1: Een Azure Cosmos-databaseaccount maken](#CreateDB).

1. Klik in **project Verkenner** met de rechter muisknop op **Azure-Cosmos-Java-sample**, klik op **Build Path** en klik vervolgens op **Configure build pad**.

1. Selecteer in het rechterdeelvenster van het scherm **Java Build Path** (Java-opbouwpad) het tabblad **Libraries** (Bibliotheken) en klik vervolgens op **Add External JARs** (Externe JAR's toevoegen). Navigeer naar de locatie van het bestand lombok.jar en klik op **Open** (Openen) en **OK**.

1. Gebruik stap 12 om het venster **Properties** (Eigenschappen) opnieuw te openen en klik in het linkerdeelvenster vervolgens op **Targeted Runtimes** (Beoogde runtimes).

1. Klik in het scherm **Targeted Runtimes** (Beoogde runtimes) op **New** (Nieuw), selecteer **Apache Tomcat v7.0** en klik vervolgens op **OK**.

1. Gebruik stap 12 om het venster **Properties** (Eigenschappen) opnieuw te openen en klik in het linkerdeelvenster vervolgens op **Project Facets** (Projectfacetten).

1. Selecteer in het scherm **Project Facets** (Projectfacetten) achtereenvolgens **Dynamic Web Module** (Dynamische webmodule) en **Java** en klik vervolgens op **OK**.

1. Klik op het tabblad **Servers** onder aan het scherm met de rechtermuisknop op **Tomcat v7.0 Server at localhost** (Tomcat v7.0 Server op localhost) en klik vervolgens op **Add and Remove** (Toevoegen en verwijderen).

1. Verplaats in het venster **toevoegen en verwijderen** **Azure-Cosmos-Java-sample** naar het **geconfigureerde** vak en klik vervolgens op **volt ooien**.

1. Klik op het tabblad **Servers** met de rechtermuisknop op **Tomcat v7.0-server op localhost** en klik vervolgens op **Opnieuw starten**.

1. Ga in een browser naar `http://localhost:8080/azure-cosmos-java-sample/` en begin met het toevoegen van taken aan uw takenlijst. Als u de standaardpoortwaarden hebt gewijzigd, wijzigt u 8080 in de waarde die u hebt geselecteerd.

1. Zie [Stap 6: Uw toepassing implementeren op Azure Web Sites](#Deploy).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een node.js-toepassing maken met Azure Cosmos DB](sql-api-nodejs-application.md)