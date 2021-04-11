---
title: 'Quick Start: uw eerste Java-query'
description: In deze Snelstartgids volgt u de stappen voor het inschakelen van de resource Graph maven-pakketten voor Java en voert u uw eerste query uit.
ms.date: 03/30/2021
ms.topic: quickstart
ms.custom: devx-track-java
ms.openlocfilehash: 97c04cb8b8180034bdc5109446c79deb56e457c9
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106223963"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-java"></a>Snelstartgids: uw eerste resource grafiek query uitvoeren met Java

De eerste stap voor het gebruik van Azure resource Graph is om te controleren of de vereiste maven-pakketten voor Java zijn geïnstalleerd. In deze Snelstartgids wordt u begeleid bij het toevoegen van de Maven-pakketten aan uw Java-installatie.

Aan het einde van dit proces hebt u de Maven-pakketten aan uw Java-installatie toegevoegd en voert u de eerste resource grafiek query uit.

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

- Controleer of de meest recente versie van Azure CLI is geïnstalleerd (ten minste **2.21.0**). Als deze nog niet is geïnstalleerd, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

  > [!NOTE]
  > Azure CLI is vereist om de Azure-SDK voor java in te scha kelen voor gebruik van de **CLI-gebaseerde verificatie** in de volgende voor beelden. Zie de [Azure Identity client-bibliotheek voor Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity)voor meer informatie over andere opties.

- De [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), versie
  8.

- [Apache Maven](https://maven.apache.org/), versie 3,6 of hoger.

## <a name="create-the-resource-graph-project"></a>Het Resource Graph-project maken

Als u Java wilt inschakelen om een query uit te voeren op een Azure-resource grafiek, maakt en configureert u een nieuwe toepassing met maven en installeert u de vereiste maven-pakketten.

1. Initialiseer een nieuwe Java-toepassing met de naam "argQuery" met een [maven-archetype](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html):

   ```cmd
   mvn -B archetype:generate -DarchetypeGroupId="org.apache.maven.archetypes" -DgroupId="com.Fabrikam" -DartifactId="argQuery"
   ```

1. Wijzig de mappen in de nieuwe projectmap `argQuery` en open `pom.xml` deze in uw favoriete editor. Voeg de volgende `<dependency>` knoop punten toe onder het bestaande `<dependencies>` knoop punt:

   ```xml
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-identity</artifactId>
        <version>1.2.4</version>
    </dependency>
    <dependency>
        <groupId>com.azure.resourcemanager</groupId>
        <artifactId>azure-resourcemanager-resourcegraph</artifactId>
        <version>1.0.0-beta.1</version>
    </dependency>
   ```

1. Voeg in het `pom.xml` bestand het volgende `<properties>` knoop punt onder het basis `<project>` knooppunt toe om de bron-en doel versie bij te werken:

   ```xml
   <properties>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

1. Voeg in het `pom.xml` bestand het volgende `<build>` knoop punt onder het basis `<project>` knooppunt toe om het doel en de belangrijkste klasse te configureren waarin het project moet worden uitgevoerd.

   ```xml
   <build>
       <plugins>
           <plugin>
               <groupId>org.codehaus.mojo</groupId>
               <artifactId>exec-maven-plugin</artifactId>
               <version>1.2.1</version>
               <executions>
                   <execution>
                       <goals>
                           <goal>java</goal>
                       </goals>
                   </execution>
               </executions>
               <configuration>
                   <mainClass>com.Fabrikam.App</mainClass>
               </configuration>
           </plugin>
       </plugins>
   </build>
   ```

1. Vervang de standaard waarde `App.java` in `\argQuery\src\main\java\com\Fabrikam` met de volgende code en sla het bijgewerkte bestand op:

   ```java
   package com.Fabrikam;
   
   import java.util.Arrays;
   import java.util.List;
   import com.azure.core.management.AzureEnvironment;
   import com.azure.core.management.profile.AzureProfile;
   import com.azure.identity.DefaultAzureCredentialBuilder;
   import com.azure.resourcemanager.resourcegraph.ResourceGraphManager;
   import com.azure.resourcemanager.resourcegraph.models.QueryRequest;
   import com.azure.resourcemanager.resourcegraph.models.QueryRequestOptions;
   import com.azure.resourcemanager.resourcegraph.models.QueryResponse;
   import com.azure.resourcemanager.resourcegraph.models.ResultFormat;
   
   public class App
   {
       public static void main( String[] args )
       {
           List<String> listSubscriptionIds = Arrays.asList(args[0]);
           String strQuery = args[1];
   
           ResourceGraphManager manager = ResourceGraphManager.authenticate(new DefaultAzureCredentialBuilder().build(), new AzureProfile(AzureEnvironment.AZURE));
   
           QueryRequest queryRequest = new QueryRequest()
               .withSubscriptions(listSubscriptionIds)
               .withQuery(strQuery);
           
           QueryResponse response = manager.resourceProviders().resources(queryRequest);
   
           System.out.println("Records: " + response.totalRecords());
           System.out.println("Data:\n" + response.data());
       }
   }
   ```

1. De `argQuery` console toepassing bouwen:

   ```bash
   mvn package
   ```

## <a name="run-your-first-resource-graph-query"></a>Uw eerste Resource Graph-query uitvoeren

Wanneer de toepassing Java-Console is gebouwd, is het tijd om een eenvoudige resource grafiek query uit te proberen. De query retourneert de eerste vijf Azure-resources met de **naam** en het **resourcetype** van elke resource.

In elke aanroep naar `argQuery` worden verschillende variabelen gebruikt die u moet vervangen door uw eigen waarden:

- Vervang `{subscriptionId}` door uw abonnements-ID
- `{query}` -Vervangen door de query van uw Azure-resource grafiek

1. Gebruik de Azure CLI voor verificatie met `az login` .

1. Wijzig de mappen in de `argQuery` projectmap die u hebt gemaakt met de vorige `mvn -B archetype:generate` opdracht.

1. Voer uw eerste Azure resource Graph-query uit met behulp van Maven om de console toepassing te compileren en de argumenten door te geven. De `exec.args` eigenschap identificeert argumenten op spaties. Als u de query wilt identificeren als één argument, worden deze gewikkeld met enkele aanhalings tekens ( `'` ).

   ```bash
   mvn compile exec:java -Dexec.args "{subscriptionId} 'Resources | project name, type | limit 5'"
   ```

   > [!NOTE]
   > Omdat deze voorbeeldquery geen sorteermodificator geeft, bijvoorbeeld `order by`, zal deze query waarschijnlijk per aanvraag een andere set resources opleveren als de query meerdere keren wordt uitgevoerd.

1. Wijzig het argument in `argQuery.exe` en wijzig de query in `order by` de eigenschap **name** :

   ```bash
   mvn compile exec:java -Dexec.args "{subscriptionId} 'Resources | project name, type | limit 5 | order by name asc'"
   ```

   > [!NOTE]
   > Net als bij de eerste query zal deze query waarschijnlijk per aanvraag een andere set resources opleveren als de query meerdere keren wordt uitgevoerd. De volgorde van de queryopdrachten is belangrijk. In dit voorbeeld komt `order by` na `limit`. Met deze opdracht worden de queryresultaten eerst beperkt en vervolgens gerangschikt.

1. Wijzig de laatste parameter in `argQuery.exe` en wijzig de query om eerst te `order by` op de eigenschap **Naam** en daarna de resultaten van de top vijf te `limit`:

   ```bash
   mvn compile exec:java -Dexec.args "{subscriptionId} 'Resources | project name, type | order by name asc | limit 5'"
   ```

Wanneer de laatste query meerdere keren wordt uitgevoerd, ervan uitgaande dat niets in uw omgeving verandert, zijn de geretourneerde resultaten consistent en gesorteerd op de eigenschap **Naam**, maar nog steeds beperkt tot de top vijf.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de Java-Console toepassing en de geïnstalleerde pakketten wilt verwijderen, kunt u dit doen door de `argQuery` projectmap te verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze Snelstartgids hebt u een Java-Console toepassing gemaakt met de vereiste resource grafiek pakketten en voert u uw eerste query uit. Ga door naar de pagina met details van de querytaal voor meer informatie over de taal van Resource Graph.

> [!div class="nextstepaction"]
> [Meer informatie over de querytaal](./concepts/query-language.md)