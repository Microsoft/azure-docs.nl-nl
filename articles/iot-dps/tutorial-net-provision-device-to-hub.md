---
title: 'Zelfstudie: apparaat inrichten met behulp van Azure IoT Hub Device Provisioning Service (.NET)'
description: In deze zelfstudie leert u hoe u uw apparaat kunt inrichten in één IoT-hub met behulp van Azure IoT Hub Device Provisioning Service (DPS) en .NET.
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: f9a14ee6ee3e10b36d64ec11fc23807efe2bfaf2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94966561"
---
# <a name="tutorial-enroll-the-device-to-an-iot-hub-using-the-azure-iot-hub-provisioning-service-client-net"></a>Zelfstudie: Het apparaat registreren bij een IoT-hub met behulp van IoT Hub Device Provisioning Service-client (.NET)

In de vorige zelfstudie hebt u geleerd hoe u een apparaat kunt instellen om verbinding te maken met Device Provisioning Service. In deze zelfstudie leert u hoe u deze service gebruikt om uw apparaat in te richten voor één IoT-hub. U gebruikt daarvoor zowel **_Individuele registratie_** als **_Registratiegroepen_**. In deze zelfstudie ontdekt u hoe u:

> [!div class="checklist"]
> * Het apparaat inschrijven
> * Het apparaat starten
> * Controleren of het apparaat is geregistreerd

## <a name="prerequisites"></a>Vereisten

Voordat u verder gaat, moeten uw apparaat en diens *Hardware Security Module* zijn geconfigureerd zoals is besproken in de zelfstudie [Een apparaat instellen voor inrichten met behulp van Azure IoT Hub Device Provisioning Service](./tutorial-set-up-device.md).

* Visual Studio

> [!NOTE]
> Visual Studio is niet vereist. De installatie van [.NET](https://www.microsoft.com/net) is voldoende en ontwikkelaars kunnen een willekeurige editor gebruiken op Windows of Linux.  

Deze zelfstudie simuleert de periode tijdens of direct na het hardwareproductieproces, wanneer apparaatgegevens aan de inrichtingsservice worden toegevoegd. Deze code wordt doorgaans uitgevoerd op een pc of fabrieksapparaat waarop .NET-code kan worden uitgevoerd. De code mag niet aan de apparaten zelf worden toegevoegd.


## <a name="enroll-the-device"></a>Het apparaat inschrijven

In deze stap worden de unieke beveiligingsartefacten van het apparaat toegevoegd aan Device Provisioning Service. Deze beveiligingsartefacten zijn als volgt:

- Voor TPM-apparaten:
    - De *Goedkeuringssleutel* die uniek is voor elke TPM-chip of simulatie. Lees [TPM-goedkeuringssleutel begrijpen](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770443(v=ws.11)) voor meer informatie.
    - De *Registratie-id* die wordt gebruikt om een apparaat op unieke wijze te identificeren in de naamruimte of het bereik. Deze mag gelijk zijn aan de apparaat-id, maar dit hoeft niet. De id is verplicht voor elk apparaat. Voor TPM-apparaten kan de registratie-id worden afgeleid van de TPM zelf, bijvoorbeeld een SHA-256-hash van de TPM-goedkeuringssleutel.

- Voor X.509-apparaten:
    - Het [X.509-certificaat dat is verleend aan het apparaat](/windows/win32/seccertenroll/about-x-509-public-key-certificates), in de vorm van een *PEM*- of *CER*-bestand. Voor een afzonderlijke inschrijving moet u het *leaf certificaat* voor het X.509-systeem gebruiken. Voor registratiegroepen moet u het *basiscertificaat* of een gelijkwaardig *certificaat van ondertekenaar* gebruiken.
    - De *Registratie-id* die wordt gebruikt om een apparaat op unieke wijze te identificeren in de naamruimte of het bereik. Deze mag gelijk zijn aan de apparaat-id, maar dit hoeft niet. De id is verplicht voor elk apparaat. Voor X.509-apparaten is de registratie-id afgeleid van de algemene naam (CN) van het certificaat. Zie [Device concepts](./concepts-service.md) (Apparaatconcepten) voor meer informatie over deze vereisten.

Er zijn twee manieren om het apparaat te registreren bij Device Provisioning Service:

- **Afzonderlijke inschrijvingen**: dit is een vermelding voor een enkel apparaat dat kan worden geregistreerd met Device Provisioning Service. Afzonderlijke inschrijvingen kunnen X.509-certificaten of SAS-tokens (in een echt of virtueel TPM) gebruiken als attestation-mechanismen. We raden u aan om afzonderlijke inschrijvingen te gebruiken voor apparaten die unieke initiële configuraties vereisen, en voor apparaten die alleen SAS-tokens via TPM als attestation-mechanisme kunnen gebruiken. Afzonderlijke inschrijvingen hebben mogelijk de gewenste apparaat-id voor IoT Hub die is opgegeven.

- **Inschrijvingsgroepen**: dit is een groep apparaten die een specifiek attestation-mechanisme delen. We raden u aan om een inschrijvingsgroep te gebruiken bij een groot aantal apparaten die een gewenste initiële configuratie delen, of voor apparaten die allemaal zijn verbonden met dezelfde tenant. Inschrijvingsgroepen gebruiken alleen X.509 en delen allemaal een ondertekeningscertificaat in hun keten van X.509-certificaten.

### <a name="enroll-the-device-using-individual-enrollments"></a>Het apparaat registreren met Afzonderlijke inschrijvingen

1. Maak in Visual Studio een Visual C#-consoletoepassingsproject met behulp van de projectsjabloon **Consoletoepassing**. Noem het project **DeviceProvisioning**.
    
1. Klik in Solution Explorer met de rechtermuisknop op het project **DeviceProvisioning** en klik op **NuGet-pakketten beheren...**.

1. Selecteer in het venster **NuGet Package Manager** de optie **Bladeren** en zoek naar **microsoft.azure.devices.provisioning.service**. Selecteer de vermelding en klik op **Installeren** om het pakket **Microsoft.Azure.Devices.Provisioning.Service** te installeren en de gebruiksvoorwaarden te accepteren. Met deze procedure worden het NuGet-pakket van de [Azure IoT Device Provisioning Service SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) en de bijbehorende afhankelijkheden gedownload en geïnstalleerd. Ook worden verwijzingen hiernaar toegevoegd.

1. Voeg aan het begin van het bestand **Program.cs** de volgende `using` instructies toe:
   
    ```csharp
    using Microsoft.Azure.Devices.Provisioning.Service;
    ```

1. Voeg de volgende velden toe aan de klasse **Program**: Vervang de tijdelijke aanduiding door de verbindingsreeks van Device Provisioning Service die u in de vorige sectie hebt genoteerd.
   
    ```csharp
    static readonly string ServiceConnectionString = "{Device Provisioning Service connection string}";

    private const string SampleRegistrationId = "sample-individual-csharp";
    private const string SampleTpmEndorsementKey =
            "AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUS" +
            "cTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3d" +
            "yKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKR" +
            "dln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFe" +
            "WlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==";
    private const string OptionalDeviceId = "myCSharpDevice";
    private const ProvisioningStatus OptionalProvisioningStatus = ProvisioningStatus.Enabled;
    ```

1. Voeg het volgende toe om de registratie voor het apparaat te implementeren:

    ```csharp
    static async Task SetRegistrationDataAsync()
    {
        Console.WriteLine("Starting SetRegistrationData");

        Attestation attestation = new TpmAttestation(SampleTpmEndorsementKey);

        IndividualEnrollment individualEnrollment = new IndividualEnrollment(SampleRegistrationId, attestation);

        individualEnrollment.DeviceId = OptionalDeviceId;
        individualEnrollment.ProvisioningStatus = OptionalProvisioningStatus;

        Console.WriteLine("\nAdding new individualEnrollment...");
        var serviceClient = ProvisioningServiceClient.CreateFromConnectionString(ServiceConnectionString);

        IndividualEnrollment individualEnrollmentResult =
            await serviceClient.CreateOrUpdateIndividualEnrollmentAsync(individualEnrollment).ConfigureAwait(false);

        Console.WriteLine("\nIndividualEnrollment created with success.");
        Console.WriteLine(individualEnrollmentResult);
    }
    ```

1. Voeg tot slot de volgende code aan de methode **Main** toe om de verbinding met uw IoT-hub te openen en de registratie te starten:
   
    ```csharp
    try
    {
        Console.WriteLine("IoT Device Provisioning example");

        SetRegistrationDataAsync().GetAwaiter().GetResult();
            
        Console.WriteLine("Done, hit enter to exit.");
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    Console.ReadLine();
    ```
        
1. Klik in Solution Explorer van Visual Studio met de rechtermuisknop op uw oplossing en klik op **Set StartUp Projects...** . Selecteer **Single startup project** en selecteer het project **DeviceProvisioning** in de vervolgkeuzelijst.  

1. Voer de app voor het .NET-apparaat **DeviceProvisiong** uit. Deze moet de inrichting van het apparaat instellen: 

    ![Uitvoering van afzonderlijke registratie](./media/tutorial-net-provision-device-to-hub/individual.png)

Wanneer het apparaat is ingeschreven, ziet u dat deze in de portal als volgt wordt weergegeven:

   ![Geslaagde inschrijving in de portal](./media/tutorial-net-provision-device-to-hub/individual-portal.png)

### <a name="enroll-the-device-using-enrollment-groups"></a>Het apparaat registreren met Registratiegroepen

> [!NOTE]
> Het voorbeeld van de inschrijvingsgroep heeft een X.509-certificaat nodig.

1. Open in de Solution Explorer van Visual Studio het project **DeviceProvisioning** dat eerder is gemaakt. 

1. Voeg aan het begin van het bestand **Program.cs** de volgende `using` instructies toe:
    
    ```csharp
    using System.Security.Cryptography.X509Certificates;
    ```

1. Voeg de volgende velden toe aan de klasse **Program**: Vervang de tijdelijke aanduiding door de locatie van het X.509-certificaat.
   
    ```csharp
    private const string X509RootCertPathVar = "{X509 Certificate Location}";
    private const string SampleEnrollmentGroupId = "sample-group-csharp";
    ```

1. Voeg het volgende toe aan **Progam.cs** om de registratie voor de groep te implementeren:

    ```csharp
    public static async Task SetGroupRegistrationDataAsync()
    {
        Console.WriteLine("Starting SetGroupRegistrationData");

        using (ProvisioningServiceClient provisioningServiceClient =
                ProvisioningServiceClient.CreateFromConnectionString(ServiceConnectionString))
        {
            Console.WriteLine("\nCreating a new enrollmentGroup...");

            var certificate = new X509Certificate2(X509RootCertPathVar);

            Attestation attestation = X509Attestation.CreateFromRootCertificates(certificate);

            EnrollmentGroup enrollmentGroup = new EnrollmentGroup(SampleEnrollmentGroupId, attestation);

            Console.WriteLine(enrollmentGroup);
            Console.WriteLine("\nAdding new enrollmentGroup...");

            EnrollmentGroup enrollmentGroupResult =
                await provisioningServiceClient.CreateOrUpdateEnrollmentGroupAsync(enrollmentGroup).ConfigureAwait(false);

            Console.WriteLine("\nEnrollmentGroup created with success.");
            Console.WriteLine(enrollmentGroupResult);
        }
    }
    ```

1. Vervang tot slot de volgende code in de methode **Main** om de verbinding met uw IoT-hub te openen en de groepsregistratie te starten:
   
    ```csharp
    try
    {
        Console.WriteLine("IoT Device Group Provisioning example");

        SetGroupRegistrationDataAsync().GetAwaiter().GetResult();
            
        Console.WriteLine("Done, hit enter to exit.");
        Console.ReadLine();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

1. Voer de app voor het .NET-apparaat **DeviceProvisiong** uit. Deze moet de groepsinrichting van het apparaat instellen: 

    ![Uitvoering van groepsregistratie](./media/tutorial-net-provision-device-to-hub/group.png)

    Wanneer de groep met apparaten is ingeschreven, ziet u dat deze in de portal als volgt wordt weergegeven:

   ![Geslaagde groepsregistratie in de portal](./media/tutorial-net-provision-device-to-hub/group-portal.png)


## <a name="start-the-device"></a>Het apparaat starten

Op dit punt is de volgende installatie gereed voor apparaatregistratie:

1. Uw apparaat of groep met apparaten zijn geregistreerd bij Device Provisioning Service, en 
2. Uw apparaat is gereed, de beveiliging is geconfigureerd en het apparaat is toegankelijk via de toepassing met behulp van de SDK van de Device Provisioning Service-client.

Start het apparaat zodat de registratie via de clienttoepassing met Device Provisioning service kan worden gestart.  


## <a name="verify-the-device-is-registered"></a>Controleren of het apparaat is geregistreerd

Zodra het apparaat is opgestart, worden de volgende acties uitgevoerd. Zie het [voorbeeld Apparaatclient inrichten](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device) voor meer informatie. 

1. Vanaf het apparaat wordt een registratie-aanvraag verzonden naar Device Provisioning Service.
2. Voor TPM-apparaten wordt vanuit Device Provisioning Service een registratie-uitdaging teruggestuurd waarop het apparaat reageert. 
3. Als de registratie is gelukt, worden Device Provisioning Service de IoT Hub-URI, apparaat-id en versleutelingssleutel via Device Provisioning Service teruggestuurd naar het apparaat. 
4. De IoT Hub-clienttoepassing op het apparaat maakt vervolgens verbinding met de hub. 
5. Als de verbinding met de hub is gemaakt, wordt het apparaat weergegeven in **Device Explorer** van de IoT-hub. 

    ![Geslaagde verbinding met de hub in de portal](./media/tutorial-net-provision-device-to-hub/hub-connect-success.png)

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Het apparaat inschrijven
> * Het apparaat starten
> * Controleren of het apparaat is geregistreerd

Ga naar de volgende zelfstudie voor informatie over het inrichten van meerdere apparaten via hubs met gelijke taakverdeling. 

> [!div class="nextstepaction"]
> [Apparaten inrichten in meerdere IoT-hubs met gelijke taakverdeling](./tutorial-provision-multiple-hubs.md)