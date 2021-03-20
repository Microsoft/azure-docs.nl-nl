---
title: Verbinding maken en query's uitvoeren op Azure SQL Edge
description: Meer informatie over hoe u verbinding kunt maken met Azure SQL Edge en hoe u er query's op uitvoert.
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/25/2020
ms.openlocfilehash: b56b65261950e9cf534a3755d214229ef7d5bb1e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93395203"
---
# <a name="connect-and-query-azure-sql-edge"></a>Verbinding maken en query's uitvoeren op Azure SQL Edge

In Azure SQL Edge kunt u, nadat u een container hebt geïmplementeerd, verbinding maken met de data base-engine vanaf een van de volgende locaties:

- In de container
- Vanuit een andere docker-container die op dezelfde host wordt uitgevoerd
- Op de hostmachine
- Van elke andere client computer in het netwerk

## <a name="tools-to-connect-to-azure-sql-edge"></a>Hulp middelen om verbinding te maken met Azure SQL Edge

U kunt verbinding maken met een exemplaar van de Azure SQL Edge-instantie vanuit een van deze algemene hulpprogram ma's:

* [Sqlcmd](/sql/linux/sql-server-linux-setup-tools): Sqlcmd-client hulpprogramma's zijn al opgenomen in de container installatie kopie van Azure SQL Edge. Als u aan een actieve container met een interactieve bash-shell koppelt, kunt u de hulpprogram ma's lokaal uitvoeren. SQL Client-hulpprogram ma's zijn niet beschikbaar op het ARM64-platform, omdat ze niet zijn opgenomen in de ARM64-versie van de SQL Edge-containers. 
* [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms)
* [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)
* [Visual Studio Code](/sql/visual-studio-code/sql-server-develop-use-vscode)

Als u verbinding wilt maken met een Azure SQL Edge data base-engine vanaf een netwerk computer, hebt u het volgende nodig:

- **IP-adres of netwerk naam van de hostmachine**: dit is de hostmachine waarop de Azure SQL Edge-container wordt uitgevoerd.
- **Poort toewijzing van Azure SQL Edge-container**: dit is de toewijzing voor de docker-container poort aan een poort op de host. Binnen de container wordt Azure SQL Edge altijd toegewezen aan poort 1433. U kunt deze desgewenst wijzigen. Als u het poort nummer wilt wijzigen, moet u de opties voor het maken van de **container** bijwerken voor de Azure SQL Edge-module in azure IOT Edge. In het volgende voor beeld wordt poort 1433 op de container toegewezen aan poort 1600 op de host.

    ```JSON
    {
        "PortBindings": {
          "1433/tcp": [
            {
              "HostPort": "1600"
            }
          ]
        }
    }
    ```

- **Sa-wacht woord voor het exemplaar van de Azure SQL Edge**: dit is de waarde die is opgegeven voor de `SA_PASSWORD` omgevings variabele tijdens de implementatie van de Azure SQL-rand.

## <a name="connect-to-the-database-engine-from-within-the-container"></a>Verbinding maken met de data base-engine vanuit de container

De [SQL Server opdracht regel Programma's](/sql/linux/sql-server-linux-setup-tools) zijn opgenomen in de container installatie kopie van Azure SQL Edge. Als u aan de container met een interactieve opdracht prompt koppelt, kunt u de hulpprogram ma's lokaal uitvoeren. SQL Client-hulpprogram ma's zijn niet beschikbaar op het ARM64-platform, omdat ze niet zijn opgenomen in de ARM64-versie van de SQL Edge-containers. 

1. Gebruik de opdracht `docker exec -it` voor het starten van een interactieve bash-shell in de container die wordt uitgevoerd. In het volgende voor beeld `e69e056c702d` is de container-ID.

    ```bash
    docker exec -it <Azure SQL Edge container ID or name> /bin/bash
    ```

    > [!TIP]
    > U hoeft niet altijd de volledige container-ID op te geven. U hoeft alleen genoeg tekens op te geven om deze uniek te identificeren. In dit voor beeld is het mogelijk voldoende om te gebruiken `e6` of `e69` , in plaats van de volledige id.

2. Wanneer u zich in de container bevindt, kunt u lokaal verbinding maken met Sqlcmd. Sqlcmd bevindt zich standaard niet in het pad, dus u moet het volledige pad opgeven.

    ```bash
    /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P '<YourPassword>'
    ```

3. Wanneer u klaar bent met Sqlcmd, typt u `exit` .

4. Wanneer u klaar bent met de interactieve opdracht prompt, typt u `exit` . Uw container blijft actief nadat u de interactieve bash-shell hebt afgesloten.

## <a name="connect-to-azure-sql-edge-from-another-container-on-the-same-host"></a>Verbinding maken met Azure SQL Edge vanuit een andere container op dezelfde host

Omdat twee containers die worden uitgevoerd op dezelfde host zich op hetzelfde docker-netwerk bevinden, kunt u deze eenvoudig openen met behulp van de container naam en het poort adres voor de service. Als u bijvoorbeeld verbinding maakt met het exemplaar van Azure SQL Edge vanuit een andere python-module (container) op dezelfde host, kunt u een connection string op de volgende manier gebruiken. (In dit voor beeld wordt ervan uitgegaan dat Azure SQL Edge is geconfigureerd om te Luis teren op de standaard poort.)

```python

import pyodbc
server = 'MySQLEdgeContainer' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

## <a name="connect-to-azure-sql-edge-from-another-network-machine"></a>Verbinding maken met Azure SQL Edge vanaf een andere netwerk computer

U kunt verbinding maken met het exemplaar van Azure SQL Edge vanaf een andere computer in het netwerk. Als u dit wilt doen, gebruikt u het IP-adres van de docker-host en de poort van de host waaraan de Azure SQL Edge-container is toegewezen. Als het IP-adres van de docker-host bijvoorbeeld *xxx.xxx.xxx.xxx* is en de Azure SQL Edge-container is toegewezen aan poort *1600* van de host, zou het server adres voor het exemplaar van Azure SQL Edge *xxx. xxx. xxx. xxx, 1.600* zijn. Het bijgewerkte python-script is:

```python

import pyodbc
server = 'xxx.xxx.xxx.xxx,1600' # Replace this with the actual name of your SQL Edge Docker container
username = 'sa' # SQL Server username
password = 'MyStrongestP@ssword' # Replace this with the actual SA password from your deployment
database = 'MyEdgeDatabase' # Replace this with the actual database name from your deployment. If you do not have a database created, you can use Master database.
db_connection_string = "Driver={ODBC Driver 17 for SQL Server};Server=" + server + ";Database=" + database + ";UID=" + username + ";PWD=" + password + ";"
conn = pyodbc.connect(db_connection_string, autocommit=True)

```

Als u verbinding wilt maken met een exemplaar van Azure SQL Edge door SQL Server Management Studio op een Windows-computer uit te voeren, raadpleegt u [SQL Server Management Studio](/sql/linux/sql-server-linux-manage-ssms).

Zie [Visual Studio code](/sql/visual-studio-code/sql-server-develop-use-vscode)om verbinding te maken met een exemplaar van Azure SQL Edge door Visual Studio code op een Windows-, Mac-of Linux-computer te gebruiken.

Als u verbinding wilt maken met een exemplaar van Azure SQL Edge door Azure Data Studio te gebruiken op een Windows-, Mac-of Linux-computer, raadpleegt u [Azure Data Studio](/sql/azure-data-studio/quickstart-sql-server).

## <a name="next-steps"></a>Volgende stappen

[Verbinding maken en query's uitvoeren](/sql/linux/sql-server-linux-configure-docker#connect-and-query)

[SQL Server-hulpprogram ma's installeren in Linux](/sql/linux/sql-server-linux-setup-tools)