---
title: Registratie beheer
description: In dit onderwerp wordt uitgelegd hoe u apparaten registreert bij Notification hubs om Push meldingen te ontvangen.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: fd0ee230-132c-4143-b4f9-65cef7f463a1
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 07/07/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 04/08/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 7807d28da459656938acb399eb8c621e4c292372
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89001566"
---
# <a name="registration-management"></a>Registratiebeheer

In dit onderwerp wordt uitgelegd hoe u apparaten registreert bij Notification hubs om Push meldingen te ontvangen. In het onderwerp worden registraties op hoog niveau beschreven. vervolgens worden de twee belangrijkste patronen voor het registreren van apparaten geïntroduceerd: de registratie van het apparaat rechtstreeks op de notification hub en registreren via een back-end van de toepassing.

## <a name="what-is-device-registration"></a>Wat is apparaatregistratie

Apparaatregistratie met een notification hub wordt uitgevoerd met behulp van een **registratie** of **installatie**.

### <a name="registrations"></a>Registraties

Bij een registratie wordt de PNS-ingang (platform Notification Service) voor een apparaat gekoppeld aan Tags en mogelijk een sjabloon. De PNS-ingang kan een kanaal, een token of een FCM-registratie-ID zijn. Labels worden gebruikt voor het routeren van meldingen naar de juiste set met apparaat-ingangen. Zie [route ring en label expressies](notification-hubs-tags-segment-push-message.md)voor meer informatie. Sjablonen worden gebruikt voor het implementeren van trans formatie per registratie. Zie [Sjablonen](notification-hubs-templates-cross-platform-push-messages.md) voor meer informatie.

> [!NOTE]
> Azure Notification Hubs ondersteunt Maxi maal 60 Tags per apparaat.

### <a name="installations"></a>Installaties

Een installatie is een uitgebreide registratie die een verzameling van push gerelateerde eigenschappen bevat. Het is de meest recente en beste manier om uw apparaten te registreren. Dit wordt echter niet ondersteund door de .NET SDK van de client ([Notification hub SDK voor back-upbewerkingen](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)) vanaf nog.  Dit betekent dat als u zich registreert vanaf het client apparaat zelf, u de [Notification Hubs rest API](/rest/api/notificationhubs/create-overwrite-installation) aanpak moet gebruiken ter ondersteuning van installaties. Als u een back-end-service gebruikt, moet u de [SDK voor de notification hub kunnen gebruiken voor back-end-bewerkingen](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

Hieronder vindt u enkele belang rijke voor delen van het gebruik van installaties:

- Het is volledig idempotent om een installatie te maken of bij te werken. Daarom kunt u het opnieuw proberen zonder dat er problemen zijn met het dupliceren van registraties.
- Het installatie model ondersteunt een speciale label indeling ( `$InstallationId:{INSTALLATION_ID}` ) waarmee een melding rechtstreeks naar het specifieke apparaat kan worden verzonden. Als de code van de app bijvoorbeeld een installatie-ID `joe93developer` voor dit specifieke apparaat instelt, kan een ontwikkelaar dit apparaat richten bij het verzenden van een melding naar de `$InstallationId:{joe93developer}` tag. Zo kunt u een specifiek apparaat richten zonder extra code ring uit te voeren.
- U kunt met behulp van installaties gedeeltelijke registratie-updates uitvoeren. De gedeeltelijke update van een installatie wordt met behulp van de [JSON-patch-standaard](https://tools.ietf.org/html/rfc6902)met een patch methode aangevraagd. Dit is handig wanneer u labels voor de registratie wilt bijwerken. U hoeft niet de volledige registratie op te halen en vervolgens opnieuw alle vorige Tags te verzenden.

Een installatie kan de volgende eigenschappen bevatten. Zie [een installatie met rest API](/rest/api/notificationhubs/create-overwrite-installation) of [installatie-eigenschappen](/dotnet/api/microsoft.azure.notificationhubs.installation)maken of overschrijven voor een volledige lijst met installatie-eigenschappen.

```json
// Example installation format to show some supported properties
{
    installationId: "",
    expirationTime: "",
    tags: [],
    platform: "",
    pushChannel: "",
    ………
    templates: {
        "templateName1" : {
            body: "",
            tags: [] },
        "templateName2" : {
            body: "",
            // Headers are for Windows Store only
            headers: {
                "X-WNS-Type": "wns/tile" }
            tags: [] }
    },
    secondaryTiles: {
        "tileId1": {
            pushChannel: "",
            tags: [],
            templates: {
                "otherTemplate": {
                    bodyTemplate: "",
                    headers: {
                        ... }
                    tags: [] }
            }
        }
    }
}
```

> [!NOTE]
> Registraties en installaties verlopen standaard niet.

Registraties en installaties moeten een geldige PNS-ingang voor elk apparaat/kanaal bevatten. Omdat PNS-ingangen alleen kunnen worden verkregen in een client-app op het apparaat, moet een patroon rechtstreeks op dat apparaat worden geregistreerd met de client-app. Op de andere kant moeten de beveiligings overwegingen en de bedrijfs logica met betrekking tot Tags ertoe leiden dat u apparaatregistratie in de back-end van de app beheert.

> [!NOTE]
> De installatie-API biedt geen ondersteuning voor de Baidu-service (hoewel de registraties-API wel). 

### <a name="templates"></a>Sjablonen

Als u [sjablonen](notification-hubs-templates-cross-platform-push-messages.md)wilt gebruiken, bevat de installatie van het apparaat ook alle sjablonen die zijn gekoppeld aan dat apparaat in een JSON-indeling (Zie voor beeld hierboven). De naam van de sjabloon helpt bij het richten op verschillende sjablonen voor hetzelfde apparaat.

Elke sjabloon naam wordt toegewezen aan een sjabloon hoofdtekst en een optionele set tags. Daarnaast kan elk platform aanvullende sjabloon eigenschappen hebben. Voor Windows Store (met WNS) en Windows Phone 8 (met behulp van MPNS) kan een extra set kopteksten deel uitmaken van de sjabloon. In het geval van APNs kunt u een verloop eigenschap instellen op een constante of op een sjabloon expressie. Voor een volledig overzicht van de installatie-eigenschappen, [maakt of overschrijft u een installatie met rest](/rest/api/notificationhubs/create-overwrite-installation) onderwerp.

### <a name="secondary-tiles-for-windows-store-apps"></a>Secundaire tegels voor Windows Store-apps

Voor Windows Store-client toepassingen is het verzenden van meldingen naar secundaire tegels hetzelfde als het verzenden van berichten naar het primaire venster. Dit wordt ook ondersteund in-installaties. Secundaire tegels hebben een andere kanaal, die door de SDK van uw client toepassing transparant wordt afgehandeld.

De SecondaryTiles-woorden lijst gebruikt dezelfde TileId die wordt gebruikt om het SecondaryTiles-object in uw Windows Store-app te maken. Net als bij de primaire kanaal kunnen ChannelUris van secundaire tegels op elk moment worden gewijzigd. Om ervoor te zorgen dat de installaties in de notification hub worden bijgewerkt, moet het apparaat deze vernieuwen met de huidige ChannelUris van de secundaire tegels.

## <a name="registration-management-from-the-device"></a>Registratie beheer van het apparaat

Bij het beheren van apparaatregistratie van client-apps is de back-end alleen verantwoordelijk voor het verzenden van meldingen. Client-apps houden PNS up-to-date en registreren Tags. In de volgende afbeelding ziet u dit patroon.

![Registratie van het apparaat](./media/notification-hubs-registration-management/notification-hubs-registering-on-device.png)

Het apparaat haalt eerst de PNS-ingang op uit de PNS en registreert vervolgens rechtstreeks de notification hub. Nadat de registratie is voltooid, kan de back-end van de app een melding verzenden die als doel heeft. Zie [route ring en label expressies](notification-hubs-tags-segment-push-message.md)voor meer informatie over het verzenden van meldingen.

In dit geval gebruikt u alleen Luister rechten voor toegang tot uw notification hubs vanaf het apparaat. Zie [beveiliging](notification-hubs-push-notification-security.md)voor meer informatie.

De registratie van het apparaat is de eenvoudigste methode, maar heeft een aantal nadelen:

- Een client-app kan de labels alleen bijwerken wanneer de app actief is. Als een gebruiker bijvoorbeeld twee apparaten heeft die labels registreren die zijn gerelateerd aan sport teams, wanneer het eerste apparaat wordt geregistreerd voor een extra tag (bijvoorbeeld Seahawks), ontvangt het tweede apparaat geen meldingen over de Seahawks totdat de app op het tweede apparaat een tweede keer wordt uitgevoerd. Meer in het algemeen, wanneer labels worden beïnvloed door meerdere apparaten, is het beheren van tags van de back-end een wenselijke optie.
- Aangezien apps kunnen worden gekraakt, is extra aandacht vereist voor het beveiligen van de registratie voor specifieke tags, zoals wordt uitgelegd in de artikel [beveiliging](notification-hubs-push-notification-security.md).

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-an-installation"></a>Voorbeeld code voor registratie bij een notification hub van een apparaat met een installatie

Op dit moment wordt dit alleen ondersteund met behulp van de [Notification Hubs rest API](/rest/api/notificationhubs/create-overwrite-installation).

U kunt ook de PATCH-methode gebruiken met behulp van de [JSON-patch standaard](https://tools.ietf.org/html/rfc6902) voor het bijwerken van de installatie.

```csharp
class DeviceInstallation
{
    public string installationId { get; set; }
    public string platform { get; set; }
    public string pushChannel { get; set; }
    public string[] tags { get; set; }

    private async Task<HttpStatusCode> CreateOrUpdateInstallationAsync(DeviceInstallation deviceInstallation,
        string hubName, string listenConnectionString)
    {
        if (deviceInstallation.installationId == null)
            return HttpStatusCode.BadRequest;

        // Parse connection string (https://msdn.microsoft.com/library/azure/dn495627.aspx)
        ConnectionStringUtility connectionSaSUtil = new ConnectionStringUtility(listenConnectionString);
        string hubResource = "installations/" + deviceInstallation.installationId + "?";
        string apiVersion = "api-version=2015-04";

        // Determine the targetUri that we will sign
        string uri = connectionSaSUtil.Endpoint + hubName + "/" + hubResource + apiVersion;

        //=== Generate SaS Security Token for Authorization header ===
        // See https://msdn.microsoft.com/library/azure/dn495627.aspx
        string SasToken = connectionSaSUtil.getSaSToken(uri, 60);

        using (var httpClient = new HttpClient())
        {
            string json = JsonConvert.SerializeObject(deviceInstallation);

            httpClient.DefaultRequestHeaders.Add("Authorization", SasToken);

            var response = await httpClient.PutAsync(uri, new StringContent(json, System.Text.Encoding.UTF8, "application/json"));
            return response.StatusCode;
        }
    }

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    string installationId = null;
    var settings = ApplicationData.Current.LocalSettings.Values;

    // If we have not stored an installation ID in application data, create and store as application data.
    if (!settings.ContainsKey("__NHInstallationId"))
    {
        installationId = Guid.NewGuid().ToString();
        settings.Add("__NHInstallationId", installationId);
    }

    installationId = (string)settings["__NHInstallationId"];

    var deviceInstallation = new DeviceInstallation
    {
        installationId = installationId,
        platform = "wns",
        pushChannel = channel.Uri,
        //tags = tags.ToArray<string>()
    };

    var statusCode = await CreateOrUpdateInstallationAsync(deviceInstallation, 
                    "<HUBNAME>", "<SHARED LISTEN CONNECTION STRING>");

    if (statusCode != HttpStatusCode.Accepted)
    {
        var dialog = new MessageDialog(statusCode.ToString(), "Registration failed. Installation Id : " + installationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
    else
    {
        var dialog = new MessageDialog("Registration successful using installation Id : " + installationId);
        dialog.Commands.Add(new UICommand("OK"));
        await dialog.ShowAsync();
    }
}
```

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration"></a>Voorbeeld code voor registratie bij een notification hub van een apparaat met behulp van een registratie

Met deze methoden wordt een registratie gemaakt of bijgewerkt voor het apparaat waarop ze worden genoemd. Dit betekent dat u de gehele registratie moet overschrijven om de ingang of de tags bij te werken. Houd er rekening mee dat registraties tijdelijk zijn, dus u moet altijd beschikken over een betrouw bare Store met de huidige tags die een specifiek apparaat nodig heeft.

```csharp
// Initialize the Notification Hub
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

// The Device ID from the PNS
var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

// If you are registering from the client itself, then store this registration ID in device
// storage. Then when the app starts, you can check if a registration ID already exists or not before
// creating.
var settings = ApplicationData.Current.LocalSettings.Values;

// If we have not stored a registration ID in application data, store in application data.
if (!settings.ContainsKey("__NHRegistrationId"))
{
    // make sure there are no existing registrations for this push handle (used for iOS and Android)    
    string newRegistrationId = null;
    var registrations = await hub.GetRegistrationsByChannelAsync(pushChannel.Uri, 100);
    foreach (RegistrationDescription registration in registrations)
    {
        if (newRegistrationId == null)
        {
            newRegistrationId = registration.RegistrationId;
        }
        else
        {
            await hub.DeleteRegistrationAsync(registration);
        }
    }

    newRegistrationId = await hub.CreateRegistrationIdAsync();

    settings.Add("__NHRegistrationId", newRegistrationId);
}

string regId = (string)settings["__NHRegistrationId"];

RegistrationDescription registration = new WindowsRegistrationDescription(pushChannel.Uri);
registration.RegistrationId = regId;
registration.Tags = new HashSet<string>(YourTags);

try
{
    await hub.CreateOrUpdateRegistrationAsync(registration);
}
catch (Microsoft.WindowsAzure.Messaging.RegistrationGoneException e)
{
    settings.Remove("__NHRegistrationId");
}
```

## <a name="registration-management-from-a-backend"></a>Registratie beheer van een back-end

Als u registraties van de back-end wilt beheren, moet u extra code schrijven. De app van het apparaat moet elke keer dat de app wordt gestart (samen met tags en sjablonen) de bijgewerkte PNS-ingang naar de back-end geven en de back-end moet deze ingang bijwerken op de notification hub. In de volgende afbeelding ziet u dit ontwerp.

![Registratiebeheer](./media/notification-hubs-registration-management/notification-hubs-registering-on-backend.png)

De voor delen van het beheer van registraties van de back-end zijn de mogelijkheid om tags te wijzigen in registraties, zelfs wanneer de bijbehorende app op het apparaat inactief is en de client-app te verifiëren voordat u een tag toevoegt aan de registratie.

### <a name="example-code-to-register-with-a-notification-hub-from-a-backend-using-an-installation"></a>Voorbeeld code voor de registratie bij een notification hub van een back-end met een installatie

Het client apparaat ontvangt nog steeds de PNS-ingang en relevante installatie-eigenschappen als voorheen en roept een aangepaste API aan op de back-end die de registratie kan uitvoeren en Tags enz. De back-end kan gebruikmaken [van de SDK voor de notification hub voor back-end-bewerkingen](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

U kunt ook de PATCH-methode gebruiken met behulp van de [JSON-patch standaard](https://tools.ietf.org/html/rfc6902) voor het bijwerken van de installatie.

```csharp
// Initialize the Notification Hub
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(listenConnString, hubName);

// Custom API on the backend
public async Task<HttpResponseMessage> Put(DeviceInstallation deviceUpdate)
{

    Installation installation = new Installation();
    installation.InstallationId = deviceUpdate.InstallationId;
    installation.PushChannel = deviceUpdate.Handle;
    installation.Tags = deviceUpdate.Tags;

    switch (deviceUpdate.Platform)
    {
        case "mpns":
            installation.Platform = NotificationPlatform.Mpns;
            break;
        case "wns":
            installation.Platform = NotificationPlatform.Wns;
            break;
        case "apns":
            installation.Platform = NotificationPlatform.Apns;
            break;
        case "fcm":
            installation.Platform = NotificationPlatform.Fcm;
            break;
        default:
            throw new HttpResponseException(HttpStatusCode.BadRequest);
    }


    // In the backend we can control if a user is allowed to add tags
    //installation.Tags = new List<string>(deviceUpdate.Tags);
    //installation.Tags.Add("username:" + username);

    await hub.CreateOrUpdateInstallationAsync(installation);

    return Request.CreateResponse(HttpStatusCode.OK);
}
```

### <a name="example-code-to-register-with-a-notification-hub-from-a-device-using-a-registration-id"></a>Voorbeeld code voor registratie bij een notification hub van een apparaat met behulp van een registratie-ID

Vanuit de back-end van uw app kunt u elementaire ruwe bewerkingen uitvoeren op registraties. Bijvoorbeeld:

```csharp
var hub = NotificationHubClient.CreateClientFromConnectionString("{connectionString}", "hubName");

// create a registration description object of the correct type, e.g.
var reg = new WindowsRegistrationDescription(channelUri, tags);

// Create
await hub.CreateRegistrationAsync(reg);

// Get by ID
var r = await hub.GetRegistrationAsync<RegistrationDescription>("id");

// update
r.Tags.Add("myTag");

// update on hub
await hub.UpdateRegistrationAsync(r);

// delete
await hub.DeleteRegistrationAsync(r);
```

De back-end moet gelijktijdigheid tussen registratie-updates verwerken. Service Bus biedt optimistisch gelijktijdigheids beheer voor registratie beheer. Op het HTTP-niveau wordt dit geïmplementeerd met het gebruik van ETag op registratie beheer bewerkingen. Deze functie wordt transparant gebruikt door micro soft-Sdk's, waardoor een uitzonde ring wordt gegenereerd als een update wordt afgewezen voor de gelijktijdigheids oorzaken. De back-end van de app is verantwoordelijk voor het verwerken van deze uitzonde ringen en het opnieuw proberen van de update, indien nodig.
