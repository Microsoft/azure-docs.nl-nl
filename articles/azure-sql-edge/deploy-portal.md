---
title: Azure SQL Edge implementeren met behulp van de Azure Portal
description: Meer informatie over het implementeren van Azure SQL Edge met behulp van de Azure Portal
keywords: SQL-rand implementeren
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 5d7d232ada814d5d3c30e7b012c6289f847d641f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93395084"
---
# <a name="deploy-azure-sql-edge"></a>Azure SQL Edge implementeren 

Azure SQL Edge is een relationele data base-engine die is geoptimaliseerd voor IoT-en Azure IoT Edge-implementaties. Het biedt mogelijkheden voor het maken van een hoogwaardige gegevens opslag en verwerkings laag voor IoT-toepassingen en-oplossingen. In deze Quick start ziet u hoe u aan de slag gaat met het maken van een Azure SQL Edge-module via Azure IoT Edge met behulp van de Azure Portal.

## <a name="before-you-begin"></a>Voordat u begint

* Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) aan.
* Meld u aan bij de [Azure-portal](https://portal.azure.com/).
* Een [Azure-IOT hub](../iot-hub/iot-hub-create-through-portal.md)maken.
* Maak een [Azure IOT edge-apparaat](../iot-edge/how-to-install-iot-edge.md).

> [!NOTE]   
> Zie deze [Snelstartgids](../iot-edge/quickstart-linux.md)voor informatie over het implementeren van een Azure Linux-VM als IOT edge apparaat.

## <a name="deploy-sql-edge-module-from-azure-marketplace"></a>SQL Edge-module implementeren vanuit Azure Marketplace

Azure Marketplace is een online Marketplace voor toepassingen en services waar u kunt bladeren door een breed scala aan bedrijfs toepassingen en oplossingen die zijn gecertificeerd en geoptimaliseerd om te worden uitgevoerd op Azure, met inbegrip van [IOT Edge-modules](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Azure SQL Edge kan via Marketplace worden geïmplementeerd op een edge-apparaat.

1. Zoek de Azure SQL Edge-module op de Azure Marketplace.<br><br>

   ![SQL-Edge in MarketPlace](media/deploy-portal/find-offer-marketplace.png)

2. Kies het software abonnement dat het beste voldoet aan uw vereisten en klik op **maken**. <br><br>

   ![Het juiste software abonnement kiezen](media/deploy-portal/pick-correct-plan.png)

3. Geef op de pagina doel apparaten voor IoT Edge module de volgende details op en klik vervolgens op **maken**

   |**Veld**  |**Beschrijving**  |
   |---------|---------|
   |Abonnement  |  Het Azure-abonnement waarmee de IoT Hub is gemaakt |
   |IoT Hub   |  De naam van de IoT Hub waarbij het IoT Edge-apparaat is geregistreerd en selecteer vervolgens de optie op een apparaat implementeren|
   |Naam van IoT Edge apparaat  |  De naam van het IoT Edge apparaat waarop de SQL-rand moet worden geïmplementeerd |

4. Op de pagina **modules op apparaat instellen:** klikt u op de Azure SQL Edge-module onder **IOT Edge modules**. De standaard module naam is ingesteld op *AzureSQLEdge*. 

5. Geef in de sectie *module-instellingen* van de Blade **Update IOT Edge module** de gewenste waarden op voor de naam van de *IOT Edge module*, *Start het beleid opnieuw* op en de *gewenste status*. 

   > [!IMPORTANT]    
   > Wijzig of werk de URI-instellingen voor de **installatie kopie** niet bij in de module.

6. Geef in de sectie *omgevings variabelen* van de Blade **Update IOT Edge module** de gewenste waarden op voor de omgevings variabelen. Zie [configure using Environment Varia](configure.md#configure-by-using-environment-variables)bles voor een volledige lijst met Azure SQL Edge-omgevings variabelen. De volgende standaard omgevings variabelen zijn voor de module gedefinieerd. 

   |**Parameter**  |**Beschrijving**|
   |---------|---------|
   | MSSQL_SA_PASSWORD  | Wijzig de standaard waarde om een sterk wacht woord op te geven voor het SQL Edge-beheerders account. |
   | MSSQL_LCID   | Wijzig de standaard waarde om de gewenste taal-ID in te stellen voor gebruik voor SQL-rand. Bijvoorbeeld, 1036 is Frans. |
   | MSSQL_COLLATION | Wijzig de standaard waarde om de standaard sortering voor de SQL-rand in te stellen. Deze instelling overschrijft de standaard toewijzing van taal-ID (LCID) in sortering. |

   > [!IMPORTANT]    
   > Wijzig of werk de **ACCEPT_EULA** omgevings variabele voor de module niet bij.

7. Werk in de sectie *Opties voor container maken* van de Blade **Update IOT Edge module** de volgende opties bij volgens vereiste. 
   - **Poort van host:** Wijs de opgegeven host poort toe aan poort 1433 (standaard SQL-poort) in de container.
   - **Bindingen** en **koppelen:** als u meer dan één SQL-rand module moet implementeren, moet u ervoor zorgen dat u de koppelings optie bijwerkt om een nieuw bron & doel paar te maken voor het permanente volume. Zie [volumes gebruiken](https://docs.docker.com/storage/volumes/) in docker-documentatie voor meer informatie over koppels en volumes. 

   ```json
   {
    "HostConfig": {
        "CapAdd": [
            "SYS_PTRACE"
        ],
        "Binds": [
            "sqlvolume:/sqlvolume"
        ],
        "PortBindings": {
            "1433/tcp": [
                {
                    "HostPort": "1433"
                }
            ]
        },
        "Mounts": [
            {
                "Type": "volume",
                "Source": "sqlvolume",
                "Target": "/var/opt/mssql"
            }
        ]
    },
    "Env": [
        "MSSQL_AGENT_ENABLED=TRUE",
        "ClientTransportType=AMQP_TCP_Only",
        "PlanId=asde-developer-on-iot-edge"
    ]
   }
   ```
   > [!IMPORTANT]    
   > Wijzig de `PlanId` omgevings variabele die is gedefinieerd in de instelling configuratie maken niet. Als deze waarde wordt gewijzigd, kan de Azure SQL Edge-container niet worden gestart. 
   
8. Klik in het deel venster **IOT Edge module bijwerken** op **bijwerken**.
9. Klik op de pagina **modules op apparaat instellen** op **volgende: routes >** als u routes voor uw implementatie wilt definiëren. Klik anders op **beoordeling + maken**. Zie [modules implementeren en routes instellen in IOT Edge](../iot-edge/module-composition.md)voor meer informatie over het configureren van routes.
11. Klik op de pagina **modules op apparaat instellen** op **maken**.

## <a name="connect-to-azure-sql-edge"></a>Verbinding maken met Azure SQL Edge

In de volgende stappen wordt het Azure SQL Edge-opdrachtregelprogramma, **sqlcmd**, in de container gebruikt om verbinding te maken met Azure SQL Edge.

> [!NOTE]      
> SQL-opdracht regel programma's (Sqlcmd) zijn niet beschikbaar in de ARM64-versie van Azure SQL Edge-containers.

1. Gebruik de opdracht `docker exec -it` voor het starten van een interactieve bash-shell in de container die wordt uitgevoerd. In het volgende voor beeld `azuresqledge` is de naam die is opgegeven door de `Name` para meter van uw IOT Edge-module.

   ```bash
   sudo docker exec -it azuresqledge "bash"
   ```

2. Maak als u in de container bent lokaal verbinding met sqlcmd. sqlcmd bevindt zich standaard niet in het pad, dus u moet het volledige pad opgeven.

   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P "<YourNewStrong@Passw0rd>"
   ```

   > [!TIP]    
   > U kunt het wachtwoord op de opdrachtregel weglaten om te worden gevraagd het in te voeren.

3. Als dit is gelukt, krijgt u een opdrachtprompt voor **sqlcmd**: `1>`.

## <a name="create-and-query-data"></a>Gegevens maken en er query's op uitvoeren

In de volgende secties wordt uitgelegd hoe u **Sqlcmd** en Transact-SQL kunt gebruiken om een nieuwe Data Base te maken, gegevens toe te voegen en een query uit te voeren.

### <a name="create-a-new-database"></a>Een nieuwe database maken

Met de volgende stappen maakt u een nieuwe database met de naam `TestDB`.

1. Plak de volgende Transact-SQL-opdracht van de opdrachtprompt van **sqlcmd** om een testdatabase te maken:

   ```sql
   CREATE DATABASE TestDB
   Go
   ```

2. Schrijf op de volgende regel een query om de naam van alle databases op uw server te retourneren:

   ```sql
   SELECT Name from sys.Databases
   Go
   ```

### <a name="insert-data"></a>Gegevens invoegen

Maak vervolgens een nieuwe tabel, `Inventory`, en voeg twee nieuwe rijen in.

1. Ga vanuit de opdrachtprompt van **sqlcmd** naar de nieuwe `TestDB`-database:

   ```sql
   USE TestDB
   ```

2. Maak een nieuwe tabel met de naam `Inventory`:

   ```sql
   CREATE TABLE Inventory (id INT, name NVARCHAR(50), quantity INT)
   ```

3. Voeg gegevens in de nieuwe tabel in:

   ```sql
   INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (2, 'orange', 154);
   ```

4. Typ `GO` om de vorige opdrachten uit te voeren:

   ```sql
   GO
   ```

### <a name="select-data"></a>Gegevens selecteren

Voer nu een query uit om gegevens op te halen uit de tabel `Inventory`.

1. Voer vanuit de opdrachtprompt van **sqlcmd** een query in die rijen uit de tabel `Inventory` retourneert, waarbij de hoeveelheid groter is dan 152:

   ```sql
   SELECT * FROM Inventory WHERE quantity > 152;
   ```

2. Voer de opdracht uit:

   ```sql
   GO
   ```

### <a name="exit-the-sqlcmd-command-prompt"></a>De sqlcmd-opdrachtprompt afsluiten

1. Als u uw **sqlcmd**-sessie wilt beëindigen, typt u `QUIT`:

   ```sql
   QUIT
   ```

2. Als u de interactieve opdrachtprompt in de container wilt afsluiten, typt u `exit`. Uw container blijft actief nadat u de interactieve bash-shell hebt afgesloten.

## <a name="connect-from-outside-the-container"></a>Verbinding maken van buiten de container

U kunt vanuit elk extern Linux-, Windows-of macOS-hulp programma verbinding maken met SQL-query's en deze uitvoeren op een exemplaar van de Azure SQL-rand die SQL-verbindingen ondersteunt. Zie [Verbinding maken met en query's uitvoeren op Azure SQL Edge](./connect.md) voor meer informatie over het maken van een verbinding met een SQL Edge-container van buiten.

In deze Quick Start hebt u een SQL Edge-module geïmplementeerd op een IoT Edge apparaat. 

## <a name="next-steps"></a>Volgende stappen

- [Machine Learning en kunst matige intelligentie met ONNX in SQL Edge](onnx-overview.md)
- [Een end-to-end IoT-oplossing bouwen met SQL Edge met behulp van IoT Edge](tutorial-deploy-azure-resources.md)
- [Gegevensstreaming in Azure SQL Edge](stream-data.md)
- [Implementatiefouten oplossen](troubleshoot.md)