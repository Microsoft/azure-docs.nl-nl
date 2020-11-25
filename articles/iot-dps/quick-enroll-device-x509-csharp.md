---
title: Snelstart - X.509-apparaten inschrijven bij Azure Device Provisioning Service met behulp van C#
description: In deze quickstart wordt gebruikgemaakt van groepsregistraties. In deze quickstart schrijft u X.509-apparaten in bij Azure IoT Hub Device Provisioning Service (DPS) met behulp van C#.
author: wesmc7777
ms.author: wesmc
ms.date: 09/28/2020
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 9fc34532818a742ef67e4b2532966874d083199d
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94959846"
---
# <a name="quickstart-enroll-x509-devices-to-the-device-provisioning-service-using-c"></a>Snelstart: X.509-apparaten registreren bij Device Provisioning Service met behulp van C#

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

In deze snelstart wordt uitgelegd hoe u met C# programmatisch een [registratiegroep](concepts-service.md#enrollment-group) kunt maken die gebruikmaakt van tussenliggende of hoofd-CA X.509-certificaten. De inschrijvingsgroep wordt gemaakt met behulp van de [Microsoft Azure IoT-SDK voor .NET](https://github.com/Azure/azure-iot-sdk-csharp) en een C# .NET Core-voorbeeldtoepassing. Een registratiegroep beheert de toegang tot de inrichtingsservice voor apparaten die een gemeenschappelijk handtekeningcertificaat in hun certificaatketen delen. Zie [Apparaattoegang tot de inrichtingsservice beheren met X.509-certificaten](./concepts-x509-attestation.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates) voor meer informatie. Zie [Overzicht van beveiliging op basis van X.509-CA-certificaten](../iot-hub/iot-hub-x509ca-overview.md) voor meer informatie over het gebruik vanop X.509-certificaten gebaseerde Public Key Infrastructure (PKI) met Azure IoT Hub en Device Provisioning Service. 

Voor deze quickstart wordt aangenomen dat u al een IoT-hub en Device Provisioning Service-instantie hebt gemaakt. Als u deze resources nog niet hebt gemaakt, voert u de quickstart [IoT Hub Device Provisioning Service instellen met Azure Portal](./quick-setup-auto-provision.md) uit voordat u verdergaat met dit artikel.

Hoewel de stappen in dit artikel zowel op Windows- als op Linux-computers werken, gebruiken we in dit artikel een Windows-ontwikkelcomputer.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* Installeer [Visual Studio 2019](https://www.visualstudio.com/vs/).
* Installeer [.NET Core SDK](https://www.microsoft.com/net/download/windows).
* Installeer [Git](https://git-scm.com/download/).

## <a name="prepare-test-certificates"></a>Testcertificaten voorbereiden

Voor deze snelstart hebt u een .pem- of een .cer-bestand met het openbare gedeelte van een tussenliggend of hoofd-CA x.509-certificaat nodig. Dit certificaat moet worden geüpload naar uw inrichtingsservice en door de service worden geverifieerd.

De [Azure IoT C-SDK](https://github.com/Azure/azure-iot-sdk-c) bevat testhulpmiddelen waarmee u een X.509-certificaatketen kunt maken, een basis- of tussencertificaat kunt uploaden vanuit die keten en een bewijs van eigendom kunt uitvoeren met de service om het certificaat te verifiëren.

> [!CAUTION]
> Gebruik uitsluitend certificaten die zijn gemaakt met de SDK-hulpmiddelen voor ontwikkeltesten.
> Gebruik deze certificaten niet in een productieomgeving.
> Ze bevatten in code vastgelegde wachtwoorden, zoals *1234*, die na 30 dagen verlopen.
> Zie [Een x.509-CA-certificaat ophalen](../iot-hub/iot-hub-x509ca-overview.md#how-to-get-an-x509-ca-certificate) in de documentatie van Azure IoT Hub voor meer informatie over het verkrijgen van certificaten die geschikt zijn voor productiegebruik.
>

Voer de volgende stappen uit om met deze testhulpmiddelen certificaten te genereren:

1. Zoek de tagnaam voor de [nieuwste versie](https://github.com/Azure/azure-iot-sdk-c/releases/latest) van de Azure IoT C-SDK.

2. Open een opdrachtprompt of Git Bash-shell en wijzig deze in een werkmap op uw computer. Voer de volgende opdrachten uit om de nieuwste release van de GitHub-opslagplaats van de [Azure IoT C-SDK](https://github.com/Azure/azure-iot-sdk-c) te klonen. Gebruik de tag die u in de vorige stap hebt gevonden als waarde voor de parameter `-b`:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Deze bewerking kan enkele minuten in beslag nemen.

   De testhulpmiddelen bevinden zich in de *azure-iot-sdk-c/tools/CACertificates* van de opslagplaats die u hebt gekloond.

3. Volg de stappen in [Managing test CA certificates for samples and tutorials](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) (CA-testcertificaten voor voorbeelden en zelfstudies beheren).

Naast de hulpmiddelen in de C-SDK geeft het [voorbeeld van verificatie door een groepscertificaat](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample) in de *Microsoft Azure IoT-SDK voor .NET* weer hoe u een bewijs van eigendom kunt uitvoeren in C# met een bestaand X.509-tussencertificaat of -basis-CA-certificaat.

## <a name="get-the-connection-string-for-your-provisioning-service"></a>De verbindingsreeks voor de inrichtingsservice ophalen

Voor het voorbeeld in deze snelstart hebt u de verbindingsreeks voor de inrichtingsservice nodig.

1. Meld u aan bij Azure Portal, selecteer **Alle resources** en vervolgens uw Device Provisioning Service.

1. Selecteer **Gedeeld toegangsbeleid** en kies vervolgens het toegangsbeleid dat u wilt gebruiken om de eigenschappen te openen. Kopieer in **Toegangsbeleid** de verbindingstekenreeks van de primaire sleutel en sla deze op.

    ![Verbindingsreeks voor de inrichtingsservice ophalen uit de portal](media/quick-enroll-device-x509-csharp/get-service-connection-string-vs2019.png)

## <a name="create-the-enrollment-group-sample"></a>Een voorbeeld van een registratiegroep maken 

In deze sectie wordt beschreven hoe u een .NET Core-console-app maakt waarmee een inschrijvingsgroep aan uw inrichtingsservice wordt toegevoegd. Met enkele aanpassingen kunt u deze stappen ook volgen om een [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot) console-app te maken om aan de registratiegroep toe te voegen. Zie de [Windows IoT Core-documentatie voor ontwikkelaars](/windows/iot-core/) voor meer informatie over ontwikkelen met IoT-Core.

1. Open Visual Studio en selecteer **Een nieuw project maken**. Kies in **Een nieuw project maken** de **Console-app (.NET Core)** voor een C#-projectsjabloon en selecteer **Volgende**.

1. Noem het project *CreateEnrollmentGroup* en selecteer vervolgens **Maken**.

    ![Windows Classic Desktop-project in Visual C# configureren](media//quick-enroll-device-x509-csharp/configure-app-vs2019.png)

1. Wanneer de oplossing in Visual Studio wordt geopend, klikt u in het deelvenster **Solution Explorer** met de rechtermuisknop op het project **CreateEnrollmentGroup** en selecteert u vervolgens **NuGet-pakketten beheren**.

1. Selecteer in **NuGet Package Manager** de optie **Bladeren**, zoek en kies **Microsoft.Azure.Devices.Provisioning.Service** en selecteer vervolgens **Installeren**.

    ![Sluit het venster Nuget Package Manager.](media//quick-enroll-device-x509-csharp/add-nuget.png)

   Met deze stap worden het NuGet-pakket [Azure IoT Provisioning Service Client-SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) en de bijbehorende afhankelijkheden gedownload en geïnstalleerd. Ook worden verwijzingen hiernaar toegevoegd.

1. Voeg bovenaan in `Program.cs` de volgende `using`-instructies toe na de andere `using`-instructies:

   ```csharp
   using System.Security.Cryptography.X509Certificates;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Provisioning.Service;
   ```

1. Voeg de volgende velden toe aan de klasse `Program` en breng de vermelde wijzigingen aan.  

   ```csharp
   private static string ProvisioningConnectionString = "{ProvisioningServiceConnectionString}";
   private static string EnrollmentGroupId = "enrollmentgrouptest";
   private static string X509RootCertPath = @"{Path to a .cer or .pem file for a verified root CA or intermediate CA X.509 certificate}";
   ```

   * Vervang de tijdelijke aanduiding `ProvisioningServiceConnectionString` door de verbindingstekenreeks van de inrichtingsservice waarvoor u de inschrijving wilt maken.

   * Vervang de waarde van de tijdelijke plaatsaanduiding `X509RootCertPath` door het pad naar een PEM- of CER-bestand. Dit bestand staat voor het openbare gedeelte van een X.509 CA-tussen- of -basiscertificaat dat eerder is geüpload en geverifieerd met uw inrichtingsservice.

   * Wijzig eventueel de waarde `EnrollmentGroupId`. De tekenreeks mag alleen kleine letters en afbreekstreepjes bevatten.

   > [!IMPORTANT]
   > Voor productiecode dient u op de hoogte te zijn van de volgende beveiligingsoverwegingen:
   >
   > * Hard-coding van de verbindingsreeks voor de inrichtingsservicebeheerder is in strijd met de aanbevolen beveiligingsprocedures. In plaats daarvan moet de verbindingsreeks op een veilige manier worden ondergebracht, zoals in een beveiligd configuratiebestand of in het register.
   > * Upload alleen het openbare deel van het handtekeningcertificaat. Upload nooit .pfx- (PKCS12) of .pem-bestanden met persoonlijke sleutels naar de inrichtingsservice.

1. Voeg de volgende methode toe aan de klasse `Program`. Met deze code wordt een inschrijvingsgroepvermelding gemaakt en vervolgens de methode `CreateOrUpdateEnrollmentGroupAsync` in `ProvisioningServiceClient` aangeroepen om de inschrijvingsgroep toe te voegen aan de inrichtingsservice.

   ```csharp
   public static async Task RunSample()
   {
       Console.WriteLine("Starting sample...");
 
       using (ProvisioningServiceClient provisioningServiceClient =
               ProvisioningServiceClient.CreateFromConnectionString(ProvisioningConnectionString))
       {
           #region Create a new enrollmentGroup config
           Console.WriteLine("\nCreating a new enrollmentGroup...");
           var certificate = new X509Certificate2(X509RootCertPath);
           Attestation attestation = X509Attestation.CreateFromRootCertificates(certificate);
           EnrollmentGroup enrollmentGroup =
                   new EnrollmentGroup(
                           EnrollmentGroupId,
                           attestation)
                   {
                       ProvisioningStatus = ProvisioningStatus.Enabled
                   };
           Console.WriteLine(enrollmentGroup);
           #endregion
 
           #region Create the enrollmentGroup
           Console.WriteLine("\nAdding new enrollmentGroup...");
           EnrollmentGroup enrollmentGroupResult =
               await provisioningServiceClient.CreateOrUpdateEnrollmentGroupAsync(enrollmentGroup).ConfigureAwait(false);
           Console.WriteLine("\nEnrollmentGroup created with success.");
           Console.WriteLine(enrollmentGroupResult);
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

## <a name="run-the-enrollment-group-sample"></a>Het voorbeeld van de registratiegroep uitvoeren
  
Voer het voorbeeld uit in Visual Studio om de registratiegroep te maken. Er wordt een opdrachtpromptvenster weergegeven waarin bevestigingsberichten worden getoond. Als de inschrijving is gemaakt, worden in het opdrachtpromptvenster de eigenschappen van de nieuwe inschrijvingsgroep weergegeven.

U kunt controleren of de inschrijvingsgroep is gemaakt. Ga naar het overzicht voor Device Provisioning Service en selecteer **Inschrijvingen beheren** en vervolgens **Inschrijvingsgroepen**. U ziet nu een nieuwe registratievermelding die overeenkomt met de registratie-ID die u in het voorbeeld hebt gebruikt.

![Eigenschappen van de inschrijving in de portal](media/quick-enroll-device-x509-csharp/verify-enrollment-portal-vs2019.png)

Selecteer de vermelding om de vingerafdruk van het certificaat en andere eigenschappen van de vermelding te controleren.

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent om het servicevoorbeeld voor C#- te verkennen, verwijdert u de resources die u in deze quickstart hebt gemaakt niet. Wilt u dat niet, dan kunt u met de volgende stappen alle resources verwijderen die in deze quickstart zijn gemaakt.

1. Sluit het venster met de voorbeelduitvoer voor C# op de computer.

1. Navigeer naar Device Provisioning Service in Azure Portal, selecteer **Inschrijvingen beheren** en vervolgens **Inschrijvingsgroepen**. Selecteer de *registratie-id* voor de inschrijving die u hebt gemaakt met behulp van deze quickstart en selecteer vervolgens **Verwijderen**.

1. Selecteer in Device Provisioning Service in Azure Portal de optie **Certificaten**, kies het certificaat dat u hebt geüpload voor deze quickstart en selecteer **Verwijderen** bovenaan in **Certificaatgegevens**.  

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een inschrijvingsgroep voor een X.509 CA-tussen- of -basiscertificaat gemaakt met Azure IoT Hub Device Provisioning Service. Voor meer informatie over device provisioning, gaat u verder met de zelfstudie voor het instellen van Device Provisioning Service in Azure Portal.

> [!div class="nextstepaction"]
> [Zelfstudies over Azure IoT Hub Device Provisioning Service](./tutorial-set-up-cloud.md)