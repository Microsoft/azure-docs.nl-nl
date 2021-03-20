---
title: Een Azure IoT-hub maken met behulp van de resource provider REST API | Microsoft Docs
description: Meer informatie over het gebruik van de resource provider C# REST API om een IoT Hub programmatisch te maken en te beheren.
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 2f393701b97be76acab49a627a195b337018fa29
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92144437"
---
# <a name="create-an-iot-hub-using-the-resource-provider-rest-api-net"></a>Een IoT-hub maken met behulp van de REST API van de resource provider (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

U kunt de [IOT hub resource provider rest API](/rest/api/iothub/iothubresource) gebruiken om via een programma een Azure IOT hubs te maken en te beheren. Deze zelf studie laat zien hoe u de IoT Hub resource provider REST API kunt gebruiken om een IoT-hub te maken op basis van een C#-programma.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Visual Studio.

* Een actief Azure-account. Als u geen account hebt, kunt u in slechts een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) maken.

* [Azure PowerShell 1,0](/powershell/azure/install-Az-ps) of hoger.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Uw Visual Studio-project voorbereiden

1. Maak in Visual Studio een Visual C# Windows klassiek bureau blad-project met behulp van de project sjabloon **console-app (.NET Framework)** . Geef het project de naam **CreateIoTHubREST**.

2. Klik in Solution Explorer met de rechter muisknop op uw project en vervolgens op **NuGet-pakketten beheren**.

3. Controleer in NuGet package manager de optie **Prerelease toevoegen** en op de pagina **Bladeren** zoeken naar **micro soft. Azure. Management. Resource Manager**. Selecteer het pakket, klik op **installeren**, klik in **wijzigingen controleren** op **OK** en klik vervolgens op **Ik ga akkoord** om de licenties te accepteren.

4. Zoek in NuGet package manager naar **micro soft. Identity model. clients. ActiveDirectory**.  Klik op **installeren**, in **wijzigingen controleren** , klik op **OK** en klik vervolgens op **Ik ga akkoord** om de licentie te accepteren.

5. Vervang in Program. cs de bestaande **using** -instructies door de volgende code:

    ```csharp
    using System;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    using Microsoft.Rest;
    using System.Linq;
    using System.Threading;
    ```

6. Voeg in Program. cs de volgende statische variabelen toe om de waarden van de tijdelijke aanduidingen te vervangen. U hebt eerder in deze zelf studie de volgende opmerking gemaakt: **ApplicationId**, **SubscriptionId**, **TenantId** en **wacht woord** . De naam van de **resource groep** is de naam van de resource groep die u gebruikt bij het maken van de IOT-hub. U kunt een bestaande of een nieuwe resource groep gebruiken. **IOT hub naam** is de naam van de IOT hub die u maakt, zoals **MyIoTHub**. De naam van uw IoT-hub moet wereld wijd uniek zijn. De naam van de **implementatie** is een naam voor de implementatie, zoals **Deployment_01**.

    ```csharp
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";

    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name including your initials}";
    ```
   
    [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="use-the-resource-provider-rest-api-to-create-an-iot-hub"></a>De resource provider REST API gebruiken om een IoT-hub te maken

Gebruik de [IOT hub resource provider rest API](/rest/api/iothub/iothubresource) om een IOT-hub in uw resource groep te maken. U kunt ook de REST API van de resource provider gebruiken om wijzigingen aan te brengen in een bestaande IoT-hub.

1. Voeg de volgende methode toe aan Program. CS:

    ```csharp
    static void CreateIoTHub(string token)
    {

    }
    ```

2. Voeg de volgende code toe aan de methode **CreateIoTHub** . Met deze code wordt een **httpclient maakt** -object gemaakt met het verificatie token in de headers:

    ```csharp
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. Voeg de volgende code toe aan de methode **CreateIoTHub** . Met deze code wordt de IoT-hub beschreven voor het maken en genereren van een JSON-weer gave. Voor de huidige lijst met locaties die ondersteuning bieden voor IoT Hub raadpleegt u de [Azure-status](https://azure.microsoft.com/status/):

    ```csharp
    var description = new
    {
      name = iotHubName,
      location = "East US",
      sku = new
      {
        name = "S1",
        tier = "Standard",
        capacity = 1
      }
    };

    var json = JsonConvert.SerializeObject(description, Formatting.Indented);
    ```

4. Voeg de volgende code toe aan de methode **CreateIoTHub** . Met deze code wordt de REST-aanvraag verzonden naar Azure. De code controleert vervolgens het antwoord en haalt de URL op die u kunt gebruiken om de status van de implementatie taak te controleren:

    ```csharp
    var content = new StringContent(JsonConvert.SerializeObject(description), Encoding.UTF8, "application/json");
    var requestUri = string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var result = client.PutAsync(requestUri, content).Result;

    if (!result.IsSuccessStatusCode)
    {
      Console.WriteLine("Failed {0}", result.Content.ReadAsStringAsync().Result);
      return;
    }

    var asyncStatusUri = result.Headers.GetValues("Azure-AsyncOperation").First();
    ```

5. Voeg de volgende code toe aan het einde van de methode **CreateIoTHub** . Deze code maakt gebruik van het **asyncStatusUri** -adres dat u in de vorige stap hebt opgehaald om te wachten tot de implementatie is voltooid:

    ```csharp
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{\"status\":\"Running\"}");
    ```

6. Voeg de volgende code toe aan het einde van de methode **CreateIoTHub** . Met deze code worden de sleutels opgehaald van de IoT-hub die u hebt gemaakt en afgedrukt op de-console:

    ```csharp
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;

    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```

## <a name="complete-and-run-the-application"></a>De toepassing volt ooien en uitvoeren

U kunt de toepassing nu volt ooien door de **CreateIoTHub** -methode aan te roepen voordat u deze bouwt en uitvoert.

1. Voeg de volgende code toe aan het einde van de methode **Main** :

    ```csharp
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```

2. Klik op **Build** en **bouw** vervolgens de oplossing. Corrigeer eventuele fouten.

3. Klik op **fouten opsporen** en **Start de fout opsporing** om de toepassing uit te voeren. Het kan enkele minuten duren voordat de implementatie is uitgevoerd.

4. Als u wilt controleren of uw toepassing de nieuwe IoT hub heeft toegevoegd, gaat u naar de [Azure Portal](https://portal.azure.com/) en bekijkt u de lijst met resources. U kunt ook de Power shell **-cmdlet Get-AzResource** gebruiken.

> [!NOTE]
> In dit voor beeld wordt een standaard IoT Hub van S1 toegevoegd, waarvoor u wordt gefactureerd. Wanneer u klaar bent, kunt u de IoT-hub verwijderen via de [Azure Portal](https://portal.azure.com/) of met de Power shell **-cmdlet Remove-AzResource** wanneer u klaar bent.

## <a name="next-steps"></a>Volgende stappen

Nu u een IoT-hub hebt geïmplementeerd met behulp van de resource provider REST API, kunt u het beste verder verkennen:

* Meer informatie over de mogelijkheden van de [IOT hub resource provider rest API](/rest/api/iothub/iothubresource).

* Lees [Azure Resource Manager overzicht](../azure-resource-manager/management/overview.md) voor meer informatie over de mogelijkheden van Azure Resource Manager.

Raadpleeg de volgende artikelen voor meer informatie over het ontwikkelen van IoT Hub:

* [Inleiding tot C SDK](iot-hub-device-sdk-c-intro.md)

* [SDK's voor Azure IoT](iot-hub-devguide-sdks.md)

Zie voor meer informatie over de mogelijkheden van IoT Hub:

* [AI implementeren op Edge-apparaten met Azure IoT Edge](../iot-edge/quickstart-linux.md)