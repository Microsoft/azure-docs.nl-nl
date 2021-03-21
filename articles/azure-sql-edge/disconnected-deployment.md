---
title: Azure SQL Edge implementeren met Docker - Azure SQL Edge
description: Meer informatie over het implementeren van Azure SQL Edge met Docker
keywords: SQL Edge, container, docker
services: sql-edge
ms.service: sql-edge
ms.topic: quickstart
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: fce098767fffd36376399bbd9396699e3d9fbfd3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93392075"
---
# <a name="deploy-azure-sql-edge-with-docker"></a>Azure SQL Edge implementeren met behulp van Docker

In deze quickstart gebruikt u Docker om de installatiekopie van de Azure SQL Edge-container op te halen en uit te voeren. Maak vervolgens verbinding met **sqlcmd** om uw eerste database te maken en query's uit te voeren.

Deze installatiekopie bestaat uit Azure SQL Edge op basis van Ubuntu 18.04. Het kan worden gebruikt met de Docker Engine 1.8 + op Linux of op Docker voor Mac/Windows.

## <a name="prerequisites"></a>Vereisten

- Docker Engine 1.8 + op een ondersteunde Linux-distributie of Docker voor Mac/Windows. Zie [Docker installeren](https://docs.docker.com/engine/installation/) voor meer informatie. Omdat de installatiekopieën van Azure SQL Edge zijn gebaseerd op Ubuntu 18.04, is het raadzaam om een dockerhost van Ubuntu 18.04 te gebruiken.
- Docker-stuurprogramma voor opslag **overlay2**. Dit is de standaardinstelling voor de meeste gebruikers. Als u merkt dat u deze opslagprovider niet gebruikt en moet wijzigen, raadpleegt u de instructies en waarschuwingen in de [Docker-documentatie voor het configureren van overlay2](https://docs.docker.com/storage/storagedriver/overlayfs-driver/#configure-docker-with-the-overlay-or-overlay2-storage-driver).
- Minimaal 10 GB schijfruimte.
- Minimaal 1 GB aan RAM-geheugen.
- [Hardwarevereisten voor Azure SQL Edge](./features.md#hardware-support).


## <a name="pull-and-run-the-container-image"></a>De containerinstallatiekopie ophalen en uitvoeren

Voordat u de volgende stappen uitvoert, moet u ervoor zorgen dat u de gewenste shell (bash, PowerShell of cmd) bovenaan dit artikel hebt geselecteerd.

1. Haal de Azure SQL Edge-containerinstallatiekopie op vanuit het Microsoft-containerregister.

    - De containerinstallatiekopie van de Azure SQL Edge ophalen
        ```bash
        sudo docker pull mcr.microsoft.com/azure-sql-edge:latest 
        ```

> [!NOTE]
> Voor de bash-opdrachten in dit artikel wordt `sudo` gebruikt. In macOS en Windows is sudo mogelijk niet vereist. Als u in Linux geen sudo wilt gebruiken om Docker uit te voeren, kunt u een Docker-groep configureren en gebruikers aan deze groep toevoegen. Zie [Stappen na de installatie voor Linux](https://docs.docker.com/engine/install/linux-postinstall/) voor meer informatie.

Met de vorige opdracht worden de laatste containerinstallatiekopieën van de Azure SQL Edge opgehaald. Zie [de pagina van de Docker-hub van Azure SQL Edge](https://hub.docker.com/_/microsoft-azure-sql-edge) voor een overzicht van alle beschikbare installatiekopieën.

2. Als u de containerinstallatiekopie wilt uitvoeren met Docker, kunt u de volgende opdracht gebruiken vanuit een bash-shell (Linux/macOS) of een PowerShell-opdrachtprompt met verhoogde bevoegdheden.
    
    - Een Azure SQL Edge-exemplaar starten dat wordt uitgevoerd als Developer Edition
        ```bash
        sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=yourStrong(!)Password' -p 1433:1433 --name azuresqledge -d mcr.microsoft.com/azure-sql-edge
        ```

    - Een Azure SQL Edge-exemplaar starten dat wordt uitgevoerd als Premium Edition
        ```bash
        sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=yourStrong(!)Password' -e 'MSSQL_PID=Premium' -p 1433:1433 --name azuresqledge -d mcr.microsoft.com/azure-sql-edge
        ```
    > [!NOTE]
    > Als u PowerShell gebruikt in Windows om deze opdrachten uit te voeren, gebruikt u dubbele aanhalingstekens in plaats van enkele aanhalingstekens.


    > [!NOTE]
    > Het wachtwoord moet het standaardwachtwoordbeleid van Microsoft SQL Database Engine volgen, anders kan de container de SQL-engine niet instellen en werkt deze niet meer. Standaard moet het wachtwoord ten minste acht tekens lang zijn en tekens bevatten van drie van de volgende vier typen: Hoofdletters, kleine letters, cijfers (0-9) en symbolen. U kunt het foutenlogboek controleren door de opdracht [docker logs](https://docs.docker.com/engine/reference/commandline/logs/) uit te voeren.
    
    De volgende tabel bevat een beschrijving van de parameters in het vorige voorbeeld van `docker run`:

    | Parameter | Beschrijving |
    |-----|-----|
    | **-e "ACCEPT_EULA=Y"** |  De variabele **ACCEPT_EULA** instellen op een willekeurige waarde om te bevestigen dat u de [Licentieovereenkomst voor eindgebruikers](https://go.microsoft.com/fwlink/?linkid=2139274) hebt geaccepteerd. Vereiste instelling voor de Azure SQL Edge-installatiekopie. |
    | **-e "MSSQL_SA_PASSWORD=yourStrong(!)Password"** | Een eigen sterk wachtwoord opgeven dat uit ten minste 8 tekens bestaat en aan de [vereisten voor het Azure SQL Edge-wachtwoord](/sql/relational-databases/security/password-policy) voldoet. Vereiste instelling voor de Azure SQL Edge-installatiekopie. |
    | **-p 1433:1433** | Een TCP-poort in de hostomgeving (eerste waarde) toewijzen aan een TCP-poort in de container (tweede waarde). In dit voorbeeld luistert Azure SQL Edge op TCP 1433 in de container en wordt dit weergegeven voor de poort, 1433, op de host. |
    | **--name azuresqledge** | Een aangepaste naam voor de container opgeven in plaats van een willekeurig gegenereerde naam. Als u meer dan één container uitvoert, kunt u deze naam niet opnieuw gebruiken. |
    | **-d** | De container op de achtergrond uitvoeren (daemon) |

    Zie [Azure SQL Edge met omgevingsvariabelen configureren](configure.md#configure-by-using-environment-variables) voor een volledige lijst met alle Azure SQL Edge-omgevingsvariabelen. U kunt ook een [MSSQL. conf-bestand](configure.md#configure-by-using-an-mssqlconf-file) gebruiken voor het configureren van Azure SQL Edge-containers.

3. Als u uw Docker-containers wilt weergeven, gebruikt u de opdracht `docker ps`.
   
   ```bash
    sudo docker ps -a
   ```

4. Als in de kolom **STATUS** de status **Actief** wordt weergegeven, wordt Azure SQL Edge uitgevoerd in de container en wordt er geluisterd op de poort die is opgegeven in de kolom **POORTEN**. Als in de kolom **STATUS** voor uw Azure SQL Edge-container **Afgesloten** wordt weergegeven, raadpleegt u de sectie Probleemoplossing van de Azure SQL Edge-documentatie.

    De parameter `-h` (hostnaam) is ook handig, maar deze wordt niet in deze zelfstudie gebruikt om het eenvoudig te houden. Hiermee wordt de interne naam van de container gewijzigd in een aangepaste waarde. Dit is de naam die wordt weergegeven in de volgende Transact-SQL-query:

    ```sql
    SELECT @@SERVERNAME,
        SERVERPROPERTY('ComputerNamePhysicalNetBIOS'),
        SERVERPROPERTY('MachineName'),
        SERVERPROPERTY('ServerName')
    ```

    Het instellen van `-h` en `--name` op dezelfde waarde is een goede manier om de doelcontainer gemakkelijk te identificeren.

5. Als laatste stap wijzigt u uw SA-wachtwoord omdat de `SA_PASSWORD` zichtbaar is in uitvoer van `ps -eax` en wordt opgeslagen in de omgevingsvariabele met dezelfde naam. Zie de onderstaande stappen.

## <a name="change-the-sa-password"></a>Het SA-wachtwoord wijzigen

Het **SA**-account is een systeembeheerder op het exemplaar van de Azure SQL Edge dat tijdens de installatie wordt gemaakt. Nadat u de Azure SQL Edge-container hebt gemaakt, kan de door u opgegeven omgevingsvariabele `MSSQL_SA_PASSWORD` worden gedetecteerd door `echo $SA_PASSWORD` in de container uit te voeren. Wijzig het SA-wachtwoord om veiligheidsredenen.

1. Kies een sterk wachtwoord voor de SA-gebruiker.

2. Gebruik `docker exec` om **sqlcmd** uit te voeren om het wachtwoord te wijzigen met behulp van Transact-SQL. Vervang in het volgende voorbeeld het oude wachtwoord, `<YourStrong!Passw0rd>`, en het nieuwe wachtwoord, `<YourNewStrong!Passw0rd>`, door uw eigen wachtwoorden.

   ```bash
   sudo docker exec -it azuresqledge /opt/mssql-tools/bin/sqlcmd \
      -S localhost -U SA -P "<YourStrong@Passw0rd>" \
      -Q 'ALTER LOGIN SA WITH PASSWORD="<YourNewStrong@Passw0rd>"'
   ```

## <a name="connect-to-azure-sql-edge"></a>Verbinding maken met Azure SQL Edge

In de volgende stappen wordt het Azure SQL Edge-opdrachtregelprogramma, **sqlcmd**, in de container gebruikt om verbinding te maken met Azure SQL Edge.

> [!NOTE]
> Het hulpprogramma sqlcmd is niet beschikbaar in de ARM64-versie van SQL Edge-containers.

1. Gebruik de opdracht `docker exec -it` voor het starten van een interactieve bash-shell in de container die wordt uitgevoerd. In het volgende voorbeeld is `azuresqledge` de naam die is opgegeven door de parameter `--name` bij het maken van de container.

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

In de volgende secties wordt uitgelegd hoe u **sqlcmd** en Transact-SQL kunt gebruiken om een nieuwe database te maken, gegevens toe te voegen en een eenvoudige query uit te voeren.

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

U kunt ook verbinding maken met het Azure SQL Edge-exemplaar op uw Docker-machine vanuit elk extern Linux-, Windows- of macOS-hulpprogramma dat SQL-verbindingen ondersteunt. Zie [Verbinding maken met en query's uitvoeren op Azure SQL Edge](connect.md) voor meer informatie over het maken van een verbinding met een SQL Edge-container van buiten.

## <a name="remove-your-container"></a>De container verwijderen

Als u de Azure SQL Edge-container die in deze zelfstudie wordt gebruikt wilt verwijderen, voert u de volgende opdrachten uit:

```bash
sudo docker stop azuresqledge
sudo docker rm azuresqledge
```

> [!WARNING]
> Als u een container stopt en verwijdert, worden alle Azure SQL Edge-gegevens in de container definitief verwijderd. Als u uw gegevens wilt behouden, [maakt en kopieert u een back-upbestand vanuit de container](backup-restore.md) of gebruikt u een [techniek voor containergegevenspersistentie](configure.md#persist-your-data).

## <a name="next-steps"></a>Volgende stappen

- [Machine Learning en kunstmatige intelligentie met ONNX in SQL Edge](onnx-overview.md).
- [Een end-to-end IoT-oplossing bouwen met SQL Edge met behulp van IoT Edge](tutorial-deploy-azure-resources.md).
- [Gegevensstreaming in Azure SQL Edge](stream-data.md)