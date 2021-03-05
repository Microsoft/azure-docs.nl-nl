---
title: 'Zelfstudie: Linux Java-app met MongoDB'
description: Leer hoe u een gegevensgestuurde Linux Java-app kunt laten werken in Azure App Service, met een verbinding met een MongoDB die wordt uitgevoerd in Azure (Cosmos DB).
author: rloutlaw
ms.author: routlaw
ms.devlang: java
ms.topic: tutorial
ms.date: 12/10/2018
ms.custom: mvc, seodec18, seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 5a481e7ef37e36578b7f71a7afe70dcad737de89
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102217462"
---
# <a name="tutorial-build-a-java-spring-boot-web-app-with-azure-app-service-on-linux-and-azure-cosmos-db"></a>Zelfstudie: Een Java Spring Boot-web-app bouwen met Azure App Service in Linux en Azure Cosmos DB

In deze zelfstudie wordt u door het proces van het ontwikkelen, configureren, implementeren en schalen van Java-web-apps in Azure geleid. Wanneer u klaar bent, hebt u een [Spring Boot](https://projects.spring.io/spring-boot/)-toepassing die gegevens opslaat in [Azure Cosmos DB](../cosmos-db/index.yml) op [Azure App Service in Linux](overview.md).

![Spring Boot-toepassing die gegevens opslaat in Azure Cosmos DB](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maak een Cosmos DB-database.
> * Een voorbeeld-app verbinden met de database en deze lokaal testen
> * De voorbeeld-app implementeren in Azure
> * Logboeken met diagnostische gegevens vanaf App Service streamen
> * Extra exemplaren toevoegen om de voorbeeld-app uit te breiden

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* [Azure CLI](/cli/azure/overview), geïnstalleerd op uw eigen computer. 
* [Git](https://git-scm.com/)
* [Java JDK](/azure/developer/java/fundamentals/java-jdk-long-term-support)
* [Maven](https://maven.apache.org)

## <a name="clone-the-sample-todo-app-and-prepare-the-repo"></a>Kloon de voorbeeld-app voor taken en bereid de opslagplaats voor

In deze zelfstudie wordt een voorbeeld-app voor een takenlijst gebruikt met een web-UI die een Spring REST API aanroept met ondersteuning van [Spring Data voor Azure Cosmos DB](https://github.com/Microsoft/spring-data-cosmosdb). De code voor de app is beschikbaar [op GitHub](https://github.com/Microsoft/spring-todo-app). Zie voor meer informatie over het schrijven van Java-apps met Spring en Cosmos DB de zelfstudie [Spring Boot Starter met de Azure Cosmos DB SQL-API](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) en de snelstart [Spring Data voor Azure Cosmos DB](https://github.com/Microsoft/spring-data-cosmosdb#quick-start).


Voer de volgende opdrachten in de terminal uit om de voorbeeldopslagplaats te klonen en de omgeving van de voorbeeld-app in te stellen.

```bash
git clone --recurse-submodules https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git
cd e2e-java-experience-in-app-service-linux-part-2
yes | cp -rf .prep/* .
```

## <a name="create-an-azure-cosmos-db"></a>Een Azure Cosmos DB maken

Volg deze stappen voor het maken van een Azure Cosmos DB-database in uw abonnement. De takenlijst-app maakt verbinding met deze database en slaat de gegevens op wanneer deze actief is, waarbij de status van de toepassing wordt behouden ongeacht waar u de toepassing uitvoert.

1. Meld uw Azure-CLI aan en stel eventueel uw abonnement in als er meer dan één abonnement verbonden is met uw aanmeldingsreferenties.

    ```azurecli
    az login
    az account set -s <your-subscription-id>
    ```   

2. Maak een Azure-resourcegroep, waarbij de naam van de resourcegroep noteert.

    ```azurecli
    az group create -n <your-azure-group-name> \
        -l <your-resource-group-region>
    ```

3. Maken Azure Cosmos DB met het `GlobalDocumentDB` type. Voor de Cosmos DB-naam mag u alleen kleine letters gebruiken. Noteer het veld `documentEndpoint` in de reactie van de opdracht.

    ```azurecli
    az cosmosdb create --kind GlobalDocumentDB \
        -g <your-azure-group-name> \
        -n <your-azure-COSMOS-DB-name-in-lower-case-letters>
    ```

4. Haal uw Azure Cosmos DB-sleutel op om verbinding te maken met de app. Houd de `primaryMasterKey`, `documentEndpoint` bij de hand, want u hebt ze in de volgende stap nodig.

    ```azurecli
    az cosmosdb list-keys -g <your-azure-group-name> -n <your-azure-COSMOSDB-name>
    ```

## <a name="configure-the-todo-app-properties"></a>De eigenschappen van de takenlijst-app configureren

Open een terminal op uw computer. Kopieer het bestand met het voorbeeldscript naar de gekloonde opslagplaats, zodat u deze kunt aanpassen voor de Cosmos DB-database die u zojuist hebt gemaakt.

```bash
cd initial/spring-todo-app
cp set-env-variables-template.sh .scripts/set-env-variables.sh
```
 
Bewerk `.scripts/set-env-variables.sh` in uw favoriete editor en voorziet deze van de Azure Cosmos DB-verbindingsgegevens. Gebruik voor de configuratie van de App Service Linux, dezelfde regio als voorheen (`your-resource-group-region`) en de resourcegroep (`your-azure-group-name`) die wordt gebruikt bij het maken van de Cosmos DB-database. Kies een WEBAPP_NAME die uniek is, omdat hiervoor niet de naam van een web-app in een Azure-implementatie kan worden gedupliceerd.

```bash
export COSMOSDB_URI=<put-your-COSMOS-DB-documentEndpoint-URI-here>
export COSMOSDB_KEY=<put-your-COSMOS-DB-primaryMasterKey-here>
export COSMOSDB_DBNAME=<put-your-COSMOS-DB-name-here>

# App Service Linux Configuration
export RESOURCEGROUP_NAME=<put-your-resource-group-name-here>
export WEBAPP_NAME=<put-your-Webapp-name-here>
export REGION=<put-your-REGION-here>
```

Voer daarna het script uit:

```bash
source .scripts/set-env-variables.sh
```
   
Deze omgevingsvariabelen worden in `application.properties` in de takenlijst-app gebruikt. Met de velden in het eigenschappenbestand wordt een standaardconfiguratie voor de opslagplaats voor Spring Data ingesteld:

```properties
azure.cosmosdb.uri=${COSMOSDB_URI}
azure.cosmosdb.key=${COSMOSDB_KEY}
azure.cosmosdb.database=${COSMOSDB_DBNAME}
```

```java
@Repository
public interface TodoItemRepository extends DocumentDbRepository<TodoItem, String> {
}
```

De voorbeeld-app gebruikt vervolgens de aantekening `@Document` die is geïmporteerd uit `com.microsoft.azure.spring.data.cosmosdb.core.mapping.Document` om een entiteitstype in te stellen dat wordt opgeslagen en beheerd door Cosmos DB:

```java
@Document
public class TodoItem {
    private String id;
    private String description;
    private String owner;
    private boolean finished;
```

## <a name="run-the-sample-app"></a>De voorbeeld-app uitvoeren

Gebruik Maven om het voorbeeld uit te voeren.

```bash
mvn package spring-boot:run
```

De uitvoer moet er als volgt uitzien.

```output
bash-3.2$ mvn package spring-boot:run
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building spring-todo-app 2.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 


[INFO] SimpleUrlHandlerMapping - Mapped URL path [/webjars/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
[INFO] SimpleUrlHandlerMapping - Mapped URL path [/**] onto handler of type [class org.springframework.web.servlet.resource.ResourceHttpRequestHandler]
[INFO] WelcomePageHandlerMapping - Adding welcome page: class path resource [static/index.html]
2018-10-28 15:04:32.101  INFO 7673 --- [           main] c.m.azure.documentdb.DocumentClient      : Initializing DocumentClient with serviceEndpoint [https://sample-cosmos-db-westus.documents.azure.com:443/], ConnectionPolicy [ConnectionPolicy [requestTimeout=60, mediaRequestTimeout=300, connectionMode=Gateway, mediaReadMode=Buffered, maxPoolSize=800, idleConnectionTimeout=60, userAgentSuffix=;spring-data/2.0.6;098063be661ab767976bd5a2ec350e978faba99348207e8627375e8033277cb2, retryOptions=com.microsoft.azure.documentdb.RetryOptions@6b9fb84d, enableEndpointDiscovery=true, preferredLocations=null]], ConsistencyLevel [null]
[INFO] AnnotationMBeanExporter - Registering beans for JMX exposure on startup
[INFO] TomcatWebServer - Tomcat started on port(s): 8080 (http) with context path ''
[INFO] TodoApplication - Started TodoApplication in 45.573 seconds (JVM running for 76.534)
```

U hebt lokaal toegang tot de Spring-takenlijst-app via deze koppeling zodra de app wordt gestart: `http://localhost:8080/`.

 ![Spring TODO-app lokaal openen](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-locally.jpg)

Als u uitzonderingen ziet in plaats van het bericht 'TodoApplication gestart', controleert u of het script `bash` in de vorige stap de omgevingsvariabelen correct heeft geëxporteerd en of de waarden juist zijn voor de Azure Cosmos DB-database die u hebt gemaakt.

## <a name="configure-azure-deployment"></a>Azure-implementatie configureren

Open het bestand `pom.xml` in de map `initial/spring-boot-todo` en voeg de volgende configuratie van [Azure Web App-invoegtoepassing voor Maven](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md) toe.

```xml    
<plugins> 

    <!--*************************************************-->
    <!-- Deploy to Java SE in App Service Linux           -->
    <!--*************************************************-->
       
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.11.0</version>
        <configuration>
            <schemaVersion>v2</schemaVersion>

            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>

            <!-- Java Runtime Stack for Web App on Linux-->
            <runtime>
                 <os>linux</os>
                 <javaVersion>jre8</javaVersion>
                 <webContainer>jre8</webContainer>
             </runtime>
             <deployment>
                 <resources>
                 <resource>
                     <directory>${project.basedir}/target</directory>
                     <includes>
                     <include>*.jar</include>
                     </includes>
                 </resource>
                 </resources>
             </deployment>

            <appSettings>
                <property>
                    <name>COSMOSDB_URI</name>
                    <value>${COSMOSDB_URI}</value>
                </property> 
                <property>
                    <name>COSMOSDB_KEY</name>
                    <value>${COSMOSDB_KEY}</value>
                </property>
                <property>
                    <name>COSMOSDB_DBNAME</name>
                    <value>${COSMOSDB_DBNAME}</value>
                </property>
                <property>
                    <name>JAVA_OPTS</name>
                    <value>-Dserver.port=80</value>
                </property>
            </appSettings>

        </configuration>
    </plugin>           
    ...
</plugins>
```

## <a name="deploy-to-app-service-on-linux"></a>Implementeren naar App Service in Linux

Gebruik het Maven-doel `mvn azure-webapp:deploy` om de takenlijst-app naar Azure App Service te implementeren in Linux.

```bash

# Deploy
bash-3.2$ mvn azure-webapp:deploy
[INFO] Scanning for projects...
[INFO] 
[INFO] ------------------------------------------------------------------------
[INFO] Building spring-todo-app 2.0-SNAPSHOT
[INFO] ------------------------------------------------------------------------
[INFO] 
[INFO] --- azure-webapp-maven-plugin:1.11.0:deploy (default-cli) @ spring-todo-app ---
[INFO] Auth Type : AZURE_CLI, Auth Files : [C:\Users\testuser\.azure\azureProfile.json, C:\Users\testuser\.azure\accessTokens.json]
[INFO] Subscription : xxxxxxxxx
[INFO] Target Web App doesn't exist. Creating a new one...
[INFO] Creating App Service Plan 'ServicePlanb6ba8178-5bbb-49e7'...
[INFO] Successfully created App Service Plan.
[INFO] Successfully created Web App.
[INFO] Using 'UTF-8' encoding to copy filtered resources.
[INFO] Copying 1 resource to /home/test/e2e-java-experience-in-app-service-linux-part-2/initial/spring-todo-app/target/azure-webapp/spring-todo-app-61bb5207-6fb8-44c4-8230-c1c9e4c099f7
[INFO] Trying to deploy artifact to spring-todo-app...
[INFO] Renaming /home/test/e2e-java-experience-in-app-service-linux-part-2/initial/spring-todo-app/target/azure-webapp/spring-todo-app-61bb5207-6fb8-44c4-8230-c1c9e4c099f7/spring-todo-app-2.0-SNAPSHOT.jar to app.jar
[INFO] Deploying the zip package spring-todo-app-61bb5207-6fb8-44c4-8230-c1c9e4c099f7718326714198381983.zip...
[INFO] Successfully deployed the artifact to https://spring-todo-app.azurewebsites.net
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time: 02:19 min
[INFO] Finished at: 2019-11-06T15:32:03-07:00
[INFO] Final Memory: 50M/574M
[INFO] ------------------------------------------------------------------------
```

De uitvoer bevat de URL naar uw geïmplementeerde toepassing (in dit voorbeeld `https://spring-todo-app.azurewebsites.net` ). U kunt deze URL kopiëren naar uw webbrowser of de volgende opdracht in uw Terminal-venster uitvoeren om uw app te laden.

```bash
curl https://spring-todo-app.azurewebsites.net
```

U moet de app zien wanneer die wordt uitgevoerd met de externe URL in de adresbalk:

 ![Spring Boot-toepassing die wordt uitgevoerd met een externe URL](./media/tutorial-java-spring-cosmosdb/spring-todo-app-running-in-app-service.jpg)

## <a name="stream-diagnostic-logs"></a>Diagnostische logboeken streamen

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]


## <a name="scale-out-the-todo-app"></a>De takenlijst-app uitbreiden

Breid de toepassing uit door een andere werknemer toe te voegen:

```azurecli
az appservice plan update --number-of-workers 2 \
   --name ${WEBAPP_PLAN_NAME} \
   --resource-group <your-azure-group-name>
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze resources niet voor een andere zelfstudie nodig hebt (zie [Volgende stappen](#next)), kunt u ze verwijderen door de volgende opdracht in de Cloud Shell uit te voeren: 
  
```azurecli
az group delete --name <your-azure-group-name>
```

<a name="next"></a>

## <a name="next-steps"></a>Volgende stappen

[Azure voor Java-ontwikkelaars](/java/azure/)
[Spring Boot](https://spring.io/projects/spring-boot), [Spring Data voor Cosmos DB](/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db), [Azure Cosmos DB](../cosmos-db/introduction.md) en [Azure App Service op Linux](overview.md).

Lees meer over het uitvoeren van Java-apps in Azure App Service in Linux in de handleiding voor ontwikkelaars.

> [!div class="nextstepaction"] 
> [Linux-handleiding voor ontwikkelaars over Java in App Service](configure-language-java.md?pivots=platform-linux)
