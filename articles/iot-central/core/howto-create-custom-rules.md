---
title: Uw Azure IoT Central uitbreiden met aangepaste regels en meldingen | Microsoft Docs
description: Als oplossingsontwikkelaar configureert u een IoT Central voor het verzenden van e-mailmeldingen wanneer een apparaat stopt met het verzenden van telemetrie. Deze oplossing maakt Azure Stream Analytics, Azure Functions en SendGrid.
author: philmea
ms.author: philmea
ms.date: 02/09/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc, devx-track-csharp
manager: philmea
ms.openlocfilehash: a65d9dbaed4d197c2e0843e73ff3f45b8678017e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107864213"
---
# <a name="extend-azure-iot-central-with-custom-rules-using-stream-analytics-azure-functions-and-sendgrid"></a>Azure IoT Central uitbreiden met aangepaste regels met behulp van Stream Analytics, Azure Functions en SendGrid

In deze handleiding ziet u, als ontwikkelaar van oplossingen, hoe u uw IoT Central uitbreidt met aangepaste regels en meldingen. In het voorbeeld ziet u hoe een melding naar een operator wordt verzonden wanneer een apparaat stopt met het verzenden van telemetrie. De oplossing gebruikt een [Azure Stream Analytics](../../stream-analytics/index.yml) om te detecteren wanneer een apparaat is gestopt met het verzenden van telemetrie. De Stream Analytics maakt gebruik van [Azure Functions](../../azure-functions/index.yml) voor het verzenden van e-mailberichten met [behulp van SendGrid.](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/)

In deze handleiding ziet u hoe u uw IoT Central verder kunt uitbreiden dan wat het al kan doen met de ingebouwde regels en acties.

In deze handleiding leert u het volgende:

* Telemetrie streamen vanuit een IoT Central met behulp van *continue gegevensexport.*
* Maak een Stream Analytics query die detecteert wanneer een apparaat is gestopt met het verzenden van gegevens.
* Verzend een e-mailmelding met behulp Azure Functions en SendGrid-services.

## <a name="prerequisites"></a>Vereisten

Als u de stappen in deze handleiding wilt uitvoeren, hebt u een actief Azure-abonnement nodig.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

### <a name="iot-central-application"></a>IoT Central-toepassing

Maak een IoT Central toepassing op de [Azure IoT Central Application Manager-website](https://aka.ms/iotcentral) met de volgende instellingen:

| Instelling | Waarde |
| ------- | ----- |
| Prijsplan | Standard |
| Toepassingsjabloon | In-store analytics – bewaking van voorwaarden |
| De naam van de toepassing | Accepteer de standaardwaarde of kies uw eigen naam |
| URL | Accepteer de standaardwaarde of kies uw eigen unieke URL-voorvoegsel |
| Directory | Uw Azure Active Directory-tenant |
| Azure-abonnement | Uw Azure-abonnement |
| Region | Uw dichtstbijzijnde regio |

In de voorbeelden en schermafbeeldingen in dit artikel wordt de **Verenigde Staten** gebruikt. Kies een locatie dicht bij u in de buurt en zorg ervoor dat u al uw resources in dezelfde regio maakt.

Deze toepassingssjabloon bevat twee gesimuleerde thermostaatapparaten die telemetrie verzenden.

### <a name="resource-group"></a>Resourcegroep

Gebruik de [Azure Portal om een resourcegroep](https://portal.azure.com/#create/Microsoft.ResourceGroup) met de naam **DetectStoppedDevices te** maken die de andere resources bevat die u maakt. Maak uw Azure-resources op dezelfde locatie als uw IoT Central toepassing.

### <a name="event-hubs-namespace"></a>Event Hubs-naamruimte

Gebruik de [Azure Portal om een Event Hubs maken met](https://portal.azure.com/#create/Microsoft.EventHub) de volgende instellingen:

| Instelling | Waarde |
| ------- | ----- |
| Naam    | Kies de naam van uw naamruimte |
| Prijscategorie | Basic |
| Abonnement | Uw abonnement |
| Resourcegroep | DetectStoppedDevices |
| Locatie | VS - oost |
| Doorvoereenheden | 1 |

### <a name="stream-analytics-job"></a>Stream Analytics-taak

Gebruik de [Azure Portal om een nieuwe Stream Analytics maken](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob)  met de volgende instellingen:

| Instelling | Waarde |
| ------- | ----- |
| Naam    | De naam van uw taak kiezen |
| Abonnement | Uw abonnement |
| Resourcegroep | DetectStoppedDevices |
| Locatie | VS - oost |
| Hostingomgeving | Cloud |
| Streaming-eenheden | 3 |

### <a name="function-app"></a>Functie-app

Gebruik de [Azure Portal om een functie-app te maken](https://portal.azure.com/#create/Microsoft.FunctionApp) met de volgende instellingen:

| Instelling | Waarde |
| ------- | ----- |
| Naam van app    | De naam van uw functie-app kiezen |
| Abonnement | Uw abonnement |
| Resourcegroep | DetectStoppedDevices |
| Besturingssysteem | Windows |
| Hostingabonnement | Verbruiksabonnement |
| Locatie | VS - oost |
| Runtimestack | .NET |
| Storage | Nieuwe maken |

### <a name="sendgrid-account-and-api-keys"></a>SendGrid-account en API-sleutels

Als u geen Sendgrid-account hebt, maakt u een [gratis account](https://app.sendgrid.com/) voordat u begint.

1. Selecteer API-sleutels in de Sendgrid-dashboardinstellingen in **het menu links.**
1. Klik **op API-sleutel maken.**
1. Noem de nieuwe **API-sleutel AzureFunctionAccess.**
1. Klik **op Maken & weergave.**

    :::image type="content" source="media/howto-create-custom-rules/sendgrid-api-keys.png" alt-text="Schermopname van De SendGrid-API-sleutel maken.":::

Daarna krijgt u een API-sleutel. Sla deze tekenreeks op voor later gebruik.

## <a name="create-an-event-hub"></a>Een Event Hub maken

U kunt een toepassing IoT Central om continu telemetrie te exporteren naar een Event Hub. In deze sectie maakt u een Event Hub om telemetrie te ontvangen van uw IoT Central toepassing. De Event Hub levert de telemetrie aan uw Stream Analytics taak voor verwerking.

1. Navigeer Azure Portal naar uw Event Hubs en selecteer **+ Event Hub**.
1. Noem uw Event Hub **centralexport** en selecteer **Maken.**

Uw Event Hubs-naamruimte ziet er als volgt uit: 

```:::image type="content" source="media/howto-create-custom-rules/event-hubs-namespace.png" alt-text="Screenshot of Event Hubs namespace." border="false":::

## Define the function

This solution uses an Azure Functions app to send an email notification when the Stream Analytics job detects a stopped device. To create your function app:

1. In the Azure portal, navigate to the **App Service** instance in the **DetectStoppedDevices** resource group.
1. Select **+** to create a new function.
1. Select **HTTP Trigger**.
1. Select **Add**.

    :::image type="content" source="media/howto-create-custom-rules/add-function.png" alt-text="Image of the Default HTTP trigger function"::: 

## Edit code for HTTP Trigger

The portal creates a default function called **HttpTrigger1**:

```:::image type="content" source="media/howto-create-custom-rules/default-function.png" alt-text="Screenshot of Edit HTTP trigger function.":::

1. Replace the C# code with the following code:

    ```csharp
    #r "Newtonsoft.Json"
    #r "SendGrid"
    using System;
    using SendGrid.Helpers.Mail;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    public static SendGridMessage Run(HttpRequest req, ILogger log)
    {
        string requestBody = new StreamReader(req.Body).ReadToEnd();
        log.LogInformation(requestBody);
        var notifications = JsonConvert.DeserializeObject<IList<Notification>>(requestBody);

        SendGridMessage message = new SendGridMessage();
        message.Subject = "Contoso device notification";

        var content = "The following device(s) have stopped sending telemetry:<br/><br/><table><tr><th>Device ID</th><th>Time</th></tr>";
        foreach(var notification in notifications) {
            log.LogInformation($"No message received - Device: {notification.deviceid}, Time: {notification.time}");
            content += $"<tr><td>{notification.deviceid}</td><td>{notification.time}</td></tr>";
        }
        content += "</table>";
        message.AddContent("text/html", content);  

        return message;
    }

    public class Notification
    {
        public string deviceid { get; set; }
        public string time { get; set; }
    }
    ```

    You may see an error message until you save the new code.
1. Select **Save** to save the function.

## Add SendGrid Key

To add your SendGrid API Key, you need to add it to your **Function Keys** as follows:

1. Select **Function Keys**.
1. Choose **+ New Function Key**.
1. Enter the *Name* and *Value* of the API Key you created before.
1. Click **OK.**

    :::image type="content" source="media/howto-create-custom-rules/add-key.png" alt-text="Screenshot of Add Sangrid Key.":::


## Configure HttpTrigger function to use SendGrid

To send emails with SendGrid, you need to configure the bindings for your function as follows:

1. Select **Integrate**.
1. Choose **Add Output** under **HTTP ($return)**.
1. Select **Delete.**
1. Choose **+ New Output**.
1. For Binding Type, then choose **SendGrid**.
1. For SendGrid API Key Setting Type, click New.
1. Enter the *Name* and *Value* of your SendGrid API key.
1. Add the following information:

| Setting | Value |
| ------- | ----- |
| Message parameter name | Choose your name |
| To address | Choose the name of your To Address |
| From address | Choose the name of your From Address |
| Message subject | Enter your subject header |
| Message text | Enter the message from your integration |

1. Select **OK**.

    :::image type="content" source="media/howto-create-custom-rules/add-output.png" alt-text="Screenshot of Add SandGrid Output.":::


### Test the function works

To test the function in the portal, first choose **Logs** at the bottom of the code editor. Then choose **Test** to the right of the code editor. Use the following JSON as the **Request body**:

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

De functielogboekberichten worden weergegeven in het **deelvenster Logboeken:**

```:::image type="content" source="media/howto-create-custom-rules/function-app-logs.png" alt-text="Function log output":::

After a few minutes, the **To** email address receives an email with the following content:

```txt
The following device(s) have stopped sending telemetry:

Device ID    Time
test-device-1    2019-05-02T14:23:39.527Z
test-device-2    2019-05-02T14:23:50.717Z
test-device-3    2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Een Stream Analytics toevoegen

Deze oplossing gebruikt een Stream Analytics om te detecteren wanneer een apparaat meer dan 120 seconden geen telemetrie meer verstuurt. De query gebruikt de telemetrie van de Event Hub als invoer. De taak verzendt de queryresultaten naar de functie-app. In deze sectie configureert u de Stream Analytics taak:

1. Navigeer in Azure Portal naar uw Stream Analytics taak,  selecteer onder Takentopologie de optie **Invoer,** kies **+ Stroominvoer** toevoegen en kies **vervolgens Event Hub**.
1. Gebruik de informatie in de volgende tabel om de invoer te configureren met behulp van de Event Hub die u eerder hebt gemaakt en kies vervolgens **Opslaan:**

    | Instelling | Waarde |
    | ------- | ----- |
    | Invoeralias | centraltelemetry |
    | Abonnement | Uw abonnement |
    | Event hub-naamruimte | Uw Event Hub-naamruimte |
    | Naam van de Event Hub | Bestaande gebruiken - **centralexport** |

1. Selecteer **onder Takentopologie** **de optie Uitvoer,** kies **+ Toevoegen** en kies vervolgens **Azure Function.**
1. Gebruik de informatie in de volgende tabel om de uitvoer te configureren en kies vervolgens **Opslaan:**

    | Instelling | Waarde |
    | ------- | ----- |
    | Uitvoeralias | e-mailnotificatie |
    | Abonnement | Uw abonnement |
    | Functie-app | Uw functie-app |
    | Functie  | HttpTrigger1 |

1. Selecteer **query onder Takentopologie** **en** vervang de bestaande query door de volgende SQL:

    ```sql
    with
    LeftSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid1, 
        EventEnqueuedUtcTime AS time1
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    ),
    RightSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid2, 
        EventEnqueuedUtcTime AS time2
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    )

    SELECT
        LeftSide.deviceid1 as deviceid,
        LeftSide.time1 as time
    INTO
        [emailnotification]
    FROM
        LeftSide
        LEFT OUTER JOIN
        RightSide 
        ON
        LeftSide.deviceid1=RightSide.deviceid2 AND DATEDIFF(second,LeftSide,RightSide) BETWEEN 1 AND 120
        where
        -- Find records where a device didn't send a message 120 seconds
        RightSide.deviceid2 is NULL
    ```

1. Selecteer **Opslaan**.
1. Als u de Stream Analytics wilt starten, **kiest** u Overzicht, vervolgens **Start**, **vervolgens Nu** en **vervolgens Start**:

    :::image type="content" source="media/howto-create-custom-rules/stream-analytics.png" alt-text="Schermopname van Stream Analytics.":::

## <a name="configure-export-in-iot-central"></a>Export configureren in IoT Central 

[Navigeer Azure IoT Central toepassingsbeheerwebsite](https://aka.ms/iotcentral) naar de IoT Central toepassing die u hebt gemaakt.

In deze sectie configureert u de toepassing voor het streamen van de telemetrie van de gesimuleerde apparaten naar uw Event Hub. De export configureren:

1. Navigeer **naar de pagina Gegevensexport,** selecteer + **Nieuw** en klik **vervolgens Azure Event Hubs**.
1. Gebruik de volgende instellingen om de export te configureren en selecteer vervolgens **Opslaan:** 

    | Instelling | Waarde |
    | ------- | ----- |
    | Weergavenaam | Exporteren naar Event Hubs |
    | Ingeschakeld | Uit |
    | Type gegevens dat moet worden geëxporteerd | Telemetrie |
    | Verrijkingen | Voer de gewenste sleutel/waarde in van de manier waarop u wilt dat de geëxporteerde gegevens worden geordend | 
    | Doel | Nieuwe maken en informatie invoeren voor waar de gegevens worden geëxporteerd |

    :::image type="content" source="media/howto-create-custom-rules/cde-configuration.png" alt-text="Schermopname van de gegevensexport.":::

Wacht totdat de exportstatus Actief is **voordat** u doorgaat.

## <a name="test"></a>Testen

Als u de oplossing wilt testen, kunt u de continue gegevensexport uitschakelen IoT Central gesimuleerde gestopte apparaten:

1. Navigeer in IoT Central toepassing naar de pagina **Gegevensexport** en selecteer exporteren naar **Event Hubs** exportconfiguratie.
1. Stel **Ingeschakeld in op** **Uit** en kies **Opslaan.**
1. Na ten minste twee minuten ontvangt het **e-mailadres Aan** een of meer e-mailberichten die lijken op de volgende voorbeeldinhoud:

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID         Time
    Thermostat-Zone1  2019-11-01T12:45:14.686Z
    ```

## <a name="tidy-up"></a>Opruimen

Verwijder de resourcegroep **DetectStoppedDevices** in de Azure Portal om deze op te schonen en onnodige kosten te Azure Portal.

U kunt de IoT Central verwijderen van de **beheerpagina** in de toepassing.

## <a name="next-steps"></a>Volgende stappen

In deze handleiding hebt u het volgende geleerd:

* Telemetrie streamen vanuit een IoT Central met behulp van *continue gegevensexport.*
* Maak een Stream Analytics query die detecteert wanneer een apparaat is gestopt met het verzenden van gegevens.
* Verzend een e-mailmelding met behulp Azure Functions en SendGrid-services.

Nu u weet hoe u aangepaste regels en meldingen maakt, is de voorgestelde volgende stap het uitbreiden van Azure IoT Central [met aangepaste analyses.](howto-create-custom-analytics.md)
