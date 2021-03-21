---
title: bestand opnemen
description: include-bestand met code voor het maken van een back-end ASP .NET WebAPI-project.
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 09/11/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 6f490b6f25112ed8a10bbd865070bd07ea3ee84f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96016937"
---
## <a name="create-the-webapi-project"></a>Het WebAPI-project maken

In de volgende secties wordt het maken van een nieuwe ASP.NET WebAPI-back-end besproken. Dit proces heeft drie hoofddoelen:

- **Clients verifiëren**: er wordt een berichtenhandler toegevoegd voor het verifiëren van aanvragen van clients en het koppelen van de gebruiker aan de aanvraag.
- **Registreren voor meldingen met behulp van de WebAPI-back-end**: u voegt een controller toe voor het afhandelen van nieuwe registraties voor een clientapparaat voor het ontvangen van meldingen. De naam van de geverifieerde gebruiker wordt automatisch aan de registratie toegevoegd als een [tag](../articles/notification-hubs/notification-hubs-tags-segment-push-message.md).
- **Meldingen verzenden naar clients**: u voegt een controller toe om gebruikers een manier te bieden voor het activeren van een beveiligde push naar apparaten en clients die zijn gekoppeld aan de tag.

Maak de nieuwe ASP.NET WebAPI-back-end door de volgende acties uit te voeren:

> [!IMPORTANT]
> Als u Visual Studio 2015 of eerder gebruikt, moet u voordat u deze zelfstudie begint controleren of u de meest recente versie van NuGet Package Manager voor Visual Studio hebt geïnstalleerd.
>
>Start Visual Studio om dit te controleren. Selecteer **Extensies en updates** in het menu **Extra**. Zoek naar **NuGet Package Manager** in uw versie van Visual Studio en controleer of u de meest recente versie hebt. Als uw versie niet de nieuwste versie is, verwijdert u deze en installeert u NuGet Package Manager.

![Schermopname van het dialoogvenster Extensies en updates met NuGet-pakketbeheer voor het Visual Studio-pakket gemarkeerd.][B4]

> [!NOTE]
> Controleer of u de Visual Studio [Azure SDK](https://azure.microsoft.com/downloads/) voor website-implementatie hebt geïnstalleerd.

1. Start Visual Studio of Visual Studio Express.

2. Selecteer **Server Explorer** en meld u aan bij uw Azure-account. U moet zijn aangemeld om de websiteresources voor uw account te maken.

3. Klik in Visual Studio met de rechtermuisknop op de Visual Studio-oplossing, houd de muisaanwijzer op **Toevoegen** en klik op **Nieuw item**.
4. Vouw **Visual C#** uit, selecteer **Web**, en klik op **SP.NET-webtoepassing**.

5. Typ **AppBackend** in het vak **Naam** en selecteer **OK**.

    ![Het venster Nieuw project][B1]

6. Selecteer in het venster **Nieuw ASP.NET-project** het selectievakje **Web-API** en selecteer vervolgens **OK**.

    ![Het venster Nieuw ASP.NET-project][B2]

7. Selecteer een abonnement in het venster **Microsoft Azure Web App configureren** en voer in de lijst **App Service-plan** een van de volgende acties uit:

    * Selecteer een App Service-plan dat u al hebt gemaakt.
    * Selecteer **Een nieuw App Service-plan maken**, en maak er vervolgens een.

   U hebt geen database nodig voor deze zelfstudie. Nadat u uw App Service-plan hebt geselecteerd, selecteert u **OK** om het project te maken.

    ![Het venster Microsoft Azure Web App configureren][B5]

    Als u deze pagina voor het configureren van het App Service-plan niet ziet, gaat u door met de zelfstudie. U kunt het configureren en de app later publiceren. 

## <a name="authenticate-clients-to-the-webapi-backend"></a>Clients verifiëren bij de WebAPI-back-end

In deze sectie maakt u een nieuwe berichtenhandlerklasse met de naam **AuthenticationTestHandler** voor de nieuwe back-end. Deze klasse wordt afgeleid van [DelegatingHandler](/previous-versions/visualstudio/hh193679(v=vs.118)) en toegevoegd als een berichtenhandler zodat deze alle aanvragen die de back-end ontvangt, kan verwerken.

1. Klik in Solution Explorer met de rechtermuisknop op het project **AppBackend**, selecteer **Toevoegen** en selecteer vervolgens **Klasse**.
2. Geef de nieuwe klasse de naam **AuthenticationTestHandler.cs** en selecteer **Toevoegen** om de klasse te genereren. Deze klasse verifieert gebruikers met behulp van *Basisverificatie* om het eenvoudig te houden. Uw app kan een willekeurig verificatieschema gebruiken.
3. Voeg in AuthenticationTestHandler.cs de volgende `using`-instructies toe:

    ```csharp
    using System.Net.Http;
    using System.Threading;
    using System.Security.Principal;
    using System.Net;
    using System.Text;
    using System.Threading.Tasks;
    ```

4. Vervang in AuthenticationTestHandler.cs de `AuthenticationTestHandler`-klassedefinitie door de volgende code:

    Deze handler verifieert de aanvraag wanneer de volgende drie voorwaarden waar zijn:

   * De aanvraag bevat een *autorisatie* header.
   * De aanvraag maakt gebruik van *basis* verificatie.
   * De gebruikersnaamtekenreeks en de wachtwoordtekenreeks zijn dezelfde tekenreeks.

   Anders wordt de aanvraag geweigerd. Deze verificatie is geen bestaande benadering op verificatie en autorisatie. Dit is slechts een eenvoudig voorbeeld voor deze zelfstudie.

   Als het aanvraagbericht is geverifieerd en geautoriseerd door `AuthenticationTestHandler`, wordt de basisverificatiegebruiker toegevoegd aan de huidige aanvraag in [HttpContext](/dotnet/api/system.web.httpcontext.current). Gebruikersgegevens in HttpContext worden later gebruikt door een andere controller (RegisterController) om een [tag](/previous-versions/azure/azure-services/dn530749(v=azure.100)) toe te voegen aan de aanvraag voor meldingen van registraties.

    ```csharp
    public class AuthenticationTestHandler : DelegatingHandler
    {
        protected override Task<HttpResponseMessage> SendAsync(
        HttpRequestMessage request, CancellationToken cancellationToken)
        {
            var authorizationHeader = request.Headers.GetValues("Authorization").First();

            if (authorizationHeader != null && authorizationHeader
                .StartsWith("Basic ", StringComparison.InvariantCultureIgnoreCase))
            {
                string authorizationUserAndPwdBase64 =
                    authorizationHeader.Substring("Basic ".Length);
                string authorizationUserAndPwd = Encoding.Default
                    .GetString(Convert.FromBase64String(authorizationUserAndPwdBase64));
                string user = authorizationUserAndPwd.Split(':')[0];
                string password = authorizationUserAndPwd.Split(':')[1];

                if (VerifyUserAndPwd(user, password))
                {
                    // Attach the new principal object to the current HttpContext object
                    HttpContext.Current.User =
                        new GenericPrincipal(new GenericIdentity(user), new string[0]);
                    System.Threading.Thread.CurrentPrincipal =
                        System.Web.HttpContext.Current.User;
                }
                else return Unauthorized();
            }
            else return Unauthorized();

            return base.SendAsync(request, cancellationToken);
        }

        private bool VerifyUserAndPwd(string user, string password)
        {
            // This is not a real authentication scheme.
            return user == password;
        }

        private Task<HttpResponseMessage> Unauthorized()
        {
            var response = new HttpResponseMessage(HttpStatusCode.Forbidden);
            var tsc = new TaskCompletionSource<HttpResponseMessage>();
            tsc.SetResult(response);
            return tsc.Task;
        }
    }
    ```

    > [!NOTE]
    > Opmerking over beveiliging: de `AuthenticationTestHandler`-klasse biedt geen echte verificatie. Het wordt alleen gebruikt om basisverificatie na te bootsen en is niet beveiligd. U moet een veilig verificatiemechanisme implementeren in uw productietoepassingen en -services.
5. Voeg de volgende code toe aan het einde van de `Register`-methode in de klasse **App_Start/WebApiConfig.cs** om de berichtenhandler te registreren:

    ```csharp
    config.MessageHandlers.Add(new AuthenticationTestHandler());
    ```
6. Sla uw wijzigingen op.

## <a name="register-for-notifications-by-using-the-webapi-backend"></a>Registreren voor meldingen met behulp van de WebAPI-back-end

In deze sectie voegen we een nieuwe controller toe aan de WebAPI-back-end om aanvragen voor het registreren van een gebruiker en een apparaat voor meldingen af te handelen met behulp van de clientbibliotheek voor notification hubs. De controller voegt een gebruikerstag toe voor de gebruiker die door `AuthenticationTestHandler` is geverifieerd en gekoppeld aan HttpContext. De tag heeft de indeling van de tekenreeks, `"username:<actual username>"`.

1. Klik in Solution Explorer met de rechtermuisknop op het project **AppBackend** en selecteer vervolgens **NuGet-pakketten beheren**.

2. Selecteer in het linkerdeelvenster **Online** en typ **Microsoft.Azure.NotificationHubs** in het vak **Zoeken**.

3. Selecteer **Microsoft Azure Notification Hubs** in de lijst met resultaten en selecteer vervolgens **Installeren**. Voltooi de installatie en sluit vervolgens het venster Nuget Package Manager.

    Met deze actie wordt een verwijzing toegevoegd aan de Azure Notification Hubs-SDK met het [Microsoft.Azure.Notification Hubs NuGet-pakket](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

4. Maak een nieuw klassebestand waarmee de verbinding met de notification hub die wordt gebruikt voor het verzenden van meldingen. Klik in Solution Explorer met de rechtermuisknop op de map **Modellen** en selecteer achtereenvolgens **Toevoegen** en **Klasse**. Noem de nieuwe klasse **Notifications.cs** en selecteer vervolgens **Toevoegen** om de klasse te genereren.

    ![Het venster Nieuw Item toevoegen][B6]

5. Voeg in Notifications.cs de volgende `using`-instructie toe aan het begin van het bestand:

    ```csharp
    using Microsoft.Azure.NotificationHubs;
    ```

6. Vervang de `Notifications`-klassedefinitie door de volgende code en vervang de twee tijdelijke aanduidingen door de verbindingsreeks (met volledige toegang) voor uw notification hub en de naam van de hub (beschikbaar in [Azure Portal](https://portal.azure.com)):

    ```csharp
    public class Notifications
    {
        public static Notifications Instance = new Notifications();

        public NotificationHubClient Hub { get; set; }

        private Notifications() {
            Hub = NotificationHubClient.CreateClientFromConnectionString("<your hub's DefaultFullSharedAccessSignature>",
                                                                            "<hub name>");
        }
    }
    ```
    > [!IMPORTANT]
    > Voer de **naam** en de **DefaultListenSharedAccessSignature** van uw hub in voordat u verdergaat. 
    
7. Maak vervolgens een nieuwe controller met de naam **RegisterController**. Klik in Solution Explorer met de rechtermuisknop op de map **Controllers** en selecteer achtereenvolgens **Toevoegen** en **Controller**.

8. Selecteer **Web API 2 Controller - leeg** en selecteer vervolgens **Toevoegen**.

    ![Het venster Scaffold toevoegen][B7]

9. Typ in het vak **Controllernaam****RegisterController** als naam voor de nieuwe klasse en selecteer vervolgens **Toevoegen**.

    ![Het venster Controller toevoegen][B8]

10. Voeg in RegisterController.cs de volgende `using`-instructies toe:

    ```csharp
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Azure.NotificationHubs.Messaging;
    using AppBackend.Models;
    using System.Threading.Tasks;
    using System.Web;
    ```
11. Voeg de volgende code in de `RegisterController`-klassedefinitie. In deze code voegt u een gebruikerstag toe voor de gebruiker die is gekoppeld aan HttpContext. De gebruiker is geverifieerd en gekoppeld aan HttpContext door het berichtenfilter dat u hebt toegevoegd, `AuthenticationTestHandler`. U kunt ook optionele controles toevoegen om te controleren of de gebruiker rechten heeft voor het registreren voor de aangevraagde tags.

    ```csharp
    private NotificationHubClient hub;

    public RegisterController()
    {
        hub = Notifications.Instance.Hub;
    }

    public class DeviceRegistration
    {
        public string Platform { get; set; }
        public string Handle { get; set; }
        public string[] Tags { get; set; }
    }

    // POST api/register
    // This creates a registration id
    public async Task<string> Post(string handle = null)
    {
        string newRegistrationId = null;

        // make sure there are no existing registrations for this push handle (used for iOS and Android)
        if (handle != null)
        {
            var registrations = await hub.GetRegistrationsByChannelAsync(handle, 100);

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
        }

        if (newRegistrationId == null) 
            newRegistrationId = await hub.CreateRegistrationIdAsync();

        return newRegistrationId;
    }

    // PUT api/register/5
    // This creates or updates a registration (with provided channelURI) at the specified id
    public async Task<HttpResponseMessage> Put(string id, DeviceRegistration deviceUpdate)
    {
        RegistrationDescription registration = null;
        switch (deviceUpdate.Platform)
        {
            case "mpns":
                registration = new MpnsRegistrationDescription(deviceUpdate.Handle);
                break;
            case "wns":
                registration = new WindowsRegistrationDescription(deviceUpdate.Handle);
                break;
            case "apns":
                registration = new AppleRegistrationDescription(deviceUpdate.Handle);
                break;
            case "fcm":
                registration = new FcmRegistrationDescription(deviceUpdate.Handle);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }

        registration.RegistrationId = id;
        var username = HttpContext.Current.User.Identity.Name;

        // add check if user is allowed to add these tags
        registration.Tags = new HashSet<string>(deviceUpdate.Tags);
        registration.Tags.Add("username:" + username);

        try
        {
            await hub.CreateOrUpdateRegistrationAsync(registration);
        }
        catch (MessagingException e)
        {
            ReturnGoneIfHubResponseIsGone(e);
        }

        return Request.CreateResponse(HttpStatusCode.OK);
    }

    // DELETE api/register/5
    public async Task<HttpResponseMessage> Delete(string id)
    {
        await hub.DeleteRegistrationAsync(id);
        return Request.CreateResponse(HttpStatusCode.OK);
    }

    private static void ReturnGoneIfHubResponseIsGone(MessagingException e)
    {
        var webex = e.InnerException as WebException;
        if (webex.Status == WebExceptionStatus.ProtocolError)
        {
            var response = (HttpWebResponse)webex.Response;
            if (response.StatusCode == HttpStatusCode.Gone)
                throw new HttpRequestException(HttpStatusCode.Gone.ToString());
        }
    }
    ```
12. Sla uw wijzigingen op.

## <a name="send-notifications-from-the-webapi-backend"></a>Meldingen verzenden vanuit de WebAPI-back-end

In deze sectie voegt u een nieuwe domeincontroller toe die clientapparaten een manier biedt om een melding te verzenden. De melding is gebaseerd op de gebruikersnaamtag die gebruikmaakt van de Azure Notification Hubs .NET-bibliotheek in de ASP.NET WebAPI-back-end.

1. Maak nog een nieuwe controller met de naam **NotificationsController** op dezelfde manier als u **RegisterController** in de vorige sectie hebt gemaakt.

2. Voeg in NotificationsController.cs de volgende `using`-instructies toe:

    ```csharp
    using AppBackend.Models;
    using System.Threading.Tasks;
    using System.Web;
    ```
3. Voeg de volgende methode toe aan de klasse **NotificationsController**:

    Met deze code wordt een meldingstype verzonden die is gebaseerd op de `pns`-parameter (Platform Notification Service, PNS). De waarde van `to_tag` wordt gebruikt om de tag *gebruikersnaam* in te stellen in het bericht. Deze tag moet overeenkomen met een gebruikersnaamtag van een actieve notification hub-registratie. Het meldingsbericht wordt opgehaald uit de hoofdtekst van de POST-aanvraag en geformatteerd voor de doel-PNS.

    Afhankelijk van de PNS die uw ondersteunde apparaten gebruiken om meldingen te ontvangen, worden de meldingen met verschillende indelingen ondersteund. Bijvoorbeeld op Windows-apparaten kunt u een [pop-upmelding met WNS](/uwp/schemas/tiles/toastschema/schema-root) gebruiken die niet rechtstreeks wordt ondersteund door een andere PNS. In een dergelijk geval moet uw back-end de melding in een ondersteunde melding indelen voor de PNS van apparaten die u wilt ondersteunen. Gebruik vervolgens de juiste API voor verzending in de [NotificationHubClient-klasse](/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient).

    ```csharp
    public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag)
    {
        var user = HttpContext.Current.User.Identity.Name;
        string[] userTag = new string[2];
        userTag[0] = "username:" + to_tag;
        userTag[1] = "from:" + user;

        Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;
        HttpStatusCode ret = HttpStatusCode.InternalServerError;

        switch (pns.ToLower())
        {
            case "wns":
                // Windows 8.1 / Windows Phone 8.1
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" + 
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
                break;
            case "apns":
                // iOS
                var alert = "{\"aps\":{\"alert\":\"" + "From " + user + ": " + message + "\"}}";
                outcome = await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(alert, userTag);
                break;
            case "fcm":
                // Android
                var notif = "{ \"data\" : {\"message\":\"" + "From " + user + ": " + message + "\"}}";
                outcome = await Notifications.Instance.Hub.SendFcmNativeNotificationAsync(notif, userTag);
                break;
        }

        if (outcome != null)
        {
            if (!((outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Abandoned) ||
                (outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Unknown)))
            {
                ret = HttpStatusCode.OK;
            }
        }

        return Request.CreateResponse(ret);
    }
    ```
4. Selecteer de toets **F5** om de toepassing uit te voeren en om de juistheid van uw werk tot nu toe te controleren. De app opent een webbrowser en deze wordt weergegeven met de startpagina van ASP.NET.

## <a name="publish-the-new-webapi-backend"></a>De nieuwe WebAPI-back-end publiceren

U gaat de app nu implementeren op een Azure-website zodat deze toegankelijk is vanaf alle apparaten.

1. Klik met de rechtermuisknop op het project **AppBackend** en selecteer **Publiceren**.

2. Selecteer **Microsoft Azure App Service** als publicatiedoel en selecteer vervolgens \*\*Publiceren. Het venster App Service maken wordt geopend. Hier kunt u alle benodigde Azure-resources maken die nodig zijn om de ASP.NET-web-app in Azure uit te voeren.

    ![De tegel Microsoft Azure App Service][B15]

3. Selecteer uw Azure-account in het venster **App Service maken**. Selecteer **Type wijzigen** > **Web App**. Houd de standaard **Web App-naam** en selecteer het **Abonnement**, de **Resourcegroep** en het **App Service-plan**.

4. Selecteer **Maken**.

5. Noteer de **Site-URL**-eigenschap in de sectie **Samenvatting**. Deze URL is het *eindpunt van uw back-end* verderop in de zelfstudie.

6. Selecteer **Publiceren**.

Nadat u de wizard hebt voltooid, wordt de ASP.NET-web-app naar Azure gepubliceerd. Daarna wordt de app geopend in de standaardbrowser.  Uw toepassing is zichtbaar in Azure App Services.

De URL maakt gebruik van de web-appnaam die u eerder hebt opgegeven, met de notatie http://<app_naam>.azurewebsites.net.

[B1]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push1.png
[B2]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push2.png
[B3]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push3.png
[B4]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push4.png
[B5]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push5.png
[B6]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push6.png
[B7]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push7.png
[B8]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push8.png
[B14]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push14.png
[B15]: ./media/notification-hubs-aspnet-backend-notifyusers/publish-to-app-service.png
[B16]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users16.PNG
[B18]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users18.PNG