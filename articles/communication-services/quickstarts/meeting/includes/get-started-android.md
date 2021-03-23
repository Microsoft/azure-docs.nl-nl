---
title: 'Quick Start: een team toevoegen aan een Android-app met behulp van Azure Communication Services'
description: In deze Quick Start leert u hoe u de bibliotheek Azure Communication Services-teams kunt gebruiken voor Android.
author: palatter
ms.author: palatter
ms.date: 01/25/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: e9069b5d43044ef0d0341717a12fcce7c4a72dc7
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104803972"
---
In deze Quick Start leert u hoe u kunt deel nemen aan een team vergadering met behulp van de Azure Communication Services teams-bibliotheek voor Android.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Android Studio](https://developer.android.com/studio), voor het maken van uw Android-toepassing.
- Een geïmplementeerde Communication Services-resource. [Een Communication Services maken](../../create-communication-resource.md).
- Een [Token voor gebruikerstoegang](../../access-tokens.md) voor uw Azure Communication Service.

## <a name="setting-up"></a>Instellen

### <a name="create-an-android-app-with-an-empty-activity"></a>Een Android-app maken met een lege activiteit

Selecteer Een nieuw Android Studio-project starten in Android Studio.

:::image type="content" source="../media/android/studio-new-project.png" alt-text="Schermopname waarin de knop Een nieuw Android Studio-project starten is geselecteerd in Android Studio.":::

Selecteer de projectsjabloon Lege activiteit onder Telefoon en tablet.

:::image type="content" source="../media/android/studio-blank-activity.png" alt-text="Schermopname waarin de optie Lege activiteit is geselecteerd op het scherm Projectsjabloon.":::

Noem het project `TeamsEmbedAndroidGettingStarted` , stel de taal in op Java en selecteer minimale client bibliotheek van API 21: Android 5,0 (Lollipop) of hoger.

:::image type="content" source="../media/android/studio-calling-min-api.png" alt-text="Schermopname waarin de optie Lege activiteit is geselecteerd op het scherm Projectsjabloon 2.":::


### <a name="install-the-azure-package"></a>Het Azure-pakket installeren

Voeg in het app-niveau build. gradle de volgende regels toe aan de secties dependencies en Android

```groovy
android {
    ...
    packagingOptions {
        pickFirst  'META-INF/*'
    }
}
```

```groovy
dependencies {
    ...
    implementation 'com.azure.android:azure-communication-common:1.0.0-beta.6'
    ...
}
```

### <a name="install-the-teams-embed-package"></a>Het pakket voor het insluiten van teams installeren

Down load het `MicrosoftTeamsSDK` pakket.

Pak vervolgens de map MicrosoftTeamsSDK uit in de app-map van uw projecten. Bijvoorbeeld `TeamsEmbedAndroidGettingStarted/app/MicrosoftTeamsSDK`.

### <a name="add-teams-embed-package-to-your-buildgradle"></a>Een pakket voor het insluiten van teams toevoegen aan uw build. gradle

Voeg op uw app-niveau `build.gradle` de volgende regel toe aan het einde van het bestand:

```groovy
apply from: 'MicrosoftTeamsSDK/MicrosoftTeamsSDK.gradle'
```

Project synchroniseren met gradle-bestanden.

### <a name="create-application-class"></a>Toepassings klasse maken

Maak een nieuw Java-klassen bestand met de naam `TeamsEmbedAndroidGettingStarted` . Dit is de toepassings klasse die moet worden uitgebreid `TeamsSDKApplication` . [Documentatie voor Android](https://developer.android.com/reference/android/app/Application)

:::image type="content" source="../media/android/application-class-location.png" alt-text="Scherm opname waarin wordt weer gegeven waar een toepassings klasse moet worden gemaakt in Android Studio":::

```java
package com.microsoft.teamsembedandroidgettingstarted;

import com.microsoft.teamssdk.app.TeamsSDKApplication;

public class TeamsEmbedAndroidGettingStarted extends TeamsSDKApplication {
}
```

### <a name="update-themes"></a>Thema's bijwerken

Stel de naam van de stijl `AppTheme` in in zowel uw `theme.xml` als- `theme.xml (night)` bestanden.

:::image type="content" source="../media/android/theme-settings.png" alt-text="Scherm opname van de theme.xml bestanden in Android Studio":::

```xml
<style name="AppTheme" parent="Theme.AppCompat.DayNight.DarkActionBar">
```

```xml
<style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
```

### <a name="add-permissions-to-application-manifest"></a>Machtigingen toevoegen aan het toepassingsmanifest

De `RECORD_AUDIO` machtiging toevoegen aan het manifest van de toepassing ( `app/src/main/AndroidManifest.xml` ):  


```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.yourcompany.TeamsEmbedAndroidGettingStarted">
    <uses-permission android:name="android.permission.RECORD_AUDIO"/>
    <application
```

### <a name="add-app-name-and-theme-to-application-manifest"></a>De app-naam en het thema toevoegen aan het toepassings manifest

Voeg ' xmlns: tools = ' http://schemas.android.com/tools ' toe aan het manifest.

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    package="com.yourcompany.TeamsEmbedAndroidGettingStarted">
```

Voeg toe `.TeamsEmbedAndroidGettingStarted` aan `android:name` , `android:name` aan `tools:replace` en wijzig de `android:theme` in `@style/AppTheme`

```xml
<application
    android:name=".TeamsEmbedAndroidGettingStarted"
    tools:replace="android:name"
    android:theme="@style/AppTheme"
    android:allowBackup="true"
    android:icon="@mipmap/ic_launcher"
    android:label="@string/app_name"
    android:roundIcon="@mipmap/ic_launcher_round"
    android:supportsRtl="true">
```

### <a name="set-up-the-layout-for-the-app"></a>De indeling van de app instellen

Maak een knop met de ID van `join_meeting` . Ga naar (`app/src/main/res/layout/activity_main.xml`) en vervang de inhoud van het bestand door het volgende:

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".MainActivity">

    <Button
        android:id="@+id/join_meeting"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Join Meeting"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintEnd_toEndOf="parent"
        app:layout_constraintStart_toStartOf="parent"
        app:layout_constraintTop_toTopOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

### <a name="create-the-main-activity-scaffolding-and-bindings"></a>De hoofdopbouw en -bindingen van de activiteit maken

Als de lay-out is gemaakt, kan de basis steiger van de activiteit samen met de vereiste bindingen worden toegevoegd. Met de activiteit wordt het aanvragen van runtime-machtigingen verwerkt, wordt de Meeting-client gemaakt en wordt er een vergadering toegevoegd wanneer op de knop wordt geklikt. Elk onderdeel wordt in een aparte sectie behandeld. 

De `onCreate` methode wordt overschreven om aan te roepen `getAllPermissions` en `createAgent` ook de bindingen voor de knop toe te voegen `Join Meeting` . Dit wordt slechts eenmalig uitgevoerd wanneer de activiteit wordt gemaakt. Raadpleeg de handleiding [Informatie over de levenscyclus van activiteiten](https://developer.android.com/guide/components/activities/activity-lifecycle) voor meer informatie over `onCreate`.

Ga naar **MainActivity.java** en vervang de inhoud door de volgende code:

```java
package com.yourcompany.teamsembedandroidgettingstarted;

import androidx.appcompat.app.AppCompatActivity;
import androidx.core.app.ActivityCompat;

import android.Manifest;
import android.content.pm.PackageManager;
import android.os.Bundle;
import android.widget.Button;
import android.widget.Toast;

import com.azure.android.communication.common.CommunicationTokenCredential;
import com.azure.android.communication.common.CommunicationTokenRefreshOptions;
import com.azure.android.communication.ui.meetings.MeetingJoinOptions;
import com.azure.android.communication.ui.meetings.MeetingUIClient;

import java.util.ArrayList;
import java.util.concurrent.Callable;

public class MainActivity extends AppCompatActivity {

    private final String displayName = "John Smith";

    private MeetingUIClient meetingUIClient;
    private MeetingJoinOptions meetingJoinOptions;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        meetingJoinOptions = new MeetingJoinOptions(displayName);
        
        getAllPermissions();
        createMeetingClient();

        Button joinMeeting = findViewById(R.id.join_meeting);
        joinMeeting.setOnClickListener(l -> joinMeeting());
    }

    private void createMeetingClient() {
        // See section on creating meeting client
    }

    private void joinMeeting() {
        // See section on joining a meeting
    }

    private void getAllPermissions() {
        // See section on getting permissions
    }
}
```

### <a name="request-permissions-at-runtime"></a>Machtigingen tijdens runtime aanvragen

Voor Android 6.0 en hoger (API-niveau 23) en `targetSdkVersion` 23 of hoger worden machtigingen toegekend tijdens de runtime in plaats van tijdens de installatie van de app. Om dit te ondersteunen kan `getAllPermissions` worden geïmplementeerd om `ActivityCompat.checkSelfPermission` en `ActivityCompat.requestPermissions` aan te roepen voor elke vereiste machtiging.

```java
/**
 * Request each required permission if the app doesn't already have it.
 */
private void getAllPermissions() {
    String[] requiredPermissions = new String[]{Manifest.permission.RECORD_AUDIO};
    ArrayList<String> permissionsToAskFor = new ArrayList<>();
    for (String permission : requiredPermissions) {
        if (ActivityCompat.checkSelfPermission(this, permission) != PackageManager.PERMISSION_GRANTED) {
            permissionsToAskFor.add(permission);
        }
    }
    if (!permissionsToAskFor.isEmpty()) {
        ActivityCompat.requestPermissions(this, permissionsToAskFor.toArray(new String[0]), 202);
    }
}
```

> [!NOTE]
> Houd tijdens het ontwerpen van uw app rekening met het moment waarop deze machtigingen moeten worden aangevraagd. Machtigingen moeten worden aangevraagd wanneer u ze nodig hebt, niet van tevoren. Zie de [handleiding met machtigingen voor Android](https://developer.android.com/training/permissions/requesting) voor meer informatie.

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de Azure Communication Services-teams bibliotheek voor insluiting:

| Naam                                  | Beschrijving                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| MeetingUIClient| De MeetingUIClient is het belangrijkste ingangs punt voor de insluitings bibliotheek van teams. |
| MeetingJoinOptions | MeetingJoinOptions worden gebruikt voor Configureer bare opties zoals weergave naam. |
| CallState | De CallState wordt gebruikt voor het rapporteren van status wijzigingen van de oproep. U kunt kiezen uit de volgende opties: `connecting` , `waitingInLobby` , en `connected` `ended` . |

## <a name="create-a-meetingclient-from-the-user-access-token"></a>Een MeetingClient maken op basis van het token voor gebruikers toegang

Een geverifieerde Meeting-client kan worden geïnstantieerd met een token voor gebruikers toegang. Dit token wordt meestal gegenereerd door een service met verificatie die specifiek is voor de toepassing. Raadpleeg de hand leiding voor [toegangs tokens voor gebruikers](../../access-tokens.md) voor meer informatie over tokens voor gebruikers toegang. Voor de quickstart vervangt u `<USER_ACCESS_TOKEN>` door een gebruikerstoegangstoken dat voor uw Azure Communication Service-resource is gegenereerd.

```java
private void createMeetingClient() {
    try {
        CommunicationTokenRefreshOptions refreshOptions = new CommunicationTokenRefreshOptions(tokenRefresher, true, "<USER_ACCESS_TOKEN>");
        CommunicationTokenCredential credential = new CommunicationTokenCredential(refreshOptions);
        meetingUIClient = new MeetingUIClient(credential);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to create meeting client: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

## <a name="setup-token-refreshing"></a>Het installatie token vernieuwen

Een aanroep bare `tokenRefresher` methode maken. Maak vervolgens een `fetchToken` methode om het token van de gebruiker op te halen. [Hier vindt u instructies over hoe u dit kunt doen](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens?pivots=programming-language-java)

```java
Callable<String> tokenRefresher = () -> {
    return fetchToken();
};

public String fetchToken() {
    // Get token
    return USER_ACCESS_TOKEN;
}
```

## <a name="get-the-teams-meeting-link"></a>Koppeling naar de Teams-vergadering ophalen

De koppeling naar de Teams-vergadering kan worden opgehaald met behulp van Graph API’s. Dit wordt beschreven in [Graph-documentatie](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true).
De communicatie services die client bibliotheek aanroept, accepteren een koppeling naar de vergadering volledige teams. Deze koppeling wordt geretourneerd als onderdeel van de `onlineMeeting`-resource, die toegankelijk is bij de [eigenschap `joinWebUrl`](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true). U kunt de vereiste vergaderingsgegevens ook ophalen via de **URL** in de uitnodiging voor de Teams-vergadering zelf.

## <a name="start-a-meeting-using-the-meeting-client"></a>Een vergadering starten met de Meeting-client

Deelname aan een vergadering kan worden gedaan via de en `MeetingClient` alleen een `meetingURL` en de `JoinOptions` . Vervangen `<MEETING_URL>` door een team dat een URL heeft.

```java
/**
 * Join a meeting with a meetingURL.
 */
private void joinMeeting() {
    try {
        meetingUIClient.join("<MEETING_URL>", meetingJoinOptions);
    } catch (Exception ex) {
        Toast.makeText(getApplicationContext(), "Failed to join meeting: " + ex.getMessage(), Toast.LENGTH_SHORT).show();
    }
}
```

## <a name="launch-the-app-and-join-a-meeting"></a>De app starten en deel nemen aan een vergadering

De app kan nu worden gestart met behulp van de knop App uitvoeren op de werkbalk (Shift + F10). 

:::image type="content" source="../media/android/quickstart-android-join-meeting.png" alt-text="Schermopname met de voltooide toepassing.":::

:::image type="content" source="../media/android/quickstart-android-joined-meeting.png" alt-text="Scherm opname waarin de voltooide toepassing wordt weer gegeven nadat de vergadering is toegevoegd.":::

## <a name="sample-code"></a>Voorbeeldcode

U kunt de voorbeeld-app downloaden uit [GitHub](https://github.com/Azure-Samples/teams-embed-android-getting-started).
