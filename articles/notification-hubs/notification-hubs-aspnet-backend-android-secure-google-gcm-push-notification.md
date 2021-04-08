---
title: Beveiligde push meldingen verzenden met Azure Notification Hubs
description: Meer informatie over het verzenden van beveiligde push meldingen naar een Android-app vanuit Azure. Code voorbeelden geschreven in Java en C#.
documentationcenter: android
keywords: push melding, Push meldingen, Push berichten, Android-push meldingen
author: sethmanheim
manager: femila
services: notification-hubs
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 08/07/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: f2d5d618fabbe7400ce825f984ace1622a524f05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88004028"
---
# <a name="send-secure-push-notifications-with-azure-notification-hubs"></a>Beveiligde push meldingen verzenden met Azure Notification Hubs

> [!div class="op_single_selector"]
> * [Windows Universal](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>Overzicht

> [!IMPORTANT]
> U hebt een actief Azure-account nodig om deze zelfstudie te voltooien. Als u geen account hebt, kunt u binnen een paar minuten een gratis proefaccount maken. Zie [Gratis proefversie van Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started) voor meer informatie.

Met ondersteuning voor push meldingen in Microsoft Azure kunt u toegang krijgen tot een gebruiks vriendelijke, geschaalde push-bericht infrastructuur met meerdere platforms, waardoor de implementatie van push meldingen voor zowel consumenten als bedrijfs toepassingen voor mobiele platforms aanzienlijk wordt vereenvoudigd.

Vanwege regelgeving of beveiligings beperkingen kan het gebeuren dat een toepassing iets wil toevoegen in de melding die niet kan worden verzonden via de standaard infra structuur voor push meldingen. In deze zelf studie wordt beschreven hoe u dezelfde ervaring kunt krijgen door gevoelige informatie te verzenden via een beveiligde, geverifieerde verbinding tussen het Android-apparaat van de client en de back-end van de app.

Op hoog niveau is de stroom als volgt:

- De app-back-end:
  * Slaat een beveiligde Payload op in de back-enddatabase.
  * Hiermee wordt de ID van deze melding naar het Android-apparaat verzonden (er worden geen beveiligde gegevens verzonden).
- De app op het apparaat wanneer de melding wordt ontvangen:
  * Het Android-apparaat neemt contact op met de back-end die de beveiligde Payload aanvraagt.
  * De app kan de payload weer geven als een melding op het apparaat.

Het is belang rijk te weten dat in de voor gaande stroom (en in deze zelf studie) wordt aangenomen dat het apparaat een verificatie token opslaat in de lokale opslag nadat de gebruiker zich heeft aangemeld. Deze aanpak zorgt voor een naadloze ervaring, omdat het apparaat de beveiligde payload van de melding kan ophalen met dit token. Als uw toepassing geen verificatie tokens opslaat op het apparaat of als deze tokens verlopen zijn, moet de apparaat-app na het ontvangen van de push melding een algemene melding weer geven waarin de gebruiker wordt gevraagd de app te starten. De app verifieert vervolgens de gebruiker en toont de meldings lading.

Deze zelf studie laat zien hoe u beveiligde push meldingen verzendt. Het is gebaseerd op de zelf studie [gebruikers melden](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md) , dus moet u eerst de stappen in deze zelf studie volt ooien.

> [!NOTE]
> In deze zelf studie wordt ervan uitgegaan dat u uw notification hub hebt gemaakt en geconfigureerd zoals wordt beschreven in aan de [slag met Notification hubs (Android)](notification-hubs-android-push-notification-google-gcm-get-started.md).

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-android-project"></a>Het Android-project wijzigen

Nu u de back-end van uw app hebt gewijzigd om alleen de ID van een push melding te verzenden, moet u uw Android-app wijzigen zodat deze melding wordt afgehandeld en vervolgens terugbellen naar uw back-end om het beveiligde bericht weer te geven.
Voor dit doel moet u ervoor zorgen dat uw Android-app weet hoe u zich kunt verifiëren met uw back-end wanneer de push meldingen worden ontvangen.

Wijzig nu de aanmeldings stroom om de waarde van de verificatie header op te slaan in de gedeelde voor keuren van uw app. Analoge mechanismen kunnen worden gebruikt voor het opslaan van verificatie tokens (bijvoorbeeld OAuth-tokens) die de app moet gebruiken zonder dat hiervoor gebruikers referenties zijn vereist.

1. Voeg in uw Android-app-project de volgende constanten toe boven aan de `MainActivity` klasse:

    ```java
    public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
    public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";
    ```

2. `MainActivity`Werk de methode die zich nog in de klasse `getAuthorizationHeader()` bevinden, bij met de volgende code:

    ```java
    private String getAuthorizationHeader() throws UnsupportedEncodingException {
        EditText username = (EditText) findViewById(R.id.usernameText);
        EditText password = (EditText) findViewById(R.id.passwordText);
        String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
        basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);

        SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
        sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();

        return basicAuthHeader;
    }
    ```

3. Voeg `import` boven aan het bestand de volgende-instructies toe `MainActivity` :

    ```java
    import android.content.SharedPreferences;
    ```

Wijzig nu de handler die wordt aangeroepen wanneer de melding wordt ontvangen.

1. Wijzig in de `MyHandler` klasse de `OnReceive()` methode die moet worden opgenomen:

    ```java
    public void onReceive(Context context, Bundle bundle) {
        ctx = context;
        String secureMessageId = bundle.getString("secureId");
        retrieveNotification(secureMessageId);
    }
    ```

2. Voeg vervolgens de `retrieveNotification()` -methode toe, waarbij u de tijdelijke aanduiding vervangt `{back-end endpoint}` door het back-end-eind punt dat is verkregen tijdens het implementeren van uw back-end:

    ```java
    private void retrieveNotification(final String secureMessageId) {
        SharedPreferences sp = ctx.getSharedPreferences(MainActivity.NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
        final String authorizationHeader = sp.getString(MainActivity.AUTHORIZATION_HEADER_PROPERTY, null);

        new AsyncTask<Object, Object, Object>() {
            @Override
            protected Object doInBackground(Object... params) {
                try {
                    HttpUriRequest request = new HttpGet("{back-end endpoint}/api/notifications/"+secureMessageId);
                    request.addHeader("Authorization", "Basic "+authorizationHeader);
                    HttpResponse response = new DefaultHttpClient().execute(request);
                    if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                        Log.e("MainActivity", "Error retrieving secure notification" + response.getStatusLine().getStatusCode());
                        throw new RuntimeException("Error retrieving secure notification");
                    }
                    String secureNotificationJSON = EntityUtils.toString(response.getEntity());
                    JSONObject secureNotification = new JSONObject(secureNotificationJSON);
                    sendNotification(secureNotification.getString("Payload"));
                } catch (Exception e) {
                    Log.e("MainActivity", "Failed to retrieve secure notification - " + e.getMessage());
                    return e;
                }
                return null;
            }
        }.execute(null, null, null);
    }
    ```

Met deze methode wordt de back-end van uw app aangeroepen om de meldings inhoud op te halen met behulp van de referenties die zijn opgeslagen in de gedeelde voor keuren en wordt deze weer gegeven als normale melding. De melding zoekt naar de app-gebruiker op dezelfde manier als andere push meldingen.

Het is aan te raden om de gevallen van ontbrekende verificatie-header-eigenschap of afwijzing door de back-end af te handelen. De specifieke verwerking van deze gevallen is voornamelijk afhankelijk van uw doel gebruikers ervaring. Een optie is om een melding weer te geven met een algemene prompt voor de gebruiker om de daad werkelijke melding op te halen.

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de volgende acties uit om de toepassing uit te voeren:

1. Zorg ervoor dat **project appbackend** is geïmplementeerd in Azure. Als u Visual Studio gebruikt, voert u de **project appbackend** Web API-toepassing uit. Er wordt een ASP.NET-webpagina weer gegeven.
2. In eclips voert u de app uit op een fysiek Android-apparaat of de emulator.
3. Voer een gebruikers naam en wacht woord in de gebruikers interface van de Android-app in. Dit kan een wille keurige teken reeks zijn, maar ze moeten dezelfde waarde hebben.
4. Klik in de gebruikers interface van de Android-app op **Aanmelden**. Klik vervolgens op **Push verzenden**.
