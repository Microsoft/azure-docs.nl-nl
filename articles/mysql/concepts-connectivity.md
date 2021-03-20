---
title: Tijdelijke connectiviteits fouten-Azure Database for MySQL
description: Meer informatie over het afhandelen van problemen met de tijdelijke verbinding en om efficiënt verbinding te maken met Azure Database for MySQL.
keywords: MySQL-verbinding, connection string, connectiviteits problemen, tijdelijke fout, verbindings fout, efficiënt verbinding maken
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 89673c14c38947dc5aeb91cacde1eb2755e84138
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94542606"
---
# <a name="handle-transient-errors-and-connect-efficiently-to-azure-database-for-mysql"></a>Tijdelijke fouten afhandelen en efficiënt verbinding maken met Azure Database for MySQL

In dit artikel wordt beschreven hoe u tijdelijke fouten verwerkt en efficiënt verbinding maakt met Azure Database for MySQL.

## <a name="transient-errors"></a>Tijdelijke fouten

Een tijdelijke fout, ook wel bekend als tijdelijke fout, is een fout die zichzelf zal oplossen. Meestal worden deze fouten manifesten beschouwd als een verbinding met de database server die wordt verwijderd. Er kunnen ook geen nieuwe verbindingen met een server worden geopend. Tijdelijke fouten kunnen bijvoorbeeld optreden als er een hardware-of netwerk fout optreedt. Een andere reden kan een nieuwe versie zijn van een PaaS-service die wordt geïmplementeerd. De meeste van deze gebeurtenissen worden in minder dan 60 seconden automatisch door het systeem beperkt. Een best practice voor het ontwerpen en ontwikkelen van toepassingen in de Cloud is het verwachten van tijdelijke fouten. We gaan ervan uit dat ze op elk gewenst moment in elk onderdeel kunnen plaatsvinden en dat ze de juiste logica hebben om deze situaties af te handelen.

## <a name="handling-transient-errors"></a>Tijdelijke fouten verwerken

Tijdelijke fouten moeten worden afgehandeld met behulp van logica opnieuw proberen. Situaties die in overweging moeten worden genomen:

* Er treedt een fout op wanneer u een verbinding probeert te openen
* Er wordt een niet-actieve verbinding aan de server zijde verwijderd. Wanneer u een opdracht probeert uit te geven, kan deze niet worden uitgevoerd
* Een actieve verbinding die momenteel een opdracht uitvoert, wordt verwijderd.

Het eerste en tweede geval zijn redelijk direct in de richting van de hand. Probeer de verbinding opnieuw te openen. Wanneer u dit hebt gedaan, wordt de tijdelijke fout door het systeem verholpen. U kunt uw Azure Database for MySQL opnieuw gebruiken. We raden u aan te wachten op het opnieuw proberen van de verbinding. Back-ups maken als de eerste pogingen mislukken. Op deze manier kan het systeem alle bronnen gebruiken die beschikbaar zijn om de fout situatie op te lossen. Een goed patroon dat u moet volgen:

* Wacht vijf seconden vóór uw eerste nieuwe poging.
* Voor elke volgende poging wordt de wacht tijd exponentieel verhoogd, tot 60 seconden.
* Stel een maximum aantal nieuwe pogingen in op het moment dat de bewerking door uw toepassing wordt beschouwd.

Wanneer een verbinding met een actieve trans actie mislukt, is het moeilijker om het herstel af te handelen. Er zijn twee gevallen: als de trans actie alleen-lezen is, is het veilig om de verbinding opnieuw te openen en de trans actie opnieuw uit te voeren. Als de trans actie echter ook is geschreven naar de data base, moet u bepalen of de trans actie is teruggedraaid of dat deze is geslaagd voordat de tijdelijke fout is opgetreden. In dat geval is het mogelijk dat u alleen de bevestiging van de door Voer van de database server hebt ontvangen.

Een manier om dit te doen, is door een unieke ID te genereren op de client die wordt gebruikt voor alle nieuwe pogingen. U geeft deze unieke ID door als onderdeel van de trans actie op de server en om deze op te slaan in een kolom met een unieke beperking. Op deze manier kunt u de trans actie veilig opnieuw proberen. Deze wordt uitgevoerd als de vorige trans actie teruggedraaid is en de door de client gegenereerde unieke ID nog niet in het systeem bestaat. Deze fout kan optreden als de unieke ID eerder is opgeslagen, omdat de vorige trans actie is voltooid.

Wanneer uw programma communiceert met Azure Database for MySQL via middleware van een derde partij, vraagt u de leverancier of de middleware logica voor tijdelijke fouten bevat.

Zorg ervoor dat u de logica probeert te testen. Probeer bijvoorbeeld uw code uit te voeren terwijl u de reken resources van uw Azure Database for MySQL-server omhoog of omlaag kunt schalen. Uw toepassing moet de korte downtime die tijdens deze bewerking wordt aangetroffen zonder problemen verwerken.

## <a name="connect-efficiently-to-azure-database-for-mysql"></a>Efficiënt verbinding maken met Azure Database for MySQL

Database verbindingen zijn een beperkte bron, waardoor het effectief gebruik van groepsgewijze verbindingen voor toegang tot Azure Database for MySQL de prestaties te verbeteren. In de volgende sectie wordt uitgelegd hoe u Groepsgewijze verbinding of permanente verbindingen gebruikt om toegang te krijgen tot Azure Database for MySQL.

## <a name="access-databases-by-using-connection-pooling-recommended"></a>Access-data bases met behulp van groepsgewijze verbindingen (aanbevolen)

Het beheren van database verbindingen kan een grote invloed hebben op de prestaties van de toepassing als geheel. Het doel is om de prestaties van uw toepassing te optimaliseren door het aantal keren dat verbindingen tot stand worden gebracht en de tijd voor het tot stand brengen van verbindingen in sleutel code paden te verminderen. We raden u ten zeerste aan om verbinding te maken met de pooling van database verbindingen of permanente verbindingen met Azure Database for MySQL. Met database verbindings groepen wordt het maken, beheren en toewijzen van database verbindingen afgehandeld. Wanneer een programma een database verbinding aanvraagt, wordt er prioriteit gegeven aan de toewijzing van bestaande niet-actieve database verbindingen, in plaats van het maken van een nieuwe verbinding. Nadat het programma is voltooid met behulp van de database verbinding, wordt de verbinding hersteld ter voor bereiding op verdere gebruik, in plaats van gewoon te worden afgesloten.

Voor een betere afbeelding biedt dit artikel [een stukje voorbeeld code](./sample-scripts-java-connection-pooling.md) die Java als voor beeld gebruikt. Zie [apache common dbcp](https://commons.apache.org/proper/commons-dbcp/)(Engelstalig) voor meer informatie.

> [!NOTE]
> De server configureert een time-outmechanisme om een verbinding te sluiten waarvan de status niet-actief is geweest gedurende enige tijd om bronnen vrij te maken. Zorg ervoor dat u het verificatie systeem instelt om te zorgen voor de effectiviteit van permanente verbindingen wanneer u deze gebruikt. Zie [verificatie systemen configureren aan de client zijde voor meer informatie over de effectiviteit van permanente verbindingen](concepts-connectivity.md#configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections).

## <a name="access-databases-by-using-persistent-connections-recommended"></a>Toegang krijgen tot data bases met behulp van permanente verbindingen (aanbevolen)

Het concept van permanente verbindingen is vergelijkbaar met die van verbindings groepen. Het vervangen van korte verbindingen met permanente verbindingen vereist alleen kleine wijzigingen in de code, maar heeft een belang rijke invloed op de prestaties in veel typische toepassings scenario's.

## <a name="access-databases-by-using-wait-and-retry-mechanism-with-short-connections"></a>Toegang krijgen tot data bases met behulp van een wacht tijd en opnieuw proberen met korte verbindingen

Als u resource beperkingen hebt, raden we u ten zeerste aan database groepen of permanente verbindingen te gebruiken voor toegang tot data bases. Als uw toepassing gebruikmaakt van korte verbindingen en verbindings fouten in de praktijk wanneer u de bovengrens voor het aantal gelijktijdige verbindingen nadert, kunt u wachten en het mechanisme opnieuw proberen. U kunt een geschikte wacht tijd instellen, met een korte wacht tijd na de eerste poging. Daarna kunt u meerdere keren proberen om te wachten op gebeurtenissen.

## <a name="configure-verification-mechanisms-in-clients-to-confirm-the-effectiveness-of-persistent-connections"></a>Verificatie mechanismen in clients configureren om de effectiviteit van permanente verbindingen te bevestigen

De-server configureert een time-outmechanisme om een verbinding te sluiten waarvan de status niet actief is om bronnen vrij te maken. Wanneer de client weer toegang heeft tot de data base, is het gelijk aan het maken van een nieuwe verbindings aanvraag tussen de client en de server. Configureer een verificatie mechanisme op de client om de effectiviteit van verbindingen te garanderen tijdens het gebruik ervan. Zoals in het volgende voor beeld wordt weer gegeven, kunt u Tomcat JDBC-verbindings groepering gebruiken om dit verificatie mechanisme te configureren.

Wanneer u de para meter TestOnBorrow instelt, wordt de effectiviteit van alle beschik bare inactieve verbindingen automatisch gecontroleerd door de verbindings groep. Als een dergelijke verbinding effectief is, wordt de verbinding met de rechtstreeks geretourneerde andere verbindings groep ingetrokken. De verbindings groep maakt vervolgens een nieuwe efficiënte verbinding en retourneert deze. Dit proces zorgt ervoor dat de data base efficiënt wordt geopend. 

Zie voor meer informatie over de specifieke instellingen het [document JDBC-verbindings groep officiële Inleiding](https://tomcat.apache.org/tomcat-7.0-doc/jdbc-pool.html#Common_Attributes). U moet hoofd zakelijk de volgende drie para meters instellen: TestOnBorrow (ingesteld op True), ValidationQuery (ingesteld op SELECT 1) en ValidationQueryTimeout (ingesteld op 1). De specifieke voorbeeld code wordt hieronder weer gegeven:

```java
public class SimpleTestOnBorrowExample {
      public static void main(String[] args) throws Exception {
          PoolProperties p = new PoolProperties();
          p.setUrl("jdbc:mysql://localhost:3306/mysql");
          p.setDriverClassName("com.mysql.jdbc.Driver");
          p.setUsername("root");
          p.setPassword("password");
            // The indication of whether objects will be validated by the idle object evictor (if any). 
            // If an object fails to validate, it will be dropped from the pool. 
            // NOTE - for a true value to have any effect, the validationQuery or validatorClassName parameter must be set to a non-null string. 
          p.setTestOnBorrow(true); 

            // The SQL query that will be used to validate connections from this pool before returning them to the caller.
            // If specified, this query does not have to return any data, it just can't throw a SQLException.
          p.setValidationQuery("SELECT 1");

            // The timeout in seconds before a connection validation queries fail. 
            // This works by calling java.sql.Statement.setQueryTimeout(seconds) on the statement that executes the validationQuery. 
            // The pool itself doesn't timeout the query, it is still up to the JDBC driver to enforce query timeouts. 
            // A value less than or equal to zero will disable this feature.
          p.setValidationQueryTimeout(1);
            // set other useful pool properties.
          DataSource datasource = new DataSource();
          datasource.setPoolProperties(p);

          Connection con = null;
          try {
            con = datasource.getConnection();
            // execute your query here
          } finally {
            if (con!=null) try {con.close();}catch (Exception ignore) {}
          }
      }
  }
```

## <a name="next-steps"></a>Volgende stappen

* [Verbindingsproblemen met Azure Databases for MySQL oplossen](howto-troubleshoot-common-connection-issues.md)
