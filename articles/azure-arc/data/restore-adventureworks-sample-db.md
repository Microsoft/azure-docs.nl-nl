---
title: De AdventureWorks-voorbeeld database herstellen in een SQL-beheerd exemplaar
description: De AdventureWorks-voorbeeld database herstellen in een SQL-beheerd exemplaar
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a6b3577fe820c9dadcb3c8415aa7a951a3283284
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105641816"
---
# <a name="restore-the-adventureworks-sample-database-into-sql-managed-instance---azure-arc"></a>De AdventureWorks-voorbeeld database in een SQL-beheerd exemplaar herstellen-Azure-boog

[AdventureWorks](/sql/samples/adventureworks-install-configure) is een voorbeeld database met een OLTP-data base die vaak wordt gebruikt in zelf studies en voor beelden. Het wordt door micro soft aangehouden en onderhouden als onderdeel van de SQL Server-voor [beelden github-opslag plaats](https://github.com/microsoft/sql-server-samples/tree/master/samples/databases).

In dit document wordt een eenvoudig proces beschreven om de AdventureWorks-voorbeeld database te herstellen in uw SQL Managed instance-Azure Arc.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="download-the-adventureworks-backup-file"></a>Het AdventureWorks-back-upbestand downloaden

Down load het AdventureWorks-back-upbestand (. bak) naar uw SQL Managed instance-container. In dit voor beeld gebruikt u de `kubectl exec` opdracht om op afstand een opdracht uit te voeren binnen de container SQL Managed instance om het bak-bestand in de container te downloaden. Down load dit bestand van een locatie die toegankelijk `wget` is als u andere back-upbestanden voor de Data Base hebt die u wilt ophalen binnen de container voor SQL Managed instance. Zodra deze zich binnen de SQL Managed instance-container bevindt, kan deze eenvoudig worden hersteld met behulp van standaard `RESTORE DATABASE` T-SQL.

Voer een opdracht zoals deze uit om het. bak-bestand te downloaden waarbij de waarde van de naam van de Pod en de naam ruimte wordt vervangen voordat u deze uitvoert.
> [!NOTE]
>  Uw container moet een Internet verbinding hebben via 443 om het bestand te downloaden van GitHub

```console
kubectl exec <SQL pod name> -n <namespace name> -c arc-sqlmi -- wget https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks2019.bak -O /var/opt/mssql/data/AdventureWorks2019.bak
```

Voorbeeld

```console
kubectl exec sqltest1-0 -n arc -c arc-sqlmi -- wget https://github.com/Microsoft/sql-server-samples/releases/download/adventureworks/AdventureWorks2019.bak -O /var/opt/mssql/data/AdventureWorks2019.bak
```

## <a name="restore-the-adventureworks-database"></a>De AdventureWorks-data base herstellen

Op dezelfde manier kunt u een `kubectl` exec-opdracht uitvoeren om het `sqlcmd` cli-hulp programma te gebruiken dat is opgenomen in de SQL Managed instance-container om de T-SQL-opdracht uit te voeren om de data base te herstellen.

Voer een opdracht zoals deze uit om de data base te herstellen. Vervang de waarde van de pod-naam, het wacht woord en de naam van de naam ruimte door voordat u deze uitvoert.

```console
kubectl exec <SQL pod name> -n <namespace name> -c arc-sqlmi -- /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P <password> -Q "RESTORE DATABASE AdventureWorks2019 FROM  DISK = N'/var/opt/mssql/data/AdventureWorks2019.bak' WITH MOVE 'AdventureWorks2017' TO '/var/opt/mssql/data/AdventureWorks2019.mdf', MOVE 'AdventureWorks2017_Log' TO '/var/opt/mssql/data/AdventureWorks2019_Log.ldf'"
```
Voorbeeld

```console
kubectl exec sqltest1-0 -n arc -- -c arc-sqlmi /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P MyPassword! -Q "RESTORE DATABASE AdventureWorks2019 FROM  DISK = N'/var/opt/mssql/data/AdventureWorks2019.bak' WITH MOVE 'AdventureWorks2017' TO '/var/opt/mssql/data/AdventureWorks2019.mdf', MOVE 'AdventureWorks2017_Log' TO '/var/opt/mssql/data/AdventureWorks2019_Log.ldf'"
```
