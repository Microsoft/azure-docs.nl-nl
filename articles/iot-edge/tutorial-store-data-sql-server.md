---
title: 'Zelfstudie: gegevens opslaan met SQL-module met behulp van Azure IoT Edge'
description: Deze zelfstudie laat zien hoe u lokaal gegevens kunt opslaan op uw IoT-Edge-apparaat met een SQL Server-module
services: iot-edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/04/2020
ms.topic: tutorial
ms.service: iot-edge
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 709c7bf352635ffb1c17f6cb24aee59dcae2e033
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479760"
---
# <a name="tutorial-store-data-at-the-edge-with-sql-server-databases"></a>Zelfstudie: Gegevens aan de rand opslaan met SQL Server-databases

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Implementeer een SQL Server module voor het opslaan van gegevens op een apparaat met Azure IoT Edge Linux-containers.

Gebruik Azure IoT Edge en SQL Server voor het opslaan en opvragen van gegevens aan de rand. Azure IoT Edge heeft basisopslagmogelijkheden waarmee berichten in cache worden geplaatst als een apparaat offline is, en worden doorgestuurd wanneer de verbinding weer tot stand is gebracht. Mogelijk wilt u echter meer geavanceerde opslagmogelijkheden, zoals het lokaal kunnen opvragen van gegevens. Uw IoT Edge-apparaten kunnen lokale databases gebruiken om complexere computing uit te voeren zonder een verbinding met IoT Hub in stand te hoeven houden.

Dit artikel bevat instructies voor het implementeren van een SQL Server-database op een IoT Edge-apparaat. Azure Functions, dat op het IoT Edge-apparaat wordt uitgevoerd, structureert de binnenkomende gegevens en verzendt deze naar de database. De stappen in dit artikel kunnen ook worden toegepast op andere databases die in containers werken, zoals MySQL of PostgreSQL.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
>
> * Visual Studio Code gebruiken om een Azure-functie te maken
> * Een SQL-database op uw IoT Edge-apparaat implementeren
> * Gebruik Visual Studio Code om modules te bouwen en deze op uw IoT Edge-apparaat te implementeren
> * Gegenereerde gegevens weergeven

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

Voordat u met deze zelfstudie begint, moet u de vorige zelfstudie hebben doorlopen om uw ontwikkelomgeving voor Linux-containerontwikkeling in te stellen: [IoT Edge-modules ontwikkelen voor Linux-apparaten](tutorial-develop-for-linux.md). Als u die zelfstudie hebt voltooid, hebt u de volgende vereisten klaarstaan:

* Een gratis of standaard [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)-laag in Azure.
* Een AMD64-apparaat met Azure IoT Edge linux-containers. U kunt de quickstarts gebruiken om een [Linux-apparaat](quickstart-linux.md) of [Windows-apparaat in te stellen.](quickstart.md)
  * Op ARM-apparaten, zoals Raspberry Pi's, kan SQL Server niet worden uitgevoerd. Als u SQL wilt gebruiken op een ARM-apparaat, kunt u [Azure SQL Edge.](../azure-sql-edge/overview.md)
* Een containerregister, zoals [Azure Container Registry](../container-registry/index.yml).
* [Visual Studio Code](https://code.visualstudio.com/) geconfigureerd met de [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* [Docker CE](https://docs.docker.com/install/) geconfigureerd voor het uitvoeren van Linux-containers.

In deze zelfstudie wordt een Azure Functions-module gebruikt om gegevens naar de SQL Server te verzenden. Om een IoT Edge-module te ontwikkelen met Azure Functions, installeert de volgende aanvullende vereisten op uw ontwikkelcomputer:

* [C#-extensie voor Visual Studio Code (van OmniSharp) voor Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).

## <a name="create-a-function-project"></a>Een functieproject maken

Om gegevens naar een database te verzenden hebt u een module nodig die de gegevens correct kan structureren en vervolgens in een tabel opslaat.

### <a name="create-a-new-project"></a>Een nieuw project maken

De volgende stappen laten zien hoe u een IoT-Edge-functie maakt met behulp van Visual Studio Code en de Azure IoT Edge-hulpprogramma's.

1. Open Visual Studio Code.

2. Selecteer **View** > **Command Palette** en open het VS Code-opdrachtpalet.

3. Typ in het opdrachtpalet de opdracht **Azure IoT Edge: New IoT Edge solution** in en voer deze uit. Geef in het opdrachtpalet de volgende informatie op om de oplossing te maken:

   | Veld | Waarde |
   | ----- | ----- |
   | Map selecteren | Kies de locatie op uw ontwikkelcomputer waar VS Code de oplossingsbestanden moet maken. |
   | Een naam opgeven voor de oplossing | Voer een beschrijvende naam voor de oplossing in, bijvoorbeeld **SqlSolution**, of accepteer de standaardinstelling. |
   | Modulesjabloon selecteren | Kies **Azure Functions - C#** . |
   | Een modulenaam opgeven | Noem uw module **sqlFunction**. |
   | Opslagplaats voor Docker-afbeeldingen voor de module opgeven | Een opslagplaats voor afbeeldingen bevat de naam van het containerregister en de naam van uw containerafbeelding. De containerafbeelding wordt vooraf gevuld vanuit de laatste stap. Vervang **localhost:5000** door de waarde van de **aanmeldingsserver** uit uw Azure-containerregister. U vindt de aanmeldingsserver op de overzichtspagina van het containerregister in de Azure-portal. <br><br>De uiteindelijke tekenreeks ziet er ongeveer als volgt uit \<registry name\>.azurecr.io/sqlfunction. |

   In het VS Code-venster wordt de werkruimte van de IoT Edge-oplossing geladen.

### <a name="add-your-registry-credentials"></a>Uw registerreferenties toevoegen

In het omgevingsbestand worden de referenties voor het containerregister opgeslagen. Deze referenties worden gedeeld met de IoT Edge-runtime. De runtime heeft deze referenties nodig om uw persoonlijke installatiekopieën naar het IoT Edge-apparaat te halen.

De IoT Edge-extensie probeert uw containerregisterreferenties van Azure op te halen en deze in het omgevingsbestand in te vullen. Controleer of uw referenties al zijn toegevoegd. Als dat niet het geval is, voegt u ze nu toe:

1. Open in VS Code Explorer het .env-bestand.
2. Werk de velden **gebruikersnaam** en **wachtwoord** bij met de waarden die u hebt gekopieerd uit het Azure-containerregister.
3. Sla dit bestand op.

### <a name="select-your-target-architecture"></a>Uw doelarchitectuur selecteren

U moet voor elke oplossing selecteren op welke architectuur u zich richt, omdat de container voor elk type architectuur anders wordt opgebouwd en anders wordt uitgevoerd. De standaardinstelling is Linux AMD64.

1. Open het opdrachtpalet en zoek **Azure IoT Edge: standaarddoelplatform voor Edge-oplossing instellen** of selecteer het snelkoppelingspictogram onderaan het venster.

2. Selecteer in het opdrachtpalet de doelarchitectuur in de lijst met opties. In deze zelfstudie gebruiken we een virtuele Ubuntu-machine als het IoT Edge-apparaat, dus laten we de standaardinstelling **amd64** staan.

### <a name="update-the-module-with-custom-code"></a>De module bijwerken met aangepaste code

1. Open in VS Code Explorer **modules** > **sqlFunction** > **sqlFunction.csproj**.

2. Zoek de groep met pakketverwijzingen en voeg een nieuwe toe voor SqlClient.

   ```csproj
   <PackageReference Include="System.Data.SqlClient" Version="4.5.1"/>
   ```

3. Sla het bestand **sqlFunction.csproj** op.

4. Open het bestand **sqlFunction.cs**.

5. Vervang de volledige inhoud van het bestand door de volgende code:

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.IO;
   using System.Text;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Client;
   using Microsoft.Azure.WebJobs;
   using Microsoft.Azure.WebJobs.Extensions.EdgeHub;
   using Microsoft.Azure.WebJobs.Host;
   using Microsoft.Extensions.Logging;
   using Newtonsoft.Json;
   using Sql = System.Data.SqlClient;

   namespace Functions.Samples
   {
       public static class sqlFunction
       {
           [FunctionName("sqlFunction")]
           public static async Task FilterMessageAndSendMessage(
               [EdgeHubTrigger("input1")] Message messageReceived,
               [EdgeHub(OutputName = "output1")] IAsyncCollector<Message> output,
               ILogger logger)
           {
               const int temperatureThreshold = 20;
               byte[] messageBytes = messageReceived.GetBytes();
               var messageString = System.Text.Encoding.UTF8.GetString(messageBytes);

               if (!string.IsNullOrEmpty(messageString))
               {
                   logger.LogInformation("Info: Received one non-empty message");
                   // Get the body of the message and deserialize it.
                   var messageBody = JsonConvert.DeserializeObject<MessageBody>(messageString);

                   //Store the data in SQL db
                   const string str = "<sql connection string>";
                   using (Sql.SqlConnection conn = new Sql.SqlConnection(str))
                   {
                       conn.Open();
                       var insertMachineTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'machine', " + messageBody.machine.temperature + ");";
                       var insertAmbientTemperature = "INSERT INTO MeasurementsDB.dbo.TemperatureMeasurements VALUES (CONVERT(DATETIME2,'" + messageBody.timeCreated + "', 127), 'ambient', " + messageBody.ambient.temperature + ");";
                       using (Sql.SqlCommand cmd = new Sql.SqlCommand(insertMachineTemperature + "\n" + insertAmbientTemperature, conn))
                       {
                           //Execute the command and log the # rows affected.
                           var rows = await cmd.ExecuteNonQueryAsync();
                           logger.LogInformation($"{rows} rows were updated");
                       }
                   }

                   if (messageBody != null && messageBody.machine.temperature > temperatureThreshold)
                   {
                       // Send the message to the output as the temperature value is greater than the threashold.
                       using (var filteredMessage = new Message(messageBytes))
                       {
                            // Copy the properties of the original message into the new Message object.
                            foreach (KeyValuePair<string, string> prop in messageReceived.Properties)
                            {filteredMessage.Properties.Add(prop.Key, prop.Value);}
                            // Add a new property to the message to indicate it is an alert.
                            filteredMessage.Properties.Add("MessageType", "Alert");
                            // Send the message.
                            await output.AddAsync(filteredMessage);
                            logger.LogInformation("Info: Received and transferred a message with temperature above the threshold");
                       }
                   }
               }
           }
       }
       //Define the expected schema for the body of incoming messages.
       class MessageBody
       {
           public Machine machine {get; set;}
           public Ambient ambient {get; set;}
           public string timeCreated {get; set;}
       }
       class Machine
       {
           public double temperature {get; set;}
           public double pressure {get; set;}
       }
       class Ambient
       {
           public double temperature {get; set;}
           public int humidity {get; set;}
       }
   }
   ```

6. Vervang op regel 35 de tekenreeks **\<sql connection string\>** door de volgende tekenreeks. De eigenschap **Gegevensbron** verwijst naar de SQL Server-container, die nog niet bestaat. U maakt deze in de volgende sectie, met de naam **SQL**.

   ```csharp
   Data Source=tcp:sql,1433;Initial Catalog=MeasurementsDB;User Id=SA;Password=Strong!Passw0rd;TrustServerCertificate=False;Connection Timeout=30;
   ```

7. Sla het bestand **sqlFunction.cs** op.

## <a name="add-the-sql-server-container"></a>De SQL Server-container toevoegen

Een [distributiemanifest](module-composition.md) declareert welke modules de IoT Edge-runtime op uw IoT Edge-apparaat zal installeren. U hebt de code voor het maken van een ​​aangepaste functiemodule in de vorige sectie opgegeven, maar de SQL Server-module is al gebouwd en beschikbaar in Azure Marketplace. U hoeft alleen aan de IoT Edge-runtime op te geven dat het deze module moet opnemen, en die vervolgens op uw apparaat te configureren.

1. Open in Visual Studio Code het opdrachtpalet door **Beeld** > **Opdrachtpalet** te selecteren.

2. Typ in het opdrachtpalet de opdracht **Azure IoT Edge: IoT Edge-module toevoegen** en voer deze uit. Geef in het opdrachtpalet de volgende informatie op om een nieuwe module toe te voegen:

   | Veld | Waarde |
   | ----- | ----- |
   | Implementatiesjabloonbestand selecteren | Het bestand **deployment.template.json** in uw huidige oplossingsmap wordt gemarkeerd in het opdrachtpalet. Selecteer dat bestand.  |
   | Modulesjabloon selecteren | Selecteer **Module uit Azure Marketplace**. |

3. Zoek in de IoT Edge-module-Marketplace naar **SQL Server Module** en selecteer deze.

4. Wijzig de modulenaam in **sql**, in kleine letters. Deze naam komt overeen met de containernaam die is gedeclareerd in de verbindingsreeks in het bestand sqlFunction.cs.

5. Selecteer **Importeren** om de module toe te voegen aan uw oplossing.

6. Open het bestand **deployment.template.json** in uw oplossingsmap.

7. Ga naar de sectie **modules**. U zou drie modules moeten zien. De module *SimulatedTemperatureSensor* wordt standaard in nieuwe oplossingen opgenomen, en zorgt voor testgegevens om bij uw andere modules te gebruiken. De module *sqlFunction* is de module die u aanvankelijk hebt gemaakt en bijgewerkt met nieuwe code. En ten slotte is de module *sql* geïmporteerd uit Azure Marketplace.

   >[!Tip]
   >De SQL Server-module heeft een standaardwachtwoord dat wordt ingesteld in de omgevingsvariabelen van het distributiemanifest. Telkens wanneer u een SQL Server-container in een productieomgeving maakt, moet u [het standaardwachtwoord van de systeembeheerder](/sql/linux/quickstart-install-connect-docker)wijzigen.

8. Sluit het bestand **deployment.template.json**.

## <a name="build-your-iot-edge-solution"></a>Uw eigen IoT Edge-oplossing bouwen

In de voorgaande secties hebt u een oplossing met één module gemaakt en er vervolgens nog een toegevoegd aan de distributiemanifestsjabloon. De SQL Server-module wordt openbaar door Microsoft gehost, maar u moet de code containeriseren in de module Functions. In deze sectie bouwt u de oplossing, maakt u containerinstallatiekopieën voor de sqlFunction-module en de pusht u de installatiekopieën naar uw containerregister.

1. Open de in Visual Studio Code geïntegreerde terminal door **View** > **Terminal** te selecteren.  

1. Meld u aan bij uw containerregister in Visual Studio Code, zodat u uw installatiekopieën naar uw register kunt pushen. Gebruik dezelfde ACR-referenties (ACR = Azure Container Registry) die u aan het .env-bestand hebt toegevoegd. Voer de volgende opdracht in de geïntegreerde terminal in:

    ```csh/sh
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

    Mogelijk ziet u een beveiligingswaarschuwing die het gebruik van de parameter --password-stdin aanraadt. Hoewel buiten het bestek van dit artikel, wordt u deze best practice aangeraden. Zie de documentatie voor de opdracht [docker login](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) voor meer informatie.

1. Klik in VS Code Explorer met de rechtermuisknop op het bestand **deployment.template.json** en selecteer **Build and Push IoT Edge solution**.

   Met de opdracht voor bouwen en pushen worden drie bewerkingen gestart. Eerst wordt er een nieuwe map met de naam **config** in de oplossing gemaakt die het volledige implementatiemanifest bevat, die is gebouwd op basis van de informatie in de implementatiesjabloon en andere oplossingsbestanden. Daarna wordt `docker build` uitgevoerd om de containerinstallatiekopie te bouwen op basis van de juiste dockerfile voor uw doelarchitectuur. Vervolgens wordt `docker push` uitgevoerd om de opslagplaats van de installatiekopie naar het containerregister te pushen.

   Dit proces kan de eerste keer enkele minuten duren, maar de volgende keer dat u de opdrachten uitvoert, wordt het sneller uitgevoerd.

   U kunt controleren of de sqlFunction-module met succes naar uw containerregister is gepusht. Ga in de Azure-portal naar uw containerregister. Selecteer **opslagplaatsen** en zoek naar **sqlFunction**. De andere twee modules, SimulatedTemperatureSensor en sql, worden niet naar uw containerregister gepusht, omdat hun opslagplaatsen zich al in de Microsoft-registers bevinden.

## <a name="deploy-the-solution-to-a-device"></a>De oplossing implementeren op een apparaat

U kunt modules op een apparaat instellen via de IoT Hub, maar u hebt ook toegang tot uw IoT Hub en apparaten via Visual Studio Code. In dit gedeelte stelt u de toegang tot uw IoT Hub in en gebruikt u VS Code om uw oplossing op uw IoT Edge-apparaat te implementeren.

1. Vouw in de Visual Studio Code Explorer, onder de sectie **Azure IoT Hub**, de optie **Apparaten** uit om de lijst met IoT-apparaten weer te geven.

2. Klik met de rechtermuisknop op het doelapparaat van uw implementatie en selecteer **Implementatie maken voor één apparaat**.

3. Selecteer het bestand **deployment.amd64.json** in de map **config** en klik vervolgens op **Edge-distributiemanifest selecteren**. Gebruik niet het bestand deployment.template.json.

4. Vouw onder uw apparaat **Modules** uit voor een lijst met de geïmplementeerde en actieve modules. Klik op de knop Vernieuwen. U ziet nu dat de nieuwe modules **sql** en **sqlFunction** worden uitgevoerd, samen met de module **SimulatedTemperatureSensor** en de **$edgeAgent** en **$edgeHub**.

    U kunt ook controleren of alle modules op uw apparaat actief zijn. Voer op uw IoT Edge-apparaat de volgende opdracht uit om de status van de modules te bekijken.

   ```cmd/sh
   iotedge list
   ```

    Het kan enkele minuten duren voordat de modules zijn gestart. De IoT Edge-runtime moet het nieuwe implementatiemanifest ontvangen, de module-installatiekopieën uit de containerruntime ophalen en vervolgens elke nieuwe module starten.

## <a name="create-the-sql-database"></a>De SQL-database maken

Wanneer u het distributiemanifest op uw apparaat toepast, krijgt u drie modules die worden uitgevoerd. De module SimulatedTemperatureSensor genereert gesimuleerde omgevingsgegevens. De module sqlFunction neemt de gegevens en deelt deze in voor een database. Deze sectie leidt u door het instellen van de SQL-database voor het opslaan van de temperatuurgegevens.

Voer de volgende opdrachten uit op uw IoT Edge-apparaat. Met deze opdrachten wordt verbinding gemaakt met de **sql**-module die wordt uitgevoerd op uw apparaat en worden er een database en een tabel gemaakt voor de opslag van de temperatuurgegevens die naar de module worden verzonden.

1. Gebruik een opdrachtregelprogramma op uw IoT Edge-apparaat om verbinding te maken met uw database.

      ```bash
      sudo docker exec -it sql bash
      ```

2. Open het SQL-opdrachthulpprogramma.

      ```bash
      /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'Strong!Passw0rd'
      ```

3. Uw database maken:

      ```sql
      CREATE DATABASE MeasurementsDB
      ON
      (NAME = MeasurementsDB, FILENAME = '/var/opt/mssql/measurementsdb.mdf')
      GO
      ```

4. Definieer uw tabel.

   ```sql
   CREATE TABLE MeasurementsDB.dbo.TemperatureMeasurements (measurementTime DATETIME2, location NVARCHAR(50), temperature FLOAT)
   GO
   ```

U kunt het dockerbestand van uw SQL Server aanpassen om uw SQL Server automatisch in te stellen om te worden geïmplementeerd op meerdere IoT Edge-apparaten. Zie het [Microsoft SQL Server-containerdemo-project](https://github.com/twright-msft/mssql-node-docker-demo-app)voor meer informatie.

## <a name="view-the-local-data"></a>De lokale gegevens bekijken

Nadat uw tabel is gemaakt, begint de module sqlFunction gegevens op te slaan in een lokale SQL Server 2017-database op uw IoT Edge-apparaat.

Voer vanuit het SQL-opdrachthulpprogramma de volgende opdracht uit om uw opgemaakte tabelgegevens te bekijken:

   ```sql
   SELECT * FROM MeasurementsDB.dbo.TemperatureMeasurements
   GO
   ```

   ![Inhoud van lokale database bekijken](./media/tutorial-store-data-sql-server/view-data.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent door te gaan met het volgende aanbevolen artikel, kunt u de resources en configuraties die u hebt gemaakt behouden en opnieuw gebruiken. U kunt ook hetzelfde IoT Edge-apparaat blijven gebruiken als een testapparaat.

Anders kunt u de lokale configuraties en Azure-resources die u in dit artikel hebt gemaakt, verwijderen om kosten te voorkomen.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

In deze zelfstudie hebt u een Azure Functions-module gemaakt die code bevat voor het filteren van onbewerkte gegevens die worden gegenereerd door uw IoT Edge-apparaat. Wanneer u klaar bent om uw eigen modules te bouwen, kunt u meer informatie krijgen over het [ontwikkelen van Azure Functions met Azure IoT Edge voor Visual Studio Code](./how-to-vs-code-develop-module.md).

## <a name="next-steps"></a>Volgende stappen

Als u een andere opslagmethode wilt gebruiken aan de rand, lees dan verder over het gebruiken van Azure Blob Storage op IoT Edge.

> [!div class="nextstepaction"]
> [Gegevens opslaan aan de rand met Azure Blob Storage op IoT Edge](how-to-store-data-blob.md)