---
title: Azure-IoT Central uitbreiden met aangepaste regels en meldingen | Microsoft Docs
description: Als oplossings ontwikkelaar kunt u een IoT Central-toepassing configureren om e-mail meldingen te verzenden wanneer een apparaat stopt met het verzenden van telemetrie. Deze oplossing maakt gebruik van Azure Stream Analytics, Azure Functions en SendGrid.
author: dominicbetts
ms.author: dobett
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc, devx-track-csharp
manager: philmea
ms.openlocfilehash: c79367ca8cf9e4a4884c829c675d794b2e734737
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98220262"
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

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

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
| Regio | Uw dichtstbijzijnde regio |

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

### <a name="sendgrid-account"></a>SendGrid-account

Gebruik de [Azure Portal om een SendGrid-account te maken](https://portal.azure.com/#create/Sendgrid.sendgrid) met de volgende instellingen:

| Instelling | Waarde |
| ------- | ----- |
| Naam    | Uw SendGrid-account naam kiezen |
| Wachtwoord | Een wacht woord maken |
| Abonnement | Uw abonnement |
| Resourcegroep | DetectStoppedDevices |
| Prijscategorie | F1 Free |
| Contactgegevens | Vereiste gegevens invullen |

Wanneer u alle vereiste resources hebt gemaakt, ziet uw **DetectStoppedDevices** -resource groep er als volgt uit:

![Resource groep voor gestopt apparaten detecteren](media/howto-create-custom-rules/resource-group.png)

## <a name="create-an-event-hub"></a>Een Event Hub maken

U kunt een IoT Central-toepassing configureren om voortdurend telemetrie te exporteren naar een Event Hub. In deze sectie maakt u een Event Hub voor het ontvangen van telemetrie van uw IoT Central-toepassing. De Event Hub levert de telemetrie naar uw Stream Analytics-taak voor verwerking.

1. Ga in het Azure Portal naar uw Event Hubs-naam ruimte en selecteer **+ Event hub**.
1. Geef uw Event Hub **centralexport** een naam en selecteer **maken**.

De naam ruimte van uw Event Hubs ziet eruit als in de volgende scherm afbeelding:

![Event Hubs-naamruimte](media/howto-create-custom-rules/event-hubs-namespace.png)

## <a name="get-sendgrid-api-key"></a>SendGrid-API-sleutel ophalen

De functie-app heeft een SendGrid API-sleutel nodig om e-mail berichten te verzenden. Een SendGrid-API-sleutel maken:

1. Ga in het Azure Portal naar uw SendGrid-account. Kies vervolgens **beheren** om toegang te krijgen tot uw SendGrid-account.
1. Kies in uw SendGrid-account **instellingen** en vervolgens de **API-sleutels**. Kies **API-sleutel maken**:

    ![SendGrid API-sleutel maken](media/howto-create-custom-rules/sendgrid-api-keys.png)

1. Maak op de pagina **API-sleutel maken** een sleutel met de naam **AzureFunctionAccess** met **volledige toegangs** machtigingen.
1. Noteer de API-sleutel, u hebt deze nodig bij het configureren van de functie-app.

## <a name="define-the-function"></a>Definieer de functie

Deze oplossing maakt gebruik van een Azure Functions-app om een e-mail bericht te verzenden wanneer de Stream Analytics-taak een gestopt apparaat detecteert. De functie-app maken:

1. Ga in het Azure Portal naar de **app service** instantie in de resource groep **DetectStoppedDevices** .
1. Selecteer deze optie **+** om een nieuwe functie te maken.
1. Kies op de pagina **een ontwikkel omgeving kiezen** de optie **in-portal** en selecteer vervolgens **door gaan**.
1. Kies op de pagina **een functie maken** de optie **webhook + API** en selecteer vervolgens **maken**.

De portal maakt een standaard functie met de naam **HttpTrigger1**:

![Standaard functie voor HTTP-triggers](media/howto-create-custom-rules/default-function.png)

### <a name="configure-function-bindings"></a>Functie bindingen configureren

Als u e-mail berichten met SendGrid wilt verzenden, moet u de bindingen voor uw functie als volgt configureren:

1. Selecteer **integreren**, kies de uitvoer **http ($Return)** en selecteer vervolgens **verwijderen**.
1. Kies **+ nieuwe uitvoer**, kies **SendGrid** en kies vervolgens **selecteren**. Kies **installeren** om de SendGrid-extensie te installeren.
1. Wanneer de installatie is voltooid, selecteert **u functie retour waarde gebruiken**. Voeg een geldig **adres toe** aan om e-mail meldingen te ontvangen.  Voeg een geldig **afzender adres** toe voor gebruik als de afzender van het e-mail bericht.
1. Selecteer **Nieuw** naast **SendGrid API Key app setting**. Voer **SendGridAPIKey** in als de sleutel en de SENDGRID-API-sleutel die u eerder hebt genoteerd als waarde. Selecteer vervolgens **Maken**.
1. Kies **Opslaan** om de SendGrid-bindingen voor uw functie op te slaan.

De instellingen voor integreren zien eruit als in de volgende scherm afbeelding:

![Integratie van functie-app](media/howto-create-custom-rules/function-integrate.png)

### <a name="add-the-function-code"></a>De functie code toevoegen

Als u de functie wilt implementeren, voegt u de C#-code toe om de binnenkomende HTTP-aanvraag te parseren en de e-mail berichten te verzenden als volgt:

1. Kies de functie **HttpTrigger1** in de functie-app en vervang de C#-code door de volgende code:

    ```csharp
    #r "Newtonsoft.Json"
    #r "..\bin\SendGrid.dll"

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

### <a name="test-the-function-works"></a>De functie testen

Als u de functie in de portal wilt testen, kiest u eerst **Logboeken** aan de onderkant van de code-editor. Kies vervolgens **testen** rechts van de code-editor. Gebruik de volgende JSON als de **hoofd tekst** van de aanvraag:

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

De functie logboek berichten worden weer gegeven in het deel venster **Logboeken** :

![Uitvoer van functie logboek](media/howto-create-custom-rules/function-app-logs.png)

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

    ![Stream Analytics](media/howto-create-custom-rules/stream-analytics.png)

## <a name="configure-export-in-iot-central"></a>Exporteren configureren in IoT Central

Ga op de website van [Azure IOT Central Application Manager](https://aka.ms/iotcentral) naar de IOT Central toepassing die u hebt gemaakt op basis van de contoso-sjabloon. In deze sectie configureert u de toepassing voor het streamen van de telemetrie van de gesimuleerde apparaten naar uw Event Hub. Het exporteren configureren:

1. Ga naar de pagina voor het **exporteren van gegevens** , selecteer **+ Nieuw** en klik vervolgens op **Azure Event hubs**.
1. Gebruik de volgende instellingen om het exporteren te configureren en selecteer vervolgens **Opslaan**:

    | Instelling | Waarde |
    | ------- | ----- |
    | Weergavenaam | Exporteren naar Event Hubs |
    | Ingeschakeld | Uit |
    | Event Hubs-naamruimte | De naam van uw Event Hubs-naam ruimte |
    | Event Hub | centralexport |
    | Metingen | Uit |
    | Apparaten | Aan |
    | Apparaatsjablonen | Aan |

![Configuratie voor continue gegevensexport](media/howto-create-custom-rules/cde-configuration.png)

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
