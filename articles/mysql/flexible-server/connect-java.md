---
title: 'Quickstart: Java en JDBC gebruiken met Azure Database for MySQLFlexible Server'
description: Ontdek hoe u Java en JDBC gebruikt met een Azure Database for MySQL Flexible Server-database.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.custom: mvc, devcenter, devx-track-azurecli
ms.topic: quickstart
ms.devlang: java
ms.date: 01/16/2021
ms.openlocfilehash: 93a8a7644c7ca729f0e4fd201b88e995576d4bda
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98605936"
---
# <a name="quickstart-use-java-and-jdbc-with-azure-database-for-mysql-flexible-server"></a>Quickstart: Java en JDBC gebruiken met Azure Database for MySQL Flexible Server

In dit onderwerp wordt uitgelegd hoe u een voorbeeldtoepassing maakt die gebruikmaakt van Java en [JDBC](https://en.wikipedia.org/wiki/Java_Database_Connectivity) om gegevens op te slaan en op te halen in [Azure Database for MySQL Flexible Server](./index.yml).

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
AZ_DATABASE_NAME= flexibleserverdb
AZ_LOCATION=<YOUR_AZURE_REGION>
AZ_MYSQL_USERNAME=demo
AZ_MYSQL_PASSWORD=<YOUR_MYSQL_PASSWORD>
AZ_LOCAL_IP_ADDRESS=<YOUR_LOCAL_IP_ADDRESS>
```

Vervang de tijdelijke aanduidingen door de volgende waarden, die overal in dit artikel worden gebruikt:

- `<YOUR_DATABASE_NAME>`: De naam van uw MySQL-server. Deze moet uniek zijn binnen Azure.
- `<YOUR_AZURE_REGION>`: De Azure-regio die u gaat gebruiken. U kunt standaard `eastus` gebruiken, maar we raden u aan om een regio dichtbij uw locatie te configureren. U kunt de volledige lijst met beschikbare regio's bekijken door `az account list-locations` uit te voeren.
- `<YOUR_MYSQL_PASSWORD>`: Het wachtwoord van de MySQL-databaseserver. Het wachtwoord moet uit minimaal acht tekens bestaan. U moet tekens gebruiken uit drie van de volgende categorieën: Nederlandse hoofdletters, Nederlandse kleine letters, cijfers (0-9) en niet-alfanumerieke tekens (!, $, #, %, enzovoort).
- `<YOUR_LOCAL_IP_ADDRESS>`: Het IP-adres van de lokale computer waarop u de Java-toepassing gaat uitvoeren. Een handige manier om dit adres te vinden is door [whatismyip.akamai.com](http://whatismyip.akamai.com/) in te voeren in uw browser.

Maak vervolgens een resourcegroep:

```azurecli
az group create \
    --name $AZ_RESOURCE_GROUP \
    --location $AZ_LOCATION \
  	| jq
```

> [!NOTE]
> We gebruiken het hulpprogramma `jq` (standaard geïnstalleerd voor [Azure Cloud Shell](https://shell.azure.com/)) om JSON-gegevens weer te geven en deze beter leesbaar te maken.
> Als u dit hulpprogramma liever niet gebruikt, kunt u het gedeelte `| jq` verwijderen uit alle opdrachten die we gebruiken.

## <a name="create-an-azure-database-for-mysql-instance"></a>Een Azure Database for MySQL-instantie maken

We maken eerst een beheerde MySQL-server.

> [!NOTE]
> Meer gedetailleerde informatie over het maken van MySQL-servers vindt u in [Een Azure Database for MySQL-server maken met Azure Portal](./quickstart-create-server-portal.md).

Voer in [Azure Cloud Shell](https://shell.azure.com/) het volgende script uit:

```azurecli
az mysql flexible-server create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME \
    --location $AZ_LOCATION \
    --sku-name Standard_B1ms \
    --storage-size 5120 \
    --admin-user $AZ_MYSQL_USERNAME \
    --admin-password $AZ_MYSQL_PASSWORD \
    --public-access $AZ_LOCAL_IP_ADDRESS
  	| jq
```

Zorg ervoor dat u <uw IP-adres> invoert om toegang te krijgen tot de server vanaf uw lokale computer. Met deze opdracht maakt u een flexibele MySQL-server met een laag met burstmogelijkheden die geschikt is voor ontwikkeling.

De MySQL-server die u hebt gemaakt, heeft een lege database met de naam **flexibleserverdb**. Deze database wordt in dit artikel gebruikt.

[Ondervindt u problemen? Laat het ons weten.](https://github.com/MicrosoftDocs/azure-docs/issues)

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
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.20</version>
        </dependency>
    </dependencies>
</project>
```

Dit bestand is een [Apache Maven](https://maven.apache.org/) waarmee het project wordt geconfigureerd voor gebruik:

- Java 8
- Een recent MySQL-stuurprogramma voor Java

### <a name="prepare-a-configuration-file-to-connect-to-azure-database-for-mysql"></a>Een configuratiebestand voorbereiden om verbinding te maken met Azure Database for MySQL

Maak een bestand *src/main/resources/application.properties* en voeg het volgende toe:

```properties
url=jdbc:mysql://$AZ_DATABASE_NAME.mysql.database.azure.com:3306/demo?serverTimezone=UTC
user=demo
password=$AZ_MYSQL_PASSWORD
```

- Vervang de twee variabelen `$AZ_DATABASE_NAME` door de waarde die u hebt geconfigureerd aan het begin van dit artikel.
- Vervang de variabele `$AZ_MYSQL_PASSWORD` door de waarde die u hebt geconfigureerd aan het begin van dit artikel.

> [!NOTE]
> We voegen `?serverTimezone=UTC` toe aan de configuratie-eigenschap `url` om het JDBC-stuurprogramma te laten weten dat de UTC datumnotatie (of Coordinated Universal Time) moet worden gebruikt wanneer verbinding wordt gemaakt met de database. Anders gebruikt de Java-server niet dezelfde datumnotatie als de database, hetgeen resulteert in een fout.

### <a name="create-an-sql-file-to-generate-the-database-schema"></a>Een SQL-bestand maken om het databaseschema te genereren

We gebruiken een bestand *src/main/resources/`schema.sql`* om een databaseschema te maken. Maak het bestand met de volgende inhoud:

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id SERIAL PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BOOLEAN);
```

## <a name="code-the-application"></a>De toepassing coderen

### <a name="connect-to-the-database"></a>Verbinding maken met de database

Voeg vervolgens de Java-code toe die gebruik gaat maken van JDBC om gegevens op te slaan en op te halen van uw MySQL-server.

Maak een bestand *src/main/java/DemoApplication.java* met de volgende inhoud:

```java
package com.example.demo;

import com.mysql.cj.jdbc.AbandonedConnectionCleanupThread;

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
        AbandonedConnectionCleanupThread.uncheckedShutdown();
    }
}
```

[Ondervindt u problemen? Laat het ons weten.](https://github.com/MicrosoftDocs/azure-docs/issues)

Deze Java-code maakt gebruik van de bestanden *application.properties* en *schema.sql* die we eerder hebben gemaakt om verbinding te maken met de MySQL-server en een schema te maken waarmee onze gegevens worden opgeslagen.

In dit bestand kunt u zien dat we commentaar hebben gemaakt van methoden voor het invoegen, lezen, bijwerken en verwijderen van gegevens. We gaan deze methoden coderen in de rest van dit artikel en u kunt dan de commentaartekens /* en */ per methode verwijderen.

> [!NOTE]
> De databasereferenties worden opgeslagen in de eigenschappen *users* en *password* van het bestand *application.properties*. Deze referenties worden gebruikt bij het uitvoeren van `DriverManager.getConnection(properties.getProperty("url"), properties);`, omdat het eigenschappenbestand als argument wordt doorgegeven.

> [!NOTE]
> De regel `AbandonedConnectionCleanupThread.uncheckedShutdown();` aan het einde is een opdracht voor MySQL-stuurprogramma's voor het vernietigen van een interne thread bij het afsluiten van de app.
> Dit kan gerust worden genegeerd.

U kunt nu deze main-klasse uitvoeren met uw favoriete tool:

- Klik in uw IDE met de rechtermuisknop op de klasse *DemoApplication* en voer deze uit.
- Als u Maven gebruikt, kunt u de toepassing uitvoeren met de volgende opdracht: `mvn exec:java -Dexec.mainClass="com.example.demo.DemoApplication"`.

De app moet verbinding maken met Azure Database for MySQL, een databaseschema maken en vervolgens de verbinding sluiten, zoals in de consolelogboeken moet worden aangegeven:

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

### <a name="insert-data-into-azure-database-for-mysql"></a>Gegevens invoegen in Azure Database for MySQL

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

### <a name="reading-data-from-azure-database-for-mysql"></a>Gegevens lezen uit Azure Database for MySQL

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

[Ondervindt u problemen? Laat het ons weten.](https://github.com/MicrosoftDocs/azure-docs/issues)

### <a name="updating-data-in-azure-database-for-mysql"></a>Gegevens bijwerken in Azure Database for MySQL

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

### <a name="deleting-data-in-azure-database-for-mysql"></a>Gegevens verwijderen in Azure Database for MySQL

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

## <a name="clean-up-resources"></a>Resources opschonen

Gefeliciteerd! U hebt een Java-app gemaakt die gebruikmaakt van JDBC om gegevens op te slaan en op te halen uit Azure Database for MySQL.

Als u alle resources wilt opschonen die tijdens deze quickstart zijn gebruikt, verwijdert u de resourcegroep. Dit kan met de volgende opdracht:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Uw MySQL-database migreren naar Azure Database voor MySQL met behulp van dumpen en terugzetten](../concepts-migrate-dump-restore.md)
