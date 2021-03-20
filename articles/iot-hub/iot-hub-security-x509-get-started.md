---
title: Zelf studie voor X. 509-beveiliging in azure IoT Hub | Microsoft Docs
description: Ga aan de slag met de beveiliging op basis van X. 509 in uw Azure IoT hub in een gesimuleerde omgeving.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom:
- amqp
- 'Role: Cloud Development'
- 'Role: IoT Device'
- devx-track-csharp
ms.openlocfilehash: a3e328418a0f111cd0b985310ea6dc497999772d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92909791"
---
# <a name="set-up-x509-security-in-your-azure-iot-hub"></a>X.509-beveiliging instellen in uw Azure IoT Hub

In deze zelf studie worden de stappen beschreven die u nodig hebt om uw Azure IoT hub te beveiligen met de *X. 509-certificaat verificatie*. Voor het doel van de afbeelding gebruiken we het open source-hulp programma OpenSSL om certificaten lokaal op uw Windows-machine te maken. U wordt aangeraden deze zelf studie alleen voor test doeleinden te gebruiken. Voor een productie omgeving moet u de certificaten aanschaffen bij een basis certificerings *instantie (CA)*. Zorg er ook voor dat u in productie een strategie hebt voor het afhandelen van de certificaat overschakeling wanneer een certificaat voor een apparaat of een CA-certificaat verloopt.

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="prerequisites"></a>Vereisten

Voor deze zelf studie moet u de volgende resources gereed hebben:

* U hebt een IoT-hub gemaakt met uw Azure-abonnement. Zie [een IOT-hub maken via de portal](iot-hub-create-through-portal.md) voor gedetailleerde stappen.

* U hebt [Visual studio 2017 of Visual studio 2019](https://www.visualstudio.com/vs/) geïnstalleerd.

## <a name="get-x509-ca-certificates"></a>X. 509 CA-certificaten ophalen

De X. 509 beveiliging op basis van certificaten in de IoT Hub moet u beginnen met een [X. 509-certificaat keten](https://en.wikipedia.org/wiki/X.509#Certificate_chains_and_cross-certification), inclusief het basis certificaat en eventuele tussenliggende certificaten tot het blad certificaat.

U kunt een van de volgende manieren kiezen om uw certificaten op te halen:

* Koop X. 509-certificaten van een basis certificerings *instantie (CA)*. Deze methode wordt aanbevolen voor productie omgevingen.

* Maak uw eigen X. 509-certificaten met een hulp programma van derden, zoals [openssl](https://www.openssl.org/). Deze techniek is prima voor test-en ontwikkelings doeleinden. Zie [test-CA-certificaten beheren voor voor beelden en zelf studies](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) voor informatie over het genereren van test-CA-certificaten met Power shell of bash. In de rest van deze zelf studie worden test certificerings instanties gebruikt die worden gegenereerd door de instructies in het [beheer van test-CA-certificaten voor voor beelden en zelf studies](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)te volgen.

* Genereer een [X. 509-TUSSENLIGGEND CA-certificaat](iot-hub-x509ca-overview.md#sign-devices-into-the-certificate-chain-of-trust) dat is ondertekend door een bestaand basis-CA-certificaat en upload het naar de hub. Zodra het tussenliggende certificaat is geüpload en geverifieerd, zoals hieronder aangegeven, kan het worden gebruikt in de plaats van een basis certificaat dat hieronder wordt vermeld. Hulpprogram ma's zoals OpenSSL ([openssl req](https://www.openssl.org/docs/man1.1.0/man1/req.html) en [openssl ca](https://www.openssl.org/docs/man1.1.0/man1/ca.html)) kunnen worden gebruikt voor het genereren en ondertekenen van een tussenliggend CA-certificaat.

> [!NOTE]
> Upload de externe basis niet als deze niet uniek is omdat de andere klanten van de derde partij hun apparaten op uw IoT Hub kunnen aansluiten.

## <a name="register-x509-ca-certificates-to-your-iot-hub"></a>X. 509 CA-certificaten bij uw IoT-hub registreren

Deze stappen laten zien hoe u een nieuwe certificerings instantie aan uw IoT-hub kunt toevoegen via de portal. Wanneer u X. 509 certificerings instantie CA-verificatie gebruikt, moet u het nieuwe certificaat registreren voordat het bestaande wordt verlopen als onderdeel van de strategie voor certificaat overschakeling.

> [!NOTE]
> Het maximum aantal X. 509-CA-certificaten dat kan worden geregistreerd voor een IoT-hub is 25. Zie [Azure IOT hub quota's en beperking](iot-hub-devguide-quotas-throttling.md)voor meer informatie.

1. Navigeer in het Azure Portal naar uw IOT-hub en selecteer **instellingen**  >  **certificaten** voor de hub.

1. Selecteer **toevoegen** om een nieuw certificaat toe te voegen.

1. Voer in **certificaat naam** een beschrijvende weergave naam in en selecteer het certificaat bestand dat u hebt gemaakt in de vorige sectie van de computer.

1. Zodra u een melding krijgt dat het certificaat is geüpload, selecteert u **Opslaan**.

    ![Certificaat uploaden](./media/iot-hub-security-x509-get-started/iot-hub-add-cert.png)  

   Uw certificaat wordt weer gegeven in de lijst certificaten met de status niet **geverifieerd**.

1. Selecteer het certificaat dat u zojuist hebt toegevoegd om **certificaat Details** weer te geven en selecteer vervolgens **verificatie code genereren**.

   ![Certificaat verifiëren](./media/iot-hub-security-x509-get-started/copy-verification-code.png)  

1. Kopieer de **verificatie code** naar het klem bord. U gebruikt deze om het eigendom van het certificaat te valideren.

1. Volg stap 3 bij het [beheren van test-CA-certificaten voor voor beelden en zelf studies](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md).  Dit proces ondertekent uw verificatie code met de persoonlijke sleutel die is gekoppeld aan uw X. 509 CA-certificaat, waardoor een hand tekening wordt gegenereerd. Er zijn hulpprogram ma's beschikbaar om dit handtekening proces uit te voeren, bijvoorbeeld OpenSSL. Dit proces staat bekend als het [bewijs van bezit](https://tools.ietf.org/html/rfc5280#section-3.1).

1. Zoek en open het handtekening bestand onder **certificaat Details** onder **verificatie certificaat. pem-of CER-bestand**. Selecteer vervolgens **verifiëren**.

   De status van uw certificaat wordt **gecontroleerd**. Selecteer **vernieuwen** als het certificaat niet automatisch wordt bijgewerkt.

## <a name="create-an-x509-device-for-your-iot-hub"></a>Een X. 509-apparaat maken voor uw IoT-hub

1. Navigeer in het Azure Portal naar uw IOT-hub en selecteer vervolgens IOT-apparaten in **Explorer**  >  .

1. Selecteer **Nieuw** om een nieuw apparaat toe te voegen.

1. Voer in **apparaat-id** een beschrijvende weergave naam in. Kies bij **verificatie type** **X. 509 ca ondertekend** en selecteer vervolgens **Opslaan**.

   ![X. 509-apparaat maken in de portal](./media/iot-hub-security-x509-get-started/new-x509-device.png)

## <a name="authenticate-your-x509-device-with-the-x509-certificates"></a>Uw X. 509-apparaat verifiëren met de X. 509-certificaten

Als u uw X. 509-apparaat wilt verifiëren, moet u het apparaat eerst ondertekenen met het CA-certificaat. Het ondertekenen van Leaf-apparaten wordt normaal gesp roken uitgevoerd op het fabrieks bedrijf, waar de productie hulpprogramma's dienovereenkomstig zijn ingeschakeld. Wanneer het apparaat van de ene fabrikant naar een andere gaat, wordt de ondertekenings actie van elke fabrikant vastgelegd als een tussenliggend certificaat binnen de keten. Het resultaat is een certificaat keten van het CA-certificaat naar het Leaf-certificaat van het apparaat. Stap 4 bij het [beheren van test-CA-certificaten voor voor beelden en zelf studies](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) genereert een certificaat voor een apparaat.

We laten nu zien hoe u een C#-toepassing kunt maken om het X. 509-apparaat te simuleren dat voor uw IoT-hub is geregistreerd. De waarden voor de Tempe ratuur en lucht vochtigheid worden verzonden vanaf het gesimuleerde apparaat naar uw hub. In deze zelf studie maakt u alleen de apparaat-app. Het is aan de lezers te blijven om de IoT Hub-service toepassing te maken die een reactie verzendt naar de gebeurtenissen die door dit gesimuleerde apparaat worden verzonden. De C#-toepassing veronderstelt dat u de stappen in het [beheer van test-CA-certificaten voor voor beelden en zelf studies](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)hebt gevolgd.

1. Open Visual Studio, selecteer **een nieuw project maken** en kies vervolgens de project sjabloon **console-app (.NET Framework)** . Selecteer **Next**.

1. Geef in **uw nieuwe project** de naam project *SimulateX509Device* en selecteer vervolgens **maken**.

   ![X. 509 Device-project maken in Visual Studio](./media/iot-hub-security-x509-get-started/create-device-project-vs2019.png)

1. Klik in Solution Explorer met de rechter muisknop op het project **SimulateX509Device** en selecteer vervolgens **NuGet-pakketten beheren**.

1. Selecteer in de **NuGet-pakket manager** **Bladeren** en zoek naar en kies **micro soft. Azure. devices. client**. Selecteer **Installeren**.

   ![Het apparaat SDK NuGet-pakket toevoegen in Visual Studio](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

    Met deze stap wordt een verwijzing naar het Azure IoT Device SDK NuGet-pakket en de bijbehorende afhankelijkheden gedownload, geïnstalleerd en toegevoegd.

1. Voeg aan het begin van het bestand **Program.cs** de volgende `using` instructies toe:

    ```csharp
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using System.Security.Cryptography.X509Certificates;
    ```

1. Voeg de volgende velden toe aan de klasse **Program** :

    ```csharp
        private static int MESSAGE_COUNT = 5;
        private const int TEMPERATURE_THRESHOLD = 30;
        private static String deviceId = "<your-device-id>";
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();
    ```

    Gebruik de beschrijvende naam van het apparaat dat u in de voor gaande sectie hebt gebruikt in plaats van _<your_device_id>_.

1. Voeg de volgende functie toe om wille keurige getallen te maken voor de Tempe ratuur en vochtigheid en deze waarden naar de hub te verzenden:

    ```csharp
    static async Task SendEvent(DeviceClient deviceClient)
    {
        string dataBuffer;
        Console.WriteLine("Device sending {0} messages to IoTHub...\n", MESSAGE_COUNT);

        for (int count = 0; count < MESSAGE_COUNT; count++)
        {
            temperature = rnd.Next(20, 35);
            humidity = rnd.Next(60, 80);
            dataBuffer = string.Format("{{\"deviceId\":\"{0}\",\"messageId\":{1},\"temperature\":{2},\"humidity\":{3}}}", deviceId, count, temperature, humidity);
            Message eventMessage = new Message(Encoding.UTF8.GetBytes(dataBuffer));
            eventMessage.Properties.Add("temperatureAlert", (temperature > TEMPERATURE_THRESHOLD) ? "true" : "false");
            Console.WriteLine("\t{0}> Sending message: {1}, Data: [{2}]", DateTime.Now.ToLocalTime(), count, dataBuffer);

            await deviceClient.SendEventAsync(eventMessage);
        }
    }
    ```

1. Voeg ten slotte de volgende regels code toe aan de functie **Main** , waarbij de tijdelijke aanduidingen _apparaat-id_, _uw-IOT-hub-naam_ en _absoluut pad_ naar het pfx-bestand worden vervangen als vereist door uw installatie.

    ```csharp
    try
    {
        var cert = new X509Certificate2(@"<absolute-path-to-your-device-pfx-file>", "1234");
        var auth = new DeviceAuthenticationWithX509Certificate("<device-id>", cert);
        var deviceClient = DeviceClient.Create("<your-iot-hub-name>.azure-devices.net", auth, TransportType.Amqp_Tcp_Only);

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
    ```

   Deze code maakt verbinding met uw IoT-hub door de connection string voor uw X. 509-apparaat te maken. Zodra de verbinding tot stand is gebracht, worden de gebeurtenissen van de Tempe ratuur en vochtigheid naar de hub verzonden en wordt gewacht op de reactie.

1. Voer de app uit. Omdat deze toepassing toegang heeft tot een *PFX* -bestand, moet u deze app mogelijk uitvoeren als beheerder.

   1. Bouw de Visual Studio-oplossing.

   1. Open een nieuw opdracht prompt venster met de optie **als administrator uitvoeren**.  

   1. Ga naar de map die uw oplossing bevat en navigeer naar het pad naar de *bin/fout opsporing* in de map met oplossingen.

   1. Voer de toepassing uit **SimulateX509Device.exe** vanaf de opdracht prompt.

   U moet het apparaat zien dat er verbinding wordt gemaakt met de hub en de gebeurtenissen verzendt.

   ![Apparaat-app uitvoeren](./media/iot-hub-security-x509-get-started/device-app-success.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het beveiligen van uw IoT-oplossing:

* [Best practices voor IoT-beveiliging](../iot-fundamentals/iot-security-best-practices.md)

* [IoT-beveiligings architectuur](../iot-fundamentals/iot-security-architecture.md)

* [Uw IoT-implementatie beveiligen](../iot-fundamentals/iot-security-deployment.md)

Zie voor meer informatie over de mogelijkheden van IoT Hub:

* [AI implementeren op Edge-apparaten met Azure IoT Edge](../iot-edge/quickstart-linux.md)