---
title: Connectiviteitsproblemen oplossen
description: Problemen met de verbinding in een toegewezen SQL-groep (voorheen SQL DW) oplossen.
services: synapse-analytics
author: anumjs
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse, devx-track-csharp
ms.openlocfilehash: 8b23a3634b34277b732d4ba18bef7e71c783ebd5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98681183"
---
# <a name="troubleshooting-connectivity-issues-in-dedicated-sql-pool-formerly-sql-dw"></a>Verbindings problemen in een toegewezen SQL-groep (voorheen SQL DW) oplossen

Dit artikel bevat algemene probleemoplossings technieken om verbinding te maken met uw toegewezen SQL-groeps database (voorheen SQL DW).

## <a name="check-service-availability"></a>Beschik baarheid van service controleren

Controleer of de service beschikbaar is. Ga in het Azure Portal naar de toegewezen SQL-groep (voorheen SQL DW) waarmee u verbinding probeert te maken. Klik in het deel venster links TOC op **problemen vaststellen en oplossen**.

![Resource status selecteren](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

De status van uw toegewezen SQL-groep (voorheen SQL DW) wordt hier weer gegeven. Als de service niet wordt weer gegeven als **beschikbaar**, raadpleegt u verdere stappen.

![Service beschikbaar](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Als uw resource status laat zien dat uw toegewezen SQL-groep (voorheen SQL DW) is gepauzeerd of geschaald, volgt u de richt lijnen om uw exemplaar te hervatten.

![Scherm afbeelding toont een exemplaar van een toegewezen SQL-groep die is onderbroken of geschaald.](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png)
Meer informatie over Resource Health kunt u hier vinden.

## <a name="check-for-paused-or-scaling-operation"></a>Controleren op onderbroken of schaalbewerking

Controleer de portal om te zien of uw toegewezen SQL-groep (voorheen SQL DW)-exemplaar is onderbroken of geschaald.

![Scherm afbeelding laat zien hoe u kunt controleren of een Data Warehouse is gepauzeerd.](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Als u ziet dat uw service is gepauzeerd of geschaald, controleert u of deze niet tijdens de onderhouds planning wordt weer geven. In de portal voor het *overzicht* van uw toegewezen SQL-groep (voorheen SQL DW) ziet u de gekozen onderhouds planning.

![Onderhouds planning overzicht](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

Neem anders contact op met uw IT-beheerder om te controleren of dit onderhoud geen geplande gebeurtenis is. Volg [deze stappen](pause-and-resume-compute-portal.md)om de toegewezen SQL-groep (voorheen SQL DW) te hervatten.

## <a name="check-your-firewall-settings"></a>Uw firewallinstellingen controleren

De toegewezen SQL-groep (voorheen SQL DW)-data base communiceert via poort 1433.Als u verbinding probeert te maken vanuit een bedrijfsnetwerk, is uitgaand verkeer via poort 1433 mogelijk niet toegestaan vanwege de firewall van het netwerk. In dat geval kunt u geen verbinding maken met uw [logische server](../../azure-sql/database/logical-servers.md) , tenzij uw IT-afdeling poort 1433 opent. Meer informatie over Firewall configuraties vindt u [hier](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#create-and-manage-ip-firewall-rules).

## <a name="check-your-vnetservice-endpoint-settings"></a>De instellingen van uw VNet/service-eindpunt controleren

Als u fouten 40914 en 40615 ontvangt, raadpleegt u de [fout beschrijving en oplossingen hier](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615).

## <a name="check-for-the-latest-drivers"></a>Controleren op de meest recente stuurprogramma's

### <a name="software"></a>Software

Controleer of u de nieuwste hulpprogram ma's gebruikt om verbinding te maken met uw toegewezen SQL-groep (voorheen SQL DW):

- SSMS
- Azure Data Studio
- SQL Server Data Tools (Visual Studio)

### <a name="drivers"></a>Stuurprogramma's

Controleer of u de meest recente versies van het stuur programma gebruikt.Het gebruik van een oudere versie van de Stuur Programma's kan leiden tot onverwacht gedrag als de oudere Stuur Programma's mogelijk geen ondersteuning bieden voor nieuwe functies.

- [ODBC](/sql/connect/odbc/download-odbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [JDBC](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [OLE DB](/sql/connect/oledb/download-oledb-driver-for-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)
- [PHP](/sql/connect/php/download-drivers-php-sql-server?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

## <a name="check-your-connection-string"></a>Uw verbindingsreeks controleren

Controleer of de verbindingsreeksen juist zijn ingesteld.  Hieronder ziet u enkele voor beelden.  Meer informatie over verbindingstekenreeksen vindt u [hier](sql-data-warehouse-connection-strings.md).

ADO.NET-verbindingsreeks

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

ODBC-verbindings reeks

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

PHP-verbindings reeks

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

JDBC-verbindingsreeks

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="intermittent-connection-issues"></a>Onregelmatige verbindingsproblemen

Controleer of er sprake is van een zware belasting op de server met een groot aantal aanvragen in de wachtrij. Mogelijk moet u uw toegewezen SQL-groep (voorheen SQL DW) omhoog schalen voor meer bronnen.

## <a name="common-error-messages"></a>Veelvoorkomende foutberichten

Fouten 40914 en 40615, Zie [hier de beschrijving en oplossing](../../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#errors-40914-and-40615)van de fout.

## <a name="still-having-connectivity-issues"></a>Hebt u nog steeds verbindings problemen?

Maak een [ondersteunings ticket](sql-data-warehouse-get-started-create-support-ticket.md) zodat het technische team u kan ondersteunen.
