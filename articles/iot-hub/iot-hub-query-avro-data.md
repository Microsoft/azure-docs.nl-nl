---
title: Query's uitvoeren op Avro-gegevens met behulp van Azure Data Lake Analytics | Microsoft Docs
description: Gebruik eigenschappen van bericht hoofdtekst om apparaat-telemetrie te routeren naar Blob-opslag en de Avro-indelings gegevens op te vragen die worden geschreven naar de Blob-opslag.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: 3cfe75edcf338f5248baf396147a5b77803fbfb3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97655931"
---
# <a name="query-avro-data-by-using-azure-data-lake-analytics"></a>Query's uitvoeren op Avro-gegevens met behulp van Azure Data Lake Analytics

In dit artikel wordt beschreven hoe u Avro-gegevens doorzoekt om berichten van Azure IoT Hub efficiënt te routeren naar Azure-Services. Met [bericht routering](iot-hub-devguide-messages-d2c.md) kunt u gegevens filteren met uitgebreide query's op basis van bericht eigenschappen, bericht tekst, dubbele Tags voor apparaten en dubbele eigenschappen van het apparaat. Zie het artikel over de query [syntaxis voor bericht routering](iot-hub-devguide-routing-query-syntax.md)voor meer informatie over de mogelijkheden voor het uitvoeren van Query's in bericht routering.

De uitdaging is dat toen Azure IoT Hub berichten naar Azure Blob-opslag stuurt, standaard IoT Hub de inhoud in de Avro-indeling schrijft, met zowel een eigenschap bericht hoofdtekst als een bericht eigenschap. De Avro-indeling wordt niet gebruikt voor andere eind punten. Hoewel de Avro-indeling geweldig is voor gegevens-en bericht behoud, is het een uitdaging om deze te gebruiken om gegevens op te vragen. De JSON-of CSV-indeling is in vergelijking veel gemakkelijker voor het uitvoeren van query's op gegevens. IoT Hub ondersteunt nu het schrijven van gegevens naar Blob Storage in JSON en in AVRO.

Zie [Azure Storage gebruiken als een eind punt voor route ring](iot-hub-devguide-messages-d2c.md#azure-storage-as-a-routing-endpoint)voor meer informatie.

U kunt veel van de Big Data-patronen gebruiken voor het transformeren en schalen van gegevens, zodat u niet-relationele vereisten en indelingen voor Big data hoeft op te lossen en deze uitdaging te verhelpen. Een van de patronen, ' betalen per query ', is Azure Data Lake Analytics, de focus van dit artikel. Hoewel u de query eenvoudig kunt uitvoeren in Hadoop of andere oplossingen, is Data Lake Analytics vaak beter geschikt voor deze methode ' betalen per gebruik '.

Er is een ' Extractor ' voor Avro in U-SQL. Zie [voor beeld van U-SQL Avro](https://github.com/Azure/usql/tree/master/Examples/AvroExamples)voor meer informatie.

## <a name="query-and-export-avro-data-to-a-csv-file"></a>Avro-gegevens opvragen en exporteren naar een CSV-bestand

In deze sectie gaat u een query uitvoeren op Avro-gegevens en deze exporteren naar een CSV-bestand in Azure Blob-opslag, hoewel u de gegevens in andere opslag plaatsen of gegevens archieven eenvoudig kunt opslaan.

1. Azure IoT Hub instellen om gegevens te routeren naar een Azure Blob Storage-eind punt met behulp van een eigenschap in de hoofd tekst van het bericht om berichten te selecteren.

   ![De sectie Aangepaste eind punten](./media/iot-hub-query-avro-data/query-avro-data-1a.png)

   ![De routerings regels](./media/iot-hub-query-avro-data/query-avro-data-1b.png)

   Zie [bericht routering voor een IOT-hub](iot-hub-create-through-portal.md#message-routing-for-an-iot-hub)voor meer informatie over instellingen voor routes en aangepaste eind punten.

2. Zorg ervoor dat uw apparaat beschikt over de code ring, het inhouds type en de benodigde gegevens in de eigenschappen of de hoofd tekst van het bericht, zoals verwezen in de product documentatie. Wanneer u deze kenmerken in Device Explorer bekijkt, zoals hier wordt weer gegeven, kunt u controleren of ze juist zijn ingesteld.

   ![Het deel venster Event hub-gegevens](./media/iot-hub-query-avro-data/query-avro-data-2.png)

3. Stel een Azure Data Lake Store-exemplaar en een Data Lake Analytics-exemplaar in. Azure IoT Hub stuurt geen route naar een Data Lake Store exemplaar, maar een Data Lake Analytics exemplaar vereist.

   ![Data Lake Store-en Data Lake Analytics-instanties](./media/iot-hub-query-avro-data/query-avro-data-3.png)

4. In Data Lake Analytics configureert u Azure Blob Storage als een extra archief, dezelfde Blob-opslag die door Azure IoT Hub wordt doorgestuurd naar gegevens.

   ![Het deel venster gegevens bronnen](./media/iot-hub-query-avro-data/query-avro-data-4.png)

5. Zoals beschreven in het [u-SQL Avro-voor beeld](https://github.com/Azure/usql/tree/master/Examples/AvroExamples)hebt u vier dll-bestanden nodig. Upload deze bestanden naar een locatie in uw Data Lake Store-exemplaar.

   ![Vier geüploade DLL-bestanden](./media/iot-hub-query-avro-data/query-avro-data-5.png)

6. Maak in Visual Studio een U-SQL-project.

   ![Een U-SQL-project maken](./media/iot-hub-query-avro-data/query-avro-data-6.png)

7. Plak de inhoud van het volgende script in het bestand dat u zojuist hebt gemaakt. Wijzig de drie gemarkeerde secties: uw Data Lake Analytics-account, de bijbehorende DLL-bestands paden en het juiste pad voor uw opslag account.

   ![De drie secties die moeten worden gewijzigd](./media/iot-hub-query-avro-data/query-avro-data-7a.png)

   Het daad werkelijke U-SQL-script voor eenvoudige uitvoer naar een CSV-bestand:

    ```sql
        DROP ASSEMBLY IF EXISTS [Avro];
        CREATE ASSEMBLY [Avro] FROM @"/Assemblies/Avro/Avro.dll";
        DROP ASSEMBLY IF EXISTS [Microsoft.Analytics.Samples.Formats];
        CREATE ASSEMBLY [Microsoft.Analytics.Samples.Formats] FROM @"/Assemblies/Avro/Microsoft.Analytics.Samples.Formats.dll";
        DROP ASSEMBLY IF EXISTS [Newtonsoft.Json];
        CREATE ASSEMBLY [Newtonsoft.Json] FROM @"/Assemblies/Avro/Newtonsoft.Json.dll";
        DROP ASSEMBLY IF EXISTS [log4net];
        CREATE ASSEMBLY [log4net] FROM @"/Assemblies/Avro/log4net.dll";

        REFERENCE ASSEMBLY [Newtonsoft.Json];
        REFERENCE ASSEMBLY [log4net];
        REFERENCE ASSEMBLY [Avro];
        REFERENCE ASSEMBLY [Microsoft.Analytics.Samples.Formats];

        // Blob container storage account filenames, with any path
        DECLARE @input_file string = @"wasb://hottubrawdata@kevinsayazstorage/kevinsayIoT/{*}/{*}/{*}/{*}/{*}/{*}";
        DECLARE @output_file string = @"/output/output.csv";

        @rs =
        EXTRACT
        EnqueuedTimeUtc string,
        Body byte[]
        FROM @input_file

        USING new Microsoft.Analytics.Samples.Formats.ApacheAvro.AvroExtractor(@"
        {
            ""type"":""record"",
            ""name"":""Message"",
            ""namespace"":""Microsoft.Azure.Devices"",
            ""fields"":
           [{
                ""name"":""EnqueuedTimeUtc"",
                ""type"":""string""
            },
            {
                ""name"":""Properties"",
                ""type"":
                {
                    ""type"":""map"",
                    ""values"":""string""
                }
            },
            {
                ""name"":""SystemProperties"",
                ""type"":
                {
                    ""type"":""map"",
                    ""values"":""string""
                }
            },
            {
                ""name"":""Body"",
                ""type"":[""null"",""bytes""]
            }]
        }"
        );

        @cnt =
        SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
        FROM @rs;

        OUTPUT @cnt TO @output_file USING Outputters.Text(); 
    ```

    Het duurde Data Lake Analytics vijf minuten om het volgende script uit te voeren. Dit is beperkt tot 10 analytische eenheden en verwerkte bestanden van 177. Het resultaat wordt weer gegeven in de CSV-bestands uitvoer die wordt weer gegeven in de volgende afbeelding:

    ![Resultaten van de uitvoer naar het CSV-bestand](./media/iot-hub-query-avro-data/query-avro-data-7b.png)

    ![De uitvoer is geconverteerd naar een CSV-bestand](./media/iot-hub-query-avro-data/query-avro-data-7c.png)

    Als u de JSON wilt parseren, gaat u door naar stap 8.

8. De meeste IoT-berichten hebben een JSON-bestands indeling. Door de volgende regels toe te voegen, kunt u het bericht parseren in een JSON-bestand. Hiermee kunt u de WHERE-component toevoegen en alleen de benodigde gegevens uitvoeren.

    ```sql
       @jsonify =
         SELECT Microsoft.Analytics.Samples.Formats.Json.JsonFunctions.JsonTuple(Encoding.UTF8.GetString(Body))
           AS message FROM @rs;
    
        /*
        @cnt =
            SELECT EnqueuedTimeUtc AS time, Encoding.UTF8.GetString(Body) AS jsonmessage
            FROM @rs;
        
        OUTPUT @cnt TO @output_file USING Outputters.Text();
        */
        
        @cnt =
            SELECT message["message"] AS iotmessage,
                message["event"] AS msgevent,
                message["object"] AS msgobject,
                message["status"] AS msgstatus,
                message["host"] AS msghost
            FROM @jsonify;
            
        OUTPUT @cnt TO @output_file USING Outputters.Text();
    ```

    In de uitvoer wordt een kolom voor elk item in de opdracht weer gegeven `SELECT` .

    ![Uitvoer met een kolom voor elk item](./media/iot-hub-query-avro-data/query-avro-data-8.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u Avro-gegevens kunt opvragen om berichten van Azure IoT Hub efficiënt te routeren naar Azure-Services.

Zie de [documentatie van Azure IOT Solution Accelerators](../iot-accelerators/index.yml)voor voor beelden van complete end-to-end-oplossingen die gebruikmaken van IOT hub.

Raadpleeg de [IOT hub ontwikkelaars handleiding](iot-hub-devguide.md)voor meer informatie over het ontwikkelen van oplossingen met IOT hub.

Zie [berichten verzenden en ontvangen met IOT hub](iot-hub-devguide-messaging.md)voor meer informatie over bericht routering in IOT hub.
