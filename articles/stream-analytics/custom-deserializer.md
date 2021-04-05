---
title: 'Zelfstudie: Aangepaste .NET-deserializers voor Azure Stream Analytics-cloudtaken'
description: In deze zelfstudie wordt gedemonstreerd hoe u een aangepaste .NET-deserializer maakt voor een Azure Stream Analytics-cloudtaak met behulp van Visual Studio.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/17/2020
ms.openlocfilehash: 6db65d85b5d5d72e7b4764949abcffed004cae85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98018204"
---
# <a name="tutorial-custom-net-deserializers-for-azure-stream-analytics"></a>Zelfstudie: Aangepaste .NET-deserializers voor Azure Stream Analytics

Azure Stream Analytics heeft [ingebouwde ondersteuning voor drie gegevensindelingen](stream-analytics-parsing-json.md): JSON, CSV en Avro. Met aangepaste .NET-deserializers kunt u gegevens lezen uit andere indelingen, zoals [Protocol Buffer](https://developers.google.com/protocol-buffers/), [Bond](https://github.com/Microsoft/bond) en andere door de gebruiker gedefinieerde indelingen voor zowel cloud- als Edge-taken.

In deze zelfstudie wordt gedemonstreerd hoe u een aangepaste .NET-deserializer maakt voor een Azure Stream Analytics-cloudtaak met behulp van Visual Studio. Raadpleeg [.NET-deserializers voor Azure Stream Analytics-taken maken in Visual Studio Code](visual-studio-code-custom-deserializer.md) voor meer informatie over het maken van .NET-deserializers in Visual Studio Code.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een aangepaste deserializer maken voor protocol buffer.
> * Een Azure Stream Analytics-taak maken in Visual Studio.
> * Uw Stream Analytics-taak configureren voor het gebruik van de aangepaste deserializer.
> * Uw Stream Analytics-taak lokaal testen om de aangepaste deserializer te testen en fouten op te sporen.


## <a name="prerequisites"></a>Vereisten

* Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan.

* Installeer [Visual Studio 2019 (aanbevolen)](https://www.visualstudio.com/downloads/) of [Visual Studio 2017](https://www.visualstudio.com/vs/older-downloads/). Enterprise- (Ultimate/Premium), Professional- en Community-edities worden ondersteund. De Express-editie wordt niet ondersteund. 

* [Installeer de Stream Analytics-hulpprogramma's voor Visual Studio](stream-analytics-tools-for-visual-studio-install.md) of werk bij naar de nieuwste versie. 

* Open **Cloud Explorer-** in Visual Studio en meld u aan bij uw Azure-abonnement.

* Maak een container in uw Azure Storage-account.
De container die u maakt, wordt gebruikt voor het opslaan van assets die zijn gerelateerd aan uw Stream Analytics-taak. Als u al een opslagaccount met bestaande containers hebt, kunt u die gebruiken. Zo niet, [maak dan een nieuwe container](../storage/blobs/storage-quickstart-blobs-portal.md).

## <a name="create-a-custom-deserializer"></a>Een aangepaste deserializer maken

1. Open Visual Studio en selecteer **Bestand > Nieuw > Project**. Zoek naar **Stream Analytics** en selecteer **Azure Stream Analytics Custom Deserializer Project (.NET)** . Geef het project een naam, zoals **Protobuf Deserializer**.

   ![Een Visual Studio DotNet Standard-klassebibliotheekproject maken](./media/custom-deserializer/create-dotnet-library-project.png)

2. Klik in Solution Explorer met de rechtermuisknop op uw **Protobuf Deserializer**-project en selecteer in het menu **NuGet-pakketten beheren**. Installeer vervolgens de NuGet-pakketten **Microsoft.Azure.StreamAnalytics** en **Google.Protobuf**.

3. Voeg de [MessageBodyProto-klasse](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyProto.cs) en de [MessageBodyDeserializer-klasse](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/MessageBodyDeserializer.cs) toe aan uw project.

4. Bouw het **Protobuf Deserializer**-project.

## <a name="add-an-azure-stream-analytics-project"></a>Een Azure Stream Analytics-project toevoegen

1. Klik in Solution Explorer met de rechtermuisknop op de **Protobuf Deserializer**-oplossing en selecteer **Toevoegen > Nieuw project**. Onder **Azure Stream Analytics > Stream Analytics** kiest u **Azure Stream Analytics Application**. Noem deze **ProtobufCloudDeserializer** en selecteer **OK**. 

2. Klik met de rechtermuisknop op **Verwijzingen** onder het Azure Stream Analytics-project **ProtobufCloudDeserializer**. Onder **Projecten** voegt u **Protobuf Deserializer** toe. Deze moet automatisch voor u worden ingevuld.

## <a name="configure-a-stream-analytics-job"></a>Een Stream Analytics-taak configureren

1. Dubbelklik op **JobConfig.json**. Gebruik de standaardconfiguraties, met uitzondering van de volgende instellingen:

   |Instelling|Voorgestelde waarde|
   |-------|---------------|
   |Resource globale opslaginstellingen|Kies gegevensbron van het huidige account|
   |Abonnement voor globale opslaginstellingen| < uw abonnement >|
   |Globale opslaginstellingen opslagaccount| < uw opslagaccount >|
   |Resource aangepaste code opslaginstellingen|Kies gegevensbron van het huidige account|
   |Aangepaste code opslaginstellingen opslagaccount|< uw opslagaccount >|
   |Container aangepaste code opslaginstellingen|< uw opslagcontainer >|

2. Onder **Invoeren** dubbelklikt u op **Input.json**. Gebruik de standaardconfiguraties, met uitzondering van de volgende instellingen:

   |Instelling|Voorgestelde waarde|
   |-------|---------------|
   |Bron|Blob Storage|
   |Resource|Kies gegevensbron van het huidige account|
   |Abonnement|< uw abonnement >|
   |Opslagaccount|< uw opslagaccount >|
   |Container|< uw opslagcontainer >|
   |Serialisatie-indeling voor gebeurtenissen|Overige (Protobuf, XML, oorspronkelijk…)|
   |Resource|Laden vanuit ASA-projectverwijzing of CodeBehind|
   |CSharp-assemblynaam|ProtobufDeserializer.dll|
   |Klassenaam|MessageBodyProto.MessageBodyDeserializer|
   |Gebeurteniscompressietype|Geen|

3. Voeg de volgende query toe aan het bestand **Script.asaql**.

   ```sql
   SELECT * FROM Input
   ```

4. Download het [voorbeeld protobuf-invoerbestand](https://github.com/Azure/azure-stream-analytics/blob/master/CustomDeserializers/Protobuf/SimulatedTemperatureEvents.protobuf). In de map **Invoeren** klikt u met de rechtermuisknop op **Input.json** en selecteert u **Lokale invoer toevoegen**. Dubbelklik vervolgens op **local_Input.json** en configureer de volgende instellingen:

   |Instelling|Voorgestelde waarde|
   |-------|---------------|
   |Invoeralias|Invoer|
   |Brontype|Gegevensstroom|
   |Serialisatie-indeling voor gebeurtenissen|Overige (Protobuf, XML, oorspronkelijk…)|
   |CSharp-assemblynaam|ProtobufDeserializer.dll|
   |Klassenaam|MessageBodyProto.MessageBodyDeserializer|
   |Pad van lokaal invoerbestand|< het bestandspad voor het gedownloade voorbeeld protobuf-invoerbestand>|

## <a name="execute-the-stream-analytics-job"></a>De Stream Analytics-taak uitvoeren

1. Open **Script.asaql** en selecteer **Lokaal uitvoeren**.

2. Bekijk de resultaten in **Stream Analytics lokale uitvoeringsresultaten**.

U hebt een aangepaste deserializer geïmplementeerd voor uw Stream Analytics-taak! In deze zelfstudie hebt u de aangepaste deserializer lokaal getest. Voor uw werkelijke gegevens kunt u de invoer en uitvoer op de juiste manier configureren. Verzend de taak vervolgens naar Azure vanuit Visual Studio om uw taak uit te voeren in de cloud met behulp van de aangepaste deserializer die u zojuist hebt geïmplementeerd.

## <a name="debug-your-deserializer"></a>Fouten opsporen in uw deserializer

U kunt lokaal fouten opsporen in uw .NET-deserializer, op dezelfde manier als dat u fouten opspoort in standaard .NET-code.

1. Klik met de rechtermuisknop op de projectnaam **ProtobufCloudDeserializer** en stel dit project in als opstartproject.

2. Voeg onderbrekingspunten in uw functie toe.

3. Druk op **F5** om de foutopsporing te starten. Het programma stopt bij de onderbrekingspunten, zoals verwacht.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u een resourcegroep niet meer nodig hebt, verwijdert u de resourcegroep, de streamingtaak en alle gerelateerde resources. Door de taak te verwijderen, voorkomt u dat de streaming-eenheden die door de taak worden verbruikt, in rekening worden gebracht. Als u denkt dat u de taak in de toekomst nog gaat gebruiken, kunt u deze stoppen en later opnieuw starten wanneer dat nodig is. Als u deze taak niet meer gaat gebruiken, verwijdert u alle resources die in deze zelfstudie zijn gemaakt. Daarvoor voert u de volgende stappen uit:

1. Selecteer in het menu aan de linkerkant in Azure Portal de optie **Resourcegroepen** en selecteer vervolgens de resource die u hebt gemaakt.  

2. Selecteer op de pagina van uw resourcegroep de optie **Verwijderen**, typ de naam van de resource die u wilt verwijderen in het tekstvak en selecteer vervolgens **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een aangepaste .NET-deserializer kunt implementeren voor de protocol buffer-invoerserialisatie. Ga door naar het volgende artikel voor meer informatie over het maken van aangepaste deserializers:

> [!div class="nextstepaction"]
> [Andere .NET-deserializers voor Azure Stream Analytics-taken maken](custom-deserializer-examples.md)