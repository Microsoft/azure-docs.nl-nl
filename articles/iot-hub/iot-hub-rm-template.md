---
title: Een Azure-IoT Hub maken met behulp van een sjabloon (.NET) | Microsoft Docs
description: Een Azure Resource Manager sjabloon gebruiken om een IoT Hub met een C#-programma te maken.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: db4b676e65d36a9476fd72b66cc8ccfa38af4d85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92144496"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-net"></a>Een IoT-hub maken met Azure Resource Manager-sjabloon (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

U kunt Azure Resource Manager gebruiken om via een programma een Azure IoT hubs te maken en te beheren. In deze zelf studie wordt uitgelegd hoe u een Azure Resource Manager sjabloon gebruikt om een IoT-hub te maken vanuit een C#-programma.

> [!NOTE]
> Azure heeft twee verschillende implementatie modellen voor het maken van en werken met resources:  [Azure Resource Manager en klassiek](../azure-resource-manager/management/deployment-models.md).  In dit artikel wordt beschreven hoe u het Azure Resource Manager-implementatie model gebruikt.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* Visual Studio.
* Een actief Azure-account. <br/>Als u geen account hebt, kunt u in slechts een paar minuten een [gratis account][lnk-free-trial] maken.
* Een [Azure Storage-account][lnk-storage-account] waar u uw Azure Resource Manager sjabloon bestanden kunt opslaan.
* [Azure PowerShell 1,0][lnk-powershell-install] of hoger.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Uw Visual Studio-project voorbereiden

1. Maak in Visual Studio een Visual C# Windows klassiek bureau blad-project met behulp van de project sjabloon **console-app (.NET Framework)** . Geef het project de naam **CreateIoTHub**.

2. Klik in Solution Explorer met de rechter muisknop op uw project en vervolgens op **NuGet-pakketten beheren**.

3. Controleer in NuGet package manager de optie **Prerelease toevoegen** en op de pagina **Bladeren** zoeken naar **micro soft. Azure. Management. Resource Manager**. Selecteer het pakket, klik op **installeren**, klik in **wijzigingen controleren** op **OK** en klik vervolgens op **Ik ga akkoord** om de licenties te accepteren.

4. Zoek in NuGet package manager naar **micro soft. Identity model. clients. ActiveDirectory**.  Klik op **installeren**, in **wijzigingen controleren** , klik op **OK** en klik vervolgens op **Ik ga akkoord** om de licentie te accepteren.

5. Vervang in Program. cs de bestaande **using** -instructies door de volgende code:

    ```csharp
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

6. Voeg in Program. cs de volgende statische variabelen toe om de waarden van de tijdelijke aanduidingen te vervangen. U hebt eerder in deze zelf studie de volgende opmerking gemaakt: **ApplicationId**, **SubscriptionId**, **TenantId** en **wacht woord** . De **naam van uw Azure Storage-account** is de naam van het Azure Storage account waar u de Azure Resource Manager sjabloon bestanden opslaat. De naam van de **resource groep** is de naam van de resource groep die u gebruikt bij het maken van de IOT-hub. De naam kan bestaan uit een bestaande of nieuwe resource groep. De naam van de **implementatie** is een naam voor de implementatie, zoals **Deployment_01**.

    ```csharp
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    static string storageAddress = "https://{Your storage account name}.blob.core.windows.net";
    static string rgName = "{Resource group name}";
    static string deploymentName = "{Deployment name}";
    ```

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="submit-a-template-to-create-an-iot-hub"></a>Een sjabloon voor het maken van een IoT hub verzenden

Gebruik een JSON-sjabloon en een parameter bestand om een IoT-hub in uw resource groep te maken. U kunt ook een Azure Resource Manager sjabloon gebruiken om wijzigingen aan te brengen in een bestaande IoT-hub.

1. Klik in Solution Explorer met de rechter muisknop op uw project, klikt u op **toevoegen** en klik vervolgens op **Nieuw item**. Voeg een JSON-bestand met de naam **template.js** toe aan uw project.

2. Als u een standaard IoT-hub wilt toevoegen aan de regio **VS-Oost** , vervangt u de inhoud van **template.js** door met de volgende resource definitie. Voor de huidige lijst met regio's die ondersteuning bieden voor IoT Hub raadpleegt u de [Azure-status][lnk-status]:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

3. Klik in Solution Explorer met de rechter muisknop op uw project, klikt u op **toevoegen** en klik vervolgens op **Nieuw item**. Voeg een JSON-bestand met de naam **parameters.js** toe aan uw project.

4. Vervang de inhoud van **parameters.js** door met de volgende parameter informatie waarmee een naam voor de nieuwe IOT-hub wordt ingesteld, zoals **{uw initialen} mynewiothub**. De naam van de IoT-hub moet globaal uniek zijn en moet uw naam of initialen bevatten:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": { "value": "mynewiothub" }
      }
    }
    ```
   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

5. In **Server Explorer** maakt u verbinding met uw Azure-abonnement en een container met de naam **sjablonen** in uw Azure Storage-account. Stel in het deel venster **Eigenschappen** de **open bare machtiging Lees toegang** voor de **sjablonen** container in op **BLOB**.

6. Klik in **Server Explorer** met de rechter muisknop op de **sjablonen** container en vervolgens op **BLOB-container weer geven**. Klik op de knop **BLOB uploaden** , selecteer de twee bestanden **parameters.jsop** en **templates.jsop** en klik vervolgens op **openen** om de json-bestanden te uploaden naar de container **sjablonen** . De Url's van de blobs met de JSON-gegevens zijn:

    ```csharp
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.blob.core.windows.net/templates/template.json
    ```
7. Voeg de volgende methode toe aan Program. CS:

    ```csharp
    static void CreateIoTHub(ResourceManagementClient client)
    {

    }
    ```

8. Voeg de volgende code toe aan de methode **CreateIoTHub** om de sjabloon en de parameter bestanden naar het Azure Resource Manager te verzenden:

    ```csharp
    var createResponse = client.Deployments.CreateOrUpdate(
        rgName,
        deploymentName,
        new Deployment()
        {
          Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = storageAddress + "/templates/template.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = storageAddress + "/templates/parameters.json"
            }
          }
        });
    ```

9. Voeg de volgende code toe aan de **CreateIoTHub** -methode waarin de status en de sleutels voor de nieuwe IOT hub worden weer gegeven:

    ```csharp
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## <a name="complete-and-run-the-application"></a>De toepassing volt ooien en uitvoeren

U kunt de toepassing nu volt ooien door de **CreateIoTHub** -methode aan te roepen voordat u deze bouwt en uitvoert.

1. Voeg de volgende code toe aan het einde van de methode **Main** :

    ```csharp
    CreateIoTHub(client);
    Console.ReadLine();
    ```

2. Klik op **Build** en **bouw** vervolgens de oplossing. Corrigeer eventuele fouten.

3. Klik op **fouten opsporen** en **Start de fout opsporing** om de toepassing uit te voeren. Het kan enkele minuten duren voordat de implementatie is uitgevoerd.

4. Als u wilt controleren of uw toepassing de nieuwe IoT hub heeft toegevoegd, gaat u naar de [Azure Portal][lnk-azure-portal] en bekijkt u uw lijst met resources. U kunt ook de Power shell **-cmdlet Get-AzResource** gebruiken.

> [!NOTE]
> In dit voor beeld wordt een standaard IoT Hub van S1 toegevoegd, waarvoor u wordt gefactureerd. U kunt de IoT-hub verwijderen via de [Azure Portal][lnk-azure-portal] of door de Power shell **-cmdlet Remove-AzResource** te gebruiken wanneer u klaar bent.

## <a name="next-steps"></a>Volgende stappen
Nu u een IoT-hub hebt geïmplementeerd met behulp van een Azure Resource Manager sjabloon met een C#-programma, kunt u het beste verder verkennen:

* Meer informatie over de mogelijkheden van de [IOT hub resource provider rest API][lnk-rest-api].
* Lees [Azure Resource Manager overzicht][lnk-azure-rm-overview] voor meer informatie over de mogelijkheden van Azure Resource Manager.
* Zie [resource typen van micro soft. devices](/azure/templates/microsoft.devices/iothub-allversions)voor de JSON-syntaxis en-eigenschappen die in sjablonen moeten worden gebruikt.

Raadpleeg de volgende artikelen voor meer informatie over het ontwikkelen van IoT Hub:

* [Inleiding tot C SDK][lnk-c-sdk]
* [SDK's voor Azure IoT][lnk-sdks]

Zie voor meer informatie over de mogelijkheden van IoT Hub:

* [AI implementeren op Edge-apparaten met Azure IoT Edge][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: /rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/management/overview.md
[lnk-storage-account]:../storage/common/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/quickstart-linux.md