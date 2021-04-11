---
title: 'Zelf studie: de mogelijkheid van X. 509-certificaten voor het verifiëren van apparaten bij een Azure IoT Hub | Microsoft Docs'
description: Zelf studie-uw X. 509-certificaten testen voor verificatie bij Azure IoT Hub
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
- devx-track-azurecli
ms.openlocfilehash: 91eea344914120a396ba9465ec504a37f5844d4e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105630673"
---
# <a name="tutorial-testing-certificate-authentication"></a>Zelf studie: verificatie van certificaten testen

U kunt het volgende code voorbeeld van C# gebruiken om te testen of uw apparaat kan worden geverifieerd met uw IoT Hub. Houd er rekening mee dat u het volgende moet doen voordat u de test code uitvoert:

* Maak een basis-CA of een onderliggend CA-certificaat.
* Upload uw CA-certificaat naar uw IoT Hub.
* Controleer of u beschikt over het CA-certificaat.
* Voeg een apparaat toe aan uw IoT Hub.
* Maak een certificaat voor een apparaat met dezelfde apparaat-ID als uw apparaat.

## <a name="code-example"></a>Code voorbeeld

In het volgende code voorbeeld ziet u hoe u een C#-toepassing maakt voor het simuleren van het X. 509-apparaat dat is geregistreerd voor uw IoT-hub. In het voor beeld worden de waarden voor de Tempe ratuur en vochtigheid van het gesimuleerde apparaat naar uw hub verzonden. In deze zelf studie maakt u alleen de apparaat-app. Het is aan de lezers te blijven om de IoT Hub-service toepassing te maken waarmee reacties worden verzonden naar de gebeurtenissen die door dit gesimuleerde apparaat worden verzonden.

1. Open Visual Studio, selecteer **een nieuw project maken** en kies vervolgens de project sjabloon **console-app (.NET Framework)** . Selecteer **Next**.

1. Geef in **uw nieuwe project** de naam project *SimulateX509Device* en selecteer vervolgens **maken**.

   ![X. 509 Device-project maken in Visual Studio](./media/iot-hub-security-x509-get-started/create-device-project-vs2019.png)

1. Klik in Solution Explorer met de rechter muisknop op het project **SimulateX509Device** en selecteer vervolgens **NuGet-pakketten beheren**.

1. Selecteer in de **NuGet-pakket manager** **Bladeren** en zoek naar en kies **micro soft. Azure. devices. client**. Selecteer **Installeren**.

   ![Het apparaat SDK NuGet-pakket toevoegen in Visual Studio](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

    Met deze stap wordt een verwijzing naar het Azure IoT Device SDK NuGet-pakket en de bijbehorende afhankelijkheden gedownload, geïnstalleerd en toegevoegd.

    Invoer en voer de volgende code uit:

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