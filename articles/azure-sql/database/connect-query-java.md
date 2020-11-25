---
title: Java en JDBC gebruiken met Azure SQL Database
description: Lees hier hoe u Java en JDBC gebruikt met Azure SQL Database.
services: sql-database
author: jdubois
ms.author: judubois
ms.service: sql-database
ms.subservice: development
ms.topic: quickstart
ms.devlang: java
ms.date: 06/26/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: badf6b8887c356c2a7fc7308f6aa15f551e4bb67
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95972671"
---
# <a name="use-java-and-jdbc-with--azure-sql-database"></a>Java en JDBC gebruiken met Azure SQL Database

In dit onderwerp wordt uitgelegd hoe u een voorbeeldtoepassing maakt die gebruikmaakt van Java en [JDBC](https://en.wikipedia.org/wiki/Java_Database_Connectivity) om informatie op te slaan en op te halen in een [Azure SQL-database](/azure/sql-database/).

JDBC is de standaard Java-API voor het maken van verbinding met traditionele relationele databases.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account. Als u geen account hebt, kunt u [een gratis proefversie krijgen](https://azure.microsoft.com/free/).
- [Azure Cloud Shell](../../cloud-shell/quickstart.md) of [Azure CLI](/cli/azure/install-azure-cli). We raden Azure Cloud Shell aan zodat u automatisch wordt aangemeld en toegang hebt tot alle hulpprogramma's die u nodig hebt.
- Een ondersteunde [Java Development Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support)versie 8 (opgenomen in Azure Cloud Shell).
- De compilatietool [Apache Maven](https://maven.apache.org/).

## <a name="prepare-the-working-environment"></a>De werkomgeving voorbereiden

We gaan omgevingsvariabelen gebruiken om typefouten te beperken en om het voor u gemakkelijker te maken om de volgende configuratie aan te passen aan uw specifieke behoeften.

Gebruik de volgende opdrachten om deze omgevingsvariabelen in te stellen:

```bash
AZ_RESOURCE_GROUP=database-workshop
AZ_DATABASE_NAME=<YOUR_DATABASE_NAME>
AZ_LOCATION=<YOUR_AZURE_REGION>
AZ_SQL_SERVER_USERNAME=demo
AZ_SQL_SERVER_PASSWORD=<YOUR_AZURE_SQL_PASSWORD>
AZ_LOCAL_IP_ADDRESS=<YOUR_LOCAL_IP_ADDRESS>
```

Vervang de tijdelijke aanduidingen door de volgende waarden, die overal in dit artikel worden gebruikt:

- `<YOUR_DATABASE_NAME>`: De naam van uw Azure SQL Database-server. Deze moet uniek zijn binnen Azure.
- `<YOUR_AZURE_REGION>`: De Azure-regio die u gaat gebruiken. U kunt standaard `eastus` gebruiken, maar we raden u aan om een regio dichtbij uw locatie te configureren. U kunt de volledige lijst met beschikbare regio's bekijken door `az account list-locations` uit te voeren.
- `<AZ_SQL_SERVER_PASSWORD>`: Het wachtwoord van de Azure SQL Database-server. Het wachtwoord moet uit minimaal acht tekens bestaan. U moet tekens gebruiken uit drie van de volgende categorieën: Nederlandse hoofdletters, Nederlandse kleine letters, cijfers (0-9) en niet-alfanumerieke tekens (!, $, #, %, enzovoort).
- `<YOUR_LOCAL_IP_ADDRESS>`: Het IP-adres van de lokale computer waarop u de Java-toepassing gaat uitvoeren. Een handige manier om dit adres te vinden is door [whatismyip.akamai.com](http://whatismyip.akamai.com/) in te voeren in uw browser.

Maak vervolgens een resourcegroep met de volgende opdracht:

```azurecli
az group create \
    --name $AZ_RESOURCE_GROUP \
    --location $AZ_LOCATION \
  	| jq
```

> [!NOTE]
> We gebruiken het hulpprogramma `jq` om JSON-gegevens weer te geven en de code beter leesbaar te maken. Dit hulpprogramma wordt standaard geïnstalleerd in [Azure Cloud Shell](https://shell.azure.com/). Als u dit hulpprogramma liever niet gebruikt, kunt u het gedeelte `| jq` verwijderen uit alle opdrachten die we gebruiken.

## <a name="create-an-azure-sql-database-instance"></a>Een Azure SQL Database-exemplaar maken

Het eerste wat we gaan maken is een beheerde Azure SQL Database-server.

> [!NOTE]
> Als u meer wilt weten over het maken van Azure SQL Database-servers, leest u [Quickstart: Create an Azure SQL Database single database](./single-database-create-quickstart.md) (Een individuele database maken in Azure SQL Database).

Voer in [Azure Cloud Shell](https://shell.azure.com/) de volgende opdracht uit:

```azurecli
az sql server create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME \
    --location $AZ_LOCATION \
    --admin-user $AZ_SQL_SERVER_USERNAME \
    --admin-password $AZ_SQL_SERVER_PASSWORD \
  	| jq
```

Hiermee maakt u een Azure SQL Database-server.

### <a name="configure-a-firewall-rule-for-your-azure-sql-database-server"></a>Een firewallregel configureren voor uw Azure SQL Database-server

Azure SQL Database-exemplaren worden standaard beveiligd. Ze hebben een firewall die geen enkele binnenkomende verbinding toestaat. Om uw database te kunnen gebruiken, moet u een firewallregel toevoegen waarmee het lokale IP-adres toegang krijgt tot de databaseserver.

Aangezien u een lokaal IP-adres hebt geconfigureerd aan het begin van dit artikel, kunt u de firewall van de server openen door de volgende opdracht uit te voeren:

```azurecli
az sql server firewall-rule create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME-database-allow-local-ip \
    --server $AZ_DATABASE_NAME \
    --start-ip-address $AZ_LOCAL_IP_ADDRESS \
    --end-ip-address $AZ_LOCAL_IP_ADDRESS \
  	| jq
```

### <a name="configure-a-azure-sql-database"></a>Een Azure SQL-database configureren

De Azure SQL Database-server die u eerder hebt gemaakt, is leeg. De server bevat geen database die u kunt gebruiken met de Java-toepassing. Maak daarom een nieuwe database met de naam `demo` door de volgende opdracht uit te voeren:

```azurecli
az sql db create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name demo \
    --server $AZ_DATABASE_NAME \
  	| jq
```

### <a name="create-a-new-java-project"></a>Een nieuw Java-project maken

Maak met behulp van uw favoriete IDE een nieuw Java-project en voeg een bestand `pom.xml` toe aan de hoofdmap van het project:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>demo</name>

    <properties>
        <java.version>1.8</java.version>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>

    <dependencies>
        <dependency>
            <groupId>com.microsoft.sqlserver</groupId>
            <artifactId>mssql-jdbc</artifactId>
            <version>7.4.1.jre8</version>
        </dependency>
    </dependencies>
</project>
```

Dit bestand is een [Apache Maven](https://maven.apache.org/) waarmee het project wordt geconfigureerd voor gebruik:

- Java 8
- Een recent SQL Server-stuurprogramma voor Java

### <a name="prepare-a-configuration-file-to-connect-to-azure-sql-database"></a>Een configuratiebestand voorbereiden om verbinding te maken met Azure SQL Database

Maak een bestand *src/main/resources/application.properties* en voeg het volgende toe:

```properties
url=jdbc:sqlserver://$AZ_DATABASE_NAME.database.windows.net:1433;database=demo;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
user=demo@$AZ_DATABASE_NAME
password=$AZ_SQL_SERVER_PASSWORD
```

- Vervang de twee variabelen `$AZ_DATABASE_NAME` door de waarde die u hebt geconfigureerd aan het begin van dit artikel.
- Vervang de variabele `$AZ_SQL_SERVER_PASSWORD` door de waarde die u hebt geconfigureerd aan het begin van dit artikel.

### <a name="create-an-sql-file-to-generate-the-database-schema"></a>Een SQL-bestand maken om het databaseschema te genereren

We gebruiken een bestand *src/main/resources/`schema.sql`* om een databaseschema te maken. Maak het bestand met de volgende inhoud:

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id INT PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BIT);
```

## <a name="code-the-application"></a>De toepassing coderen

### <a name="connect-to-the-database"></a>Verbinding maken met de database

Voeg vervolgens de Java-code toe die gebruik gaat maken van JDBC om gegevens op te slaan en op te halen uit uw Azure SQL-database.

Maak een bestand *src/main/java/DemoApplication.java* met de volgende inhoud:

```java
package com.example.demo;

import java.sql.*;
import java.util.*;
import java.util.logging.Logger;

public class DemoApplication {

    private static final Logger log;

    static {
        System.setProperty("java.util.logging.SimpleFormatter.format", "[%4$-7s] %5$s %n");
        log =Logger.getLogger(DemoApplication.class.getName());
    }

    public static void main(String[] args) throws Exception {
        log.info("Loading application properties");
        Properties properties = new Properties();
        properties.load(DemoApplication.class.getClassLoader().getResourceAsStream("application.properties"));

        log.info("Connecting to the database");
        Connection connection = DriverManager.getConnection(properties.getProperty("url"), properties);
        log.info("Database connection test: " + connection.getCatalog());

        log.info("Create database schema");
        Scanner scanner = new Scanner(DemoApplication.class.getClassLoader().getResourceAsStream("schema.sql"));
        Statement statement = connection.createStatement();
        while (scanner.hasNextLine()) {
            statement.execute(scanner.nextLine());
        }

        /*
        Todo todo = new Todo(1L, "configuration", "congratulations, you have set up JDBC correctly!", true);
        insertData(todo, connection);
        todo = readData(connection);
        todo.setDetails("congratulations, you have updated data!");
        updateData(todo, connection);
        deleteData(todo, connection);
        */

        log.info("Closing database connection");
        connection.close();
    }
}
```

Deze Java-code maakt gebruik van de bestanden *application.properties* en *schema.sql* die we eerder hebben gemaakt, zodat u verbinding kunt maken met de SQL Server-database en een schema kunt definiëren waarmee onze gegevens worden opgeslagen.

In dit bestand kunt u zien dat we commentaar hebben gemaakt van methoden voor het invoegen, lezen, bijwerken en verwijderen van gegevens. We gaan deze methoden coderen in de rest van dit artikel en u kunt dan de commentaartekens /* en */ per methode verwijderen.

> [!NOTE]
> De databasereferenties worden opgeslagen in de eigenschappen *users* en *password* van het bestand *application.properties*. Deze referenties worden gebruikt bij het uitvoeren van `DriverManager.getConnection(properties.getProperty("url"), properties);`, omdat het eigenschappenbestand als argument wordt doorgegeven.

U kunt nu deze main-klasse uitvoeren met uw favoriete tool:

- Klik in uw IDE met de rechtermuisknop op de klasse *DemoApplication* en voer deze uit.
- Als u Maven gebruikt, kunt u de toepassing uitvoeren met de volgende opdracht: `mvn exec:java -Dexec.mainClass="com.example.demo.DemoApplication"`.

De toepassing moet verbinding maken met de Azure SQL-database, een databaseschema maken en vervolgens de verbinding sluiten, zoals in de consolelogboeken moet worden aangegeven:

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Closing database connection 
```

### <a name="create-a-domain-class"></a>Een domeinklasse maken

Maak een nieuwe Java-klasse `Todo`, naast de klasse `DemoApplication`, en voeg de volgende code toe:

```java
package com.example.demo;

public class Todo {

    private Long id;
    private String description;
    private String details;
    private boolean done;

    public Todo() {
    }

    public Todo(Long id, String description, String details, boolean done) {
        this.id = id;
        this.description = description;
        this.details = details;
        this.done = done;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }

    public String getDetails() {
        return details;
    }

    public void setDetails(String details) {
        this.details = details;
    }

    public boolean isDone() {
        return done;
    }

    public void setDone(boolean done) {
        this.done = done;
    }

    @Override
    public String toString() {
        return "Todo{" +
                "id=" + id +
                ", description='" + description + '\'' +
                ", details='" + details + '\'' +
                ", done=" + done +
                '}';
    }
}
```

Deze klasse is een domeinmodel dat is toegewezen aan de tabel `todo` die u eerder hebt gemaakt tijdens het uitvoeren van het script *schema.sql*.

### <a name="insert-data-into-azure-sql-database"></a>Gegevens invoegen in Azure SQL-database

Voeg na de methode main in het bestand *src/main/java/DemoApplication.java* de volgende methode toe om gegevens in te voegen in de database:

```java
private static void insertData(Todo todo, Connection connection) throws SQLException {
    log.info("Insert data");
    PreparedStatement insertStatement = connection
            .prepareStatement("INSERT INTO todo (id, description, details, done) VALUES (?, ?, ?, ?);");

    insertStatement.setLong(1, todo.getId());
    insertStatement.setString(2, todo.getDescription());
    insertStatement.setString(3, todo.getDetails());
    insertStatement.setBoolean(4, todo.isDone());
    insertStatement.executeUpdate();
}
```

U kunt nu de commentaartekens verwijderen van de twee volgende regels in de methode `main`:

```java
Todo todo = new Todo(1L, "configuration", "congratulations, you have set up JDBC correctly!", true);
insertData(todo, connection);
```

Als u nu de main-klasse uitvoert, zou dit de volgende uitvoer moeten opleveren:

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Closing database connection
```

### <a name="reading-data-from-azure-sql-database"></a>Gegevens lezen uit een Azure SQL-database

Laten we de gegevens lezen die u eerder hebt ingevoegd om te controleren of onze code goed werkt.

Voeg in het bestand *src/main/java/DemoApplication.java*, na de methode `insertData`, de volgende methode toe om gegevens te lezen uit de database:

```java
private static Todo readData(Connection connection) throws SQLException {
    log.info("Read data");
    PreparedStatement readStatement = connection.prepareStatement("SELECT * FROM todo;");
    ResultSet resultSet = readStatement.executeQuery();
    if (!resultSet.next()) {
        log.info("There is no data in the database!");
        return null;
    }
    Todo todo = new Todo();
    todo.setId(resultSet.getLong("id"));
    todo.setDescription(resultSet.getString("description"));
    todo.setDetails(resultSet.getString("details"));
    todo.setDone(resultSet.getBoolean("done"));
    log.info("Data read from the database: " + todo.toString());
    return todo;
}
```

U kunt nu de commentaartekens verwijderen van de volgende regel in de methode `main`:

```java
todo = readData(connection);
```

Als u nu de main-klasse uitvoert, zou dit de volgende uitvoer moeten opleveren:

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true} 
[INFO   ] Closing database connection 
```

### <a name="updating-data-in-azure-sql-database"></a>Gegevens bijwerken in Azure SQL-database

Laten we de gegevens bijwerken die we eerder hebben ingevoegd.

Voeg in het bestand *src/main/java/DemoApplication.java*, na de methode `readData`, de volgende methode toe om gegevens bij te werken in de database:

```java
private static void updateData(Todo todo, Connection connection) throws SQLException {
    log.info("Update data");
    PreparedStatement updateStatement = connection
            .prepareStatement("UPDATE todo SET description = ?, details = ?, done = ? WHERE id = ?;");

    updateStatement.setString(1, todo.getDescription());
    updateStatement.setString(2, todo.getDetails());
    updateStatement.setBoolean(3, todo.isDone());
    updateStatement.setLong(4, todo.getId());
    updateStatement.executeUpdate();
    readData(connection);
}
```

U kunt nu de commentaartekens verwijderen van de twee volgende regels in de methode `main`:

```java
todo.setDetails("congratulations, you have updated data!");
updateData(todo, connection);
```

Als u nu de main-klasse uitvoert, zou dit de volgende uitvoer moeten opleveren:

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true} 
[INFO   ] Update data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have updated data!', done=true} 
[INFO   ] Closing database connection 
```

### <a name="deleting-data-in-azure-sql-database"></a>Gegevens verwijderen in Azure SQL-database

Laten we ten slotte de gegevens verwijderen die we eerder hebben ingevoegd.

Voeg in het bestand *src/main/java/DemoApplication.java*, na de methode `updateData`, de volgende methode toe om gegevens te verwijderen uit de database:

```java
private static void deleteData(Todo todo, Connection connection) throws SQLException {
    log.info("Delete data");
    PreparedStatement deleteStatement = connection.prepareStatement("DELETE FROM todo WHERE id = ?;");
    deleteStatement.setLong(1, todo.getId());
    deleteStatement.executeUpdate();
    readData(connection);
}
```

U kunt nu de commentaartekens verwijderen van de volgende regel in de methode `main`:

```java
deleteData(todo, connection);
```

Als u nu de main-klasse uitvoert, zou dit de volgende uitvoer moeten opleveren:

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true} 
[INFO   ] Update data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have updated data!', done=true} 
[INFO   ] Delete data 
[INFO   ] Read data 
[INFO   ] There is no data in the database! 
[INFO   ] Closing database connection 
```

## <a name="conclusion-and-resources-clean-up"></a>Conclusie en opschonen van resources

Gefeliciteerd! U hebt een Java-toepassing gemaakt die gebruikmaakt van JDBC om gegevens op te slaan en op te halen uit een Azure SQL-database.

Als u alle resources wilt opschonen die tijdens deze quickstart zijn gebruikt, verwijdert u de resourcegroep. Dit kan met de volgende opdracht:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Volgende stappen

- [Uw eerste database ontwerpen in Azure SQL Database](design-first-database-tutorial.md)  
- [Microsoft JDBC-stuurprogramma voor SQL Server](https://github.com/microsoft/mssql-jdbc)  
- [Problemen melden/vragen stellen](https://github.com/microsoft/mssql-jdbc/issues)
