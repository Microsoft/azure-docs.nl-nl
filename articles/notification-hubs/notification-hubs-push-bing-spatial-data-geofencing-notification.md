---
title: Pushmeldingen verzenden met Azure Notification Hubs en ruimtelijke Bing-gegevens | Microsoft Docs
description: In deze zelfstudie leert u hoe u locatiegebaseerde pushmeldingen kunt verzenden met Azure Notification Hubs en ruimtelijke Bing-gegevens.
services: notification-hubs
documentationcenter: windows
keywords: pushmeldingen,pushmeldingen
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: f41beea1-0d62-4418-9ffc-c9d70607a1b7
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 51ad23e67a77c28d0ad8a147168a0094f5de1796
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91577999"
---
# <a name="tutorial-send-location-based-push-notifications-with-notification-hubs-and-bing-spatial-data"></a>Zelfstudie: Locatiegebaseerde pushmeldingen verzenden met Notification Hubs en ruimtelijke Bing-gegevens

In deze zelfstudie leert u hoe u locatiegebaseerde pushmeldingen kunt verzenden met Azure Notification Hubs en ruimtelijke Bing-gegevens.

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * De gegevensbron instellen
> * De UWP-toepassing instellen
> * De back-end instellen
> * Pushmeldingen testen in de UWP-app (Universal Windows Platform)

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement**. Als u nog geen abonnement op Azure hebt, [maakt u een gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.
* [Visual Studio 2015 Update 1](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx) of hoger ([Community Edition](https://go.microsoft.com/fwlink/?LinkId=691978&clcid=0x409)).
* Meest recente versie van de [Azure SDK](https://azure.microsoft.com/downloads/).
* [Bing Maps Dev Center-account](https://www.bingmapsportal.com/) (u kunt gratis een account maken en dit koppelen aan uw Microsoft-account).

## <a name="set-up-the-data-source"></a>De gegevensbron instellen

1. Meld u aan bij [Bing Maps Dev Center](https://www.bingmapsportal.com/).
2. Selecteer **Data sources** in de bovenste navigatiebalk en selecteer vervolgens **Manage Data Sources**.

    ![Schermafbeelding van Bing Maps Dev Center op de pagina ‘Manage Data Sources’ met de optie ‘Upload data as a data source’ in rood gemarkeerd.](./media/notification-hubs-geofence/bing-maps-manage-data.png)
3. Als u nog geen gegevensbron hebt, ziet u een koppeling voor het maken van een gegevensbron. Selecteer **Upload data as a data source**. U kunt ook het menu **Data sources** > **Upload Data** gebruiken.

    ![Schermafbeelding van het dialoogvenster ‘Upload a data source’.](./media/notification-hubs-geofence/bing-maps-create-data.png)
4. Maak een bestand `NotificationHubsGeofence.pipe` op de harde schijf met de volgende inhoud: In deze zelfstudie gebruikt u een voorbeeldbestand op basis van een pipe waarmee een gebied van de kust van San Francisco wordt afgebakend:

    ```text
    Bing Spatial Data Services, 1.0, TestBoundaries
    EntityID(Edm.String,primaryKey)|Name(Edm.String)|Longitude(Edm.Double)|Latitude(Edm.Double)|Boundary(Edm.Geography)
    1|SanFranciscoPier|||POLYGON ((-122.389825 37.776598,-122.389438 37.773087,-122.381885 37.771849,-122.382186 37.777022,-122.389825 37.776598))
    ```

    Het pipe-bestand vertegenwoordigt deze entiteit:

    ![Schermopname van een kaart van de kust van San Francisco met een rode veelhoek die een gebied van de pieren markeert.](./media/notification-hubs-geofence/bing-maps-geofence.png)
5. Voer op de pagina **Upload a data source** de volgende acties uit:
   1. Selecteer **pipe** bij **Data format**.
   2. Bladeren naar en selecteer het bestand `NotificationHubGeofence.pipe` dat u in de vorige stap hebt gemaakt.
   3. Selecteer de knop **Upload**.

      > [!NOTE]
      > Mogelijk wordt u gevraagd om een nieuwe sleutel op te geven als **hoofdsleutel**; deze verschilt van de **querysleutel**. Maak een nieuwe sleutel via het dashboard en vernieuw de pagina voor het uploaden van gegevensbronnen.
6. Wanneer u het gegevensbestand hebt geüpload, moet u niet vergeten de gegevensbron te publiceren. Selecteer opnieuw **Data sources** -> **Manage Data Sources**.
7. Selecteer de gegevensbron in de lijst en kies **Publish** in de kolom **Actions**.

    ![Schermafbeelding van Bing Maps Dev Center op de pagina ‘Manage Data Sources’ met het tabblad ‘Geocoded Data’ geselecteerd en de optie ‘Publish’ in rood gemarkeerd.](./media/notification-hubs-geofence/publish-button.png)
8. Ga naar het tabblad **Published Data Sources** en controleer of uw gegevensbron in de lijst staat.

    ![Schermafbeelding van Bing Maps Dev Center op de pagina ‘Manage Data Sources’ met het tabblad ‘Published Data Sources’ geselecteerd.](./media/notification-hubs-geofence/bing-maps-published-data.png)
9. Selecteer **Bewerken**. U ziet (in een oogopslag) welke locaties u met de gegevens hebt geïntroduceerd.

    ![Schermafbeelding van de pagina ‘Edit entity data’ waarop een kaart van de westelijke Verenigde Staten en een magenta stip op de kust van San Francisco wordt weergegeven.](./media/notification-hubs-geofence/bing-maps-data-details.png)

    Op dit moment worden in de portal de grenzen van de geofence die u hebt gemaakt, niet weergegeven. U hoeft alleen nog te bevestigen dat de opgegeven locatie zich in de juiste omgeving bevindt.
10. U hebt nu voldaan aan alle vereisten voor de gegevensbron. Voor meer informatie over de aanvraag-URL voor de API-aanroep kiest u in het Bing Kaarten-ontwikkelaarscentrum de optie **Gegevensbronnen** en selecteert u **Gegevensbroninformatie**.

    ![Schermafbeelding van Bing Maps Dev Center op de pagina ‘Data source information’.](./media/notification-hubs-geofence/bing-maps-data-info.png)

    De waarde bij **Query URL** is het eindpunt waarop u query’s kunt uitvoeren om te controleren of het apparaat zich momenteel binnen de grenzen van een locatie bevindt of niet. Als u deze controle wilt uitvoeren, moet u een GET-aanroep uitvoeren voor de query-URL, waarbij u de volgende parameters toevoegt:

    ```text
    ?spatialFilter=intersects(%27POINT%20LONGITUDE%20LATITUDE)%27)&$format=json&key=QUERY_KEY
    ```

    Bing Kaarten voert automatisch de berekeningen uit om te zien of het apparaat zich binnen de geofence bevindt. Wanneer u de aanvraag hebt uitgevoerd via een browser (of cURL), ontvangt u een standaard JSON-antwoord:

    ![Schermafbeelding van het standaard JSON-antwoord.](./media/notification-hubs-geofence/bing-maps-json.png)

    Dit antwoord ontvangt u alleen als het punt zich binnen de opgegeven grenzen bevindt. Als dat niet het geval is, ontvangt u een lege bucket **results**:

    ![Schermafbeelding van een JSON-antwoord met een lege resultaten-bucket.](./media/notification-hubs-geofence/bing-maps-nores.png)

## <a name="set-up-the-uwp-application"></a>De UWP-toepassing instellen

1. Open in Visual Studio een nieuw project van het type **Lege app (Universeel Windows)** .

    ![Schermafbeelding van een Visual Studio-dialoogvenster ‘New Project’ met de Visual C#-optie ‘Blank App (Universal Windows)’ gemarkeerd.](./media/notification-hubs-geofence/notification-hubs-create-blank-app.png)

    Wanneer het maken van het project is voltooid, beschikt u over de basis voor de app. U gaat nu alles instellen voor de infrastructuur met geofencing. Omdat u Bing-services gaat gebruiken voor deze oplossing, is er een openbaar REST-API-eindpunt waarmee query’s kunnen worden uitgevoerd op specifieke locatiekaders:

    ```text
    http://spatial.virtualearth.net/REST/v1/data/
    ```
    Geef de volgende parameters op om hiermee aan de slag te gaan:

   * De **gegevensbron-id** en de **gegevensbronnaam**. In de Bing Kaarten-API bevatten gegevensbronnen verschillende gerangschikte metagegevens, zoals locaties en bedrijfsuren.  
   * **Naam van de entiteit**: de entiteit die u als verwijzingspunt wilt gebruiken voor de melding.
   * **Bing Maps API Key**: dit is de sleutel die u eerder hebt verkregen tijdens het maken van het Bing Dev Center-account.

     Nu de gegevensbron klaar is, kunt u aan de slag met de UWP-toepassing.
2. Schakel locatieservices in voor uw toepassing. Open het bestand `Package.appxmanifest` in **Solution Explorer**.

    ![Schermafbeelding van Solution Explorer met het bestand Package.appxmanifest gemarkeerd.](./media/notification-hubs-geofence/vs-package-manifest.png)
3. Ga in het venster met eigenschappen van het pakket naar het tabblad **Capabilities** en selecteer **Location**.

    ![Schermafbeelding van het dialoogvenster ‘Package Properties’ dat het tabblad ‘Capabilities’ weergeeft met de optie ‘Location’ gemarkeerd.](./media/notification-hubs-geofence/vs-package-location.png)
4. Maak een nieuwe map in de oplossing met de naam `Core`. Voeg er een nieuw bestand aan toe met de naam `LocationHelper.cs`:

    ![Schermafbeelding van Solution Explorer met de map ‘Core’ gemarkeerd.](./media/notification-hubs-geofence/vs-location-helper.png)

    De klasse `LocationHelper` bevat code voor het opvragen van de locatie van de gebruiker via de systeem-API:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Windows.Devices.Geolocation;

    namespace NotificationHubs.Geofence.Core
    {
        public class LocationHelper
        {
            private static readonly uint AppDesiredAccuracyInMeters = 10;

            public async static Task<Geoposition> GetCurrentLocation()
            {
                var accessStatus = await Geolocator.RequestAccessAsync();
                switch (accessStatus)
                {
                    case GeolocationAccessStatus.Allowed:
                        {
                            Geolocator geolocator = new Geolocator { DesiredAccuracyInMeters = AppDesiredAccuracyInMeters };

                            return await geolocator.GetGeopositionAsync();
                        }
                    default:
                        {
                            return null;
                        }
                }
            }

        }
    }
    ```

    Zie [Get the user's location](/windows/uwp/maps-and-location/get-location) (Locatie van de gebruiker opvragen) voor meer informatie over het opvragen van de locatie van de gebruiker in UWP-apps.
5. Als u wilt controleren of het ophalen van de locatie werkt, opent u de codezijde van de hoofdpagina (`MainPage.xaml.cs`). Maak een nieuwe gebeurtenis-handler voor de gebeurtenis `Loaded` in de constructor `MainPage`.

    ```csharp
    public MainPage()
    {
        this.InitializeComponent();
        this.Loaded += MainPage_Loaded;
    }
    ```

    U implementeert de gebeurtenis-handler als volgt:

    ```csharp
    private async void MainPage_Loaded(object sender, RoutedEventArgs e)
    {
        var location = await LocationHelper.GetCurrentLocation();

        if (location != null)
        {
            Debug.WriteLine(string.Concat(location.Coordinate.Longitude,
                " ", location.Coordinate.Latitude));
        }
    }
    ```
6. Voer de toepassing uit en geef deze toegang tot uw locatie.

    ![Schermafbeelding van het dialoogvenster ‘Let Notification Hubs Geo Fence access your location’.](./media/notification-hubs-geofence/notification-hubs-location-access.png)
7. Wanneer de toepassing is gestart, ziet u de coördinaten in het venster **Uitvoer**:

    ![Schermafbeelding van het uitvoervenster dat de coördinaten weergeeft.](./media/notification-hubs-geofence/notification-hubs-location-output.png)

    U weet nu dat de locatie kan worden opgehaald. De gebeurtenis-handler Loaded kunt u daarom desgewenst verwijderen, aangezien u deze toch niet meer nodig hebt.
8. De volgende stap is het vastleggen van locatiewijzigingen. Voeg in de klasse `LocationHelper` de gebeurtenis-handler voor `PositionChanged` toe:

    ```csharp
    geolocator.PositionChanged += Geolocator_PositionChanged;
    ```

    De implementatie toont de locatiecoördinaten in het venster **Output**:

    ```csharp
    private static async void Geolocator_PositionChanged(Geolocator sender, PositionChangedEventArgs args)
    {
        await CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(CoreDispatcherPriority.Normal, () =>
        {
            Debug.WriteLine(string.Concat(args.Position.Coordinate.Longitude, " ", args.Position.Coordinate.Latitude));
        });
    }
    ```

## <a name="set-up-the-backend"></a>De back-end instellen

1. Download het [.NET-back-endvoorbeeld via GitHub](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/NotifyUsers).
2. Wanneer het downloaden is voltooid, opent u de map `NotifyUsers` en opent u vervolgens het bestand `NotifyUsers.sln` in Visual Studio.
3. Stel het project `AppBackend` in als **StartUp Project** open het.

    ![Schermafbeelding van het snelmenu van ‘Solution’ met de optie ‘Set as StartUp Project’ gemarkeerd.](./media/notification-hubs-geofence/vs-startup-project.png)

    Het project is al geconfigureerd voor het versturen van pushmeldingen naar doelapparaten. U hoeft dus nog maar twee dingen te doen: de juiste verbindingsreeks opgeven voor de Notification Hub en grensidentificatie toevoegen zodat de melding alleen wordt verzonden wanneer de gebruiker zich binnen de geofence bevindt.

4. Als u de verbindingsreeks wilt configureren, opent u `Notifications.cs` in de map `Models`. De functie `NotificationHubClient.CreateClientFromConnectionString` bevat de informatie over de Notification Hub die u ook kunt inzien in [Azure Portal](https://portal.azure.com) (op de pagina **Toegangsbeleid** in **Instellingen**). Sla het bijgewerkte configuratiebestand op.
5. Maak een model voor het Bing Kaarten-API-resultaat. Dat kunt u het eenvoudigst doen door de map `Models` te openen en op **Add** > **Class** te klikken. Noem deze `GeofenceBoundary.cs`. Vervolgens kopieert u de JSON uit het API-antwoord dat u in de eerste sectie hebt verkregen. Gebruik in Visual Studio **Edit** > **Paste Special** > **Paste JSON as Classes**.

    Zo zorgt u ervoor dat het object op de juiste manier wordt gedeserialiseerd. De resulterende klasseset moet er ongeveer als volgt uitzien:

    ```csharp
    namespace AppBackend.Models
    {
        public class Rootobject
        {
            public D d { get; set; }
        }

        public class D
        {
            public string __copyright { get; set; }
            public Result[] results { get; set; }
        }

        public class Result
        {
            public __Metadata __metadata { get; set; }
            public string EntityID { get; set; }
            public string Name { get; set; }
            public float Longitude { get; set; }
            public float Latitude { get; set; }
            public string Boundary { get; set; }
            public string Confidence { get; set; }
            public string Locality { get; set; }
            public string AddressLine { get; set; }
            public string AdminDistrict { get; set; }
            public string CountryRegion { get; set; }
            public string PostalCode { get; set; }
        }

        public class __Metadata
        {
            public string uri { get; set; }
        }
    }
    ```
6. Open vervolgens `Controllers` > `NotificationsController.cs`. Werk de POST-aanroep bij om rekening te houden met de lengte- en breedtegraad van de bestemming. Dit doet u door de tekenreeksen `latitude` en `longitude` toe te voegen aan de functiehandtekening.

    ```csharp
    public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag, string latitude, string longitude)
    ```
7. Maak een nieuwe klasse in het project met de naam `ApiHelper.cs`. Deze klasse gebruikt u om verbinding te maken met Bing om te controleren op grensoverschrijdingen. Implementeer de functie `IsPointWithinBounds` zoals wordt weergegeven in de volgende code:

    ```csharp
    public class ApiHelper
    {
        public static readonly string ApiEndpoint = "{YOUR_QUERY_ENDPOINT}?spatialFilter=intersects(%27POINT%20({0}%20{1})%27)&$format=json&key={2}";
        public static readonly string ApiKey = "{YOUR_API_KEY}";

        public static bool IsPointWithinBounds(string longitude,string latitude)
        {
            var json = new WebClient().DownloadString(string.Format(ApiEndpoint, longitude, latitude, ApiKey));
            var result = JsonConvert.DeserializeObject<Rootobject>(json);
            if (result.d.results != null && result.d.results.Count() > 0)
            {
                return true;
            }
            else
            {
                return false;
            }
        }
    }
    ```

    > [!IMPORTANT]
    > Zorg ervoor dat u het API-eindpunt vervangt door de query-URL die u eerder hebt verkregen via het Bing Dev Center (dit geldt ook voor de API-sleutel).

    Als er resultaten zijn op basis van de query, bevindt het opgegeven punt zich binnen de grenzen van de geofence. In dat geval wordt `true` geretourneerd. Als er geen resultaten zijn, wordt via Bing een melding weergegeven dat het punt zich buiten het kader bevindt, waardoor `false` wordt geretourneerd.
8. Voeg in `NotificationsController.cs` een controle toe vóór de switch-instructie:

    ```csharp
    if (ApiHelper.IsPointWithinBounds(longitude, latitude))
    {
        switch (pns.ToLower())
        {
            case "wns":
                //// Windows 8.1 / Windows Phone 8.1
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                // Windows 10 specific Action Center support
                toast = @"<toast><visual><binding template=""ToastGeneric""><text id=""1"">" +
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

                break;
        }
    }
    ```

## <a name="test-push-notifications-in-the-uwp-app"></a>Pushmeldingen testen in de UWP-app

1. In de UWP-app moet u nu meldingen kunnen testen. Maak in de klasse `LocationHelper` een nieuwe functie (`SendLocationToBackend`):

    ```csharp
    public static async Task SendLocationToBackend(string pns, string userTag, string message, string latitude, string longitude)
    {
        var POST_URL = "http://localhost:8741/api/notifications?pns=" +
            pns + "&to_tag=" + userTag + "&latitude=" + latitude + "&longitude=" + longitude;

        using (var httpClient = new HttpClient())
        {
            try
            {
                await httpClient.PostAsync(POST_URL, new StringContent("\"" + message + "\"",
                    System.Text.Encoding.UTF8, "application/json"));
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
            }
        }
    }
    ```

    > [!NOTE]
    > Stel `POST_URL` in op de locatie van de geïmplementeerde webtoepassing. U kunt de toepassing nu lokaal uitvoeren. Als u echter een openbare versie wilt implementeren, moet u deze hosten via een externe provider.
2. Registreer de UWP-app voor pushmeldingen. In Visual Studio kiest u **Project** > **Store** > **Associate app with the store**.

    ![Schermafbeelding van het snelmenu van ‘Solution’ met de opties ‘Store’ en ‘Associate App with the Store’ gemarkeerd.](./media/notification-hubs-geofence/vs-associate-with-store.png)
3. Wanneer u zich hebt aangemeld bij uw ontwikkelaarsaccount, selecteert u een bestaande app of maakt u een nieuwe app. Koppel het pakket daaraan.
4. Ga naar het Dev Center en open de app die u hebt gemaakt. Kies **Services** > **Push Notifications** > **Live Services site**.

    ![Schermafbeelding van Windows Dev Center dat de pagina ‘Push notifications’ weergeeft met de site ‘Live Services’ gemarkeerd.](./media/notification-hubs-geofence/ms-live-services.png)
5. Op de site bekijkt u het **toepassingsgeheim** en de **pakket-SID**. U hebt deze allebei nodig in Azure Portal. Open de Notification Hub, kies **Instellingen** > **Notification Services** > **Windows (WNS)** en voer de gegevens in de vereiste velden in.

    ![Schermafbeelding van de pagina ‘Settings’ met de opties ‘Notification Services’ en ‘Windows (WNS)’ gemarkeerd en de waarden voor ‘Package SID’ en ‘Security Key’ ingevuld.](./media/notification-hubs-geofence/notification-hubs-wns.png)
6. Kies **Opslaan**.
7. Open **Verwijzingen** in **Solution Explorer** en selecteer **NuGet-pakketten beheren**. Voeg een verwijzing naar de **beheerde Microsoft Azure Service Bus-bibliotheek** toe. Zoek hiervoor naar `WindowsAzure.Messaging.Managed` en voeg deze vermelding toe aan uw project.

    ![Schermafbeelding van het dialoogvenster ‘Manage Nuget Packages’ met het pakket WindowsAzure.Messaging.Managed gemarkeerd.](./media/notification-hubs-geofence/vs-nuget.png)
8. Voor testdoeleinden maakt u de gebeurtenis-handler voor `MainPage_Loaded` opnieuw. Voeg er nu dit codefragment aan toe:

    ```csharp
    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    var hub = new NotificationHub("HUB_NAME", "HUB_LISTEN_CONNECTION_STRING");
    var result = await hub.RegisterNativeAsync(channel.Uri);

    // Displays the registration ID so you know it was successful
    if (result.RegistrationId != null)
    {
        Debug.WriteLine("Reg successful.");
    }
    ```

    Hiermee wordt de app geregistreerd bij de Notification Hub. U bent er nu klaar voor!
9. Voeg in `LocationHelper`, binnen de handler `Geolocator_PositionChanged`, een testcodefragment toe waardoor de locatie geforceerd in de geofence wordt geplaatst:

    ```csharp
    await LocationHelper.SendLocationToBackend("wns", "TEST_USER", "TEST", "37.7746", "-122.3858");
    ```

10. Omdat u niet de echte coördinaten doorgeeft (die misschien niet binnen de grenzen liggen op dat moment) en omdat u vooraf gedefinieerde testwaarden gebruikt, wordt tijdens het bijwerken een melding weergegeven:

    ![Schermopname van een Windows-desktop waarop het TEST-bericht wordt weergegeven.](./media/notification-hubs-geofence/notification-hubs-test-notification.png)

## <a name="next-steps"></a>Volgende stappen

Er zijn een aantal stappen die u mogelijk moet uitvoeren om ervoor te zorgen dat de oplossing klaar is voor productie.

1. U moet er ten eerste voor zorgen dat de geofences dynamisch zijn. Hiervoor moet u extra bewerkingen uitvoeren met de Bing-API, zodat u nieuwe grenzen kunt uploaden in de bestaande gegevensbron. Zie voor meer informatie de Engelstalige documentatie over de [Bing Spatial Data Services-API](/bingmaps/spatial-data-services/).
2. Ten tweede is het raadzaam de geofences met [tags](notification-hubs-tags-segment-push-message.md) als doel te definiëren omdat u er zeker van wilt zijn dat er aan de juiste deelnemers wordt geleverd.

In de oplossing in deze zelfstudie wordt een scenario beschreven waarin u mogelijk veel verschillende doelplatforms hebt; daarom hebben we geofencing niet beperkt tot systeemspecifieke mogelijkheden. Los daarvan is het wel zo dat het universele Windows-platform [direct na aanschaf](/windows/uwp/maps-and-location/set-up-a-geofence) mogelijkheden biedt voor het detecteren van geofences.
