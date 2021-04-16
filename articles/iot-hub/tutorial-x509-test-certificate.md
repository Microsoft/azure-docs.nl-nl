---
title: 'Zelfstudie: test de mogelijkheid van X.509-certificaten om apparaten te verifiëren bij een Azure IoT Hub | Microsoft Docs'
description: 'Zelfstudie: uw X.509-certificaten testen om te verifiëren bij Azure IoT Hub'
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 7d1900782fce6b84ed79014e985393f3626d171b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379431"
---
# <a name="tutorial-testing-certificate-authentication"></a>Zelfstudie: Certificaatverificatie testen

U kunt het volgende C#-codevoorbeeld gebruiken om te testen of uw certificaat uw apparaat kan verifiëren bij uw IoT Hub. Houd er rekening mee dat u het volgende moet doen voordat u de testcode kunt uitvoeren:

* Maak een basis-CA of onderliggend CA-certificaat.
* Upload uw CA-certificaat naar uw IoT Hub.
* Bewijs dat u over het CA-certificaat beschikt.
* Voeg een apparaat toe aan uw IoT Hub.
* Maak een apparaatcertificaat met dezelfde apparaat-id als uw apparaat.

## <a name="code-example"></a>Codevoorbeeld

In het volgende codevoorbeeld ziet u hoe u een C#-toepassing maakt om het X.509-apparaat te simuleren dat is geregistreerd voor uw IoT-hub. In het voorbeeld worden temperatuur- en vochtigheidswaarden van het gesimuleerde apparaat naar uw hub verzendt. In deze zelfstudie maken we alleen de apparaattoepassing. Het wordt als oefening aan de lezers overgelaten om de IoT Hub-servicetoepassing te maken die antwoorden verzendt op de gebeurtenissen die door dit gesimuleerde apparaat worden verzonden.

1. Open Visual Studio, selecteer **Een nieuw project maken** en kies vervolgens de projectsjabloon **Console-app (.NET Framework).** Selecteer **Next**.

1. In **Uw nieuwe project configureren** noemt u het project *SimulateX509Device* en selecteert u **vervolgens Maken.**

   ![Een X.509-apparaatproject maken in Visual Studio](./media/iot-hub-security-x509-get-started/create-device-project-vs2019.png)

1. Klik Solution Explorer met de rechtermuisknop op het project **SimulateX509Device** en selecteer **vervolgens NuGet-pakketten beheren.**

1. Selecteer in **de NuGet Pakketbeheer** selecteer **Bladeren** en zoek naar en kies **Microsoft.Azure.Devices.Client.** Selecteer **Installeren**.

   ![Apparaat-SDK NuGet-pakket toevoegen in Visual Studio](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

    Met deze stap worden het Azure IoT Device SDK NuGet-pakket en de afhankelijkheden ervan gedownload, geïnstalleerd en toegevoegd.

    Voer de volgende code in en voer deze uit:

```csharp
using System;
using Microsoft.Azure.Devices.Client;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;
using System.Text;

namespace SimulateX509Device
{
    class Program
    {
        private static int MESSAGE_COUNT = 5;

        // Temperature and humidity variables.
        private const int TEMPERATURE_THRESHOLD = 30;
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();

        // Set the device ID to the name (device identifier) of your device.
        private static String deviceId = "{your-device-id}";

        static async Task SendEvent(DeviceClient deviceClient)
        {
            string dataBuffer;
            Console.WriteLine("Device sending {0} messages to IoTHub...\n", MESSAGE_COUNT);

            // Iterate MESSAGE_COUNT times to set randomm termperature and humidity values.
            for (int count = 0; count < MESSAGE_COUNT; count++)
            {
                // Set random values for temperature and humidity.
                temperature = rnd.Next(20, 35);
                humidity = rnd.Next(60, 80);
                dataBuffer = string.Format("{{\"deviceId\":\"{0}\",\"messageId\":{1},\"temperature\":{2},\"humidity\":{3}}}", deviceId, count, temperature, humidity);
                Message eventMessage = new Message(Encoding.UTF8.GetBytes(dataBuffer));
                eventMessage.Properties.Add("temperatureAlert", (temperature > TEMPERATURE_THRESHOLD) ? "true" : "false");
                Console.WriteLine("\t{0}> Sending message: {1}, Data: [{2}]", DateTime.Now.ToLocalTime(), count, dataBuffer);

                // Send to IoT Hub.
                await deviceClient.SendEventAsync(eventMessage);
            }
        }
        static void Main(string[] args)
        {
            try
            {
                // Create an X.509 certificate object.
                var cert = new X509Certificate2(@"{full path to pfx certificate.pfx}", "{your certificate password}");

                // Create an authentication object using your X.509 certificate. 
                var auth = new DeviceAuthenticationWithX509Certificate("{your-device-id}", cert);

                // Create the device client.
                var deviceClient = DeviceClient.Create("{your-IoT-Hub-name}.azure-devices.net", auth, TransportType.Mqtt);

                if (deviceClient == null)
                {
                    Console.WriteLine("Failed to create DeviceClient!");
                }
                else
                {
                    Console.WriteLine("Successfully created DeviceClient!");
                    SendEvent(deviceClient).Wait();
                }

                Console.WriteLine("Exiting...\n");
            }
            catch (Exception ex)
            {
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
         }
    }
}
```