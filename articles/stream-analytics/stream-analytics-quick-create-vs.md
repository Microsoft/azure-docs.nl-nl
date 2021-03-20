---
title: 'Quickstart: een Azure Stream Analytics-taak maken met behulp van Visual Studio'
description: In deze snelstart wordt getoond hoe u aan de slag kunt door een Stream Analytics-taak te maken, invoer en uitvoer te configureren en een query te definiëren met Visual Studio.
author: enkrumah
ms.author: ebnkruma
ms.date: 06/11/2019
ms.topic: quickstart
ms.service: stream-analytics
ms.openlocfilehash: 7a02d0914ae111d6e2e00493fe27613e27d836b1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98012356"
---
# <a name="quickstart-create-an-azure-stream-analytics-job-by-using-visual-studio"></a>Quickstart: Een Azure Stream Analytics-taak maken met behulp van Visual Studio

Deze snelstart laat zien hoe u een Stream Analytics-taak kunt maken en uitvoeren met Azure Stream Analytics-tools voor Visual Studio. De voorbeeldtaak leest streaminggegevens vanaf een IoT Hub-apparaat. U definieert een taak die de gemiddelde temperatuur bij meer dan 27° berekent en de resulterende uitvoergebeurtenissen naar een nieuw bestand in blobopslag schrijft.

> [!NOTE]
> De hulpprogramma's Visual Studio en Visual Studio Code bieden geen ondersteuning voor taken in de regio's China - oost, China - noord, Duitsland - centraal en Duitsland - noordoost.

## <a name="before-you-begin"></a>Voordat u begint

* Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) aan.

* Meld u aan bij de [Azure-portal](https://portal.azure.com/).

* Installeer Visual Studio 2019, Visual Studio 2015 of Visual Studio 2013 Update 4. Enterprise- (Ultimate/Premium), Professional- en Community-edities worden ondersteund. De Express-editie wordt niet ondersteund.

* Volg de [installatie-instructies](./stream-analytics-tools-for-visual-studio-install.md) om Stream Analytics-tools voor Visual Studio te installeren.

## <a name="prepare-the-input-data"></a>De invoergegevens voorbereiden

Voordat u de Stream Analytics-taak definieert, moet u de gegevens voorbereiden die later worden geconfigureerd als de taakinvoer. Voltooi de volgende stappen om de invoergegevens voor te bereiden die zijn vereist voor de taak:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

2. Selecteer **Een resource maken** > **Internet of Things** > **IoT Hub**.

3. Voer in het deelvenster **IoT Hub** de volgende informatie in:
   
   |**Instelling**  |**Voorgestelde waarde**  |**Beschrijving**  |
   |---------|---------|---------|
   |Abonnement  | \<Your subscription\> |  Selecteer het Azure-abonnement dat u wilt gebruiken. |
   |Resourcegroep   |   asaquickstart-resourcegroup  |   Selecteer **Nieuwe maken** en voer een naam voor de nieuwe resourcegroep voor uw account in. |
   |Regio  |  \<Select the region that is closest to your users\> | Selecteer een geografische locatie waar u de IoT-hub kunt hosten. Gebruik de locatie die het dichtst bij uw gebruikers is. |
   |Naam van de IoT-hub  | MyASAIoTHub  |   Selecteer een naam voor de IoT-hub.   |

   ![Een IoT Hub maken](./media/stream-analytics-quick-create-vs/create-iot-hub.png)

4. Selecteer **Volgende: Grootte instellen en schaal aanpassen**.

5. Kies uw **prijs- en schaalcategorie**. Selecteer voor deze quickstart de categorie **F1 - Gratis** als deze nog beschikbaar is voor uw abonnement. Als de categorie Gratis niet beschikbaar is, kiest u de laagste beschikbare categorie. Zie [Prijsinformatie IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/) voor meer informatie.

   ![Grootte en schaal van de IoT-hub aanpassen](./media/stream-analytics-quick-create-vs/iot-hub-size-and-scale.png)

6. Selecteer **Controleren + maken**. Controleer de informatie van de IoT-hub en klik op **Maken**. Het kan enkele minuten duren voordat de IoT-hub is gemaakt. U kunt de voortgang bewaken via het deelvenster **Meldingen**.

7. Klik in het IoT Hub-navigatiemenu onder **IoT-apparaten** op **Toevoegen**. Voeg een **Apparaat-id** toe en klik op **Opslaan**.

   ![Een apparaat toevoegen aan uw IoT-hub](./media/stream-analytics-quick-create-vs/add-device-iot-hub.png)

8. Zodra het apparaat is gemaakt, wordt het geopend vanuit de lijst **IoT-apparaten**. Kopieer de **Verbindingsreeks -- primaire sleutel** en sla deze in een kladblok op voor later gebruik.

   ![Verbindingsreeks voor IoT Hub-apparaat kopiëren](./media/stream-analytics-quick-create-vs/save-iot-device-connection-string.png)

## <a name="create-blob-storage"></a>Blob-opslag maken

1. Selecteer in de linkerbovenhoek in Azure Portal **Een resource maken** > **Storage** > **Storage-account**.

2. Voer in het deelvenster **Opslagaccount maken** een opslagaccountnaam, locatie en resourcegroep in. Kies dezelfde locatie en resourcegroep als de IoT-hub die u hebt gemaakt. Klik vervolgens op **Controleren en maken** om het account te maken.

   ![Een opslagaccount maken](./media/stream-analytics-quick-create-portal/create-storage-account.png)

3. Zodra het opslagaccount is gemaakt, selecteert u in het deelvenster **Overzicht** de tegel **Blobs**.

   ![Overzicht van opslagaccounts](./media/stream-analytics-quick-create-portal/blob-storage.png)

4. Selecteer op de pagina **Blob Service** de optie **Container** en geef een naam op voor de container, bijvoorbeeld *container1*. Laat **Niveau openbare toegang** staan op **Privé (geen anonieme toegang)** en selecteer **OK**.

   ![Blob-container maken](./media/stream-analytics-quick-create-portal/create-blob-container.png)

## <a name="create-a-stream-analytics-project"></a>Een Stream Analytics-project maken

1. Start Visual Studio.

2. Selecteer **Bestand > Nieuw > Project**.  

3. Selecteer **Stream Analytics** in de sjablonenlijst aan de linkerkant en selecteer vervolgens **Azure Stream Analytics Application**.  

4. Voer de **Naam**, **Locatie** en **Oplossingsnaam** van het project in en selecteer **OK**.

   ![Een Stream Analytics-project maken](./media/stream-analytics-quick-create-vs/create-stream-analytics-project.png)

Let op de elementen die zijn opgenomen in een Azure Stream Analytics-project.

   <img src="./media/stream-analytics-quick-create-vs/stream-analytics-project.png" alt="Azure Stream Analytics project elements" width="300px"/>


## <a name="choose-the-required-subscription"></a>Kies het vereiste abonnement

1. Selecteer in Visual Studio in het menu **Beeld** de optie **Server Explorer**.

2. Klik met de rechtermuisknop op **Azure**, selecteer **Verbinding maken met Microsoft Azure-abonnement** en meld u aan met uw Azure-account.

## <a name="define-input"></a>Invoer definiëren

1. Vouw in **Solution Explorer** het knooppunt **Inputs** uit en dubbelklik op **Input.json**.

2. Vul de **Stream Analytics-invoerconfiguratie** in met de volgende waarden:

   |**Instelling**  |**Voorgestelde waarde**  |**Beschrijving**   |
   |---------|---------|---------|
   |Invoeralias  |  Invoer   |  Voer een unieke naam in voor de invoer van de taak.   |
   |Brontype   |  Gegevensstroom |  Kies de juiste invoerbron: Gegevensstroom of Verwijzingsgegevens.   |
   |Bron  |  IoT Hub |  Kies de juiste invoerbron.   |
   |Resource  | Kies gegevensbron van het huidige account | Kies ervoor om gegevens handmatig in te voeren of selecteer een bestaand account.   |
   |Abonnement  |  \<Your subscription\>   | Selecteer het Azure-abonnement met de IoT-hub die u hebt gemaakt.   |
   |IoT Hub  |  MyASAIoTHub   |  Kies uw IoT-hub, of voer de naam ervan in. Namen van IoT-hubs worden automatisch gedetecteerd als ze worden gemaakt in hetzelfde abonnement.   |
   
3. De andere opties kunnen de standaardwaarden behouden. Selecteer **Opslaan** om de instellingen op te slaan.  

   ![Invoergegevens configureren](./media/stream-analytics-quick-create-vs/stream-analytics-vs-input.png)

## <a name="define-output"></a>Uitvoer definiëren

1. Vouw in **Solution Explorer** het knooppunt **Outputs** uit en dubbelklik op **Output.json**.

2. Vul de **Stream Analytics-uitvoerconfiguratie** in met de volgende waarden:

   |**Instelling**  |**Voorgestelde waarde**  |**Beschrijving**   |
   |---------|---------|---------|
   |Uitvoeralias  |  Uitvoer   |  Voer een unieke naam in voor de uitvoer van de taak.   |
   |Sink   |  Blob Storage |  Kies de juiste sink.    |
   |Resource  |  Gegevensbroninstellingen handmatig opgeven |  Kies ervoor om gegevens handmatig in te voeren of selecteer een bestaand account.   |
   |Abonnement  |  \<Your subscription\>   | Selecteer het Azure-abonnement met het opslagaccount dat u hebt gemaakt. Het opslagaccount kan voor hetzelfde of een ander abonnement gelden. Voor dit voorbeeld wordt aangenomen dat u een opslagaccount voor hetzelfde abonnement hebt gemaakt.   |
   |Opslagaccount  |  asaquickstartstorage   |  Kies of typ de naam van het opslagaccount. Namen van opslagaccounts worden automatisch gedetecteerd als ze worden gemaakt in hetzelfde abonnement.   |
   |Container  |  container1   |  Selecteer de bestaande container die u in uw opslagaccount hebt gemaakt.   |
   |Padpatroon  |  output   |  Voer de naam in van een bestandspad dat in de container moet worden gemaakt.   |
   
3. De andere opties kunnen de standaardwaarden behouden. Selecteer **Opslaan** om de instellingen op te slaan.  

   ![Uitvoergegevens configureren](./media/stream-analytics-quick-create-vs/stream-analytics-vs-output.png)

## <a name="define-the-transformation-query"></a>De transformatiequery definiëren

1. Open **Script.asaql** vanuit **Solution Explorer** in Visual Studio.

2. Voeg de volgende query toe:

   ```sql
   SELECT *
   INTO Output
   FROM Input
   HAVING Temperature > 27
   ```

## <a name="submit-a-stream-analytics-query-to-azure"></a>Een Stream Analytics-query naar Azure verzenden

1. Selecteer in de **Query-editor** **Verzenden naar Azure** in de scripteditor.

2. Selecteer **Een nieuwe Azure Stream Analytics-taak** maken en voer een **taaknaam** in. Kies het **Abonnement**, de **Resourcegroep** en de **Locatie** die u aan het begin van de snelstart hebt gebruikt.

   ![Verzenden van de taak naar Azure](./media/stream-analytics-quick-create-vs/stream-analytics-job-to-azure.png)

## <a name="run-the-iot-simulator"></a>De IoT-simulator uitvoeren

1. Open de [Raspberry Pi Azure IoT Online Simulator](https://azure-samples.github.io/raspberry-pi-web-simulator/) in een nieuw browsertabblad of -venster.

2. Vervang de tijdelijke aanduiding in regel 15 door de verbindingsreeks van het Azure IoT Hub-apparaat die u hebt opgeslagen in de vorige sectie.

3. Klik op **Run**. De uitvoer geeft de sensorgegevens en berichten weer die worden verzonden naar de IoT-hub.

   ![Raspberry Pi Azure IoT Online Simulator](./media/stream-analytics-quick-create-portal/ras-pi-connection-string.png)

## <a name="start-the-stream-analytics-job-and-check-output"></a>Stream Analytics-taak starten en uitvoer controleren

1. Wanneer de taak is gemaakt, wordt de taakweergave automatisch geopend. Selecteer de knop met de groene pijl om de taak te starten.

   ![Stream Analytics-taak starten](./media/stream-analytics-quick-create-vs/start-stream-analytics-job-vs.png)

2. Wijzig de **startmodus** voor de uitvoertaak in **JobStartTime** en selecteer **Start**.

   ![Taakconfiguratie starten](./media/stream-analytics-quick-create-vs/stream-analytics-start-configuration.png)

3. Merk op dat de taakstatus is gewijzigd in **Actief** en dat er invoer-/uitvoergebeurtenissen zijn. Dit kan enkele minuten duren.

   ![Actieve Stream Analytics-taak](./media/stream-analytics-quick-create-vs/stream-analytics-job-running.png)

4. Als u resultaten wilt bekijken, selecteert u in het menu **Beeld** **Cloud Explorer** en navigeert u naar het opslagaccount in uw resourcegroep. Dubbelklik onder **Blob Containers** op **container1** en vervolgens op het **uitvoer** bestandspad.

   ![Resultaten weergeven](./media/stream-analytics-quick-create-vs/stream-analytics-vs-results.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u een resourcegroep niet meer nodig hebt, verwijdert u de resourcegroep, de streamingtaak en alle gerelateerde resources. Door de taak te verwijderen, voorkomt u dat de streaming-eenheden die door de taak worden verbruikt, in rekening worden gebracht. Als u denkt dat u de taak in de toekomst nog gaat gebruiken, kunt u deze stoppen en later opnieuw starten wanneer dat nodig is. Als u deze taak niet meer gaat gebruiken, verwijdert u alle resources die in deze snelstart zijn gemaakt. Daarvoor voert u de volgende stappen uit:

1. Selecteer in het menu aan de linkerkant in Azure Portal de optie **Resourcegroepen** en selecteer vervolgens de resource die u hebt gemaakt.  

2. Selecteer op de pagina van uw resourcegroep de optie **Verwijderen**, typ de naam van de resource die u wilt verwijderen in het tekstvak en selecteer vervolgens **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een eenvoudige Stream Analytics-taak met behulp van Visual Studio geïmplementeerd. U kunt Stream Analytics-taken ook implementeren met behulp van de [Azure-portal](stream-analytics-quick-create-portal.md) en [PowerShell](stream-analytics-quick-create-powershell.md). 

Ga voor meer informatie over Azure Stream Analytics-hulpprogramma's voor Visual Studio naar het volgende artikel:

> [!div class="nextstepaction"]
> [Visual Studio gebruiken om Azure Stream Analytics-taken weer te geven](stream-analytics-vs-tools.md)