---
title: Azure-IoT Central uitbreiden met aangepaste regels en meldingen | Microsoft Docs
description: Als oplossings ontwikkelaar kunt u een IoT Central-toepassing configureren om e-mail meldingen te verzenden wanneer een apparaat stopt met het verzenden van telemetrie. Deze oplossing maakt gebruik van Azure Stream Analytics, Azure Functions en SendGrid.
author: TheJasonAndrew
ms.author: v-anjaso
ms.date: 02/09/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc, devx-track-csharp
manager: philmea
ms.openlocfilehash: 6146676121bac0089d5f520d60a97d74567a32bc
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179337"
---
# <a name="extend-azure-iot-central-with-custom-rules-using-stream-analytics-azure-functions-and-sendgrid"></a>Azure IoT Central uitbreiden met aangepaste regels met behulp van Stream Analytics, Azure Functions en SendGrid

In deze hand leiding wordt uitgelegd hoe u als ontwikkel aars van oplossingen uw IoT Central-toepassing kunt uitbreiden met aangepaste regels en meldingen. Het voor beeld toont het verzenden van een melding naar een operator wanneer een apparaat stopt met het verzenden van telemetrie. De oplossing gebruikt een [Azure stream Analytics](../../stream-analytics/index.yml) query om te detecteren wanneer een apparaat stopt met het verzenden van telemetrie. De Stream Analytics taak gebruikt [Azure functions](../../azure-functions/index.yml) om e-mail meldingen te verzenden met [SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/).

In deze hand leiding wordt uitgelegd hoe u IoT Central uitbreidt dan wat u al kunt doen met de ingebouwde regels en acties.

In deze hand leiding leert u het volgende:

* Telemetrie streamen vanuit een IoT Central-toepassing met *doorlopende gegevens export*.
* Maak een Stream Analytics query die detecteert wanneer een apparaat stopt met het verzenden van gegevens.
* Een e-mail melding verzenden met behulp van de Azure Functions-en SendGrid-Services.

## <a name="prerequisites"></a>Vereisten

Als u de stappen in deze hand leiding wilt uitvoeren, hebt u een actief Azure-abonnement nodig.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

### <a name="iot-central-application"></a>IoT Central-toepassing

Maak een IoT Central-toepassing op de website van [Azure IOT Central Application Manager](https://aka.ms/iotcentral) met de volgende instellingen:

| Instelling | Waarde |
| ------- | ----- |
| Prijs plan | Standard |
| Toepassingsjabloon | Analyses in de Store-voor waarde |
| De naam van de toepassing | Accepteer de standaard waarde of kies uw eigen naam |
| URL | Accepteer de standaard waarde of kies uw eigen unieke URL-voor voegsel |
| Directory | Uw Azure Active Directory-Tenant |
| Azure-abonnement | Uw Azure-abonnement |
| Region | Uw dichtstbijzijnde regio |

In de voor beelden en scherm afbeeldingen in dit artikel wordt gebruikgemaakt van de **Verenigde Staten** regio. Kies een locatie dicht bij u en zorg ervoor dat u alle resources in dezelfde regio maakt.

Deze toepassings sjabloon bevat twee gesimuleerde Thermo staat-apparaten die telemetrie verzenden.

### <a name="resource-group"></a>Resourcegroep

Gebruik de [Azure Portal om een resource groep te maken](https://portal.azure.com/#create/Microsoft.ResourceGroup) met de naam **DetectStoppedDevices** die de andere resources bevat die u maakt. Maak uw Azure-resources op dezelfde locatie als uw IoT Central-toepassing.

### <a name="event-hubs-namespace"></a>Event Hubs-naamruimte

Gebruik de [Azure Portal om een event hubs naam ruimte te maken](https://portal.azure.com/#create/Microsoft.EventHub) met de volgende instellingen:

| Instelling | Waarde |
| ------- | ----- |
| Naam    | De naam van de naam ruimte kiezen |
| Prijscategorie | Basic |
| Abonnement | Uw abonnement |
| Resourcegroep | DetectStoppedDevices |
| Locatie | VS - oost |
| Doorvoereenheden | 1 |

### <a name="stream-analytics-job"></a>Stream Analytics-taak

Gebruik de [Azure Portal om een stream Analytics-taak te maken](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob)  met de volgende instellingen:

| Instelling | Waarde |
| ------- | ----- |
| Naam    | Kies de naam van uw taak |
| Abonnement | Uw abonnement |
| Resourcegroep | DetectStoppedDevices |
| Locatie | VS - oost |
| Hostingomgeving | Cloud |
| Streaming-eenheden | 3 |

### <a name="function-app"></a>Functie-app

Gebruik de [Azure Portal om een functie-app te maken](https://portal.azure.com/#create/Microsoft.FunctionApp) met de volgende instellingen:

| Instelling | Waarde |
| ------- | ----- |
| Naam van app    | De naam van de functie-app kiezen |
| Abonnement | Uw abonnement |
| Resourcegroep | DetectStoppedDevices |
| Besturingssysteem | Windows |
| Hostingabonnement | Verbruiksabonnement |
| Locatie | VS - oost |
| Runtimestack | .NET |
| Storage | Nieuwe maken |

### <a name="sendgrid-account-and-api-keys"></a>SendGrid-account en API-sleutels

Als u geen Sendgrid-account hebt, maakt u een [gratis account](https://app.sendgrid.com/) voordat u begint.

1. Selecteer in de Sendgrid-dashboard instellingen in het menu links **API-sleutels**.
1. Klik op **API-sleutel maken.**
1. Noem de nieuwe API-sleutel **AzureFunctionAccess.**
1. Klik op **& weer gave maken**.

    :::image type="content" source="media/howto-create-custom-rules/sendgrid-api-keys.png" alt-text="Scherm afbeelding van de SendGrid-API-sleutel maken.":::

Daarna krijgt u een API-sleutel. Sla deze teken reeks op voor later gebruik.

## <a name="create-an-event-hub"></a>Een Event Hub maken

U kunt een IoT Central-toepassing configureren om voortdurend telemetrie te exporteren naar een Event Hub. In deze sectie maakt u een Event Hub voor het ontvangen van telemetrie van uw IoT Central-toepassing. De Event Hub levert de telemetrie naar uw Stream Analytics-taak voor verwerking.

1. Ga in het Azure Portal naar uw Event Hubs-naam ruimte en selecteer **+ Event hub**.
1. Geef uw Event Hub **centralexport** een naam en selecteer **maken**.

De naam ruimte van uw Event Hubs ziet eruit als in de volgende scherm afbeelding: 

:::image type="content" source="media/howto-create-custom-rules/event-hubs-namespace.png" alt-text="Scherm opname van Event Hubs naam ruimte." border="false":::


## <a name="define-the-function"></a>Definieer de functie

Deze oplossing maakt gebruik van een Azure Functions-app om een e-mail bericht te verzenden wanneer de Stream Analytics-taak een gestopt apparaat detecteert. De functie-app maken:

1. Ga in het Azure Portal naar de **app service** instantie in de resource groep **DetectStoppedDevices** .
1. Selecteer deze optie **+** om een nieuwe functie te maken.
1. Selecteer **http-trigger**.
1. Selecteer **Toevoegen**.

    :::image type="content" source="media/howto-create-custom-rules/add-function.png" alt-text="Afbeelding van de standaard functie voor HTTP-triggers"::: 

## <a name="edit-code-for-http-trigger"></a>Code voor HTTP-trigger bewerken

De portal maakt een standaard functie met de naam **HttpTrigger1**:

:::image type="content" source="media/howto-create-custom-rules/default-function.png" alt-text="Scherm opname van de functie HTTP-trigger bewerken.":::


1. Vervang de C#-code door de volgende code:

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

    Er wordt mogelijk een fout bericht weer gegeven totdat u de nieuwe code opslaat.
1. Selecteer **Opslaan** om de functie op te slaan.

## <a name="add-sendgrid-key"></a>SendGrid-sleutel toevoegen

Als u uw SendGrid-API-sleutel wilt toevoegen, moet u deze als volgt toevoegen aan de **functie sleutels** :

1. Selecteer **functie toetsen**.
1. Kies **+ nieuwe functie toets**.
1. Voer de *naam* en de *waarde* in van de API-sleutel die u eerder hebt gemaakt.
1. Klik op **OK.**

    :::image type="content" source="media/howto-create-custom-rules/add-key.png" alt-text="Scherm opname van de Sangrid-sleutel toevoegen.":::


## <a name="configure-httptrigger-function-to-use-sendgrid"></a>De functie http trigger configureren voor het gebruik van SendGrid

Als u e-mail berichten met SendGrid wilt verzenden, moet u de bindingen voor uw functie als volgt configureren:

1. Selecteer **Integreren**.
1. Kies **uitvoer toevoegen** onder **http ($Return)**.
1. Selecteer **verwijderen.**
1. Kies **+ nieuwe uitvoer**.
1. Kies voor bindings type de optie **SendGrid**.
1. Klik voor de SendGrid-API-sleutel instelling op nieuw.
1. Voer de *naam* en *waarde* van uw SendGrid API-sleutel in.
1. Voeg de volgende informatie toe:

| Instelling | Waarde |
| ------- | ----- |
| Naam van de berichtparameter | Uw naam kiezen |
| Naar adres | De naam van uw adres kiezen |
| Van-adres | De naam van uw from-adres kiezen |
| Onderwerp van bericht | Voer uw koptekst voor het onderwerp in |
| Berichttekst | Het bericht van uw integratie invoeren |

1. Selecteer **OK**.

    :::image type="content" source="media/howto-create-custom-rules/add-output.png" alt-text="Scherm opname van SandGrid-uitvoer toevoegen.":::


### <a name="test-the-function-works"></a>De functie testen

Als u de functie in de portal wilt testen, kiest u eerst **Logboeken** aan de onderkant van de code-editor. Kies vervolgens **testen** rechts van de code-editor. Gebruik de volgende JSON als de **hoofd tekst** van de aanvraag:

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

De functie logboek berichten worden weer gegeven in het deel venster **Logboeken** :

:::image type="content" source="media/howto-create-custom-rules/function-app-logs.png" alt-text="Uitvoer van functie logboek":::

Na enkele minuten ontvangt het e-mail adres **van de e-mail** een e-mail bericht met de volgende inhoud:

```txt
The following device(s) have stopped sending telemetry:

Device ID    Time
test-device-1    2019-05-02T14:23:39.527Z
test-device-2    2019-05-02T14:23:50.717Z
test-device-3    2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Stream Analytics query toevoegen

Deze oplossing maakt gebruik van een Stream Analytics query om te detecteren wanneer een apparaat meer dan 120 seconden stopt met het verzenden van telemetrie. De query gebruikt de telemetrie van de Event Hub als invoer. De taak verzendt de query resultaten naar de functie-app. In deze sectie configureert u de Stream Analytics taak:

1. Navigeer in het Azure Portal naar uw Stream Analytics-taak, onder **taak topologie** Selecteer **invoer**, kies **+ stroom invoer toevoegen** en kies vervolgens **Event hub**.
1. Gebruik de informatie in de volgende tabel om de invoer te configureren met behulp van de Event Hub die u eerder hebt gemaakt en kies vervolgens **Opslaan**:

    | Instelling | Waarde |
    | ------- | ----- |
    | Invoeralias | centraltelemetry |
    | Abonnement | Uw abonnement |
    | Event hub-naamruimte | De Event hub-naam ruimte |
    | Naam van de Event Hub | Bestaande- **centralexport** gebruiken |

1. Selecteer onder **taak topologie** **uitvoer**, kies **+ toevoegen** en kies **Azure function**.
1. Gebruik de informatie in de volgende tabel om de uitvoer te configureren en kies vervolgens **Opslaan**:

    | Instelling | Waarde |
    | ------- | ----- |
    | Uitvoeralias | emailnotification |
    | Abonnement | Uw abonnement |
    | Functie-app | Uw functie-app |
    | Functie  | HttpTrigger1 |

1. Selecteer bij **taak topologie** de optie **query** en vervang de bestaande query door de volgende SQL:

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
1. Als u de Stream Analytics taak wilt starten, kiest u **overzicht**, **Start**, vervolgens **nu** en **Start** u het volgende:

    :::image type="content" source="media/howto-create-custom-rules/stream-analytics.png" alt-text="Scherm opname van Stream Analytics.":::

## <a name="configure-export-in-iot-central"></a>Exporteren configureren in IoT Central 

Ga op de website van [Azure IOT Central Application Manager](https://aka.ms/iotcentral) naar de IOT Central toepassing die u hebt gemaakt.

In deze sectie configureert u de toepassing voor het streamen van de telemetrie van de gesimuleerde apparaten naar uw Event Hub. Het exporteren configureren:

1. Ga naar de pagina voor het **exporteren van gegevens** , selecteer **+ Nieuw** en klik vervolgens op **Azure Event hubs**.
1. Gebruik de volgende instellingen om het exporteren te configureren en selecteer vervolgens **Opslaan**: 

    | Instelling | Waarde |
    | ------- | ----- |
    | Weergavenaam | Exporteren naar Event Hubs |
    | Ingeschakeld | Uit |
    | Type gegevens dat moet worden geëxporteerd | Telemetrie |
    | Verrijken | Voer de gewenste sleutel/waarde in van de manier waarop u de geëxporteerde gegevens wilt ordenen | 
    | Doel | Nieuwe maken en informatie invoeren voor het exporteren van de gegevens |

    :::image type="content" source="media/howto-create-custom-rules/cde-configuration.png" alt-text="Scherm opname van het exporteren van gegevens.":::

Wacht tot de export status **actief** is voordat u doorgaat.

## <a name="test"></a>Testen

Als u de oplossing wilt testen, kunt u de continue gegevens export uitschakelen van IoT Central naar gesimuleerde gestopte apparaten:

1. Navigeer in uw IoT Central-toepassing naar de pagina voor het **exporteren van gegevens** en selecteer de configuratie **exporteren naar Event hubs** exporteren.
1. Stel **ingeschakeld** in op **uit** en kies **Opslaan**.
1. Na ten minste twee minuten ontvangt het **e-** mail adres een of meer e-mails die eruitzien als de volgende voorbeeld inhoud:

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID         Time
    Thermostat-Zone1  2019-11-01T12:45:14.686Z
    ```

## <a name="tidy-up"></a>Opruimen

Als u wilt opruimen na deze procedure en overbodige kosten wilt voor komen, verwijdert u de resource groep **DetectStoppedDevices** in de Azure Portal.

U kunt de IoT Central toepassing verwijderen van de **beheer** pagina binnen de toepassing.

## <a name="next-steps"></a>Volgende stappen

In deze hand leiding hebt u het volgende geleerd:

* Telemetrie streamen vanuit een IoT Central-toepassing met *doorlopende gegevens export*.
* Maak een Stream Analytics query die detecteert wanneer een apparaat stopt met het verzenden van gegevens.
* Een e-mail melding verzenden met behulp van de Azure Functions-en SendGrid-Services.

Nu u weet hoe u aangepaste regels en meldingen kunt maken, is de voorgestelde volgende stap informatie over het [uitbreiden van Azure IOT Central met aangepaste analyses](howto-create-custom-analytics.md).
