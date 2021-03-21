---
title: Overzicht van toepassings ontwikkeling
description: Kom meer te weten over beschikbare verbindingsbibliotheken en aanbevolen procedures voor toepassingen die verbinding maken met SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: genemi
ms.date: 11/14/2019
ms.custom: sqldbrb=2
ms.openlocfilehash: 7ea3456dbd1d0942cba48c97d70982ae088d73f9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92782974"
---
# <a name="application-development-overview---sql-database--sql-managed-instance"></a>Overzicht van toepassings ontwikkeling-SQL Database & SQL Managed instance

[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

In dit artikel worden de basis overwegingen beschreven die een ontwikkelaar moet kennen bij het schrijven van code om verbinding te maken met uw data base in Azure. Dit artikel is van toepassing op Azure SQL Database en Azure SQL Managed instance.

## <a name="language-and-platform"></a>Taal en platform

U kunt verschillende [Programmeer talen en platformen](connect-query-content-reference-guide.md) gebruiken om verbinding te maken en Azure SQL database op te vragen. U kunt [voorbeeld toepassingen](https://azure.microsoft.com/resources/samples/?service=sql-database&sort=0) vinden die u kunt gebruiken om verbinding te maken met de data base.

U kunt gebruikmaken van open-source hulpprogram ma's zoals [Cheetah](https://github.com/wunderlist/cheetah), [SQL-CLI](https://www.npmjs.com/package/sql-cli)en [VS code](https://code.visualstudio.com/). Daarnaast werkt Azure SQL Database met Microsoft-hulpprogramma's zoals [Visual Studio](https://www.visualstudio.com/downloads/) en [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms). U kunt ook de Azure Portal, Power shell en REST Api's gebruiken om extra productiviteit te verkrijgen.

## <a name="authentication"></a>Verificatie

Toegang tot Azure SQL Database is beveiligd met aanmeldingen en firewalls. Azure SQL Database ondersteunt zowel SQL Server als [Azure Active Directory verificatie](authentication-aad-overview.md) gebruikers en-aanmeldingen. Azure Active Directory-aanmeldingen zijn alleen beschikbaar in SQL Managed instance. 

Meer informatie over het [beheren van database toegang en-aanmelding](logins-create-manage.md).

## <a name="connections"></a>Verbindingen

In de verbindingslogica van de client overschrijft u de standaardtime-out zodat deze 30 seconden bedraagt. De standaardwaarde van 15 seconden is te kort voor verbindingen die afhankelijk zijn van internet.

Als u een [verbindingsgroep](/dotnet/framework/data/adonet/sql-server-connection-pooling) gebruikt, zorgt u ervoor dat de verbinding wordt verbroken zodra uw programma er niet actief meer gebruik van maakt en wanneer uw programma niet wordt voorbereid om opnieuw verbinding te maken.

Vermijd langlopende trans acties omdat een infra structuur of verbindings fout de trans actie kan terugdraaien. Als dat mogelijk is, splitst u de trans actie in de meerdere kleinere trans acties en gebruikt [u batch verwerking om de prestaties te verbeteren](../performance-improve-use-batching.md).

## <a name="resiliency"></a>Flexibiliteit

Azure SQL Database is een Cloud service waar u mogelijk tijdelijke fouten verwacht die zich voordoen in de onderliggende infra structuur of in de communicatie tussen Cloud entiteiten. Hoewel Azure SQL Database bestendig is op het mislukken van de transitieve infra structuur, kunnen deze storingen invloed hebben op uw verbinding. Wanneer er een tijdelijke fout optreedt tijdens het verbinden met SQL Database, moet uw code [de aanroep opnieuw proberen](troubleshoot-common-connectivity-issues.md). We raden u aan logica uitstel Logic te gebruiken, zodat de service niet meer hoeft te worden gebruikt voor meerdere clients tegelijk opnieuw te proberen. Pogings logica is afhankelijk van de [fout berichten voor SQL database-client Programma's](troubleshoot-common-errors-issues.md).

Zie voor meer informatie over het voorbereiden van geplande onderhouds gebeurtenissen op uw Azure SQL Database het [plannen van Azure-onderhouds gebeurtenissen in Azure SQL database](planned-maintenance.md).

## <a name="network-considerations"></a>Overwegingen voor het netwerk

- Zorg er op de computer die als host fungeert voor uw clientprogramma voor dat de firewall uitgaande TCP-communicatie toestaat via poort 1433.  Meer informatie: [een Azure SQL database firewall configureren](firewall-configure.md).
- Als uw client programma verbinding maakt met SQL Database terwijl uw client wordt uitgevoerd op een virtuele machine (VM) van Azure, moet u bepaalde poortbereiken openen op de VM. Meer informatie: [poorten boven 1433 voor ADO.NET 4,5 en SQL database](adonet-v12-develop-direct-route-ports.md).
- Client verbindingen met Azure SQL Database soms de proxy omzeilen en rechtstreeks communiceren met de data base. Andere poorten dan poort 1433 worden belangrijk. [Azure SQL database connectiviteits architectuur](connectivity-architecture.md) en [poorten hoger dan 1433 voor ADO.NET 4,5 en SQL database](adonet-v12-develop-direct-route-ports.md)voor meer informatie.
- Zie [netwerk configuratie voor SQL Managed instance](../managed-instance/how-to-content-reference-guide.md#network-configuration)voor een netwerk configuratie voor een exemplaar van SQL Managed instance.

## <a name="next-steps"></a>Volgende stappen

Verken alle mogelijkheden van [SQL database](sql-database-paas-overview.md) en [SQL Managed instance](../managed-instance/sql-managed-instance-paas-overview.md).

Als u aan de slag wilt gaan, raadpleegt u de hand leidingen voor [Azure SQL database](quickstart-content-reference-guide.md) en [Azure SQL Managed instances](../managed-instance/quickstart-content-reference-guide.md).