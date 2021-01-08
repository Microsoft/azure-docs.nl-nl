---
title: Aangepaste .NET-deserialisatie maken voor Azure Stream Analytics Cloud taken met Visual Studio code
description: In deze zelf studie wordt gedemonstreerd hoe u een aangepaste .NET-deserialisatie maakt voor een Azure Stream Analytics Cloud taak met Visual Studio code.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/22/2020
ms.openlocfilehash: 1813fb222bca74f355fec52252ce3d77fef06e5d
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98013920"
---
# <a name="create-custom-net-deserializers-for-azure-stream-analytics-in-visual-studio-code"></a>Aangepaste .net-deserialisatie voor Azure stream Analytics maken in Visual Studio code

Azure Stream Analytics heeft [ingebouwde ondersteuning voor drie gegevensindelingen](stream-analytics-parsing-json.md): JSON, CSV en Avro. Met aangepaste .net-deserialisatie kunt u gegevens lezen uit andere indelingen, zoals [protocol buffer](https://developers.google.com/protocol-buffers/), [obligatie](https://github.com/Microsoft/bond) en andere door de gebruiker gedefinieerde notaties voor Cloud taken.

## <a name="custom-net-deserializers-in-visual-studio-code"></a>Aangepaste .NET-deserialers in Visual Studio code

Met Visual Studio code kunt u een aangepaste .NET-deserialisatie maken, testen en fouten opsporen voor een Azure Stream Analytics Cloud taak.

### <a name="prerequisites"></a>Vereisten

* Installeer de [.net core-SDK](https://dotnet.microsoft.com/download) en Start Visual Studio code opnieuw.

* Gebruik deze [Quick](quick-create-visual-studio-code.md) start om te leren hoe u met Visual Studio Code een stream Analytics taak maakt.

### <a name="create-a-custom-deserializer"></a>Een aangepaste deserializer maken

1. Open een Terminal en voer de volgende opdracht uit om een .NET Class-bibliotheek te maken in Visual Studio code voor uw aangepaste deserializer met de naam **ProtobufDeserializer**.

   ```dotnetcli
   dotnet new classlib -o ProtobufDeserializer
   ```

2. Ga naar de projectmap van **ProtobufDeserializer** en installeer de [micro soft. Azure. StreamAnalytics](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics/) -en [Google. protobuf](https://www.nuget.org/packages/Google.Protobuf/) NuGet-pakketten.

   ```dotnetcli
   dotnet add package Microsoft.Azure.StreamAnalytics
   ```

   ```dotnetcli
   dotnet add package Google.Protobuf
   ```

3. Voeg de [MessageBodyProto-klasse](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyProto.cs) en de [MessageBodyDeserializer-klasse](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyDeserializer.cs) toe aan uw project.

4. Bouw het **ProtobufDeserializer** -project.

### <a name="add-an-azure-stream-analytics-project"></a>Een Azure Stream Analytics-project toevoegen

1. Open Visual Studio code en selecteer **CTRL + SHIFT + P** om het opdracht palet te openen. Voer vervolgens ASA in en selecteer **ASA: Nieuw project maken**. Noem deze **ProtobufCloudDeserializer**.

### <a name="configure-a-stream-analytics-job"></a>Een Stream Analytics-taak configureren

1. Dubbelklik op **JobConfig.json**. Gebruik de standaardconfiguraties, met uitzondering van de volgende instellingen:

   |Instelling|Voorgestelde waarde|
   |-------|---------------|
   |Resource globale opslaginstellingen|Kies gegevensbron van het huidige account|
   |Abonnement voor globale opslaginstellingen| < uw abonnement >|
   |Globale opslaginstellingen opslagaccount| < uw opslagaccount >|
   |Opslag account voor CustomCodeStorage-instellingen|< uw opslagaccount >|
   |CustomCodeStorage-instellingen container|< uw opslagcontainer >|

2. Open  in de map **met invoerinput.jsop**. Selecteer **Live-invoer toevoegen** en een invoer van Azure data Lake Storage Gen2/Blob-opslag toevoegen. Kies **selecteren in uw Azure-abonnement**. Gebruik de standaardconfiguraties, met uitzondering van de volgende instellingen:

   |Instelling|Voorgestelde waarde|
   |-------|---------------|
   |Naam|Invoer|
   |Abonnement|< uw abonnement >|
   |Opslagaccount|< uw opslagaccount >|
   |Container|< uw opslagcontainer >|
   |Type serialisatie|**Aangepaste** kiezen|
   |SerializationProjectPath|Selecteer **bibliotheek projectmap kiezen** uit code lens en selecteer het **ProtobufDeserializer** -bibliotheek project dat in de vorige sectie is gemaakt. Selecteer **project bouwen** om het project te bouwen|
   |SerializationClassName|Selecteer **deserialisatie klasse selecteren** in code lens om automatisch de klassenaam en het dll-pad op te vullen|
   |Klassenaam|MessageBodyProto.MessageBodyDeserializer|

   :::image type="content" source="./media/custom-deserializer/create-input-vscode.png" alt-text="Aangepaste invoer voor deserialisatie toevoegen.":::

3. Voeg de volgende query toe aan het bestand **ProtobufCloudDeserializer. asaql** .

   ```sql
   SELECT * FROM Input
   ```

4. Download het [voorbeeld protobuf-invoerbestand](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/SimulatedTemperatureEvents.protobuf). Klik in de map **ingangen** met de rechter muisknop op **input.js** en selecteer **lokale invoer toevoegen**. Dubbel klik vervolgens op **local_input1.js** en gebruik de standaard configuraties, met uitzonde ring van de volgende instellingen.

   |Instelling|Voorgestelde waarde|
   |-------|---------------|
   |Lokaal bestandspad selecteren|Klik op code lens om < het bestandspad voor het gedownloade voor beeld protobuf-invoer bestand te selecteren>|

### <a name="execute-the-stream-analytics-job"></a>De Stream Analytics-taak uitvoeren

1. Open **ProtobufCloudDeserializer. asaql** en selecteer **lokaal uitvoeren** vanuit code lens en kies vervolgens **lokale invoer gebruiken** in de vervolg keuzelijst.

2. Bekijk de resultaten op het tabblad **resultaten** in de weer gave taak diagram aan de rechter kant. U kunt ook op de stappen in het taak diagram klikken om het tussenliggende resultaat weer te geven. Meer informatie vindt [u in het taak diagram lokaal fouten opsporen](debug-locally-using-job-diagram-vs-code.md).

   :::image type="content" source="./media/custom-deserializer/check-local-run-result-vscode.png" alt-text="Controleer het lokale uitvoer resultaat.":::

U hebt een aangepaste deserializer geïmplementeerd voor uw Stream Analytics-taak! In deze zelf studie hebt u de aangepaste deserializer lokaal getest met lokale invoer gegevens. U kunt het ook testen [met Live gegevens invoer in de Cloud](visual-studio-code-local-run-live-input.md). Voor het uitvoeren van de taak in de Cloud, kunt u de invoer en uitvoer op de juiste manier configureren. Verzend de taak vervolgens naar Azure vanuit Visual Studio code om uw taak uit te voeren in de Cloud met behulp van de aangepaste deserializer die u zojuist hebt geïmplementeerd.

### <a name="debug-your-deserializer"></a>Fouten opsporen in uw deserializer

U kunt lokaal fouten opsporen in uw .NET-deserializer, op dezelfde manier als dat u fouten opspoort in standaard .NET-code. 

1. Onderbrekings punten toevoegen in uw .NET-functie.

2. Klik op **uitvoeren** vanuit de activiteiten balk van Visual Studio en selecteer **een launch.jsin het bestand maken**.
   :::image type="content" source="./media/custom-deserializer/create-launch-file-vscode.png" alt-text="Start bestand maken.":::

   Kies **ProtobufCloudDeserializer** en vervolgens **Azure stream Analytics** in de vervolg keuzelijst.
   :::image type="content" source="./media/custom-deserializer/create-launch-file-vscode-2.png" alt-text="Start bestand 2 maken.":::

   Bewerk de **launch.jsin** het bestand om <ASAScript> . asaql te vervangen door ProtobufCloudDeserializer. asaql.
   :::image type="content" source="./media/custom-deserializer/configure-launch-file-vscode.png" alt-text="Start bestand configureren.":::

3. Druk op **F5** om de foutopsporing te starten. Het programma stopt bij de onderbrekingspunten, zoals verwacht. Dit werkt voor zowel lokale invoer als Live invoer gegevens.

   :::image type="content" source="./media/custom-deserializer/debug-vscode.png" alt-text="Fout opsporing aangepaste deserializer.":::

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u een resourcegroep niet meer nodig hebt, verwijdert u de resourcegroep, de streamingtaak en alle gerelateerde resources. Door de taak te verwijderen, voorkomt u dat de streaming-eenheden die door de taak worden verbruikt, in rekening worden gebracht. Als u denkt dat u de taak in de toekomst nog gaat gebruiken, kunt u deze stoppen en later opnieuw starten wanneer dat nodig is. Als u deze taak niet meer gaat gebruiken, verwijdert u alle resources die in deze zelfstudie zijn gemaakt. Daarvoor voert u de volgende stappen uit:

1. Selecteer in het menu aan de linkerkant in Azure Portal de optie **Resourcegroepen** en selecteer vervolgens de resource die u hebt gemaakt.  

2. Selecteer op de pagina van uw resourcegroep de optie **Verwijderen**, typ de naam van de resource die u wilt verwijderen in het tekstvak en selecteer vervolgens **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een aangepaste .NET-deserializer kunt implementeren voor de protocol buffer-invoerserialisatie. Ga door naar het volgende artikel voor meer informatie over het maken van aangepaste deserializers:

> [!div class="nextstepaction"]
> * [Andere .NET-deserializers voor Azure Stream Analytics-taken maken](custom-deserializer-examples.md)
> * [Azure Stream Analytics taken lokaal met live input testen met Visual Studio code](visual-studio-code-local-run-live-input.md)