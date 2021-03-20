---
title: Poorten na 1433
description: Client verbindingen van ADO.NET naar Azure SQL Database kunnen de proxy overs Laan en rechtstreeks communiceren met de data base via andere poorten dan 1433.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1, devx-track-dotnet
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 06/11/2020
ms.openlocfilehash: 0d009522ea0d0986233983f8725549b618ffb537
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91444874"
---
# <a name="ports-beyond-1433-for-adonet-45"></a>Poorten boven 1433 voor ADO.NET 4.5
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In dit onderwerp wordt het Azure SQL Database verbindings gedrag voor clients beschreven die gebruikmaken van ADO.NET 4,5 of een latere versie.

> [!IMPORTANT]
> Zie voor meer informatie over connectiviteits architectuur [Azure SQL database connectiviteits architectuur](connectivity-architecture.md).
>

## <a name="outside-vs-inside"></a>Buiten zijde

Voor verbindingen met Azure SQL Database moet u eerst vragen of uw client programma *buiten* of *binnen* de Azure-Cloud grens wordt uitgevoerd. In de subsecties worden twee veelvoorkomende scenario's besproken.

### <a name="outside-client-runs-on-your-desktop-computer"></a>*Buiten:* Client wordt uitgevoerd op uw desktop computer

Poort 1433 is de enige poort die moet worden geopend op de desktop computer die als host fungeert voor uw SQL Database-client toepassing.

### <a name="inside-client-runs-on-azure"></a>*Binnen:* Client wordt uitgevoerd op Azure

Wanneer de-client wordt uitgevoerd binnen de grenzen van de Azure-Cloud, wordt gebruikgemaakt van de manier waarop we een *directe route* kunnen aanroepen om te communiceren met SQL database. Nadat een verbinding tot stand is gebracht, omvatten verdere interacties tussen de client en data base geen Azure SQL Database gateway.

De volg orde is als volgt:

1. ADO.NET 4,5 (of hoger) initieert een korte interactie met de Azure-Cloud en ontvangt een dynamisch geïdentificeerd poort nummer.

   * Het dynamisch geïdentificeerde poort nummer bevindt zich in het bereik van 11000-11999.
2. ADO.NET maakt vervolgens rechtstreeks verbinding met SQL Database, zonder middleware in tussen.
3. Query's worden rechtstreeks naar de-data base verzonden en de resultaten worden direct naar de client geretourneerd.

Zorg ervoor dat de poortbereiken van 11000-11999 op uw Azure-client machine beschikbaar blijven voor ADO.NET 4,5-client interacties met SQL Database.

* In het bijzonder moeten poorten in het bereik vrij van andere uitgaande blok keringen zijn.
* Op uw virtuele Azure-machine beheert de **Windows Firewall met geavanceerde beveiliging** de poort instellingen.
  
  * U kunt de [gebruikers interface van de firewall](/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access) gebruiken om een regel toe te voegen waarvoor u het **TCP** -protocol en een poort bereik opgeeft met de syntaxis zoals **11000-11999**.

## <a name="version-clarifications"></a>Versie-uitleg

Deze sectie verduidelijkt de monikers die verwijzen naar de product versies. Er wordt ook een aantal versies tussen producten weer gegeven.

### <a name="adonet"></a>ADO.NET

* ADO.NET 4,0 ondersteunt het TDS 7,3-protocol, maar niet 7,4.
* ADO.NET 4,5 en hoger ondersteunt het TDS 7,4-protocol.

### <a name="odbc"></a>ODBC

* Microsoft SQL Server ODBC 11 of hoger

### <a name="jdbc"></a>JDBC

* Microsoft SQL Server JDBC 4,2 of hoger (JDBC 4,0 ondersteunt werkelijk TDS 7,4, maar implementeert de omleiding niet)

## <a name="related-links"></a>Verwante koppelingen

* ADO.NET 4,6 is uitgebracht op 20 juli 2015. [Hier](https://devblogs.microsoft.com/dotnet/announcing-net-framework-4-6/)vindt u een blog aankondiging van het .net-team.
* ADO.NET 4,5 is uitgebracht op 15 augustus 2012. [Hier](https://devblogs.microsoft.com/dotnet/announcing-the-release-of-net-framework-4-5-rtm-product-and-source-code/)vindt u een blog aankondiging van het .net-team.
  * Een blog bericht over ADO.NET 4.5.1 is [hier](https://devblogs.microsoft.com/dotnet/announcing-the-net-framework-4-5-1-preview/)beschikbaar.

* Micro soft ODBC-stuur programma 17 voor SQL Server https://aka.ms/downloadmsodbcsql

* Verbinding maken met Azure SQL Database V12 via omleiding https://techcommunity.microsoft.com/t5/DataCAT/Connect-to-Azure-SQL-Database-V12-via-Redirection/ba-p/305362

* [Lijst met TDS-protocol versies](https://www.freetds.org/)
* [Overzicht van SQL Database ontwikkeling](develop-overview.md)
* [Azure SQL Database firewall](firewall-configure.md)
