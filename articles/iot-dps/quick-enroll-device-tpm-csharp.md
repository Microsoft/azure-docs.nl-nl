---
title: Snelstart - TPM-apparaten inschrijven bij Azure Device Provisioning Service met behulp van C#
description: 'Quickstart: een TPM-apparaat inschrijven bij Azure IoT Hub Device Provisioning Service (DPS) met de service-SDK voor C#. In deze snelstart wordt gebruikgemaakt van afzonderlijke inschrijvingen.'
author: wesmc7777
ms.author: wesmc
ms.date: 09/28/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 14f17c6716fe98a11d03e8d1021bcdb1058fe671
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94968108"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-c-service-sdk"></a>Quickstart: TPM-apparaat inschrijven bij IoT Hub Device Provisioning Service met behulp van de C# service-SDK

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

In dit artikel wordt beschreven hoe u via programmacode een afzonderlijke inschrijving voor een TPM-apparaat in Azure IoT Hub Device Provisioning Service maakt met behulp van de [service-SDK voor C#](https://github.com/Azure/azure-iot-sdk-csharp) en een C# .NET Core-voorbeeld-app. U kunt desgewenst ook een gesimuleerd TPM-apparaat bij de inrichtingsservice inschrijven met behulp van deze vermelding voor een afzonderlijke inschrijving. Hoewel deze stappen zowel op Windows- als op Linux-computers werken, gebruiken we in dit artikel een Windows-ontwikkelcomputer.

## <a name="prepare-the-development-environment"></a>De ontwikkelomgeving voorbereiden

1. Controleer of [Visual Studio 2019](https://www.visualstudio.com/vs/) op uw computer is geïnstalleerd.

1. Controleer of de [.NET Core SDK](https://www.microsoft.com/net/download/windows) op uw computer is geïnstalleerd.

1. Voer de stappen in [IoT Hub Device Provisioning Service instellen met Azure Portal](./quick-setup-auto-provision.md) uit voordat u verdergaat.

1. (Optioneel) Als u aan het eind van deze quickstart een gesimuleerd apparaat wilt inschrijven, volgt u de procedure in [Een gesimuleerd TPM-apparaat maken en inrichten met behulp van de apparaat-SDK voor C#](quick-create-simulated-device-tpm-csharp.md) tot de stap waarin u een goedkeuringssleutel ontvangt voor het apparaat. Sla de goedkeuringssleutel, registratie-id en, desgewenst, de apparaat-id op. U hebt deze later in deze quickstart namelijk nodig.

   > [!NOTE]
   > Voer niet de stappen voor het maken van een afzonderlijke inschrijving via Azure Portal uit.

## <a name="get-the-connection-string-for-your-provisioning-service"></a>De verbindingsreeks voor de inrichtingsservice ophalen

Voor het voorbeeld in deze snelstart hebt u de verbindingsreeks voor de inrichtingsservice nodig.

1. Meld u aan bij Azure Portal, selecteer **Alle resources** en vervolgens uw Device Provisioning Service.

1. Kies **Gedeeld toegangsbeleid** en selecteer vervolgens het toegangsbeleid dat u wilt gebruiken om de eigenschappen te openen. Kopieer in **Toegangsbeleid** de verbindingstekenreeks van de primaire sleutel en sla deze op.

    ![Verbindingsreeks voor de inrichtingsservice ophalen uit de portal](media/quick-enroll-device-tpm-csharp/get-service-connection-string-vs2019.png)

## <a name="create-the-individual-enrollment-sample"></a>Het voorbeeld van de afzonderlijke inschrijving maken

In deze sectie wordt beschreven hoe u een .NET Core-console-app maakt, waarmee een afzonderlijke inschrijving voor een TPM-apparaat aan uw inrichtingsservice wordt toegevoegd. Met enkele aanpassingen kunt u deze stappen ook volgen om een [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot) console-app te maken om aan de afzonderlijke registratie toe te voegen. Zie de [Windows IoT Core-documentatie voor ontwikkelaars](/windows/iot-core/) voor meer informatie over ontwikkelen met IoT Core.

1. Open Visual Studio en selecteer **Een nieuw project maken**. Kies in **Een nieuw project maken** de projectsjabloon voor C# **Console-app (.NET Core)** en selecteer **Volgende**.

1. Noem het project *CreateTpmEnrollment* en selecteer **Maken**.

    ![Windows Classic Desktop-project in Visual C# configureren](media/quick-enroll-device-tpm-csharp/configure-tpm-app-vs2019.png)

1. Wanneer de oplossing in Visual Studio wordt geopend, klikt u in het deelvenster **Solution Explorer** met de rechtermuisknop op het project **CreateTpmEnrollment**. Selecteer **NuGet-pakketten beheren**.

1. Selecteer in **NuGet Package Manager** de optie **Bladeren**, zoek en kies **Microsoft.Azure.Devices.Provisioning.Service** en selecteer vervolgens **Installeren**.

   ![Sluit het venster Nuget Package Manager.](media//quick-enroll-device-tpm-csharp/add-nuget.png)

   Met deze stap worden het NuGet-pakket [Azure IoT Provisioning Service Client-SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) en de bijbehorende afhankelijkheden gedownload en geïnstalleerd. Ook worden verwijzingen hiernaar toegevoegd.

1. Voeg bovenaan in `Program.cs` de volgende `using`-instructies toe na de andere `using`-instructies:
  
   ```csharp
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Provisioning.Service;
   ```

1. Voeg de volgende velden toe aan de klasse `Program` en breng de hieronder vermelde wijzigingen aan.

   ```csharp
   private static string ProvisioningConnectionString = "{ProvisioningServiceConnectionString}";
   private const string RegistrationId = "sample-registrationid-csharp";
   private const string TpmEndorsementKey =
       "AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUS" +
       "cTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3d" +
       "yKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKR" +
       "dln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFe" +
       "WlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==";
       
   // Optional parameters
   private const string OptionalDeviceId = "myCSharpDevice";
   private const ProvisioningStatus OptionalProvisioningStatus = ProvisioningStatus.Enabled;
   ```

   * Vervang de tijdelijke aanduiding `ProvisioningServiceConnectionString` door de verbindingstekenreeks van de inrichtingsservice waarvoor u de inschrijving wilt maken.

   * U kunt desgewenst de registratie-ID, goedkeuringssleutel, apparaat-ID en inrichtingsstatus wijzigen.

   * Als u deze quickstart samen met de quickstart [Een gesimuleerd TPM-apparaat met de apparaat-SDK voor C# maken en inrichten](quick-create-simulated-device-tpm-csharp.md) gebruikt om een gesimuleerd apparaat in te richten, vervangt u de goedkeuringssleutel en registratie-id door de waarden die u in deze quickstart hebt genoteerd. U kunt de apparaat-id vervangen door de waarde die wordt voorgesteld in die quickstart, uw eigen waarde gebruiken of de standaardwaarde in dit voorbeeld gebruiken.

1. Voeg de volgende methode toe aan de klasse `Program`.  Met deze code wordt een afzonderlijke inschrijvingsvermelding gemaakt en vervolgens de methode `CreateOrUpdateIndividualEnrollmentAsync` in de `ProvisioningServiceClient` aangeroepen om de afzonderlijke inschrijving toe te voegen aan de inrichtingsservice.

   ```csharp
   public static async Task RunSample()
   {
       Console.WriteLine("Starting sample...");

       using (ProvisioningServiceClient provisioningServiceClient =
               ProvisioningServiceClient.CreateFromConnectionString(ProvisioningConnectionString))
       {
           #region Create a new individualEnrollment config
           Console.WriteLine("\nCreating a new individualEnrollment...");
           Attestation attestation = new TpmAttestation(TpmEndorsementKey);
           IndividualEnrollment individualEnrollment =
                   new IndividualEnrollment(
                           RegistrationId,
                           attestation);

           // The following parameters are optional. Remove them if you don't need them.
           individualEnrollment.DeviceId = OptionalDeviceId;
           individualEnrollment.ProvisioningStatus = OptionalProvisioningStatus;
           #endregion

           #region Create the individualEnrollment
           Console.WriteLine("\nAdding new individualEnrollment...");
           IndividualEnrollment individualEnrollmentResult =
               await provisioningServiceClient.CreateOrUpdateIndividualEnrollmentAsync(individualEnrollment).ConfigureAwait(false);
           Console.WriteLine("\nIndividualEnrollment created with success.");
           Console.WriteLine(individualEnrollmentResult);
           #endregion
        
       }
   }
   ```

1. Vervang tot slot de `Main`-methode door de volgende regels:

   ```csharp
    static async Task Main(string[] args)
    {
        await RunSample();
        Console.WriteLine("\nHit <Enter> to exit ...");
        Console.ReadLine();
    }
   ```

1. Bouw de oplossing.

## <a name="run-the-individual-enrollment-sample"></a>Het voorbeeld van de afzonderlijke inschrijving uitvoeren
  
Voer het voorbeeld uit in Visual Studio om de afzonderlijke registratie voor uw TPM-apparaat te maken.

Er wordt een opdrachtpromptvenster weergegeven waarin bevestigingsberichten worden getoond. Als de inschrijving is gemaakt, worden in het opdrachtpromptvenster de eigenschappen weergegeven van de nieuwe afzonderlijke inschrijving.

U kunt controleren of er een afzonderlijke inschrijving is gemaakt. Ga naar het overzicht voor Device Provisioning Service en selecteer **Inschrijvingen beheren** en vervolgens **Afzonderlijke inschrijvingen**. U ziet nu een nieuwe registratievermelding die overeenkomt met de registratie-ID die u in het voorbeeld hebt gebruikt.

![Eigenschappen van de inschrijving in de portal](media/quick-enroll-device-tpm-csharp/verify-enrollment-portal-vs2019.png)

Selecteer de vermelding om de goedkeuringssleutel en andere eigenschappen van de vermelding te controleren.

Als u de stappen hebt uitgevoerd in de quickstart [Een gesimuleerd TPM-apparaat met de apparaat-SDK voor C# maken en inrichten](quick-create-simulated-device-tpm-csharp.md), kunt u doorgaan met de overige stappen in deze quickstart om uw gesimuleerde apparaat in te schrijven. Volg niet de stappen voor het maken van een afzonderlijke inschrijving via Azure Portal.

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om het servicevoorbeeld voor C#- te verkennen, verwijdert u de resources die u in deze quickstart hebt gemaakt niet. Wilt u dat niet, dan kunt u met de volgende stappen alle resources verwijderen die in deze quickstart zijn gemaakt.

1. Sluit het venster met de voorbeelduitvoer voor C# op de computer.

1. Navigeer naar Device Provisioning Service in Azure Portal, selecteer **Inschrijvingen beheren** en selecteer vervolgens het tabblad **Afzonderlijke inschrijvingen**. Schakel het selectievakje naast de *Registratie-id* in voor de inschrijvingsvermelding die u hebt gemaakt met behulp van deze quickstart. Klik vervolgens op de knop **Verwijderen** bovenaan het deelvenster.

1. Als u de stappen in [Een gesimuleerd TPM-apparaat met de apparaat-SDK voor C# maken en inrichten](quick-create-simulated-device-tpm-csharp.md) hebt uitgevoerd om een gesimuleerd TPM-apparaat te maken, voert u de volgende stappen uit:

    1. Sluit het venster van de TPM-simulator en het voorbeelduitvoervenster voor het gesimuleerde apparaat.

    1. Navigeer in Azure Portal naar de IoT Hub waar het apparaat is ingericht. Selecteer in het menu onder **Verkenners** de optie **IoT-apparaten**, schakel het selectievakje in naast de *DEVICE ID* van het apparaat dat u in deze quickstart hebt geregistreerd en selecteer vervolgens bovenaan in het deelvenster de knop **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

U hebt in deze quickstart via programmacode een afzonderlijke inschrijvingsvermelding gemaakt voor een TPM-apparaat. U kunt ook een gesimuleerd TPM-apparaat hebben gemaakt op de computer en dit hebben ingericht voor uw IoT-hub met behulp van Azure IoT Hub Device Provisioning Service. Voor meer informatie over device provisioning, gaat u verder met de zelfstudie voor het instellen van Device Provisioning Service in Azure Portal.

> [!div class="nextstepaction"]
> [Zelfstudies over Azure IoT Hub Device Provisioning Service](./tutorial-set-up-cloud.md)